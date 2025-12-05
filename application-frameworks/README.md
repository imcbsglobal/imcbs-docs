# 💻 Application Frameworks Documentation

Comprehensive guides for building applications with Django, Node.js, and React at IMCBS.

---

## 📚 Frameworks

### Backend Frameworks
- **[Django](./django/)** - Python web framework for building robust backend applications
  - Project setup and structure
  - Database models and migrations
  - REST API development (DRF)
  - Authentication & authorization
  - Deployment best practices

- **[Node.js](./nodejs/)** - JavaScript runtime for building scalable backend services
  - Express.js framework
  - API development
  - Database integration (MongoDB, PostgreSQL)
  - Authentication strategies
  - WebSocket implementation

### Frontend Framework
- **[React](./react/)** - JavaScript library for building user interfaces
  - Project setup (Vite, CRA, Next.js)
  - Component architecture
  - State management (Redux, Context)
  - API integration
  - Performance optimization

---

## 🎯 Common Patterns

### Full-Stack Architecture

```
┌─────────────┐      ┌──────────────┐      ┌─────────────┐
│   React     │ ───▶ │   Node.js    │ ───▶ │  MongoDB    │
│  Frontend   │ HTTP │   Backend    │      │  Database   │
└─────────────┘ ◀─── └──────────────┘      └─────────────┘
     JSON            REST API / GraphQL
```

### Technology Combinations

#### MERN Stack
- **M**ongoDB - Database
- **E**xpress.js - Backend framework
- **R**eact - Frontend library
- **N**ode.js - Runtime environment

#### Django + React
- Django - Backend API (Django REST Framework)
- React - Frontend SPA
- PostgreSQL - Database

---

## 🚀 Quick Start Templates

### Create React App with Node.js Backend

```bash
# Frontend
npx create-react-app client
cd client
npm start

# Backend
mkdir server && cd server
npm init -y
npm install express cors dotenv
node server.js
```

### Django with React

```bash
# Backend
django-admin startproject backend
cd backend
python manage.py startapp api
python manage.py runserver

# Frontend
npx create-react-app frontend
cd frontend
npm start
```

---

## 📖 Learning Resources

### Best Practices
- Component-based architecture
- RESTful API design
- Authentication & authorization
- Error handling
- Testing strategies
- Deployment workflows

---

## 🔗 Related Documentation

- [Infrastructure Setup](../infra/)
- [Deployment Guides](../devops/deployment/)
- [Project Structure Standards](../services/project-structure/)

---

**Last Updated:** December 2025