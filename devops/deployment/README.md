# 🚀 Complete Deployment Guide - IMCBS

> **Production-ready deployment guides** for Django and Node.js applications on VPS with Nginx, SSL, and best practices.

---

## 📚 Table of Contents

- [Django Deployment (Gunicorn + Nginx + SSL)](#django-deployment-gunicorn--nginx--ssl)
- [Node.js Deployment (PM2 + Nginx + SSL)](#nodejs-deployment-pm2--nginx--ssl)
- [Redeployment After Updates](#redeployment-after-updates)
- [Troubleshooting](#troubleshooting)

---

## 🐍 Django Deployment (Gunicorn + Nginx + SSL)

### 📋 Prerequisites

- Ubuntu 20.04/22.04 VPS
- Domain name pointed to server IP
- Django project on GitHub/GitLab
- SSH access to server

### 🔧 Initial Deployment Steps

#### 1️⃣ SSH into Your VPS

```bash
ssh root@your-server-ip
```

#### 2️⃣ Update System & Install Dependencies

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install python3-pip python3-dev python3-venv libpq-dev nginx curl git ufw -y
```

#### 3️⃣ Clone Your Project

```bash
cd /var/www/
git clone https://github.com/yourusername/yourproject.git
cd yourproject
```

#### 4️⃣ Set Up Virtual Environment

```bash
python3 -m venv venv
source venv/bin/activate
pip install --upgrade pip
pip install -r requirements.txt
pip install gunicorn
```

#### 5️⃣ Configure Django Settings

```python
# In settings.py
DEBUG = False
ALLOWED_HOSTS = ['yourdomain.com', 'www.yourdomain.com', 'your-server-ip']

# Static files
STATIC_ROOT = os.path.join(BASE_DIR, 'static/')
STATIC_URL = '/static/'
```

#### 6️⃣ Prepare Database & Static Files

```bash
python manage.py migrate
python manage.py collectstatic --noinput
python manage.py createsuperuser
```

#### 7️⃣ Test Gunicorn

```bash
# Check available ports
sudo netstat -tuln

# Test Gunicorn (replace 'yourproject' with your project folder name)
gunicorn --bind 127.0.0.1:8001 yourproject.wsgi:application
```

If successful (you see `Listening at: http://127.0.0.1:8001`), press `Ctrl+C` and continue.

#### 8️⃣ Create Gunicorn Service

```bash
sudo nano /etc/systemd/system/gunicorn.service
```

Add this content (replace paths and project name):

```ini
[Unit]
Description=Gunicorn daemon for Django project
After=network.target

[Service]
User=root
Group=www-data
WorkingDirectory=/var/www/yourproject
ExecStart=/var/www/yourproject/venv/bin/gunicorn \
    --workers 3 \
    --bind 127.0.0.1:8001 \
    yourproject.wsgi:application

[Install]
WantedBy=multi-user.target
```

#### 9️⃣ Start & Enable Gunicorn

```bash
sudo systemctl start gunicorn
sudo systemctl enable gunicorn
sudo systemctl status gunicorn
```

#### 🔟 Configure Nginx

```bash
sudo nano /etc/nginx/sites-available/yourproject
```

Add this content:

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location = /favicon.ico {
        access_log off;
        log_not_found off;
    }

    location /static/ {
        root /var/www/yourproject;
    }

    location /media/ {
        root /var/www/yourproject;
    }

    location / {
        include proxy_params;
        proxy_pass http://127.0.0.1:8001;
    }
}
```

#### 1️⃣1️⃣ Enable Nginx Site

```bash
sudo ln -s /etc/nginx/sites-available/yourproject /etc/nginx/sites-enabled
sudo nginx -t  # Test configuration
sudo systemctl restart nginx
```

#### 1️⃣2️⃣ Setup SSL with Certbot

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Get SSL certificate
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com
```

#### 1️⃣3️⃣ Configure Firewall

```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
sudo ufw status
```

#### 1️⃣4️⃣ Verify Auto-Renewal

```bash
sudo certbot renew --dry-run
```

---

## ⚙️ Node.js Deployment (PM2 + Nginx + SSL)

### 📋 Prerequisites

- Ubuntu 20.04/22.04 VPS
- Domain name pointed to server IP
- Node.js project on GitHub/GitLab
- SSH access to server

### 🚀 Initial Deployment Steps

#### 1️⃣ Update System & Install Essentials

```bash
sudo apt update && sudo apt upgrade -y
sudo apt install nginx curl git ufw -y
```

#### 2️⃣ Install Node.js & npm

```bash
# Install Node.js 20.x LTS
curl -fsSL https://deb.nodesource.com/setup_20.x | sudo -E bash -
sudo apt install -y nodejs

# Verify installation
node -v
npm -v
```

#### 3️⃣ Clone Your Project

```bash
# Navigate to projects directory
cd /root/projects/

# Clone your repository
git clone https://github.com/yourusername/your-repo.git
cd your-repo

# Install dependencies
npm install

# Create/configure environment variables
nano .env
```

#### 4️⃣ Install & Configure PM2

```bash
# Install PM2 globally
sudo npm install -g pm2

# Start your application
pm2 start server.js --name "my-node-app"
# OR if using npm script
pm2 start npm --name "my-node-app" -- start

# Save PM2 configuration
pm2 save

# Setup PM2 to start on boot
pm2 startup
# Copy and run the command that PM2 outputs

# Check PM2 status
pm2 status
pm2 logs
```

#### 5️⃣ Configure Nginx Reverse Proxy

```bash
sudo nano /etc/nginx/sites-available/my-node-app
```

Add this content:

```nginx
server {
    listen 80;
    server_name yourdomain.com www.yourdomain.com;

    location / {
        proxy_pass http://localhost:5000;  # Your Node.js port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Optional: Serve static files
    location /static {
        alias /root/projects/my-node-app/public;
        expires 30d;
        add_header Cache-Control "public, immutable";
    }

    error_log /var/log/nginx/my-node-app_error.log;
    access_log /var/log/nginx/my-node-app_access.log;
}
```

Enable the site:

```bash
sudo ln -s /etc/nginx/sites-available/my-node-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx
```

#### 6️⃣ Configure Firewall

```bash
sudo ufw allow OpenSSH
sudo ufw allow 'Nginx Full'
sudo ufw enable
sudo ufw status
```

#### 7️⃣ Install SSL Certificate (Let's Encrypt)

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Get SSL certificate
sudo certbot --nginx -d yourdomain.com -d www.yourdomain.com

# Test auto-renewal
sudo certbot renew --dry-run
```

#### 8️⃣ Final Verification

```bash
# Check if everything is running
pm2 status
sudo systemctl status nginx
sudo systemctl status certbot.timer

# Test your website
curl -I https://yourdomain.com
```

---

## 🔄 Redeployment After Updates

### Django Redeployment

After making changes locally and pushing to GitHub:

```bash
# 1. SSH into server
ssh root@your-server-ip

# 2. Navigate to project
cd /var/www/yourproject

# 3. Pull latest code
git pull origin main

# 4. Activate virtualenv
source venv/bin/activate

# 5. Install new dependencies (if any)
pip install -r requirements.txt

# 6. Run migrations (if needed)
python manage.py migrate

# 7. Collect static files (if changed)
python manage.py collectstatic --noinput

# 8. Find your Gunicorn service name
systemctl | grep gunicorn
# OR
grep -Ri "yourproject" /etc/systemd/system/

# 9. Restart Gunicorn
sudo systemctl restart gunicorn

# 10. Check status
sudo systemctl status gunicorn

# 11. Optional: Restart Nginx if config changed
sudo systemctl restart nginx

# 12. Check logs for errors
sudo journalctl -u gunicorn -n 50
```

### Node.js Redeployment

#### Method 1: Git Pull & Restart (Recommended)

```bash
# 1. Navigate to your project directory
cd /root/projects/my-node-app

# 2. Pull latest changes
git pull origin main

# 3. Install any new dependencies
npm install

# 4. Restart the application with PM2
pm2 restart my-node-app

# 5. Check logs for any errors
pm2 logs my-node-app --lines 50
```

#### Method 2: Zero-Downtime Reload

```bash
cd /root/projects/my-node-app
git pull origin main
npm install
pm2 reload my-node-app  # Zero downtime restart
pm2 logs my-node-app --lines 20
```

#### Method 3: Deployment Script (Automated)

Create a deployment script:

```bash
nano /root/deploy.sh
```

Add this content:

```bash
#!/bin/bash

APP_DIR="/root/projects/my-node-app"
APP_NAME="my-node-app"

echo "🚀 Starting deployment..."

cd $APP_DIR

echo "📥 Pulling latest changes..."
git pull origin main

echo "📦 Installing dependencies..."
npm install

echo "🔄 Restarting application..."
pm2 restart $APP_NAME

echo "✅ Deployment complete!"
pm2 status
pm2 logs $APP_NAME --lines 10
```

Make it executable and run:

```bash
chmod +x /root/deploy.sh
./deploy.sh
```

---

## 🔍 Troubleshooting

### Django Issues

#### Issue 1: Gunicorn Not Starting

```bash
# Check Gunicorn status
sudo systemctl status gunicorn

# View Gunicorn logs
sudo journalctl -u gunicorn -n 50

# Check if port is being used
sudo netstat -tulpn | grep :8001

# Restart Gunicorn
sudo systemctl restart gunicorn
```

#### Issue 2: Nginx 502 Bad Gateway

```bash
# Check if Gunicorn is running
sudo systemctl status gunicorn

# Check Nginx configuration
sudo nginx -t

# Check Nginx error logs
sudo tail -f /var/log/nginx/error.log

# Restart both services
sudo systemctl restart gunicorn
sudo systemctl restart nginx
```

#### Issue 3: Static Files Not Loading

```bash
# Collect static files again
cd /var/www/yourproject
source venv/bin/activate
python manage.py collectstatic --noinput

# Check permissions
sudo chown -R root:www-data /var/www/yourproject/static
sudo chmod -R 755 /var/www/yourproject/static

# Restart Nginx
sudo systemctl restart nginx
```

### Node.js Issues

#### Issue 1: PM2 App Won't Start

```bash
# Check logs
pm2 logs my-node-app

# Delete and recreate
pm2 delete my-node-app
pm2 start server.js --name my-node-app
pm2 save
```

#### Issue 2: Port Already in Use

```bash
# Find process using port
sudo lsof -i :5000

# Kill process if needed
sudo kill -9 <PID>

# Restart PM2 app
pm2 restart my-node-app
```

#### Issue 3: SSL Certificate Issues

```bash
# Renew certificate
sudo certbot renew

# Check certificate expiry
sudo certbot certificates

# Force renewal
sudo certbot renew --force-renewal
```

---

## 📊 Monitoring & Maintenance

### Check Application Status

```bash
# Django
sudo systemctl status gunicorn
sudo systemctl status nginx

# Node.js
pm2 status
pm2 monit  # Real-time monitoring

# System resources
htop
df -h
free -m
```

### View Logs

```bash
# Django - Gunicorn logs
sudo journalctl -u gunicorn -f

# Node.js - PM2 logs
pm2 logs my-node-app
pm2 logs my-node-app --lines 100

# Nginx logs
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/nginx/access.log
```

### Server Maintenance

```bash
# Update system packages
sudo apt update && sudo apt upgrade -y

# Clean up disk space
sudo apt autoremove -y
sudo apt clean

# Check disk usage
df -h

# Check running services
systemctl list-units --type=service --state=running
```

---

## 🔗 Useful Commands Cheat Sheet

### Django Commands

```bash
# Gunicorn
sudo systemctl start gunicorn
sudo systemctl stop gunicorn
sudo systemctl restart gunicorn
sudo systemctl status gunicorn
sudo journalctl -u gunicorn -f

# Django Management
python manage.py migrate
python manage.py collectstatic --noinput
python manage.py createsuperuser
python manage.py shell
```

### Node.js PM2 Commands

```bash
pm2 list                    # List all processes
pm2 start app.js            # Start app
pm2 restart app-name        # Restart specific app
pm2 reload app-name         # Zero-downtime restart
pm2 stop app-name           # Stop app
pm2 delete app-name         # Delete app
pm2 logs app-name           # View logs
pm2 logs app-name --lines 50
pm2 flush                   # Clear all logs
pm2 save                    # Save current configuration
pm2 resurrect              # Restore saved configuration
pm2 monit                   # Monitor resources
```

### Nginx Commands

```bash
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx
sudo systemctl status nginx
sudo nginx -t               # Test configuration
sudo tail -f /var/log/nginx/error.log
```

### Git Commands

```bash
git status                  # Check repository status
git pull origin main        # Pull latest changes
git log --oneline -5        # View recent commits
git branch                  # Check current branch
git stash                   # Stash local changes
git stash pop               # Apply stashed changes
```

---

## 📝 Important Notes

### Django

- Replace `yourproject` with your actual project name
- Replace `yourdomain.com` with your actual domain
- Update `ALLOWED_HOSTS` in Django settings
- Ensure your domain's DNS points to your server IP
- Keep your `requirements.txt` updated
- Consider using PostgreSQL for production instead of SQLite
- Set up regular backups for your database and media files

### Node.js

- Backup `.env` files before deployment
- Ensure all environment variables are properly set
- If using a database, make sure it's running and accessible
- Regularly check PM2 and Nginx logs for issues
- Keep your system updated with `sudo apt update && sudo apt upgrade`
- Consider setting up monitoring tools like Uptime Robot

---

## 🚨 Security Checklist

- [ ] Never commit sensitive data (API keys, passwords) to Git
- [ ] Use environment variables for sensitive settings
- [ ] Keep your system and packages updated
- [ ] Set up proper logging and monitoring
- [ ] Use strong passwords and SSH keys
- [ ] Configure firewall (UFW) properly
- [ ] Enable SSL/HTTPS for all domains
- [ ] Regular backups of database and files
- [ ] Consider using a non-root user for deployment

---

## 📚 Additional Resources

- [Django Deployment Checklist](https://docs.djangoproject.com/en/stable/howto/deployment/checklist/)
- [Gunicorn Documentation](https://docs.gunicorn.org/)
- [PM2 Documentation](https://pm2.keymetrics.io/)
- [Nginx Documentation](https://nginx.org/en/docs/)
- [Let's Encrypt Documentation](https://letsencrypt.org/docs/)
- [Digital Ocean Tutorials](https://www.digitalocean.com/community/tutorials)

---

**Happy Deploying! 🎉**
