# 🖥️ VPS Server Management Guide

> **Essential commands** for logging into VPS servers, checking status, and managing Git repositories at IMCBS.

---

## 📚 Table of Contents

- [Login to VPS](#login-to-vps)
- [Check Server Status](#check-server-status)
- [Git Operations](#git-operations)
- [Quick Commands](#quick-commands)

---

## 🔐 Login to VPS

### SSH Login with Password

```bash
# Basic SSH login
ssh root@your-server-ip

# Example
ssh root@123.45.67.89
```

### SSH Login with Custom Port

```bash
# If SSH is on a different port
ssh -p 2222 root@your-server-ip

# Example
ssh -p 2222 root@123.45.67.89
```

### SSH Login with Key File

```bash
# Login with private key
ssh -i /path/to/key.pem root@your-server-ip

# Example
ssh -i ~/.ssh/imcbs-vps.pem root@123.45.67.89
```

### Save Server Credentials

Create an SSH config file for easy access:

```bash
# Edit SSH config
nano ~/.ssh/config
```

Add this content:

```bash
Host imcbs-prod
    HostName 123.45.67.89
    User root
    Port 22
    IdentityFile ~/.ssh/imcbs-vps.pem

Host imcbs-dev
    HostName 98.76.54.32
    User root
    Port 22
```

Now login easily:

```bash
# Instead of: ssh root@123.45.67.89
# Just type:
ssh imcbs-prod
```

---

## 📊 Check Server Status

### System Information

```bash
# Check OS version
lsb_release -a
# OR
cat /etc/os-release

# Check system uptime
uptime

# Check system resources (CPU, Memory, Disk)
htop  # Interactive (press q to quit)

# Check memory usage
free -h

# Check disk usage
df -h

# Check CPU information
lscpu
```

### Running Services

```bash
# Check all running services
systemctl list-units --type=service --state=running

# Check specific service status
systemctl status nginx
systemctl status gunicorn
systemctl status pm2-root

# Check if a service is active
systemctl is-active nginx
```

### Network & Ports

```bash
# Check open ports and listening services
sudo netstat -tuln
# OR
sudo ss -tuln

# Check specific port
sudo lsof -i :80
sudo lsof -i :443
sudo lsof -i :8001

# Check firewall status
sudo ufw status
```

### Process Management

```bash
# List all running processes
ps aux

# Find specific process
ps aux | grep nginx
ps aux | grep gunicorn
ps aux | grep node

# Check PM2 processes (Node.js apps)
pm2 status
pm2 list

# Kill a process by PID
kill <PID>
# Force kill
kill -9 <PID>
```

---

## 🔧 Git Operations

### Check Git Status

```bash
# Navigate to project directory
cd /var/www/yourproject
# OR for Node.js
cd /root/projects/your-node-app

# Check current branch
git branch

# Check git status
git status

# Check recent commits
git log --oneline -5

# Check remote URLs
git remote -v
```

### Pull Latest Changes

```bash
# Make sure you're on the correct branch
git branch

# Pull latest changes
git pull origin main
# OR
git pull origin dev

# If there are conflicts, resolve them
git status
# Edit conflicting files, then:
git add .
git commit -m "resolve merge conflicts"
```

### Stash Local Changes

```bash
# If you have local changes you want to save
git stash

# Pull latest changes
git pull origin main

# Apply stashed changes back
git stash pop
```

### Reset to Remote

```bash
# ⚠️ WARNING: This discards all local changes!
git fetch origin
git reset --hard origin/main
```

### Check Git Configuration

```bash
# View git config
git config --list

# Set user name and email (if needed)
git config --global user.name "Your Name"
git config --global user.email "your.email@example.com"
```

---

## ⚡ Quick Commands

### Daily Server Checks

```bash
# Run these commands to check server health
uptime                      # How long server has been running
free -h                     # Memory usage
df -h                       # Disk space
systemctl status nginx      # Nginx status
systemctl status gunicorn   # Gunicorn status (Django)
pm2 status                  # PM2 status (Node.js)
```

### Update and Restart Services

```bash
# For Django projects
cd /var/www/yourproject
git pull origin main
source venv/bin/activate
pip install -r requirements.txt
python manage.py migrate
python manage.py collectstatic --noinput
sudo systemctl restart gunicorn
sudo systemctl restart nginx

# For Node.js projects
cd /root/projects/your-node-app
git pull origin main
npm install
pm2 restart your-app-name
```

### View Logs

```bash
# Nginx logs
sudo tail -f /var/log/nginx/error.log
sudo tail -f /var/log/nginx/access.log

# Gunicorn logs (Django)
sudo journalctl -u gunicorn -f

# PM2 logs (Node.js)
pm2 logs your-app-name
pm2 logs your-app-name --lines 50

# System logs
sudo journalctl -xe
```

### Server Maintenance

```bash
# Update system packages
sudo apt update
sudo apt upgrade -y

# Clean up disk space
sudo apt autoremove -y
sudo apt clean

# Restart server (use with caution!)
sudo reboot
```

---

## 🆘 Troubleshooting

### Can't Login to VPS

```bash
# Test SSH connection
ssh -v root@your-server-ip

# Check if port is open
telnet your-server-ip 22

# Common issues:
# 1. Wrong IP address
# 2. Wrong SSH key
# 3. Firewall blocking SSH
# 4. Server is down
```

### Service Not Running

```bash
# Check service status
sudo systemctl status nginx

# Start service
sudo systemctl start nginx

# Restart service
sudo systemctl restart nginx

# Enable service to start on boot
sudo systemctl enable nginx

# Check service logs
sudo journalctl -u nginx -n 50
```

### Git Pull Fails

```bash
# Error: Local changes would be overwritten
# Solution 1: Stash changes
git stash
git pull origin main

# Solution 2: Discard local changes (⚠️ careful!)
git reset --hard
git pull origin main

# Error: Permission denied
# Check if you have SSH key set up for GitHub/GitLab
```

### Disk Full

```bash
# Check disk usage
df -h

# Find large files
sudo du -sh /* | sort -hr | head -10

# Clean up logs
sudo journalctl --vacuum-time=7d

# Clean apt cache
sudo apt clean
```

---

## 📋 Useful Aliases

Add these to your `~/.bashrc` for quick access:

```bash
# Edit bashrc
nano ~/.bashrc

# Add these lines at the end:
alias ll='ls -lah'
alias gst='git status'
alias gpl='git pull origin'
alias pm2l='pm2 logs'
alias nginxreload='sudo systemctl reload nginx'
alias nginxrestart='sudo systemctl restart nginx'

# Save and reload
source ~/.bashrc
```

---

## 🎯 Quick Checklist

When logging into VPS, run these checks:

- [ ] Check system uptime: `uptime`
- [ ] Check disk space: `df -h`
- [ ] Check memory: `free -h`
- [ ] Check Nginx: `systemctl status nginx`
- [ ] Check application: `systemctl status gunicorn` or `pm2 status`
- [ ] Check logs for errors: `tail -f /var/log/nginx/error.log`
- [ ] Check git status: `cd /var/www/project && git status`

---

**For deployment guides, check the DevOps section! 🚀**
