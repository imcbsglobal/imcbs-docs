# 🚀 PM2 Process Manager Guide

Complete guide for managing Node.js applications in production with PM2 - Advanced production process manager.

---

## 📋 Table of Contents

- [Quick Reference](#quick-reference)
- [Installation](#installation)
- [Basic Usage](#basic-usage)
- [Cluster Mode](#cluster-mode)
- [Ecosystem File](#ecosystem-file)
- [Process Management](#process-management)
- [Monitoring](#monitoring)
- [Log Management](#log-management)
- [Startup Scripts](#startup-scripts)
- [Deployment](#deployment)
- [Best Practices](#best-practices)
- [Troubleshooting](#troubleshooting)

---

## 🚀 Quick Reference

### Essential Commands

```bash
# Start application
pm2 start app.js --name my-app

# List all processes
pm2 list

# Monitor processes
pm2 monit

# View logs
pm2 logs

# Restart process
pm2 restart my-app

# Stop process
pm2 stop my-app

# Delete process
pm2 delete my-app

# Save current process list
pm2 save

# Startup script
pm2 startup
```

### Quick Status Check

```bash
pm2 status        # List all processes
pm2 show my-app   # Detailed info
pm2 logs my-app   # Real-time logs
```

---

## 📦 Installation

### Install PM2 Globally

```bash
# Using npm
npm install -g pm2

# Using yarn
yarn global add pm2

# Verify installation
pm2 --version

# Update PM2
npm install -g pm2@latest
pm2 update
```

### Install PM2 on Server

```bash
# Ubuntu/Debian
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo npm install -g pm2

# Check installation
pm2 --version
node --version
npm --version
```

---

## 🎯 Basic Usage

### Start Applications

```bash
# Basic start
pm2 start app.js

# Start with name
pm2 start app.js --name my-app

# Start with arguments
pm2 start app.js --name my-app -- --port 3000

# Start script from package.json
pm2 start npm --name my-app -- start

# Start with specific Node.js version
pm2 start app.js --interpreter node@14

# Start in watch mode (auto-restart on file changes)
pm2 start app.js --watch

# Start with specific environment
pm2 start app.js --env production
```

### Process Management

```bash
# List all processes
pm2 list
pm2 ls
pm2 status

# Show detailed process info
pm2 show my-app
pm2 describe my-app

# Restart process
pm2 restart my-app
pm2 restart all

# Reload process (zero-downtime)
pm2 reload my-app

# Stop process
pm2 stop my-app
pm2 stop all

# Delete process from PM2
pm2 delete my-app
pm2 delete all

# Reset restart counter
pm2 reset my-app
```

---

## ⚡ Cluster Mode

### Enable Cluster Mode

```bash
# Start in cluster mode with max CPUs
pm2 start app.js -i max

# Start with specific number of instances
pm2 start app.js -i 4

# Scale to specific number
pm2 scale my-app 4

# Add or remove instances
pm2 scale my-app +2   # Add 2 instances
pm2 scale my-app -1   # Remove 1 instance
```

### Cluster Configuration

```bash
# Start with cluster mode
pm2 start app.js \
    --name my-app \
    -i max \
    --max-memory-restart 300M \
    --log /var/log/pm2/my-app.log
```

### Load Balancing

```javascript
// app.js
const express = require('express');
const app = express();

app.get('/', (req, res) => {
    res.send(`Worker PID: ${process.pid}`);
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => {
    console.log(`Server running on port ${PORT}, PID: ${process.pid}`);
});
```

```bash
# Start with 4 instances
pm2 start app.js -i 4

# PM2 automatically load balances requests across all instances
```

---

## 📄 Ecosystem File

### Create Ecosystem File

```bash
# Generate ecosystem file
pm2 ecosystem

# Or pm2 init
pm2 init
```

### Complete Ecosystem Configuration

```javascript
// ecosystem.config.js
module.exports = {
  apps: [
    {
      name: 'my-app',
      script: 'app.js',
      instances: 'max',
      exec_mode: 'cluster',
      
      // Environment variables
      env: {
        NODE_ENV: 'development',
        PORT: 3000
      },
      env_production: {
        NODE_ENV: 'production',
        PORT: 8000
      },
      
      // Restart behavior
      watch: false,
      ignore_watch: ['node_modules', 'logs'],
      max_memory_restart: '500M',
      max_restarts: 10,
      min_uptime: '10s',
      autorestart: true,
      
      // Logging
      error_file: '/var/log/pm2/app-error.log',
      out_file: '/var/log/pm2/app-out.log',
      log_date_format: 'YYYY-MM-DD HH:mm:ss Z',
      merge_logs: true,
      
      // Advanced
      cron_restart: '0 0 * * *',  // Restart every day at midnight
      restart_delay: 4000,
      kill_timeout: 5000,
      wait_ready: true,
      listen_timeout: 3000
    },
    
    // Multiple apps
    {
      name: 'api-server',
      script: 'api.js',
      instances: 2,
      exec_mode: 'cluster',
      env: {
        PORT: 5000
      }
    },
    
    {
      name: 'worker',
      script: 'worker.js',
      instances: 1,
      exec_mode: 'fork',
      cron_restart: '*/30 * * * *'  // Restart every 30 minutes
    }
  ]
};
```

### Using Ecosystem File

```bash
# Start all apps
pm2 start ecosystem.config.js

# Start specific app
pm2 start ecosystem.config.js --only my-app

# Start with production environment
pm2 start ecosystem.config.js --env production

# Update ecosystem
pm2 restart ecosystem.config.js

# Reload with zero downtime
pm2 reload ecosystem.config.js
```

### Advanced Ecosystem Examples

```javascript
// ecosystem.config.js - Production Ready
module.exports = {
  apps: [
    {
      name: 'production-app',
      script: './dist/server.js',
      instances: 'max',
      exec_mode: 'cluster',
      
      env_production: {
        NODE_ENV: 'production',
        PORT: 3000,
        DATABASE_URL: 'mongodb://localhost:27017/mydb'
      },
      
      // Auto-restart on crashes
      autorestart: true,
      max_restarts: 10,
      min_uptime: '10s',
      restart_delay: 4000,
      
      // Memory management
      max_memory_restart: '1G',
      
      // Logs
      error_file: './logs/error.log',
      out_file: './logs/out.log',
      log_date_format: 'YYYY-MM-DD HH:mm:ss',
      
      // Performance
      node_args: '--max_old_space_size=2048',
      
      // Graceful shutdown
      kill_timeout: 5000,
      wait_ready: true,
      listen_timeout: 10000
    }
  ]
};
```

---

## 🔄 Process Management

### Start/Stop/Restart

```bash
# Start
pm2 start app.js --name my-app

# Stop (keeps in process list)
pm2 stop my-app

# Restart (kills and starts)
pm2 restart my-app

# Reload (zero-downtime restart for cluster)
pm2 reload my-app

# Delete (removes from list)
pm2 delete my-app

# Graceful reload
pm2 gracefulReload my-app
```

### Manage Multiple Processes

```bash
# Start multiple apps
pm2 start app1.js
pm2 start app2.js
pm2 start app3.js

# Restart all
pm2 restart all

# Stop all
pm2 stop all

# Delete all
pm2 delete all

# Restart specific processes by ID
pm2 restart 0 2 4
```

### Process Information

```bash
# List all processes
pm2 list

# Show detailed info
pm2 show my-app

# Monitor in real-time
pm2 monit

# Get JSON info
pm2 jlist
```

---

## 📊 Monitoring

### Real-Time Monitoring

```bash
# Interactive monitor
pm2 monit

# Process list
pm2 list

# Detailed info
pm2 show my-app

# CPU and Memory usage
pm2 list --watch
```

### Web Dashboard (PM2 Plus)

```bash
# Link to PM2 Plus
pm2 link <secret_key> <public_key>

# Or simple web interface
pm2 web
# Access at http://localhost:9615
```

### Custom Monitoring Script

```javascript
// monitor.js
const pm2 = require('pm2');

pm2.connect((err) => {
  if (err) {
    console.error(err);
    process.exit(2);
  }

  pm2.list((err, processes) => {
    processes.forEach((proc) => {
      console.log(`${proc.name}: ${proc.pm2_env.status}`);
      console.log(`  Memory: ${(proc.monit.memory / 1024 / 1024).toFixed(2)} MB`);
      console.log(`  CPU: ${proc.monit.cpu}%`);
      console.log(`  Restarts: ${proc.pm2_env.restart_time}`);
    });
    
    pm2.disconnect();
  });
});
```

```bash
# Run monitoring script
node monitor.js
```

---

## 📝 Log Management

### View Logs

```bash
# View all logs
pm2 logs

# View specific app logs
pm2 logs my-app

# View last 100 lines
pm2 logs my-app --lines 100

# View logs in JSON format
pm2 logs --json

# View error logs only
pm2 logs my-app --err

# View output logs only
pm2 logs my-app --out

# Clear logs
pm2 flush
```

### Configure Logs in Ecosystem

```javascript
// ecosystem.config.js
module.exports = {
  apps: [{
    name: 'my-app',
    script: 'app.js',
    
    // Log files
    error_file: '/var/log/pm2/my-app-error.log',
    out_file: '/var/log/pm2/my-app-out.log',
    
    // Log formatting
    log_date_format: 'YYYY-MM-DD HH:mm:ss Z',
    
    // Combine logs from all instances
    merge_logs: true,
    
    // Disable logs (for performance)
    // error_file: '/dev/null',
    // out_file: '/dev/null',
  }]
};
```

### Log Rotation

```bash
# Install PM2 log rotate module
pm2 install pm2-logrotate

# Configure log rotation
pm2 set pm2-logrotate:max_size 10M
pm2 set pm2-logrotate:retain 30
pm2 set pm2-logrotate:compress true
pm2 set pm2-logrotate:dateFormat YYYY-MM-DD_HH-mm-ss
pm2 set pm2-logrotate:workerInterval 30
pm2 set pm2-logrotate:rotateInterval '0 0 * * *'
```

---

## 🔧 Startup Scripts

### Create Startup Script

```bash
# Generate startup script (run as root or with sudo)
pm2 startup

# This will output a command like:
# sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u username --hp /home/username

# Run the generated command
sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u $USER --hp $HOME
```

### Save Process List

```bash
# Start your applications
pm2 start app.js --name my-app
pm2 start api.js --name api-server

# Save current process list
pm2 save

# Now PM2 will restart these apps on system reboot
```

### Update Startup Script

```bash
# After making changes to your apps
pm2 start new-app.js
pm2 save

# Update startup script
pm2 startup

# Or explicitly update
sudo pm2 startup systemd -u $USER --hp $HOME
```

### Remove Startup Script

```bash
# Unstartup PM2
pm2 unstartup systemd
```

### Complete Deployment Setup

```bash
# 1. Start applications
pm2 start ecosystem.config.js --env production

# 2. Save process list
pm2 save

# 3. Setup startup script
pm2 startup

# 4. Run the generated command (example)
sudo env PATH=$PATH:/usr/bin pm2 startup systemd -u ubuntu --hp /home/ubuntu

# Verify
sudo systemctl status pm2-ubuntu
```

---

## 🚀 Deployment

### Simple Git-Based Deployment

```bash
# On server
cd /var/www/my-app
git pull origin main
npm install
pm2 restart my-app

# Or using ecosystem
pm2 restart ecosystem.config.js --env production
```

### PM2 Deploy Configuration

```javascript
// ecosystem.config.js
module.exports = {
  apps: [{
    name: 'my-app',
    script: 'app.js',
    instances: 'max',
    exec_mode: 'cluster'
  }],
  
  deploy: {
    production: {
      user: 'ubuntu',
      host: 'your-server.com',
      ref: 'origin/main',
      repo: 'git@github.com:username/repo.git',
      path: '/var/www/my-app',
      'pre-deploy-local': '',
      'post-deploy': 'npm install && pm2 reload ecosystem.config.js --env production',
      'pre-setup': ''
    }
  }
};
```

```bash
# Setup deployment (first time)
pm2 deploy ecosystem.config.js production setup

# Deploy
pm2 deploy ecosystem.config.js production

# Revert deployment
pm2 deploy ecosystem.config.js production revert 1
```

### Zero-Downtime Deployment

```bash
# Using reload instead of restart
pm2 reload my-app

# Or with ecosystem
pm2 reload ecosystem.config.js --env production

# Graceful reload with timeout
pm2 gracefulReload my-app
```

### Deployment Script

```bash
#!/bin/bash
# deploy.sh

echo "Pulling latest code..."
git pull origin main

echo "Installing dependencies..."
npm install

echo "Building application..."
npm run build

echo "Reloading PM2..."
pm2 reload ecosystem.config.js --env production

echo "Deployment complete!"
```

```bash
# Make executable and run
chmod +x deploy.sh
./deploy.sh
```

---

## ✅ Best Practices

### Production Configuration

```javascript
// ecosystem.config.js - Best Practices
module.exports = {
  apps: [{
    name: 'production-app',
    script: './dist/server.js',
    
    // Use cluster mode
    instances: 'max',
    exec_mode: 'cluster',
    
    // Environment
    env_production: {
      NODE_ENV: 'production'
    },
    
    // Auto-restart settings
    autorestart: true,
    max_restarts: 10,
    min_uptime: '10s',
    restart_delay: 4000,
    
    // Memory management
    max_memory_restart: '1G',
    
    // Graceful shutdown
    kill_timeout: 5000,
    wait_ready: true,
    
    // Logging
    error_file: './logs/error.log',
    out_file: './logs/out.log',
    merge_logs: true,
    log_date_format: 'YYYY-MM-DD HH:mm:ss',
    
    // Performance
    node_args: '--max_old_space_size=2048'
  }]
};
```

### Security Best Practices

```bash
# Don't run PM2 as root
# Create a dedicated user
sudo adduser --system --group --no-create-home pm2user

# Set proper permissions
sudo chown -R pm2user:pm2user /var/www/my-app

# Setup PM2 for that user
sudo su - pm2user
pm2 startup
```

### Monitoring Best Practices

```bash
# Install log rotation
pm2 install pm2-logrotate

# Enable metrics
pm2 set pm2-metrics:cpu true
pm2 set pm2-metrics:memory true

# Set up alerts (PM2 Plus)
pm2 link <secret> <public>
```

---

## 🔍 Troubleshooting

### Common Issues

**Problem: App keeps restarting**

```bash
# Check logs
pm2 logs my-app --lines 50

# Check error count
pm2 show my-app

# Increase min uptime
pm2 restart my-app --min-uptime 30s --max-restarts 5
```

**Problem: High memory usage**

```bash
# Set memory limit
pm2 restart my-app --max-memory-restart 500M

# Or in ecosystem
max_memory_restart: '500M'
```

**Problem: App not starting on reboot**

```bash
# Check startup script
sudo systemctl status pm2-$USER

# Regenerate startup script
pm2 unstartup
pm2 startup
pm2 save
```

**Problem: Port already in use**

```bash
# Find process using port
sudo lsof -i :3000

# Kill process
sudo kill -9 <PID>

# Or change port in your app
```

### Debugging Commands

```bash
# Detailed process info
pm2 show my-app

# Check PM2 version
pm2 --version

# Update PM2
npm install -g pm2@latest
pm2 update

# Reset PM2
pm2 kill
pm2 resurrect

# Check PM2 daemon
pm2 ping

# System info
pm2 info

# Flush logs
pm2 flush
```

### Performance Debugging

```bash
# Monitor CPU and Memory
pm2 monit

# Check process details
pm2 describe my-app

# Generate heap dump
pm2 trigger my-app heapdump

# CPU profiling (requires pm2-runtime)
pm2 trigger my-app cpu:profiling:start
pm2 trigger my-app cpu:profiling:stop
```

---

## 📚 Complete Production Setup

### Full Deployment Workflow

```bash
# 1. Server Setup
ssh user@your-server.com
curl -sL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt-get install -y nodejs git
sudo npm install -g pm2

# 2. Clone Repository
cd /var/www
sudo git clone https://github.com/username/my-app.git
cd my-app
sudo chown -R $USER:$USER /var/www/my-app

# 3. Install Dependencies
npm install

# 4. Create Ecosystem File
cat > ecosystem.config.js << 'EOF'
module.exports = {
  apps: [{
    name: 'my-app',
    script: 'app.js',
    instances: 'max',
    exec_mode: 'cluster',
    env_production: {
      NODE_ENV: 'production',
      PORT: 3000
    },
    max_memory_restart: '500M',
    error_file: './logs/error.log',
    out_file: './logs/out.log'
  }]
};
EOF

# 5. Create logs directory
mkdir -p logs

# 6. Start with PM2
pm2 start ecosystem.config.js --env production

# 7. Setup startup script
pm2 save
pm2 startup

# 8. Configure Nginx
sudo nano /etc/nginx/sites-available/my-app
```

```nginx
server {
    listen 80;
    server_name your-domain.com;

    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}
```

```bash
# 9. Enable Nginx site
sudo ln -s /etc/nginx/sites-available/my-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl reload nginx

# 10. Setup SSL (optional but recommended)
sudo apt install certbot python3-certbot-nginx
sudo certbot --nginx -d your-domain.com

# 11. Verify
pm2 status
curl http://localhost:3000
```

---

## 📞 Additional Resources

- [PM2 Documentation](https://pm2.keymetrics.io/)
- [PM2 GitHub](https://github.com/Unitech/pm2)
- [PM2 Plus Monitoring](https://pm2.io/)

---

**Last Updated:** December 2025  
**Maintained by:** IMCBS Engineering Team
