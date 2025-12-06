# IMCBS Engineering Documentation

Centralized documentation for all IMCBS engineering setups and standards. This repository contains comprehensive guides for configuring and deploying applications using various technologies and services.

## 📚 Documentation Structure

### Infrastructure & Server Management
- **[Nginx](./infra/nginx/)** - Web server configuration, SSL setup, reverse proxy, and load balancing
- **[Gunicorn](./infra/gunicorn/)** - WSGI server setup and configuration for Python applications
- **[PM2](./infra/pm2/)** - Process manager for Node.js applications
- **[VPS Hosting](./infra/vps-hosting/)** - Server provisioning, security hardening, and maintenance

### Application Frameworks
- **[Django](./application-frameworks/django/)** - Beginner-friendly setup, commands, and best practices (from `pip install` to DRF APIs)
- **[Node.js](./application-frameworks/nodejs/)** - Step-by-step Express REST API setup with env, middleware, auth, and deployment tips
- **[React](./application-frameworks/react/)** - Vite/CRA setup, hooks, routing, state patterns, and production-ready checks

> All framework guides are designed for **new joiners** with copy-paste commands, clear folder structures, and troubleshooting tips.

### DevOps & Deployment
- **[CI/CD Pipelines](./devops/cicd/)** - Continuous Integration and Continuous Deployment workflows
- **[Deployment](./devops/deployment/)** - Deployment strategies, rollback procedures, and production checklists
- **[Environment Setup](./devops/environment-setup/)** - Development, staging, and production environment configuration

### Services & Standards
- **[Cloudflare](./services/cloudflare/)** - DNS, CDN, SSL/TLS, WAF, and Workers configuration
- **[Project Structure](./services/project-structure/)** - Standardized folder structures and naming conventions
- **[Company Standards](./services/standards/)** - Code style guides, Git workflow, testing, and security guidelines

## 🚀 Quick Start

1. Navigate to the specific service or technology folder you need
2. Each folder contains a README with an overview and quick links to detailed guides
3. Follow the step-by-step instructions in the documentation files

## 🤝 Contributing

When adding new documentation:
1. Follow the existing folder structure
2. Include a README.md in each new folder with an overview and table of contents
3. Use clear headings and code examples
4. Keep documentation up-to-date with the latest practices

## 📝 Documentation Standards

- Use Markdown format for all documentation
- Include code examples where applicable
- Add links to official documentation for reference
- Keep guides concise and actionable
- Update documentation when processes change

## 📞 Support

For questions or updates to this documentation, please contact the IMCBS engineering team.

---

**Last Updated:** December 2025
