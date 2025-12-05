# 🐍 Gunicorn WSGI Server Guide

Complete guide for deploying Python web applications (Django, Flask) with Gunicorn in production.

---

## 📋 Table of Contents

- [Quick Reference](#quick-reference)
- [Installation](#installation)
- [Basic Configuration](#basic-configuration)
- [Django Integration](#django-integration)
- [Flask Integration](#flask-integration)
- [Systemd Service Setup](#systemd-service-setup)
- [Worker Configuration](#worker-configuration)
- [Performance Tuning](#performance-tuning)
- [Logging](#logging)
- [Troubleshooting](#troubleshooting)

---

## 🚀 Quick Reference

### Essential Commands

```bash
# Start Gunicorn
gunicorn myproject.wsgi:application

# With specific settings
gunicorn --workers 3 --bind 0.0.0.0:8000 myproject.wsgi:application

# Load from config file
gunicorn -c gunicorn_config.py myproject.wsgi:application

# Check running processes
ps aux | grep gunicorn

# Graceful restart (systemd)
sudo systemctl reload gunicorn

# View logs
sudo journalctl -u gunicorn -f
```

### Key Configuration Parameters

```python
# gunicorn_config.py
bind = "127.0.0.1:8000"
workers = 3
worker_class = "sync"
timeout = 30
keepalive = 2
```

---

## 📦 Installation

### Install Gunicorn

```bash
# In virtual environment
pip install gunicorn

# Or in requirements.txt
echo "gunicorn==21.2.0" >> requirements.txt
pip install -r requirements.txt

# Verify installation
gunicorn --version
```

### System-wide Installation (Not Recommended)

```bash
sudo apt update
sudo apt install gunicorn3
```

---

## ⚙️ Basic Configuration

### Command Line Options

```bash
# Basic startup
gunicorn myapp:app

# Specify workers and port
gunicorn --workers 4 --bind 0.0.0.0:8000 myapp:app

# With worker class
gunicorn --workers 4 --worker-class gevent --bind 0.0.0.0:8000 myapp:app

# Daemon mode (background)
gunicorn --daemon --workers 4 --bind 127.0.0.1:8000 myapp:app

# With access log
gunicorn --access-logfile access.log --error-logfile error.log myapp:app
```

### Configuration File (`gunicorn_config.py`)

```python
import multiprocessing

# Server Socket
bind = "127.0.0.1:8000"
backlog = 2048

# Worker Processes
workers = multiprocessing.cpu_count() * 2 + 1
worker_class = "sync"
worker_connections = 1000
timeout = 30
keepalive = 2

# Process Naming
proc_name = "myapp"

# Logging
accesslog = "/var/log/gunicorn/access.log"
errorlog = "/var/log/gunicorn/error.log"
loglevel = "info"
access_log_format = '%(h)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s "%(f)s" "%(a)s"'

# Server Mechanics
daemon = False
pidfile = "/var/run/gunicorn/gunicorn.pid"
umask = 0
user = None
group = None
tmp_upload_dir = None

# SSL (if needed)
# keyfile = "/path/to/key.pem"
# certfile = "/path/to/cert.pem"
```

---

## 🎯 Django Integration

### Project Structure

```
myproject/
├── myproject/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py          # Gunicorn entry point
├── manage.py
├── requirements.txt
└── gunicorn_config.py
```

### Django WSGI Configuration

```python
# myproject/wsgi.py (Django creates this automatically)
import os
from django.core.wsgi import get_wsgi_application

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'myproject.settings')
application = get_wsgi_application()
```

### Start Django with Gunicorn

```bash
# Development/Testing
gunicorn myproject.wsgi:application --bind 0.0.0.0:8000 --reload

# Production
gunicorn myproject.wsgi:application \
    --workers 3 \
    --bind 127.0.0.1:8000 \
    --timeout 60 \
    --access-logfile /var/log/gunicorn/access.log \
    --error-logfile /var/log/gunicorn/error.log
```

### Django-Specific Gunicorn Config

```python
# gunicorn_config.py for Django
import multiprocessing

# Django project path
pythonpath = '/var/www/myproject'

# Bind
bind = "127.0.0.1:8000"

# Workers
workers = multiprocessing.cpu_count() * 2 + 1
worker_class = "sync"
worker_tmp_dir = "/dev/shm"  # Use RAM for worker tmp files

# Timeouts
timeout = 120  # Increase for long-running views
graceful_timeout = 30
keepalive = 5

# Logging
accesslog = "/var/log/gunicorn/django-access.log"
errorlog = "/var/log/gunicorn/django-error.log"
loglevel = "info"

# Process naming
proc_name = "django_app"

# Security
limit_request_line = 4096
limit_request_fields = 100
limit_request_field_size = 8190
```

---

## 🌶️ Flask Integration

### Flask Application Structure

```python
# app.py
from flask import Flask

app = Flask(__name__)

@app.route('/')
def hello():
    return "Hello from Gunicorn!"

if __name__ == "__main__":
    app.run()
```

### Start Flask with Gunicorn

```bash
# Basic
gunicorn app:app

# Production
gunicorn app:app \
    --workers 4 \
    --bind 0.0.0.0:8000 \
    --access-logfile /var/log/gunicorn/flask-access.log \
    --error-logfile /var/log/gunicorn/flask-error.log
```

### Flask Gunicorn Config

```python
# gunicorn_config.py for Flask
bind = "127.0.0.1:5000"
workers = 4
worker_class = "gevent"  # Async workers for Flask
worker_connections = 1000
timeout = 30
keepalive = 2

# Logging
accesslog = "/var/log/gunicorn/flask-access.log"
errorlog = "/var/log/gunicorn/flask-error.log"
loglevel = "info"
```

---

## 🔧 Systemd Service Setup

### Create Systemd Service File

```bash
sudo nano /etc/systemd/system/gunicorn.service
```

### Systemd Service Configuration

```ini
[Unit]
Description=Gunicorn daemon for Django/Flask application
After=network.target

[Service]
Type=notify
User=www-data
Group=www-data
RuntimeDirectory=gunicorn
WorkingDirectory=/var/www/myproject
Environment="PATH=/var/www/myproject/venv/bin"
ExecStart=/var/www/myproject/venv/bin/gunicorn \
    --workers 3 \
    --bind unix:/run/gunicorn/gunicorn.sock \
    --timeout 60 \
    --access-logfile /var/log/gunicorn/access.log \
    --error-logfile /var/log/gunicorn/error.log \
    --log-level info \
    myproject.wsgi:application
ExecReload=/bin/kill -s HUP $MAINPID
KillMode=mixed
TimeoutStopSec=5
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

### Alternative: Using Config File

```ini
[Unit]
Description=Gunicorn daemon for myproject
After=network.target

[Service]
Type=notify
User=www-data
Group=www-data
WorkingDirectory=/var/www/myproject
Environment="PATH=/var/www/myproject/venv/bin"
ExecStart=/var/www/myproject/venv/bin/gunicorn \
    -c /var/www/myproject/gunicorn_config.py \
    myproject.wsgi:application
ExecReload=/bin/kill -s HUP $MAINPID
KillMode=mixed
TimeoutStopSec=5
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

### Setup and Start Service

```bash
# Create log directory
sudo mkdir -p /var/log/gunicorn
sudo chown www-data:www-data /var/log/gunicorn

# Create socket directory (if using Unix socket)
sudo mkdir -p /run/gunicorn
sudo chown www-data:www-data /run/gunicorn

# Set permissions on project directory
sudo chown -R www-data:www-data /var/www/myproject

# Reload systemd
sudo systemctl daemon-reload

# Enable service to start on boot
sudo systemctl enable gunicorn

# Start the service
sudo systemctl start gunicorn

# Check status
sudo systemctl status gunicorn

# View logs
sudo journalctl -u gunicorn -f
```

### Systemd Service Commands

```bash
# Start
sudo systemctl start gunicorn

# Stop
sudo systemctl stop gunicorn

# Restart (kills workers)
sudo systemctl restart gunicorn

# Reload (graceful restart)
sudo systemctl reload gunicorn

# Enable on boot
sudo systemctl enable gunicorn

# Disable on boot
sudo systemctl disable gunicorn

# Check status
sudo systemctl status gunicorn

# View logs
sudo journalctl -u gunicorn -f
sudo journalctl -u gunicorn --since today
sudo journalctl -u gunicorn -n 100
```

---

## 👷 Worker Configuration

### Worker Types

```python
# Sync Workers (Default)
worker_class = "sync"
workers = 4

# Async Workers (Gevent)
worker_class = "gevent"
workers = 4
worker_connections = 1000

# Async Workers (Eventlet)
worker_class = "eventlet"
workers = 4

# Async Workers (Tornado)
worker_class = "tornado"
workers = 4

# Threads
worker_class = "gthread"
workers = 4
threads = 2
```

### Calculate Optimal Workers

```python
import multiprocessing

# Formula: (2 x $num_cores) + 1
workers = multiprocessing.cpu_count() * 2 + 1
```

### Worker Lifecycle Management

```python
# gunicorn_config.py

# Maximum requests per worker (prevent memory leaks)
max_requests = 1000
max_requests_jitter = 50

# Worker timeout
timeout = 30
graceful_timeout = 30

# Keep-alive
keepalive = 2

# Worker tmp directory (use RAM)
worker_tmp_dir = "/dev/shm"

# Preload application (faster worker startup)
preload_app = True
```

### Worker Process Hooks

```python
# gunicorn_config.py

def on_starting(server):
    """Called just before master process is initialized."""
    print("Gunicorn is starting...")

def when_ready(server):
    """Called just after server is started."""
    print("Gunicorn is ready to accept connections")

def on_reload(server):
    """Called during reload."""
    print("Gunicorn is reloading...")

def pre_fork(server, worker):
    """Called before worker fork."""
    pass

def post_fork(server, worker):
    """Called after worker fork."""
    print(f"Worker spawned (pid: {worker.pid})")

def post_worker_init(worker):
    """Called after worker initialization."""
    pass

def worker_exit(server, worker):
    """Called after worker exit."""
    print(f"Worker exited (pid: {worker.pid})")

def on_exit(server):
    """Called before master process exits."""
    print("Gunicorn is shutting down...")
```

---

## ⚡ Performance Tuning

### Optimal Configuration

```python
# gunicorn_config.py - Production optimized

import multiprocessing

# Bind
bind = "127.0.0.1:8000"
backlog = 2048

# Workers
workers = multiprocessing.cpu_count() * 2 + 1
worker_class = "sync"
worker_connections = 1000
max_requests = 1000
max_requests_jitter = 50

# Timeouts
timeout = 30
graceful_timeout = 30
keepalive = 5

# Performance
worker_tmp_dir = "/dev/shm"
preload_app = True

# Logging (minimal in production)
accesslog = None  # Disable access log for performance
errorlog = "/var/log/gunicorn/error.log"
loglevel = "warning"

# Security
limit_request_line = 4094
limit_request_fields = 100
limit_request_field_size = 8190
```

### Use Unix Socket (Faster than TCP)

```python
# Instead of TCP
bind = "127.0.0.1:8000"

# Use Unix socket
bind = "unix:/run/gunicorn/gunicorn.sock"
```

### Nginx Configuration with Unix Socket

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://unix:/run/gunicorn/gunicorn.sock;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

---

## 📝 Logging

### Logging Configuration

```python
# gunicorn_config.py

# Access log
accesslog = "/var/log/gunicorn/access.log"
access_log_format = '%(h)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s "%(f)s" "%(a)s" %(D)s'

# Error log
errorlog = "/var/log/gunicorn/error.log"

# Log level
loglevel = "info"  # debug, info, warning, error, critical

# Disable access log (for performance)
accesslog = None
```

### Custom Log Format

```python
# Extended log format
access_log_format = (
    '%(h)s %(l)s %(u)s %(t)s "%(r)s" %(s)s %(b)s '
    '"%(f)s" "%(a)s" %(D)s %(p)s'
)

# Variables:
# %(h)s - Remote address
# %(l)s - '-' (not used)
# %(u)s - User name
# %(t)s - Date of request
# %(r)s - Request line
# %(s)s - Status code
# %(b)s - Response length
# %(f)s - Referer
# %(a)s - User agent
# %(D)s - Request time in microseconds
# %(p)s - Process ID
```

### Log Rotation

```bash
# Create logrotate config
sudo nano /etc/logrotate.d/gunicorn
```

```
/var/log/gunicorn/*.log {
    daily
    missingok
    rotate 14
    compress
    delaycompress
    notifempty
    create 0640 www-data www-data
    sharedscripts
    postrotate
        systemctl reload gunicorn > /dev/null 2>&1 || true
    endscript
}
```

---

## 🔍 Troubleshooting

### Common Issues

**Problem: Workers Timing Out**

```bash
# Increase timeout
gunicorn --timeout 120 myproject.wsgi:application

# Or in config
timeout = 120
```

**Problem: Too Many Workers**

```python
# Reduce workers
workers = 2

# Check memory usage
ps aux | grep gunicorn
```

**Problem: Permission Denied on Socket**

```bash
# Fix socket permissions
sudo chown www-data:www-data /run/gunicorn/gunicorn.sock
sudo chmod 660 /run/gunicorn/gunicorn.sock
```

**Problem: Application Not Reloading**

```bash
# Graceful reload
sudo systemctl reload gunicorn

# Or send HUP signal
sudo kill -HUP $(cat /var/run/gunicorn/gunicorn.pid)
```

### Debugging Commands

```bash
# Check if Gunicorn is running
ps aux | grep gunicorn
sudo systemctl status gunicorn

# View error logs
sudo tail -f /var/log/gunicorn/error.log
sudo journalctl -u gunicorn -f

# Test configuration
gunicorn -c gunicorn_config.py --check-config myproject.wsgi:application

# Check port/socket
sudo netstat -tulpn | grep :8000
sudo ls -la /run/gunicorn/gunicorn.sock

# Test application directly
curl http://127.0.0.1:8000

# Check worker processes
pstree -p $(cat /var/run/gunicorn/gunicorn.pid)
```

### Performance Monitoring

```bash
# Monitor workers
watch -n 1 'ps aux | grep gunicorn'

# Check memory usage
ps aux | grep gunicorn | awk '{sum+=$6} END {print sum/1024 " MB"}'

# Monitor connections
ss -ant | grep :8000 | wc -l
```

---

## 📚 Complete Deployment Example

### 1. Project Setup

```bash
# Create project directory
sudo mkdir -p /var/www/myproject
cd /var/www/myproject

# Create virtual environment
python3 -m venv venv
source venv/bin/activate

# Install dependencies
pip install django gunicorn
pip freeze > requirements.txt
```

### 2. Create Gunicorn Config

```bash
nano gunicorn_config.py
```

```python
import multiprocessing

bind = "unix:/run/gunicorn/gunicorn.sock"
workers = multiprocessing.cpu_count() * 2 + 1
worker_class = "sync"
worker_tmp_dir = "/dev/shm"
timeout = 60
keepalive = 5
max_requests = 1000
max_requests_jitter = 50

errorlog = "/var/log/gunicorn/error.log"
accesslog = "/var/log/gunicorn/access.log"
loglevel = "info"

preload_app = True
```

### 3. Create Systemd Service

```bash
sudo nano /etc/systemd/system/gunicorn.service
```

```ini
[Unit]
Description=Gunicorn daemon for Django
After=network.target

[Service]
Type=notify
User=www-data
Group=www-data
WorkingDirectory=/var/www/myproject
Environment="PATH=/var/www/myproject/venv/bin"
ExecStart=/var/www/myproject/venv/bin/gunicorn \
    -c /var/www/myproject/gunicorn_config.py \
    myproject.wsgi:application
ExecReload=/bin/kill -s HUP $MAINPID
KillMode=mixed
TimeoutStopSec=5
PrivateTmp=true

[Install]
WantedBy=multi-user.target
```

### 4. Setup and Start

```bash
# Create directories
sudo mkdir -p /var/log/gunicorn /run/gunicorn
sudo chown www-data:www-data /var/log/gunicorn /run/gunicorn
sudo chown -R www-data:www-data /var/www/myproject

# Start service
sudo systemctl daemon-reload
sudo systemctl enable gunicorn
sudo systemctl start gunicorn
sudo systemctl status gunicorn
```

### 5. Configure Nginx

```bash
sudo nano /etc/nginx/sites-available/myproject
```

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://unix:/run/gunicorn/gunicorn.sock;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    location /static/ {
        alias /var/www/myproject/static/;
    }

    location /media/ {
        alias /var/www/myproject/media/;
    }
}
```

```bash
# Enable site
sudo ln -s /etc/nginx/sites-available/myproject /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

---

## 📞 Additional Resources

- [Gunicorn Documentation](https://docs.gunicorn.org/)
- [Gunicorn Settings](https://docs.gunicorn.org/en/stable/settings.html)
- [Django Deployment Checklist](https://docs.djangoproject.com/en/stable/howto/deployment/checklist/)

---

**Last Updated:** December 2025  
**Maintained by:** IMCBS Engineering Team
