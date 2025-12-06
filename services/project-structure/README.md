# 📁 IMCBS Project Folder Structure Guide

> **Complete folder structure standards** for Django, Node.js, and React projects at IMCBS. Follow these structures for all new projects!

---

## 📚 Table of Contents

- [Django Project Structure](#django-project-structure)
- [Node.js/Express Project Structure](#nodejsexpress-project-structure)
- [React Project Structure (Feature-Based)](#react-project-structure-feature-based)
- [Folder Naming Rules](#folder-naming-rules)

---

## 🐍 Django Project Structure

```
project-name/
│
├── config/                          # Project configuration
│   ├── __init__.py
│   ├── settings/
│   │   ├── __init__.py
│   │   ├── base.py                 # Common settings
│   │   ├── development.py          # Dev environment
│   │   └── production.py           # Production environment
│   ├── urls.py                     # Root URL configuration
│   ├── wsgi.py                     # WSGI application
│   └── asgi.py                     # ASGI application (WebSocket)
│
├── apps/                            # Django applications
│   ├── accounts/                   # User authentication & management
│   │   ├── __init__.py
│   │   ├── models.py               # User model
│   │   ├── views.py                # Views/ViewSets
│   │   ├── serializers.py          # DRF serializers
│   │   ├── urls.py                 # App URLs
│   │   ├── admin.py                # Django admin config
│   │   ├── tests.py                # Unit tests
│   │   └── migrations/
│   │
│   ├── api/                        # API endpoints
│   │   ├── v1/                     # API version 1
│   │   │   ├── __init__.py
│   │   │   ├── urls.py
│   │   │   └── views.py
│   │   └── v2/                     # API version 2 (future)
│   │
│   ├── core/                       # Core functionality
│   │   ├── __init__.py
│   │   ├── models.py               # Shared models
│   │   ├── utils.py                # Helper functions
│   │   └── middleware.py           # Custom middleware
│   │
│   └── accesscontrol/              # Role-based access control
│       ├── models.py
│       ├── permissions.py
│       └── decorators.py
│
├── static/                          # Static files (CSS, JS, images)
│   ├── css/
│   ├── js/
│   └── images/
│
├── media/                           # User uploads (files, images)
│   └── uploads/
│
├── templates/                       # HTML templates
│   ├── base.html
│   ├── accounts/
│   └── admin/
│
├── requirements/                    # Dependencies
│   ├── base.txt                    # Common dependencies
│   ├── dev.txt                     # Development only
│   └── prod.txt                    # Production only
│
├── tests/                           # Project-level tests
│   ├── __init__.py
│   └── test_integration.py
│
├── scripts/                         # Utility scripts
│   ├── deploy.sh
│   └── backup_db.sh
│
├── .env                            # Environment variables (DON'T COMMIT!)
├── .env.example                    # Example env file (safe to commit)
├── .gitignore                      # Git ignore rules
├── manage.py                       # Django management script
├── README.md                       # Project documentation
└── requirements.txt                # All dependencies (generated)
```

### 📝 Key Folders Explained

- **`config/`**: Project-wide settings, URLs, WSGI/ASGI configuration
- **`apps/`**: Individual Django apps for different features (modular design)
- **`static/`**: CSS, JavaScript, images (collected via `collectstatic`)
- **`media/`**: User-uploaded files (images, documents)
- **`templates/`**: HTML templates for Django views
- **`requirements/`**: Split dependencies for dev and production
- **`tests/`**: Integration and unit tests

---

## ⚙️ Node.js/Express Project Structure

```
project-name/
│
├── src/
│   ├── config/                      # Configuration files
│   │   ├── database.js             # Database connection
│   │   ├── environment.js          # Environment variables
│   │   └── cors.js                 # CORS configuration
│   │
│   ├── controllers/                 # Request handlers (business logic)
│   │   ├── authController.js
│   │   ├── userController.js
│   │   └── productController.js
│   │
│   ├── models/                      # Database models (Mongoose/Sequelize)
│   │   ├── User.js
│   │   ├── Product.js
│   │   └── Order.js
│   │
│   ├── routes/                      # API route definitions
│   │   ├── index.js                # Main router
│   │   ├── authRoutes.js
│   │   ├── userRoutes.js
│   │   └── productRoutes.js
│   │
│   ├── middleware/                  # Custom middleware
│   │   ├── authMiddleware.js       # JWT authentication
│   │   ├── errorHandler.js         # Error handling
│   │   └── validateRequest.js      # Request validation
│   │
│   ├── services/                    # Business logic services
│   │   ├── authService.js
│   │   ├── emailService.js
│   │   └── paymentService.js
│   │
│   ├── utils/                       # Helper functions
│   │   ├── logger.js               # Logging utility
│   │   ├── validator.js            # Validation helpers
│   │   └── encryption.js           # Encryption helpers
│   │
│   └── app.js                      # Express app configuration
│
├── tests/                           # Test files
│   ├── unit/
│   │   ├── auth.test.js
│   │   └── user.test.js
│   └── integration/
│       └── api.test.js
│
├── public/                          # Static files (served directly)
│   ├── images/
│   └── uploads/
│
├── logs/                            # Application logs
│   ├── error.log
│   └── combined.log
│
├── .env                            # Environment variables (DON'T COMMIT!)
├── .env.example                    # Example env file
├── .gitignore                      # Git ignore rules
├── package.json                    # Dependencies & scripts
├── package-lock.json               # Locked dependency versions
├── server.js                       # Entry point (starts server)
└── README.md                       # Project documentation
```

### 📝 Key Folders Explained

- **`src/controllers/`**: Handle HTTP requests, call services, return responses
- **`src/models/`**: Database schemas and models (Mongoose, Sequelize, Prisma)
- **`src/routes/`**: Define API endpoints and link to controllers
- **`src/middleware/`**: Authentication, validation, error handling
- **`src/services/`**: Business logic (keep controllers thin)
- **`src/utils/`**: Reusable helper functions
- **`tests/`**: Unit and integration tests

---

## ⚛️ React Project Structure (Feature-Based)

```
project-name/
│
├── public/                          # Public assets
│   ├── index.html
│   ├── favicon.ico
│   └── robots.txt
│
├── src/
│   ├── features/                    # ✨ Feature-based modules
│   │   ├── auth/                  # Authentication feature
│   │   │   ├── components/         # Feature-specific components
│   │   │   │   ├── LoginForm.jsx
│   │   │   │   └── SignupForm.jsx
│   │   │   ├── hooks/              # Feature-specific hooks
│   │   │   │   └── useAuth.js
│   │   │   ├── services/           # API calls for this feature
│   │   │   │   └── authService.js
│   │   │   ├── store/              # State management (if using Redux)
│   │   │   │   └── authSlice.js
│   │   │   └── index.js            # Public exports
│   │   │
│   │   ├── dashboard/              # Dashboard feature
│   │   │   ├── components/
│   │   │   │   ├── DashboardLayout.jsx
│   │   │   │   └── StatCard.jsx
│   │   │   ├── hooks/
│   │   │   └── index.js
│   │   │
│   │   ├── profile/                # User profile feature
│   │   │   ├── components/
│   │   │   │   ├── ProfileForm.jsx
│   │   │   │   └── AvatarUpload.jsx
│   │   │   ├── hooks/
│   │   │   └── services/
│   │   │
│   │   └── products/               # Products feature
│   │       ├── components/
│   │       │   ├── ProductList.jsx
│   │       │   ├── ProductCard.jsx
│   │       │   └── ProductDetails.jsx
│   │       ├── hooks/
│   │       │   └── useProducts.js
│   │       └── services/
│   │           └── productService.js
│   │
│   ├── shared/                      # Shared/Common code
│   │   ├── components/             # Reusable components
│   │   │   ├── Button/
│   │   │   │   ├── Button.jsx
│   │   │   │   ├── Button.module.css
│   │   │   │   └── index.js
│   │   │   ├── Input/
│   │   │   ├── Modal/
│   │   │   ├── Navbar/
│   │   │   └── Footer/
│   │   │
│   │   ├── hooks/                  # Shared hooks
│   │   │   ├── useLocalStorage.js
│   │   │   ├── useDebounce.js
│   │   │   └── useFetch.js
│   │   │
│   │   ├── utils/                  # Helper functions
│   │   │   ├── formatters.js
│   │   │   ├── validators.js
│   │   │   └── constants.js
│   │   │
│   │   ├── services/               # Global API services
│   │   │   ├── api.js              # Axios instance
│   │   │   └── apiEndpoints.js
│   │   │
│   │   └── context/                # React Context providers
│   │       ├── ThemeContext.jsx
│   │       └── AuthContext.jsx
│   │
│   ├── assets/                      # Static assets
│   │   ├── images/
│   │   ├── icons/
│   │   └── fonts/
│   │
│   ├── styles/                      # Global styles
│   │   ├── globals.css
│   │   ├── variables.css
│   │   └── tailwind.css
│   │
│   ├── routes/                      # React Router configuration
│   │   ├── AppRoutes.jsx
│   │   ├── PrivateRoute.jsx
│   │   └── PublicRoute.jsx
│   │
│   ├── store/                       # Redux store (if using Redux)
│   │   ├── store.js
│   │   └── rootReducer.js
│   │
│   ├── App.jsx                     # Main App component
│   ├── index.jsx                   # Entry point
│   └── setupTests.js               # Test configuration
│
├── .env                            # Environment variables (DON'T COMMIT!)
├── .env.example                    # Example env file
├── .gitignore                      # Git ignore rules
├── package.json                    # Dependencies & scripts
├── package-lock.json               # Locked versions
├── tailwind.config.js              # Tailwind CSS config (if using)
├── vite.config.js                  # Vite config (or webpack/CRA)
└── README.md                       # Project documentation
```

### 📝 Key Folders Explained - React

- **`src/features/`**: ✨ **Feature-based architecture** - each feature is self-contained with its own components, hooks, and services
- **`src/shared/`**: Reusable components, hooks, and utilities used across features
- **`src/assets/`**: Images, icons, fonts, and other static files
- **`src/routes/`**: React Router configuration and route guards
- **`src/store/`**: Redux or Zustand state management (if used)

### ✨ Why Feature-Based Architecture?

```
✅ Benefits:
- Easy to locate feature-specific code
- Better code organization and maintainability
- Easier to add/remove features
- Teams can work on different features independently
- Clear separation of concerns
```

---

## 🏷️ Folder Naming Rules

### ✅ DO:

```bash
# Use lowercase with hyphens for multi-word folders
components/
user-profile/
access-control/
api-endpoints/

# Use PascalCase for React component folders
Button/
UserProfile/
ProductCard/
```

### ❌ DON'T:

```bash
# Avoid spaces or special characters
my components/        # Bad: spaces
user_profile/         # Bad: underscore (use hyphen)
API-Endpoints/        # Bad: mixed case
```

---

## 📦 Example .env Files

### Django `.env`

```env
# Django Settings
SECRET_KEY=your-secret-key-here
DEBUG=True
ALLOWED_HOSTS=localhost,127.0.0.1

# Database
DB_NAME=imcbs_db
DB_USER=postgres
DB_PASSWORD=your-password
DB_HOST=localhost
DB_PORT=5432

# Cloudflare R2
CLOUDFLARE_R2_ENABLED=true
CLOUDFLARE_R2_BUCKET=your-bucket
CLOUDFLARE_R2_PUBLIC_URL=pub-xxx.r2.dev
CLOUDFLARE_R2_ACCESS_KEY=your-access-key
CLOUDFLARE_R2_SECRET_KEY=your-secret-key
```

### Node.js `.env`

```env
# Server
NODE_ENV=development
PORT=5000

# Database
MONGODB_URI=mongodb://localhost:27017/mydb
# OR PostgreSQL
DATABASE_URL=postgresql://user:pass@localhost:5432/mydb

# JWT
JWT_SECRET=your-jwt-secret
JWT_EXPIRE=7d

# API Keys
STRIPE_KEY=sk_test_xxx
SENDGRID_API_KEY=SG.xxx
```

### React `.env`

```env
# API Configuration
VITE_API_URL=http://localhost:5000/api
VITE_APP_NAME=IMCBS App

# Feature Flags
VITE_ENABLE_ANALYTICS=false
VITE_ENABLE_DARK_MODE=true

# Third-party Keys
VITE_GOOGLE_MAPS_KEY=your-key
VITE_STRIPE_PUBLIC_KEY=pk_test_xxx
```

---

## 🎯 Quick Tips

1. **Keep it consistent** - Follow the same structure across all projects
2. **Use .env files** - Never hardcode sensitive data
3. **Write README** - Document setup and usage
4. **Commit .env.example** - Show required environment variables
5. **Use .gitignore** - Never commit `.env`, `node_modules`, etc.

---

**Follow these structures for all IMCBS projects! 🚀**

---

## **Node.js / Express Structure**

```
node-express-api/
│
├── src/
│   ├── config/                 # Environment config, DB config
│   ├── controllers/            # Route handlers
│   ├── middleware/             # Authentication, logging, etc.
│   ├── models/                 # Mongoose/ORM models
│   ├── routes/                 # Endpoint routing
│   ├── services/               # Business logic
│   └── utils/                  # Helpers & shared utilities
│
├── tests/
├── .env
├── package.json
└── server.js
```

---

## **React Structure (Feature-Based Architecture)**

```
react-app/
│
├── src/
│   ├── app/
│   │   ├── store.js            # Redux store (if applicable)
│   │   └── router.jsx          # App level routing
│   │
│   ├── features/               # Feature-based modules
│   │   └── auth/
│   │       ├── components/     # UI components
│   │       ├── hooks/          # Custom hooks
│   │       ├── services/       # API requests
│   │       ├── slices/         # Redux slices or state logic
│   │       ├── pages/          # Screens/pages
│   │       └── index.js
│   │
│   ├── components/             # Global reusable components
│   ├── assets/                 # Images, fonts, icons
│   ├── styles/                 # Global styles, Tailwind
│   ├── utils/                  # Helpers, formatters
│   ├── constants/
│   ├── hooks/
│   └── main.jsx                # App entry
│
└── public/
```

---

## **Next.js Structure**

```
nextjs-project/
│
├── app/ or pages/              # Routing system
├── components/                 # Shared UI components
├── lib/                        # Helpers, utilities, API
├── features/                   # Feature-based modules
├── public/                     # Static files
├── styles/                     # CSS / SCSS / global styles
└── config/                     # App config
```

---

## **Full-Stack Project Structure**

```
fullstack-project/
│
├── client/                     # React / Next / Frontend
├── server/                     # Node / Django / Backend API
├── docker-compose.yml
└── README.md
```

---

## **Monorepo Structure (e.g., Nx or Turborepo)**

```
monorepo/
│
├── apps/
│   ├── admin/
│   ├── web/
│   └── api/
│
├── packages/
│   ├── ui/                     # Shared UI components
│   ├── utils/                  # Shared helpers
│   └── config/
│
├── .turbo/
└── tsconfig.json
```

---

## **File Naming Conventions**

| Type          | Format           | Example             |
| ------------- | ---------------- | ------------------- |
| Components    | PascalCase       | `LoginForm.jsx`     |
| Pages         | PascalCase       | `DashboardPage.jsx` |
| Utils         | camelCase        | `formatDate.js`     |
| Constants     | UPPER_SNAKE_CASE | `API_URL`           |
| Files/folders | kebab-case       | `user-profile`      |

---

## **Assets & Static Organization**

```
assets/
│── images/
│── icons/
│── fonts/
│── mock-data/
```

---

### ✔️ Goal

These structures ensure:

* Scalability and clear separation of concerns
* Feature modularity
* Easy collaboration & onboarding

---

If you want, I can also:

* Create template ZIP files for each structure
* Add example boilerplate code
* Add GitHub README format version

Would you like me to generate **starter templates** as downloadable folders? 🚀
,,,,,,,,,based on this update and give new one ,its for beginner that new to our firm 