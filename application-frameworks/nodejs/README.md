# ⚙️ Node.js + Express - Complete Beginner's Guide

> **Master Node.js backend development!** Learn to build powerful REST APIs with Node.js and Express from scratch.

---

## 📚 Table of Contents

- [What is Node.js?](#what-is-nodejs)
- [Installation & Setup](#installation--setup)
- [Your First Node.js App](#your-first-nodejs-app)
- [Express.js Framework](#expressjs-framework)
- [Project Structure](#project-structure)
- [Routing & HTTP Methods](#routing--http-methods)
- [Middleware](#middleware)
- [Database Integration](#database-integration)
- [Environment Variables](#environment-variables)
- [Error Handling](#error-handling)
- [Authentication & Authorization](#authentication--authorization)
- [File Upload](#file-upload)
- [CORS Configuration](#cors-configuration)
- [Testing](#testing)
- [Common Commands](#common-commands)
- [Best Practices](#best-practices)

---

## 🤔 What is Node.js?

**Node.js** is a JavaScript runtime that lets you run JavaScript on the server (not just in browsers).

**Why Node.js?**
- ✅ Use JavaScript for both frontend and backend
- ✅ Fast and efficient (event-driven, non-blocking I/O)
- ✅ Huge ecosystem (NPM packages)
- ✅ Great for real-time applications
- ✅ Scalable and production-ready
- ✅ Large community support

**Common Uses:**
- REST APIs
- Real-time applications (chat, notifications)
- Microservices
- Server-side rendering
- Command-line tools

---

## 📦 Installation & Setup

### Step 1: Install Node.js

```bash
# Check if Node.js is installed
node --version
npm --version

# If not installed:
# Download from: https://nodejs.org/
# Choose LTS (Long Term Support) version
```

### Step 2: Initialize a New Project

```bash
# Create project folder
mkdir my-node-api
cd my-node-api

# Initialize package.json
npm init -y

# This creates package.json with default values
```

### Step 3: Install Essential Packages

```bash
# Install Express (web framework)
npm install express

# Install development tools
npm install --save-dev nodemon

# Install other useful packages
npm install dotenv cors body-parser
```

---

## 🚀 Your First Node.js App

### Create `server.js`

```javascript
// server.js
const http = require('http');

const hostname = '127.0.0.1';
const port = 3000;

const server = http.createServer((req, res) => {
  res.statusCode = 200;
  res.setHeader('Content-Type', 'text/plain');
  res.end('Hello, World!\n');
});

server.listen(port, hostname, () => {
  console.log(`Server running at http://${hostname}:${port}/`);
});
```

### Run the Server

```bash
# Run the server
node server.js

# Open browser: http://127.0.0.1:3000/
# You should see "Hello, World!"
```

---

## 🎯 Express.js Framework

Express makes building web applications much easier!

### Basic Express Server

```javascript
// server.js
const express = require('express');
const app = express();
const PORT = 3000;

// Middleware to parse JSON
app.use(express.json());

// Route
app.get('/', (req, res) => {
  res.json({ message: 'Hello from Express!' });
});

// Start server
app.listen(PORT, () => {
  console.log(`Server running on http://localhost:${PORT}`);
});
```

### Update `package.json` for Nodemon

```json
{
  "scripts": {
    "start": "node server.js",
    "dev": "nodemon server.js"
  }
}
```

```bash
# Run with auto-restart on file changes
npm run dev
```

---

## 📁 Project Structure

Organize your Node.js project properly:

```
my-node-api/
├── src/
│   ├── config/              # Configuration files
│   │   ├── database.js
│   │   └── environment.js
│   │
│   ├── controllers/         # Request handlers
│   │   ├── authController.js
│   │   └── userController.js
│   │
│   ├── models/              # Database models
│   │   └── User.js
│   │
│   ├── routes/              # API routes
│   │   ├── authRoutes.js
│   │   └── userRoutes.js
│   │
│   ├── middleware/          # Custom middleware
│   │   ├── authMiddleware.js
│   │   └── errorHandler.js
│   │
│   ├── utils/               # Helper functions
│   │   └── validator.js
│   │
│   └── app.js               # Express app setup
│
├── .env                     # Environment variables
├── .gitignore              # Git ignore file
├── package.json            # Dependencies
└── server.js               # Entry point
```

### Example `app.js`

```javascript
// src/app.js
const express = require('express');
const cors = require('cors');
require('dotenv').config();

const app = express();

// Middleware
app.use(cors());
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Routes
app.use('/api/auth', require('./routes/authRoutes'));
app.use('/api/users', require('./routes/userRoutes'));

// Health check
app.get('/health', (req, res) => {
  res.json({ status: 'OK', message: 'Server is running' });
});

// 404 handler
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

// Error handler
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something went wrong!' });
});

module.exports = app;
```

### Example `server.js`

```javascript
// server.js
const app = require('./src/app');
const PORT = process.env.PORT || 3000;

app.listen(PORT, () => {
  console.log(`🚀 Server running on http://localhost:${PORT}`);
});
```

---

## 🌐 Routing & HTTP Methods

### Basic Routes

```javascript
// src/routes/userRoutes.js
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');

// GET request
router.get('/', userController.getAllUsers);

// GET single user
router.get('/:id', userController.getUserById);

// POST request
router.post('/', userController.createUser);

// PUT request (update)
router.put('/:id', userController.updateUser);

// DELETE request
router.delete('/:id', userController.deleteUser);

module.exports = router;
```

### Controller Example

```javascript
// src/controllers/userController.js

// Get all users
exports.getAllUsers = async (req, res) => {
  try {
    // Fetch users from database
    const users = [
      { id: 1, name: 'John Doe', email: 'john@example.com' },
      { id: 2, name: 'Jane Smith', email: 'jane@example.com' }
    ];
    
    res.json({
      success: true,
      data: users
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};

// Get single user
exports.getUserById = async (req, res) => {
  try {
    const { id } = req.params;
    
    // Find user in database
    const user = { id, name: 'John Doe', email: 'john@example.com' };
    
    if (!user) {
      return res.status(404).json({
        success: false,
        error: 'User not found'
      });
    }
    
    res.json({
      success: true,
      data: user
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};

// Create new user
exports.createUser = async (req, res) => {
  try {
    const { name, email, password } = req.body;
    
    // Validation
    if (!name || !email || !password) {
      return res.status(400).json({
        success: false,
        error: 'Please provide all required fields'
      });
    }
    
    // Create user in database
    const newUser = { id: 3, name, email };
    
    res.status(201).json({
      success: true,
      data: newUser,
      message: 'User created successfully'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};

// Update user
exports.updateUser = async (req, res) => {
  try {
    const { id } = req.params;
    const { name, email } = req.body;
    
    // Update user in database
    const updatedUser = { id, name, email };
    
    res.json({
      success: true,
      data: updatedUser,
      message: 'User updated successfully'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};

// Delete user
exports.deleteUser = async (req, res) => {
  try {
    const { id } = req.params;
    
    // Delete user from database
    
    res.json({
      success: true,
      message: 'User deleted successfully'
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};
```

---

## 🔧 Middleware

Middleware functions have access to request, response, and next function.

### Built-in Middleware

```javascript
const express = require('express');
const app = express();

// Parse JSON bodies
app.use(express.json());

// Parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));

// Serve static files
app.use(express.static('public'));
```

### Custom Middleware

```javascript
// src/middleware/logger.js
const logger = (req, res, next) => {
  console.log(`${req.method} ${req.url} - ${new Date().toISOString()}`);
  next(); // Important! Call next() to continue
};

module.exports = logger;

// Use in app.js
app.use(logger);
```

### Authentication Middleware

```javascript
// src/middleware/authMiddleware.js
const jwt = require('jsonwebtoken');

const authMiddleware = (req, res, next) => {
  try {
    // Get token from header
    const token = req.headers.authorization?.split(' ')[1];
    
    if (!token) {
      return res.status(401).json({
        success: false,
        error: 'No token provided'
      });
    }
    
    // Verify token
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({
      success: false,
      error: 'Invalid token'
    });
  }
};

module.exports = authMiddleware;

// Use in routes
const authMiddleware = require('../middleware/authMiddleware');
router.get('/profile', authMiddleware, userController.getProfile);
```

---

## 💾 Database Integration

### MongoDB with Mongoose

```bash
npm install mongoose
```

```javascript
// src/config/database.js
const mongoose = require('mongoose');

const connectDB = async () => {
  try {
    await mongoose.connect(process.env.MONGODB_URI, {
      useNewUrlParser: true,
      useUnifiedTopology: true
    });
    console.log('✅ MongoDB connected');
  } catch (error) {
    console.error('❌ MongoDB connection error:', error);
    process.exit(1);
  }
};

module.exports = connectDB;

// In server.js
const connectDB = require('./src/config/database');
connectDB();
```

### Create a Model

```javascript
// src/models/User.js
const mongoose = require('mongoose');
const bcrypt = require('bcryptjs');

const userSchema = new mongoose.Schema({
  name: {
    type: String,
    required: [true, 'Name is required'],
    trim: true
  },
  email: {
    type: String,
    required: [true, 'Email is required'],
    unique: true,
    lowercase: true
  },
  password: {
    type: String,
    required: [true, 'Password is required'],
    minlength: 6
  },
  role: {
    type: String,
    enum: ['user', 'admin'],
    default: 'user'
  }
}, {
  timestamps: true
});

// Hash password before saving
userSchema.pre('save', async function(next) {
  if (!this.isModified('password')) return next();
  this.password = await bcrypt.hash(this.password, 10);
  next();
});

// Method to compare passwords
userSchema.methods.comparePassword = async function(candidatePassword) {
  return await bcrypt.compare(candidatePassword, this.password);
};

module.exports = mongoose.model('User', userSchema);
```

### Using the Model

```javascript
// src/controllers/userController.js
const User = require('../models/User');

exports.createUser = async (req, res) => {
  try {
    const { name, email, password } = req.body;
    
    // Check if user exists
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({
        success: false,
        error: 'Email already exists'
      });
    }
    
    // Create user
    const user = await User.create({ name, email, password });
    
    res.status(201).json({
      success: true,
      data: {
        id: user._id,
        name: user.name,
        email: user.email
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};

exports.getAllUsers = async (req, res) => {
  try {
    const users = await User.find().select('-password');
    
    res.json({
      success: true,
      count: users.length,
      data: users
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};
```

### PostgreSQL with Prisma

```bash
npm install prisma @prisma/client
npx prisma init
```

```prisma
// prisma/schema.prisma
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id        Int      @id @default(autoincrement())
  name      String
  email     String   @unique
  password  String
  createdAt DateTime @default(now())
  updatedAt DateTime @updatedAt
}
```

```bash
npx prisma migrate dev --name init
npx prisma generate
```

---

## 🔐 Environment Variables

### Create `.env` File

```env
# Server
PORT=3000
NODE_ENV=development

# Database
MONGODB_URI=mongodb://localhost:27017/mydb
# OR PostgreSQL
DATABASE_URL=postgresql://user:password@localhost:5432/mydb

# JWT
JWT_SECRET=your-super-secret-key-change-this
JWT_EXPIRE=7d

# Cloudflare R2
CLOUDFLARE_R2_BUCKET=mybucket
CLOUDFLARE_R2_ACCESS_KEY=your-key
CLOUDFLARE_R2_SECRET_KEY=your-secret

# External APIs
STRIPE_SECRET_KEY=sk_test_xxx
SENDGRID_API_KEY=SG.xxx
```

### Load Environment Variables

```javascript
// At the top of server.js or app.js
require('dotenv').config();

// Access variables
const PORT = process.env.PORT || 3000;
const JWT_SECRET = process.env.JWT_SECRET;
```

### `.env.example` (Safe to Commit)

```env
PORT=3000
NODE_ENV=development
MONGODB_URI=mongodb://localhost:27017/mydb
JWT_SECRET=your-secret-here
```

---

## ⚠️ Error Handling

### Global Error Handler

```javascript
// src/middleware/errorHandler.js
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

module.exports = errorHandler;

// In app.js (must be last)
app.use(errorHandler);
```

### Custom Error Class

```javascript
// src/utils/ApiError.js
class ApiError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    Error.captureStackTrace(this, this.constructor);
  }
}

module.exports = ApiError;

// Usage in controller
const ApiError = require('../utils/ApiError');

if (!user) {
  throw new ApiError('User not found', 404);
}
```

### Try-Catch Wrapper

```javascript
// src/utils/asyncHandler.js
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

module.exports = asyncHandler;

// Usage
const asyncHandler = require('../utils/asyncHandler');

exports.getUsers = asyncHandler(async (req, res) => {
  const users = await User.find();
  res.json({ success: true, data: users });
  // No need for try-catch!
});
```

---

## 🔑 Authentication & Authorization

### JWT Authentication

```bash
npm install jsonwebtoken bcryptjs
```

```javascript
// src/controllers/authController.js
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');
const User = require('../models/User');

// Register
exports.register = async (req, res) => {
  try {
    const { name, email, password } = req.body;
    
    // Check if user exists
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({
        success: false,
        error: 'Email already registered'
      });
    }
    
    // Create user
    const user = await User.create({ name, email, password });
    
    // Generate token
    const token = jwt.sign(
      { id: user._id, email: user.email },
      process.env.JWT_SECRET,
      { expiresIn: '7d' }
    );
    
    res.status(201).json({
      success: true,
      token,
      data: {
        id: user._id,
        name: user.name,
        email: user.email
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};

// Login
exports.login = async (req, res) => {
  try {
    const { email, password } = req.body;
    
    // Validation
    if (!email || !password) {
      return res.status(400).json({
        success: false,
        error: 'Please provide email and password'
      });
    }
    
    // Find user
    const user = await User.findOne({ email });
    if (!user) {
      return res.status(401).json({
        success: false,
        error: 'Invalid credentials'
      });
    }
    
    // Check password
    const isMatch = await user.comparePassword(password);
    if (!isMatch) {
      return res.status(401).json({
        success: false,
        error: 'Invalid credentials'
      });
    }
    
    // Generate token
    const token = jwt.sign(
      { id: user._id, email: user.email },
      process.env.JWT_SECRET,
      { expiresIn: '7d' }
    );
    
    res.json({
      success: true,
      token,
      data: {
        id: user._id,
        name: user.name,
        email: user.email
      }
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};

// Get current user
exports.getCurrentUser = async (req, res) => {
  try {
    const user = await User.findById(req.user.id).select('-password');
    
    res.json({
      success: true,
      data: user
    });
  } catch (error) {
    res.status(500).json({
      success: false,
      error: error.message
    });
  }
};
```

### Authorization Middleware

```javascript
// src/middleware/authorize.js
const authorize = (...roles) => {
  return (req, res, next) => {
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({
        success: false,
        error: 'You do not have permission to access this resource'
      });
    }
    next();
  };
};

module.exports = authorize;

// Usage
const authMiddleware = require('../middleware/authMiddleware');
const authorize = require('../middleware/authorize');

router.delete(
  '/:id',
  authMiddleware,
  authorize('admin'),
  userController.deleteUser
);
```

---

## 📤 File Upload

```bash
npm install multer
```

```javascript
// src/config/multer.js
const multer = require('multer');
const path = require('path');

// Configure storage
const storage = multer.diskStorage({
  destination: (req, file, cb) => {
    cb(null, 'uploads/');
  },
  filename: (req, file, cb) => {
    const uniqueSuffix = Date.now() + '-' + Math.round(Math.random() * 1E9);
    cb(null, file.fieldname + '-' + uniqueSuffix + path.extname(file.originalname));
  }
});

// File filter
const fileFilter = (req, file, cb) => {
  const allowedTypes = /jpeg|jpg|png|pdf/;
  const extname = allowedTypes.test(path.extname(file.originalname).toLowerCase());
  const mimetype = allowedTypes.test(file.mimetype);
  
  if (extname && mimetype) {
    cb(null, true);
  } else {
    cb(new Error('Only images and PDFs are allowed'));
  }
};

const upload = multer({
  storage,
  limits: { fileSize: 5 * 1024 * 1024 }, // 5MB
  fileFilter
});

module.exports = upload;

// Usage in routes
const upload = require('../config/multer');

// Single file
router.post('/upload', upload.single('file'), uploadController.uploadFile);

// Multiple files
router.post('/upload-multiple', upload.array('files', 5), uploadController.uploadMultiple);

// In controller
exports.uploadFile = (req, res) => {
  if (!req.file) {
    return res.status(400).json({
      success: false,
      error: 'No file uploaded'
    });
  }
  
  res.json({
    success: true,
    data: {
      filename: req.file.filename,
      path: req.file.path,
      size: req.file.size
    }
  });
};
```

---

## 🌐 CORS Configuration

```bash
npm install cors
```

```javascript
// src/app.js
const cors = require('cors');

// Allow all origins (development)
app.use(cors());

// Specific origins (production)
const corsOptions = {
  origin: ['https://yourdomain.com', 'https://www.yourdomain.com'],
  credentials: true,
  optionsSuccessStatus: 200
};

app.use(cors(corsOptions));
```

---

## 🧪 Testing

```bash
npm install --save-dev jest supertest
```

```javascript
// tests/auth.test.js
const request = require('supertest');
const app = require('../src/app');

describe('Auth API', () => {
  it('should register a new user', async () => {
    const res = await request(app)
      .post('/api/auth/register')
      .send({
        name: 'Test User',
        email: 'test@example.com',
        password: 'password123'
      });
    
    expect(res.statusCode).toBe(201);
    expect(res.body.success).toBe(true);
    expect(res.body).toHaveProperty('token');
  });
  
  it('should login user', async () => {
    const res = await request(app)
      .post('/api/auth/login')
      .send({
        email: 'test@example.com',
        password: 'password123'
      });
    
    expect(res.statusCode).toBe(200);
    expect(res.body.success).toBe(true);
    expect(res.body).toHaveProperty('token');
  });
});
```

```json
// package.json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watch"
  }
}
```

---

## 🛠️ Common Commands

```bash
# Project setup
npm init -y                    # Initialize project
npm install package-name       # Install package
npm install -D package-name    # Install dev dependency

# Run server
node server.js                 # Run normally
npm run dev                    # Run with nodemon (auto-restart)
npm start                      # Run production

# Database (Prisma)
npx prisma init               # Initialize Prisma
npx prisma migrate dev        # Run migrations
npx prisma generate           # Generate client
npx prisma studio             # Open database GUI

# Testing
npm test                      # Run tests
npm run test:watch            # Watch mode

# Package management
npm list                      # List installed packages
npm outdated                  # Check outdated packages
npm update                    # Update packages
```

---

## ✅ Best Practices

### 1. Use Environment Variables

```javascript
// ❌ Bad
const secret = 'my-secret-key';

// ✅ Good
const secret = process.env.JWT_SECRET;
```

### 2. Error Handling

```javascript
// ❌ Bad
app.get('/users', (req, res) => {
  const users = User.find(); // No error handling!
  res.json(users);
});

// ✅ Good
app.get('/users', async (req, res) => {
  try {
    const users = await User.find();
    res.json({ success: true, data: users });
  } catch (error) {
    res.status(500).json({ success: false, error: error.message });
  }
});
```

### 3. Input Validation

```bash
npm install joi
```

```javascript
const Joi = require('joi');

const validateUser = (req, res, next) => {
  const schema = Joi.object({
    name: Joi.string().min(2).max(50).required(),
    email: Joi.string().email().required(),
    password: Joi.string().min(6).required()
  });
  
  const { error } = schema.validate(req.body);
  if (error) {
    return res.status(400).json({
      success: false,
      error: error.details[0].message
    });
  }
  next();
};
```

### 4. Security Headers

```bash
npm install helmet
```

```javascript
const helmet = require('helmet');
app.use(helmet());
```

### 5. Rate Limiting

```bash
npm install express-rate-limit
```

```javascript
const rateLimit = require('express-rate-limit');

const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100 // limit each IP to 100 requests per windowMs
});

app.use('/api', limiter);
```

---

## 🎓 Learning Resources

- **Official Docs**: https://nodejs.org/en/docs/
- **Express Docs**: https://expressjs.com/
- **MDN Web Docs**: https://developer.mozilla.org/en-US/docs/Learn/Server-side/Express_Nodejs
- **Node.js Best Practices**: https://github.com/goldbergyoni/nodebestpractices

---

## 🆘 Common Errors & Solutions

### Error: "Cannot find module"
```bash
# Solution: Install the package
npm install package-name
```

### Error: "Port already in use"
```bash
# Windows
netstat -ano | findstr :3000
taskkill /PID <PID> /F

# Mac/Linux
lsof -i :3000
kill -9 <PID>
```

### Error: "MongoServerError: E11000 duplicate key"
```javascript
// Solution: The field must be unique (usually email)
// Check if user exists before creating
```

---

**You're now ready to build Node.js APIs! 🚀**

For deployment, check the DevOps section!
