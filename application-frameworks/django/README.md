# 🐍 Django Framework - Complete Beginner's Guide

> **Learn Django from scratch!** This guide covers everything you need to build powerful web applications with Django at IMCBS.

---

## 📚 Table of Contents

- [What is Django?](#what-is-django)
- [Installation & Setup](#installation--setup)
- [Create Your First Project](#create-your-first-project)
- [Django Project Structure](#django-project-structure)
- [Database Setup](#database-setup)
- [Creating Your First App](#creating-your-first-app)
- [Models (Database Tables)](#models-database-tables)
- [Views (Logic)](#views-logic)
- [URLs (Routing)](#urls-routing)
- [Templates (HTML)](#templates-html)
- [Django Admin](#django-admin)
- [Static & Media Files](#static--media-files)
- [Django REST Framework (API)](#django-rest-framework-api)
- [Authentication](#authentication)
- [Common Commands](#common-commands)
- [Best Practices](#best-practices)

---

## 🤔 What is Django?

Django is a **high-level Python web framework** that makes building web applications fast and easy.

**Why Django?**
- ✅ Built-in admin panel
- ✅ ORM (Object-Relational Mapping) - no need to write SQL
- ✅ Built-in authentication system
- ✅ Security features (CSRF, XSS protection)
- ✅ Scalable and production-ready
- ✅ Large community and ecosystem

**Django follows MVT Pattern:**
- **M**odel: Database structure
- **V**iew: Business logic
- **T**emplate: HTML presentation

---

## 📦 Installation & Setup

### Step 1: Install Python

```bash
# Check if Python is installed
python --version
# Should be Python 3.8 or higher

# If not installed, download from python.org
```

### Step 2: Create a Virtual Environment

```bash
# Create a folder for your project
mkdir my-django-project
cd my-django-project

# Create virtual environment
python -m venv venv

# Activate virtual environment
# Windows:
venv\Scripts\activate
# Mac/Linux:
source venv/bin/activate

# Your terminal should now show (venv) at the beginning
```

### Step 3: Install Django

```bash
# Install Django
pip install django

# Check Django version
django-admin --version

# Install other useful packages
pip install python-decouple  # For environment variables
pip install pillow          # For image handling
```

---

## 🚀 Create Your First Project

### Step 1: Create Django Project

```bash
# Create new Django project
django-admin startproject myproject .

# Note: The dot (.) creates project in current folder
# Without dot, it creates an extra folder
```

### Step 2: Run Development Server

```bash
# Start the development server
python manage.py runserver

# Open browser and go to: http://127.0.0.1:8000/
# You should see Django welcome page! 🎉
```

### Step 3: Create a Superuser (Admin)

```bash
# Create admin user
python manage.py createsuperuser

# Enter username, email, and password
# Now you can access admin at: http://127.0.0.1:8000/admin/
```

---

## 📁 Django Project Structure

After creating a project, you'll see this structure:

```
my-django-project/
├── venv/                       # Virtual environment (don't commit to git)
├── myproject/                  # Project configuration folder
│   ├── __init__.py
│   ├── settings.py            # ⚙️ All settings (database, apps, etc.)
│   ├── urls.py                # 🌐 Main URL routing
│   ├── wsgi.py                # Web server interface
│   └── asgi.py                # Async server interface
├── manage.py                   # 🛠️ Command-line utility
└── db.sqlite3                  # 💾 Database (auto-created)
```

**Key Files:**
- **`manage.py`**: Run commands (runserver, migrate, etc.)
- **`settings.py`**: Configure database, apps, middleware
- **`urls.py`**: Define URL routes

---

## 💾 Database Setup

Django supports multiple databases. By default, it uses **SQLite** (good for development).

### Using SQLite (Default)

```python
# In settings.py - Already configured by default
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

### Using PostgreSQL (Production)

```bash
# Install PostgreSQL driver
pip install psycopg2-binary
```

```python
# In settings.py
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.postgresql',
        'NAME': 'mydb',
        'USER': 'postgres',
        'PASSWORD': 'yourpassword',
        'HOST': 'localhost',
        'PORT': '5432',
    }
}
```

### Run Migrations

```bash
# Apply migrations (create database tables)
python manage.py migrate

# You should see "Applying migrations..." messages
```

---

## 📱 Creating Your First App

Django projects are divided into **apps**. An app is a module that does something specific.

### Step 1: Create an App

```bash
# Create a new app called 'blog'
python manage.py startapp blog
```

### Step 2: Register the App

```python
# In settings.py, add to INSTALLED_APPS
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    
    'blog',  # ✅ Add your app here
]
```

### App Structure

```
blog/
├── migrations/          # Database changes
├── __init__.py
├── admin.py            # Admin panel configuration
├── apps.py             # App configuration
├── models.py           # 📊 Database models
├── tests.py            # Unit tests
└── views.py            # 🎯 Business logic
```

---

## 📊 Models (Database Tables)

Models define your database structure using Python classes.

### Example: Blog Post Model

```python
# blog/models.py
from django.db import models
from django.contrib.auth.models import User

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    published = models.BooleanField(default=False)
    
    def __str__(self):
        return self.title
    
    class Meta:
        ordering = ['-created_at']  # Newest first
```

**Common Field Types:**
```python
CharField(max_length=100)      # Short text
TextField()                     # Long text
IntegerField()                  # Numbers
BooleanField()                  # True/False
DateField()                     # Date only
DateTimeField()                 # Date and time
EmailField()                    # Email validation
URLField()                      # URL validation
ImageField()                    # Images (needs Pillow)
FileField()                     # Any file
ForeignKey()                    # One-to-Many relationship
ManyToManyField()              # Many-to-Many relationship
```

### Create Database Tables

```bash
# Create migration file
python manage.py makemigrations

# Apply migrations to database
python manage.py migrate
```

**Important:** Run these commands every time you change models!

---

## 🎯 Views (Logic)

Views handle requests and return responses.

### Function-Based Views (FBV)

```python
# blog/views.py
from django.shortcuts import render, get_object_or_404
from .models import Post

def home(request):
    """Display all blog posts"""
    posts = Post.objects.filter(published=True)
    return render(request, 'blog/home.html', {'posts': posts})

def post_detail(request, post_id):
    """Display single post"""
    post = get_object_or_404(Post, id=post_id)
    return render(request, 'blog/detail.html', {'post': post})
```

### Class-Based Views (CBV)

```python
# blog/views.py
from django.views.generic import ListView, DetailView
from .models import Post

class PostListView(ListView):
    model = Post
    template_name = 'blog/home.html'
    context_object_name = 'posts'
    
    def get_queryset(self):
        return Post.objects.filter(published=True)

class PostDetailView(DetailView):
    model = Post
    template_name = 'blog/detail.html'
    context_object_name = 'post'
```

---

## 🌐 URLs (Routing)

URLs connect web addresses to views.

### Project URLs

```python
# myproject/urls.py
from django.contrib import admin
from django.urls import path, include

urlpatterns = [
    path('admin/', admin.site.urls),
    path('', include('blog.urls')),  # Include app URLs
]
```

### App URLs

```python
# blog/urls.py (create this file)
from django.urls import path
from . import views

urlpatterns = [
    path('', views.home, name='home'),
    path('post/<int:post_id>/', views.post_detail, name='post_detail'),
]
```

**URL Patterns:**
```python
path('', views.home)                    # /
path('about/', views.about)             # /about/
path('post/<int:id>/', views.detail)    # /post/1/
path('post/<slug:slug>/', views.detail) # /post/my-post/
```

---

## 🎨 Templates (HTML)

Templates are HTML files with Django template language.

### Create Template Folder

```
blog/
├── templates/
│   └── blog/
│       ├── home.html
│       └── detail.html
```

### Base Template

```html
<!-- templates/base.html -->
<!DOCTYPE html>
<html>
<head>
    <title>{% block title %}My Blog{% endblock %}</title>
</head>
<body>
    <header>
        <h1>My Blog</h1>
        <nav>
            <a href="{% url 'home' %}">Home</a>
        </nav>
    </header>
    
    <main>
        {% block content %}{% endblock %}
    </main>
</body>
</html>
```

### Home Page Template

```html
<!-- blog/templates/blog/home.html -->
{% extends 'base.html' %}

{% block title %}Home - My Blog{% endblock %}

{% block content %}
    <h2>Recent Posts</h2>
    
    {% for post in posts %}
        <article>
            <h3>{{ post.title }}</h3>
            <p>{{ post.content|truncatewords:30 }}</p>
            <small>By {{ post.author }} on {{ post.created_at|date:"M d, Y" }}</small>
            <a href="{% url 'post_detail' post.id %}">Read More</a>
        </article>
    {% empty %}
        <p>No posts yet.</p>
    {% endfor %}
{% endblock %}
```

**Template Tags:**
```django
{{ variable }}                  <!-- Display variable -->
{% for item in items %}         <!-- Loop -->
{% if condition %}              <!-- Condition -->
{% url 'name' %}               <!-- URL by name -->
{{ text|lower }}               <!-- Filter (lowercase) -->
{{ date|date:"Y-m-d" }}        <!-- Date formatting -->
```

---

## 👑 Django Admin

Django provides a powerful admin panel out of the box!

### Register Models

```python
# blog/admin.py
from django.contrib import admin
from .models import Post

@admin.register(Post)
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'author', 'created_at', 'published']
    list_filter = ['published', 'created_at']
    search_fields = ['title', 'content']
    date_hierarchy = 'created_at'
    
    # Auto-fill slug from title
    prepopulated_fields = {'slug': ('title',)}
```

**Admin Customization:**
```python
class PostAdmin(admin.ModelAdmin):
    list_display = ['title', 'author', 'published']  # Columns to show
    list_filter = ['published']                       # Sidebar filters
    search_fields = ['title', 'content']              # Search bar
    ordering = ['-created_at']                        # Default ordering
    list_per_page = 20                                # Pagination
```

Access admin at: `http://127.0.0.1:8000/admin/`

---

## 📂 Static & Media Files

### Static Files (CSS, JS, Images)

```python
# settings.py
STATIC_URL = '/static/'
STATICFILES_DIRS = [BASE_DIR / 'static']
STATIC_ROOT = BASE_DIR / 'staticfiles'  # For production
```

```
project/
├── static/
│   ├── css/
│   │   └── style.css
│   ├── js/
│   │   └── main.js
│   └── images/
│       └── logo.png
```

```html
<!-- In templates -->
{% load static %}

<link rel="stylesheet" href="{% static 'css/style.css' %}">
<img src="{% static 'images/logo.png' %}" alt="Logo">
```

### Media Files (User Uploads)

```python
# settings.py
MEDIA_URL = '/media/'
MEDIA_ROOT = BASE_DIR / 'media'
```

```python
# myproject/urls.py (for development only)
from django.conf import settings
from django.conf.urls.static import static

urlpatterns = [
    # ... your urls
]

if settings.DEBUG:
    urlpatterns += static(settings.MEDIA_URL, document_root=settings.MEDIA_ROOT)
```

---

## 🔌 Django REST Framework (API)

Build powerful APIs with Django REST Framework (DRF).

### Installation

```bash
pip install djangorestframework
```

```python
# settings.py
INSTALLED_APPS = [
    # ...
    'rest_framework',
]
```

### Create Serializer

```python
# blog/serializers.py
from rest_framework import serializers
from .models import Post

class PostSerializer(serializers.ModelSerializer):
    class Meta:
        model = Post
        fields = ['id', 'title', 'content', 'author', 'created_at', 'published']
        read_only_fields = ['author', 'created_at']
```

### Create API Views

```python
# blog/api_views.py
from rest_framework import viewsets
from rest_framework.permissions import IsAuthenticatedOrReadOnly
from .models import Post
from .serializers import PostSerializer

class PostViewSet(viewsets.ModelViewSet):
    queryset = Post.objects.filter(published=True)
    serializer_class = PostSerializer
    permission_classes = [IsAuthenticatedOrReadOnly]
    
    def perform_create(self, serializer):
        serializer.save(author=self.request.user)
```

### API URLs

```python
# myproject/urls.py
from rest_framework.routers import DefaultRouter
from blog.api_views import PostViewSet

router = DefaultRouter()
router.register('posts', PostViewSet)

urlpatterns = [
    path('admin/', admin.site.urls),
    path('api/', include(router.urls)),
]
```

**API Endpoints:**
- `GET /api/posts/` - List all posts
- `POST /api/posts/` - Create new post
- `GET /api/posts/1/` - Get single post
- `PUT /api/posts/1/` - Update post
- `DELETE /api/posts/1/` - Delete post

---

## 🔐 Authentication

### Built-in Authentication

```python
# views.py
from django.contrib.auth import authenticate, login, logout
from django.contrib.auth.decorators import login_required

def login_view(request):
    if request.method == 'POST':
        username = request.POST['username']
        password = request.POST['password']
        user = authenticate(request, username=username, password=password)
        if user:
            login(request, user)
            return redirect('home')
    return render(request, 'login.html')

@login_required
def profile_view(request):
    return render(request, 'profile.html')
```

### JWT Authentication (for APIs)

```bash
pip install djangorestframework-simplejwt
```

```python
# settings.py
REST_FRAMEWORK = {
    'DEFAULT_AUTHENTICATION_CLASSES': [
        'rest_framework_simplejwt.authentication.JWTAuthentication',
    ],
}

# urls.py
from rest_framework_simplejwt.views import TokenObtainPairView, TokenRefreshView

urlpatterns = [
    path('api/token/', TokenObtainPairView.as_view()),
    path('api/token/refresh/', TokenRefreshView.as_view()),
]
```

---

## 🛠️ Common Commands

```bash
# Project Management
django-admin startproject myproject    # Create project
python manage.py startapp myapp        # Create app
python manage.py runserver            # Start dev server
python manage.py runserver 8080       # Custom port

# Database
python manage.py makemigrations       # Create migration files
python manage.py migrate              # Apply migrations
python manage.py showmigrations       # Show migration status
python manage.py sqlmigrate app 0001  # Show SQL for migration

# Admin & Users
python manage.py createsuperuser      # Create admin user
python manage.py changepassword user  # Change password

# Shell & Testing
python manage.py shell                # Django shell
python manage.py test                 # Run tests

# Static Files
python manage.py collectstatic        # Collect static files (production)

# Database
python manage.py dumpdata > backup.json   # Backup database
python manage.py loaddata backup.json     # Restore database
python manage.py flush                    # Clear database
```

---

## ✅ Best Practices

### 1. Project Structure

```
myproject/
├── config/                 # Project settings
│   ├── settings/
│   │   ├── base.py
│   │   ├── development.py
│   │   └── production.py
│   ├── urls.py
│   └── wsgi.py
├── apps/                   # All Django apps
│   ├── accounts/
│   ├── blog/
│   └── api/
├── static/
├── media/
├── templates/
├── requirements/
│   ├── base.txt
│   ├── dev.txt
│   └── prod.txt
├── .env
├── .gitignore
└── manage.py
```

### 2. Use Environment Variables

```python
# Install python-decouple
pip install python-decouple

# .env file
SECRET_KEY=your-secret-key-here
DEBUG=True
DATABASE_URL=postgresql://user:pass@localhost/db

# settings.py
from decouple import config

SECRET_KEY = config('SECRET_KEY')
DEBUG = config('DEBUG', cast=bool)
```

### 3. Security Settings

```python
# settings.py (Production)
DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com']

# HTTPS
SECURE_SSL_REDIRECT = True
SESSION_COOKIE_SECURE = True
CSRF_COOKIE_SECURE = True
```

### 4. Database Queries

```python
# ❌ Bad: N+1 problem
posts = Post.objects.all()
for post in posts:
    print(post.author.name)  # Extra query for each post!

# ✅ Good: Use select_related
posts = Post.objects.select_related('author').all()
for post in posts:
    print(post.author.name)  # No extra queries!

# ✅ Use prefetch_related for Many-to-Many
posts = Post.objects.prefetch_related('tags').all()
```

### 5. Use Django Extensions

```bash
pip install django-extensions
pip install django-debug-toolbar  # Debug in development
pip install django-cors-headers    # CORS for APIs
```

---

## 🎓 Learning Resources

- **Official Docs**: https://docs.djangoproject.com/
- **Django Girls Tutorial**: https://tutorial.djangogirls.org/
- **Django REST Framework**: https://www.django-rest-framework.org/
- **Django Packages**: https://djangopackages.org/

---

## 🆘 Common Errors & Solutions

### Error: "No module named 'django'"
```bash
# Solution: Activate virtual environment
venv\Scripts\activate  # Windows
source venv/bin/activate  # Mac/Linux
```

### Error: "You have unapplied migrations"
```bash
# Solution: Run migrations
python manage.py migrate
```

### Error: "ImproperlyConfigured: DATABASES not configured"
```bash
# Solution: Check settings.py DATABASES configuration
```

### Error: "TemplateDoesNotExist"
```bash
# Solution: Check template path and INSTALLED_APPS
# Ensure app is registered in settings.py
```

---

**You're now ready to build Django applications! 🚀**

For deployment, check the DevOps section!
