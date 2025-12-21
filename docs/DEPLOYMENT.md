# Deployment Guide

This guide details how to deploy Infloo to a Linux-based production server.

## Requisites
- Python 3.10+
- PostgreSQL
- Redis Server
- Nginx (Recommended for reverse proxy)

## 1. Automated Deployment
We provide a `deploy.sh` script that handles:
1. Virtual Environment setup
2. Dependency installation
3. Database Migrations
4. Static File collection
5. Starting the Daphne ASGI server

```bash
chmod +x deploy.sh
./deploy.sh
```

## 2. Configuration (`config.py` or Environment)
Ensure the following variables are set for production:
- `DEBUG`: **False**
- `SECRET_KEY`: A strong, unique string.
- `ALLOWED_HOSTS`: Your domain name / IP.
- `DATABASE_URL`: Connection string to your Production DB.

## 3. ASGI vs WSGI
We use **ASGI (Daphne)** because Infloo features real-time **Chat** and **Notifications** via WebSockets.
- Default Port: `8000`
- Bind Address: `0.0.0.0`

## 4. HTTPS & Security
When `DEBUG=False` is set, Django enables:
- SSL Redirect (Require HTTPS)
- Secure Cookies (Session & CSRF)
- HSTS Headers

**Note**: You must have an SSL certificate configured at your Nginx/Load Balancer level.
