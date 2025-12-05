# 🏗️ Infrastructure Documentation

Complete infrastructure setup and configuration guides for IMCBS production environments.

---

## 📚 Contents

### Web Servers & Proxies
- **[Nginx](./nginx/)** - Web server, reverse proxy, load balancing, and SSL configuration
  - Configuration templates
  - Reverse proxy setup
  - SSL/TLS with Let's Encrypt
  - Load balancing strategies
  - Security hardening
  - Performance optimization

### Application Servers
- **[Gunicorn](./gunicorn/)** - Python WSGI HTTP server for Django/Flask applications
  - Installation and setup
  - Worker configuration
  - Systemd integration
  - Performance tuning
  - Production best practices

### Process Management
- **[PM2](./pm2/)** - Production process manager for Node.js applications
  - Installation and configuration
  - Cluster mode
  - Auto-restart and monitoring
  - Log management
  - Deployment workflows

### Server Management
- **[VPS Hosting](./vps-hosting/)** - VPS server setup, security, and maintenance
  - Initial server configuration
  - Security hardening
  - SSH setup
  - Firewall configuration
  - Monitoring and backups

---

## 🚀 Quick Start Guides

### Deploy Node.js Application
```bash
# 1. Install PM2
npm install -g pm2

# 2. Configure Nginx reverse proxy
sudo nano /etc/nginx/sites-available/your-app

# 3. Start application with PM2
pm2 start app.js --name your-app

# 4. Save PM2 configuration
pm2 save
pm2 startup
```

### Deploy Django Application
```bash
# 1. Install Gunicorn
pip install gunicorn

# 2. Create Gunicorn systemd service
sudo nano /etc/systemd/system/gunicorn.service

# 3. Configure Nginx
sudo nano /etc/nginx/sites-available/your-django-app

# 4. Start services
sudo systemctl start gunicorn
sudo systemctl start nginx
```

---

## 🔗 Related Documentation

- [DevOps CI/CD Pipelines](../devops/cicd/)
- [Deployment Strategies](../devops/deployment/)
- [Environment Setup](../devops/environment-setup/)

---

## 📞 Support

For infrastructure-related questions or issues, contact the IMCBS DevOps team.

---

**Last Updated:** December 2025