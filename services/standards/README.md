# 📘 IMCBS Development Standards & Best Practices

> **Welcome to IMCBS!** This guide will help you understand our development standards, coding practices, and workflow. Perfect for new team members!

---

## 📚 Table of Contents

- [Git Workflow](#git-workflow)
- [Code Style Guidelines](#code-style-guidelines)
- [Project Structure](#project-structure)
- [Naming Conventions](#naming-conventions)
- [Environment Variables](#environment-variables)
- [Error Handling](#error-handling)
- [API Design](#api-design)
- [Security Guidelines](#security-guidelines)

---

## 🔀 Git Workflow

### Branch Strategy

```bash
main          # Production-ready code
├── dev       # Development branch
├── staging   # Pre-production testing
└── feature/* # Feature branches
```

### Daily Workflow

```bash
# 1. Always pull latest changes before starting work
git checkout dev
git pull origin dev

# 2. Create a new feature branch
git checkout -b feature/your-feature-name

# 3. Make your changes and commit
git add .
git commit -m "feat: add user authentication"

# 4. Push your branch
git push origin feature/your-feature-name

# 5. Create Pull Request on GitHub/GitLab
```

### Commit Message Format

```bash
# Format: <type>: <description>

feat: add new feature
fix: fix bug in authentication
docs: update README
style: format code
refactor: restructure login logic
test: add unit tests
chore: update dependencies
```

**Examples:**
```bash
git commit -m "feat: add Cloudflare R2 storage integration"
git commit -m "fix: resolve CORS issue in API"
git commit -m "docs: update deployment guide"
```

---

## 💻 Code Style Guidelines

### Python (Django)

```python
# ✅ Good: Clear variable names, proper spacing
def calculate_total_price(items, discount_rate=0):
    """
    Calculate total price with optional discount.
    
    Args:
        items (list): List of item dictionaries
        discount_rate (float): Discount percentage (0-1)
    
    Returns:
        float: Total price after discount
    """
    subtotal = sum(item['price'] * item['quantity'] for item in items)
    discount = subtotal * discount_rate
    return subtotal - discount


# ❌ Bad: Unclear names, no documentation
def calc(i, d=0):
    s = sum(x['p'] * x['q'] for x in i)
    return s - (s * d)
```

### JavaScript/Node.js

```javascript
// ✅ Good: Consistent formatting, async/await
const getUserProfile = async (userId) => {
  try {
    const user = await User.findById(userId);
    if (!user) {
      throw new Error('User not found');
    }
    return user;
  } catch (error) {
    console.error('Error fetching user:', error);
    throw error;
  }
};

// ❌ Bad: Nested callbacks, poor error handling
function getUser(id, callback) {
  User.findById(id, function(err, user) {
    if(err) callback(err);
    else callback(null, user);
  });
}
```

### React

```jsx
// ✅ Good: Functional components, hooks, clear naming
import { useState, useEffect } from 'react';

const UserProfile = ({ userId }) => {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchUserData();
  }, [userId]);

  const fetchUserData = async () => {
    try {
      const response = await fetch(`/api/users/${userId}`);
      const data = await response.json();
      setUser(data);
    } catch (error) {
      console.error('Error:', error);
    } finally {
      setLoading(false);
    }
  };

  if (loading) return <div>Loading...</div>;
  return <div>{user.name}</div>;
};

// ❌ Bad: Class components, inconsistent naming
class userProfile extends React.Component {
  constructor(props) {
    super(props);
    this.state = { u: null };
  }
  // ...
}
```

---

## 📁 Project Structure

### Django Project

```
project-name/
├── config/                      # Project settings
│   ├── settings/
│   │   ├── base.py             # Base settings
│   │   ├── development.py      # Dev settings
│   │   └── production.py       # Prod settings
│   ├── urls.py
│   └── wsgi.py
│
├── apps/                        # Django apps
│   ├── accounts/               # User management
│   ├── api/                    # API endpoints
│   └── core/                   # Core functionality
│
├── static/                      # Static files (CSS, JS, images)
├── media/                       # User uploads
├── templates/                   # HTML templates
├── requirements/
│   ├── base.txt                # Common dependencies
│   ├── dev.txt                 # Development dependencies
│   └── prod.txt                # Production dependencies
│
├── .env                         # Environment variables (never commit!)
├── .gitignore
├── manage.py
└── README.md
```

### Node.js/Express Project

```
project-name/
├── src/
│   ├── controllers/            # Request handlers
│   ├── models/                 # Database models
│   ├── routes/                 # API routes
│   ├── middleware/             # Custom middleware
│   ├── utils/                  # Helper functions
│   └── config/                 # Configuration
│       ├── database.js
│       └── environment.js
│
├── tests/                      # Test files
├── public/                     # Static files
├── .env                        # Environment variables
├── .gitignore
├── package.json
├── server.js                   # Entry point
└── README.md
```

### React Project (Feature-Based)

```
project-name/
├── src/
│   ├── features/               # Feature-based modules
│   │   ├── auth/
│   │   │   ├── components/
│   │   │   ├── hooks/
│   │   │   ├── services/
│   │   │   └── index.js
│   │   ├── dashboard/
│   │   └── profile/
│   │
│   ├── shared/                 # Shared components
│   │   ├── components/
│   │   ├── hooks/
│   │   └── utils/
│   │
│   ├── assets/                 # Images, fonts, etc.
│   ├── styles/                 # Global styles
│   ├── App.jsx
│   └── index.jsx
│
├── public/
├── .env
├── package.json
└── README.md
```

---

## 🏷️ Naming Conventions

### Files & Folders

```bash
# Python/Django
user_profile.py          # Snake case for Python files
UserProfileView          # PascalCase for classes
calculate_total()        # Snake case for functions

# JavaScript/React
UserProfile.jsx          # PascalCase for components
userService.js           # CamelCase for files
calculateTotal()         # CamelCase for functions
```

### Variables & Constants

```python
# Python
USER_ROLE_ADMIN = 'admin'        # UPPER_SNAKE_CASE for constants
user_email = 'user@example.com'  # snake_case for variables
```

```javascript
// JavaScript
const API_BASE_URL = 'https://api.example.com';  // UPPER_SNAKE_CASE for constants
let userEmail = 'user@example.com';              // camelCase for variables
```

### API Endpoints

```bash
# RESTful naming
GET    /api/users              # Get all users
GET    /api/users/:id          # Get single user
POST   /api/users              # Create user
PUT    /api/users/:id          # Update user
DELETE /api/users/:id          # Delete user

# Use plural nouns, lowercase, hyphens for multi-word
GET    /api/user-profiles
POST   /api/access-tokens
```

---

## 🔐 Environment Variables

### Structure

Create a `.env` file (never commit this!):

```env
# Application
APP_NAME=IMCBS Project
APP_ENV=development
DEBUG=true
SECRET_KEY=your-secret-key-here

# Database
DB_HOST=localhost
DB_PORT=5432
DB_NAME=imcbs_db
DB_USER=postgres
DB_PASSWORD=your-password

# Cloudflare R2
CLOUDFLARE_R2_ENABLED=true
CLOUDFLARE_R2_BUCKET=your-bucket
CLOUDFLARE_R2_PUBLIC_URL=pub-xxx.r2.dev
CLOUDFLARE_R2_ACCESS_KEY=your-access-key
CLOUDFLARE_R2_SECRET_KEY=your-secret-key

# API Keys
STRIPE_API_KEY=sk_test_xxx
SENDGRID_API_KEY=SG.xxx
```

### Usage

```python
# Python/Django
import os
from dotenv import load_dotenv

load_dotenv()

DEBUG = os.getenv('DEBUG', 'False') == 'True'
DATABASE_NAME = os.getenv('DB_NAME', 'defaultdb')
```

```javascript
// Node.js
require('dotenv').config();

const config = {
  port: process.env.PORT || 3000,
  dbUrl: process.env.DB_URL,
  apiKey: process.env.API_KEY
};
```

---

## 🚨 Error Handling

### Python/Django

```python
# views.py
from rest_framework.views import APIView
from rest_framework.response import Response
from rest_framework import status
import logging

logger = logging.getLogger(__name__)

class UserProfileView(APIView):
    def get(self, request, user_id):
        try:
            user = User.objects.get(id=user_id)
            serializer = UserSerializer(user)
            return Response(serializer.data)
        except User.DoesNotExist:
            logger.warning(f'User not found: {user_id}')
            return Response(
                {'error': 'User not found'},
                status=status.HTTP_404_NOT_FOUND
            )
        except Exception as e:
            logger.error(f'Error fetching user: {str(e)}')
            return Response(
                {'error': 'Internal server error'},
                status=status.HTTP_500_INTERNAL_SERVER_ERROR
            )
```

### Node.js/Express

```javascript
// errorHandler.js
const errorHandler = (err, req, res, next) => {
  console.error(err.stack);

  const statusCode = err.statusCode || 500;
  const message = err.message || 'Internal Server Error';

  res.status(statusCode).json({
    success: false,
    error: message,
    ...(process.env.NODE_ENV === 'development' && { stack: err.stack })
  });
};

// routes/users.js
router.get('/:id', async (req, res, next) => {
  try {
    const user = await User.findById(req.params.id);
    if (!user) {
      const error = new Error('User not found');
      error.statusCode = 404;
      throw error;
    }
    res.json(user);
  } catch (error) {
    next(error);
  }
});
```

---

## 🌐 API Design

### Response Format

```javascript
// Success Response
{
  "success": true,
  "data": {
    "id": 1,
    "name": "John Doe",
    "email": "john@example.com"
  },
  "message": "User retrieved successfully"
}

// Error Response
{
  "success": false,
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "User with ID 123 not found"
  }
}

// Paginated Response
{
  "success": true,
  "data": [ /* items */ ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "totalPages": 8
  }
}
```

### Status Codes

```bash
200 OK              # Successful GET, PUT, PATCH
201 Created         # Successful POST
204 No Content      # Successful DELETE
400 Bad Request     # Invalid request data
401 Unauthorized    # Authentication required
403 Forbidden       # No permission
404 Not Found       # Resource not found
500 Server Error    # Internal server error
```

---

## 🔒 Security Guidelines

### Never Commit Sensitive Data

```bash
# Add to .gitignore
.env
.env.local
.env.*.local
*.pem
*.key
secrets/
```

### Password Handling

```python
# Django - Use built-in password hashing
from django.contrib.auth.hashers import make_password, check_password

# Hash password
hashed = make_password('user_password')

# Verify password
is_valid = check_password('user_password', hashed)
```

```javascript
// Node.js - Use bcrypt
const bcrypt = require('bcrypt');

// Hash password
const hashedPassword = await bcrypt.hash('user_password', 10);

// Verify password
const isValid = await bcrypt.compare('user_password', hashedPassword);
```

### Input Validation

```python
# Django REST Framework
from rest_framework import serializers

class UserSerializer(serializers.ModelSerializer):
    email = serializers.EmailField(required=True)
    age = serializers.IntegerField(min_value=0, max_value=150)
    
    class Meta:
        model = User
        fields = ['email', 'age', 'name']
```

```javascript
// Node.js with Joi
const Joi = require('joi');

const userSchema = Joi.object({
  email: Joi.string().email().required(),
  age: Joi.number().integer().min(0).max(150),
  name: Joi.string().min(2).max(100).required()
});

const { error, value } = userSchema.validate(req.body);
```

---

## 📝 Code Review Checklist

Before submitting a Pull Request:

- [ ] Code follows naming conventions
- [ ] No sensitive data in commits
- [ ] Tests are passing
- [ ] Documentation is updated
- [ ] Error handling is proper
- [ ] Console.logs removed (or use proper logging)
- [ ] Code is formatted consistently
- [ ] No commented-out code
- [ ] Environment variables used for configuration

---

## 🎯 Quick Tips for New Team Members

1. **Always pull latest code** before starting work
2. **Create feature branches** for new work
3. **Write clear commit messages** following our format
4. **Use environment variables** for sensitive data
5. **Follow the project structure** already in place
6. **Ask questions** if unsure - we're here to help!
7. **Review existing code** to understand our patterns
8. **Test your changes** before pushing

---

## 🆘 Need Help?

- Check our **other documentation** in this repo
- Ask in team **Slack/Discord channel**
- Review **similar code** in the project
- Reach out to **senior developers**

---

**Welcome to the team! Happy coding! 🎉**
