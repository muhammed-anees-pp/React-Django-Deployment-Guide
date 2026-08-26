# Troubleshooting Guide

This document contains common checks and troubleshooting steps for applications deployed using technologies such as Docker, Nginx, Django, React, Redis, Celery, and cloud-hosted databases.

> Replace placeholder values such as `your-container-name`, `your-domain.com`, `your-database-endpoint`, and `project-folder` according to your project configuration.

---

# General Troubleshooting Workflow

When something is not working, avoid changing multiple configurations at the same time.

Follow a step-by-step approach:

```text
Identify the Problem
        │
        ▼
Check Application Logs
        │
        ▼
Check Container Status
        │
        ▼
Check Server Resources
        │
        ▼
Check Network Connectivity
        │
        ▼
Check Service Configuration
        │
        ▼
Apply the Fix
        │
        ▼
Restart or Reload the Required Service
        │
        ▼
Verify the Application
````

---

# Application Is Not Working

Start by checking the running Docker containers:

```bash
docker ps
```

Check the status of Docker Compose services:

```bash
docker compose -f docker-compose.prod.yml ps
```

If a container has stopped, check all containers:

```bash
docker ps -a
```

Then inspect the container logs:

```bash
docker logs your-container-name --tail 100
```

---

# Backend Is Not Responding

First, check whether the backend container is running:

```bash
docker ps
```

Check the backend logs:

```bash
docker logs your-backend-container --tail 100
```

Test the backend locally from the server:

```bash
curl http://localhost:8000
```

If the backend does not respond, check:

* Container status
* Container logs
* Port configuration
* Environment variables
* Database connectivity
* Docker Compose configuration

---

# Container Keeps Stopping

Check the container status:

```bash
docker ps -a
```

View the logs:

```bash
docker logs your-container-name
```

Common causes include:

* Application errors
* Missing environment variables
* Database connection failures
* Incorrect commands
* Missing dependencies
* Memory issues

After fixing the issue, rebuild and restart the services:

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

---

# Database Connection Issues

Check the backend logs:

```bash
docker logs your-backend-container --tail 100
```

Test whether the database server is reachable.

For PostgreSQL:

```bash
nc -vz your-database-endpoint 5432
```

For MySQL:

```bash
nc -vz your-database-endpoint 3306
```

Also verify:

* Database endpoint
* Database port
* Database name
* Database username
* Database password
* Security group or firewall rules

---

# Nginx Is Not Working

Check the Nginx service status:

```bash
sudo systemctl status nginx
```

Test the Nginx configuration:

```bash
sudo nginx -t
```

If the configuration is valid, reload Nginx:

```bash
sudo systemctl reload nginx
```

If required, restart Nginx:

```bash
sudo systemctl restart nginx
```

Check the error logs:

```bash
sudo tail -n 100 /var/log/nginx/error.log
```

---

# Domain Is Not Resolving

Check DNS resolution:

```bash
nslookup your-domain.com
```

You can also use:

```bash
dig +short your-domain.com
```

Verify that the returned IP address matches your server's public IP address.

Also check:

* DNS A record
* CNAME record
* DNS propagation
* Domain spelling
* Server public IP

---

# Application Works Locally but Not Through the Domain

Test the backend directly:

```bash
curl http://localhost:8000
```

Test the domain:

```bash
curl http://your-domain.com
```

If the backend works locally but the domain does not, check:

* Nginx configuration
* DNS configuration
* Security group rules
* Open ports
* Nginx service status

Check open ports:

```bash
sudo ss -tulpn
```

---

# Port Is Already in Use

Check which process is using a specific port.

For example, to check port `8000`:

```bash
sudo lsof -i :8000
```

You can also check all listening ports:

```bash
sudo ss -tulpn
```

Stop or reconfigure the conflicting service if necessary.

---

# Docker Container Cannot Access Another Service

Check the Docker Compose service status:

```bash
docker compose -f docker-compose.prod.yml ps
```

Check the application logs:

```bash
docker compose -f docker-compose.prod.yml logs -f
```

Verify:

* Service names
* Environment variables
* Network configuration
* Connection URLs
* Ports

When services are running inside the same Docker Compose network, use the service name instead of `localhost`.

For example:

```text
redis://redis:6379/0
```

instead of:

```text
redis://localhost:6379/0
```

---

# Redis or Celery Is Not Working

Check the running containers:

```bash
docker ps
```

Check the Redis logs:

```bash
docker logs your-redis-container --tail 100
```

Check the Celery worker logs:

```bash
docker logs your-celery-worker-container --tail 100
```

Check the Celery Beat logs if applicable:

```bash
docker logs your-celery-beat-container --tail 100
```

Also verify:

* Redis is running
* Redis connection URL is correct
* Celery environment variables are configured
* The worker command is correct

---

# Frontend Build Fails

Check the available server memory:

```bash
free -h
```

A common issue during Node.js builds is:

```text
JavaScript heap out of memory
```

You can increase the memory available to Node.js:

```bash
export NODE_OPTIONS="--max-old-space-size=2048"
```

Then run the build again:

```bash
npm run build
```

You can also combine both commands:

```bash
export NODE_OPTIONS="--max-old-space-size=2048" && npm run build
```

Adjust the memory value based on your available server resources.

---

# Server Runs Out of Memory

Check memory usage:

```bash
free -h
```

Monitor running processes:

```bash
top
```

Or, if installed:

```bash
htop
```

Check whether swap is enabled:

```bash
swapon --show
```

If the server has limited RAM, consider configuring a swap file.

Example:

```bash
sudo fallocate -l 2G /swapfile
sudo chmod 600 /swapfile
sudo mkswap /swapfile
sudo swapon /swapfile
```

Verify:

```bash
free -h
```

---

# Changes Are Not Reflected After Deployment

If changes are not visible, verify that you rebuilt the correct application or container.

For Dockerized applications:

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

For frontend applications:

```bash
npm run build
```

Also check:

* Browser cache
* Nginx configuration
* Docker image rebuild status
* Correct deployment directory

---

# Environment Variable Changes Are Not Applied

After changing environment variables, the running containers may need to be recreated.

From the project root:

```bash
docker compose -f docker-compose.prod.yml down
```

Then rebuild and start:

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

Verify the containers:

```bash
docker ps
```

Check the logs if necessary:

```bash
docker compose -f docker-compose.prod.yml logs -f
```

---

# SSL or HTTPS Is Not Working

Check the Nginx configuration:

```bash
sudo nginx -t
```

Check whether the domain resolves correctly:

```bash
nslookup your-domain.com
```

Verify that HTTP is accessible:

```bash
curl http://your-domain.com
```

Test certificate renewal:

```bash
sudo certbot renew --dry-run
```

Also verify:

* Domain points to the correct server
* Port `80` is accessible
* Port `443` is accessible
* Nginx is running
* SSL certificate was generated successfully

---
