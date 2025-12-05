# 🌐 Nginx Configuration Guide

Complete guide for Nginx web server setup, configuration, and optimization for IMCBS production environments.

---

## 📋 Table of Contents

- [Quick Reference](#quick-reference)
- [Initial Setup](#initial-setup)
- [Reverse Proxy Configuration](#reverse-proxy-configuration)
- [SSL/TLS Setup](#ssltls-setup)
- [Load Balancing](#load-balancing)
- [Security Hardening](#security-hardening)
- [Performance Optimization](#performance-optimization)
- [Common Configurations](#common-configurations)
- [Troubleshooting](#troubleshooting)

---

## 🚀 Quick Reference

### Essential Commands

```bash
# Start/Stop/Restart Nginx
sudo systemctl start nginx
sudo systemctl stop nginx
sudo systemctl restart nginx
sudo systemctl reload nginx    # Reload without downtime

# Test configuration
sudo nginx -t

# View status
sudo systemctl status nginx

# View logs
sudo tail -f /var/log/nginx/access.log
sudo tail -f /var/log/nginx/error.log
```

### Key File Locations

```
/etc/nginx/nginx.conf              # Main configuration
/etc/nginx/sites-available/        # Available site configs
/etc/nginx/sites-enabled/          # Enabled site configs
/var/log/nginx/                    # Log files
/var/www/html/                     # Default web root
```

---

## 📦 Initial Setup

### 1. Installation (Ubuntu/Debian)

```bash
# Update package list
sudo apt update

# Install Nginx
sudo apt install nginx -y

# Start and enable Nginx
sudo systemctl start nginx
sudo systemctl enable nginx

# Check if running
sudo systemctl status nginx
```

### 2. Firewall Configuration

```bash
# Allow Nginx through firewall
sudo ufw allow 'Nginx Full'

# Or specific ports
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp

# Verify firewall status
sudo ufw status
```

### 3. Verify Installation

```bash
# Check Nginx version
nginx -v

# Test configuration
sudo nginx -t

# Visit your server IP in browser
curl http://your-server-ip
```

---

## 🔄 Reverse Proxy Configuration

### Basic Reverse Proxy Setup

Create `/etc/nginx/sites-available/your-domain.com`:

```nginx
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;

    location / {
        proxy_pass http://localhost:3000;  # Your app port
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
}
```

### Enable the Site

```bash
# Create symbolic link
sudo ln -s /etc/nginx/sites-available/your-domain.com /etc/nginx/sites-enabled/

# Test configuration
sudo nginx -t

# Reload Nginx
sudo systemctl reload nginx
```

### Multiple Applications (Different Ports)

```nginx
# Frontend on port 3000
upstream frontend {
    server localhost:3000;
}

# Backend API on port 5000
upstream backend {
    server localhost:5000;
}

server {
    listen 80;
    server_name your-domain.com;

    # Frontend
    location / {
        proxy_pass http://frontend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    # API endpoint
    location /api {
        proxy_pass http://backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }
}
```

---

## 🔒 SSL/TLS Setup

### Using Let's Encrypt (Certbot)

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx -y

# Obtain and install certificate
sudo certbot --nginx -d your-domain.com -d www.your-domain.com

# Test auto-renewal
sudo certbot renew --dry-run
```

### Manual SSL Configuration

```nginx
server {
    listen 443 ssl http2;
    server_name your-domain.com www.your-domain.com;

    # SSL Certificate
    ssl_certificate /etc/letsencrypt/live/your-domain.com/fullchain.pem;
    ssl_certificate_key /etc/letsencrypt/live/your-domain.com/privkey.pem;

    # SSL Configuration
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;
    ssl_prefer_server_ciphers on;
    ssl_session_cache shared:SSL:10m;
    ssl_session_timeout 10m;

    # Security Headers
    add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

# Redirect HTTP to HTTPS
server {
    listen 80;
    server_name your-domain.com www.your-domain.com;
    return 301 https://$server_name$request_uri;
}
```

---

## ⚖️ Load Balancing

### Round Robin Load Balancing

```nginx
upstream backend_servers {
    server backend1.example.com;
    server backend2.example.com;
    server backend3.example.com;
}

server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://backend_servers;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Weighted Load Balancing

```nginx
upstream backend_servers {
    server backend1.example.com weight=3;  # 60% traffic
    server backend2.example.com weight=2;  # 40% traffic
}
```

### IP Hash (Session Persistence)

```nginx
upstream backend_servers {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
}
```

### Health Checks

```nginx
upstream backend_servers {
    server backend1.example.com max_fails=3 fail_timeout=30s;
    server backend2.example.com max_fails=3 fail_timeout=30s;
    server backend3.example.com backup;  # Backup server
}
```

---

## 🛡️ Security Hardening

### Complete Security Configuration

```nginx
server {
    listen 443 ssl http2;
    server_name your-domain.com;

    # SSL Configuration
    ssl_certificate /path/to/cert.pem;
    ssl_certificate_key /path/to/key.pem;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers 'ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES128-GCM-SHA256';
    ssl_prefer_server_ciphers off;

    # Security Headers
    add_header Strict-Transport-Security "max-age=63072000; includeSubDomains; preload" always;
    add_header X-Frame-Options "DENY" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
    add_header Content-Security-Policy "default-src 'self' http: https: data: blob: 'unsafe-inline'" always;

    # Hide Nginx version
    server_tokens off;

    # Limit request size
    client_max_body_size 10M;

    # Rate limiting
    limit_req_zone $binary_remote_addr zone=one:10m rate=10r/s;
    limit_req zone=one burst=20 nodelay;

    # Block common exploits
    location ~* "(eval\()" { deny all; }
    location ~* "(base64_encode|base64_decode)" { deny all; }

    location / {
        proxy_pass http://localhost:3000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Block Bad Bots

```nginx
# Create /etc/nginx/blockuseragents.rules
map $http_user_agent $blockedagent {
    default         0;
    ~*malicious     1;
    ~*bot           1;
    ~*crawl         1;
    ~*Spider        1;
}

# In server block
if ($blockedagent) {
    return 403;
}
```

---

## ⚡ Performance Optimization

### Gzip Compression

```nginx
# /etc/nginx/nginx.conf
http {
    gzip on;
    gzip_vary on;
    gzip_min_length 1024;
    gzip_comp_level 6;
    gzip_types
        text/plain
        text/css
        text/xml
        text/javascript
        application/json
        application/javascript
        application/xml+rss
        application/rss+xml
        font/truetype
        font/opentype
        application/vnd.ms-fontobject
        image/svg+xml;
    gzip_disable "msie6";
}
```

### Browser Caching

```nginx
location ~* \.(jpg|jpeg|png|gif|ico|css|js|svg|woff|woff2|ttf|eot)$ {
    expires 1y;
    add_header Cache-Control "public, immutable";
}
```

### Buffer Size Tuning

```nginx
http {
    client_body_buffer_size 10K;
    client_header_buffer_size 1k;
    client_max_body_size 8m;
    large_client_header_buffers 4 4k;
}
```

### Worker Processes

```nginx
# /etc/nginx/nginx.conf
worker_processes auto;  # One per CPU core
worker_connections 1024;
```

---

## 📝 Common Configurations

### Static Website

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/your-domain.com/html;
    index index.html index.htm;

    location / {
        try_files $uri $uri/ =404;
    }

    # Serve specific file types
    location ~* \.(jpg|jpeg|png|gif|ico|css|js)$ {
        expires 30d;
        add_header Cache-Control "public, no-transform";
    }
}
```

### React/Vue/Angular SPA

```nginx
server {
    listen 80;
    server_name your-domain.com;
    root /var/www/your-app/build;
    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    # API proxy
    location /api {
        proxy_pass http://localhost:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }
}
```

### Django Application

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://unix:/run/gunicorn.sock;
        proxy_set_header Host $host;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    }

    location /static/ {
        alias /var/www/your-project/static/;
    }

    location /media/ {
        alias /var/www/your-project/media/;
    }
}
```

---

## 🔍 Troubleshooting

### Common Issues

**Problem: 502 Bad Gateway**

```bash
# Check if backend app is running
ps aux | grep node  # or your app process

# Check Nginx error logs
sudo tail -f /var/log/nginx/error.log

# Verify proxy_pass URL is correct
sudo nginx -t
```

**Problem: 403 Forbidden**

```bash
# Check file permissions
ls -la /var/www/your-site/

# Fix permissions
sudo chown -R www-data:www-data /var/www/your-site/
sudo chmod -R 755 /var/www/your-site/
```

**Problem: Configuration Not Loading**

```bash
# Test configuration
sudo nginx -t

# Check enabled sites
ls -la /etc/nginx/sites-enabled/

# Reload Nginx
sudo systemctl reload nginx
```

### Debugging Commands

```bash
# Check Nginx configuration syntax
sudo nginx -t

# View detailed error log
sudo tail -f /var/log/nginx/error.log

# Check which config files are loaded
sudo nginx -T

# Monitor access in real-time
sudo tail -f /var/log/nginx/access.log

# Check port usage
sudo netstat -tulpn | grep :80
sudo netstat -tulpn | grep :443
```

---

## 📚 Additional Resources

- [Official Nginx Documentation](https://nginx.org/en/docs/)
- [Nginx Configuration Generator](https://www.digitalocean.com/community/tools/nginx)
- [SSL Labs Server Test](https://www.ssllabs.com/ssltest/)

---

**Last Updated:** December 2025  
**Maintained by:** IMCBS Engineering Team
