# Deploying a React + Django Application with Redis, Celery, AWS EC2, Nginx, and SSL

# Phase 1: Create an EC2 Instance

## Step 1.1: Launch an Instance

Open the AWS Console and navigate to:

```text
EC2
→ Launch Instance
```

Use the following configuration:

### Instance Name

```text
project-name
```

### Operating System

```text
Ubuntu Server 24.04 LTS
```

### Instance Type

```text
t3.medium
```

Recommended minimum resources:

```text
2 vCPU
4 GB RAM
```

---

## Step 1.2: Create a Key Pair

Create a new key pair with the following configuration:

```text
Name:
project-key

Type:
RSA

Format:
.pem
```

Download and store the generated key securely:

```text
project-key.pem
```

> **Important:** Do not upload your `.pem` file to GitHub.

---

## Step 1.3: Configure the Security Group

Create a security group:

```text
project-prod-sg
```

Add the following inbound rules:

| Type  | Port | Source          |
| ----- | ---: | --------------- |
| SSH   |   22 | Your IP address |
| HTTP  |   80 | `0.0.0.0/0`     |
| HTTPS |  443 | `0.0.0.0/0`     |

For initial testing, SSH can temporarily be configured with:

```text
0.0.0.0/0
```

However, it is recommended to restrict SSH access to your own public IP address.

---

## Step 1.4: Configure Storage

Use:

```text
30 GB
gp3
```

After configuring the instance, launch it.

---
# Phase 2: Connect to EC2

AWS provides multiple ways to connect to an EC2 instance. For this setup, you can use either:

* **EC2 Instance Connect through the AWS Console**
* **SSH from your local machine using a key pair**

---

## Option 1: Connect Using EC2 Instance Connect

AWS provides an option to connect directly to your EC2 instance from the AWS Management Console.

Navigate to:

```text
AWS Console
→ EC2
→ Instances
→ Select Your Instance
→ Connect
```

Select:

```text
EC2 Instance Connect
```

Then click:

```text
Connect
```

A browser-based terminal will open, allowing you to access your EC2 instance directly.

If the connection is successful, you should see something similar to:

```bash
ubuntu@ip-xxx-xxx-xxx-xxx:~$
```

> This method is useful for quickly accessing your server without configuring SSH on your local machine.

---

## Option 2: Connect Using SSH from Your Local Machine

You can also connect to the EC2 instance from your local machine using the `.pem` key pair created while launching the instance.

### Step 1: Get the Public IPv4 Address

Navigate to:

```text
EC2
→ Instances
→ Select Instance
→ Public IPv4 Address
```

Example:

```text
13.xxx.xxx.xxx
```

---

### Step 2: Navigate to the Key Pair Location

Open your terminal and navigate to the directory containing your `.pem` key:

```bash
cd /path/to/key
```

---

### Step 3: Set the Correct Permissions

Set the appropriate permissions for the key file:

```bash
chmod 400 project-key.pem
```

---

### Step 4: Connect to the Instance

Use the following command:

```bash
ssh -i project-key.pem ubuntu@YOUR_PUBLIC_IP
```

Replace:

```text
project-key.pem
```

with your actual key pair filename, and replace:

```text
YOUR_PUBLIC_IP
```

with the public IPv4 address of your EC2 instance.

Example:

```bash
ssh -i project-key.pem ubuntu@13.xxx.xxx.xxx
```

If the connection is successful, you should see something similar to:

```bash
ubuntu@ip-xxx-xxx-xxx-xxx:~$
```

---
# Phase 3: Update the Server

Update the package lists and upgrade installed packages:

```bash
sudo apt update
sudo apt upgrade -y
```

Install commonly required utilities:

```bash
sudo apt install -y \
  git \
  curl \
  wget \
  unzip \
  vim
```

---
# Phase 4: Install Docker

Install Docker using the official installation script:

```bash
curl -fsSL https://get.docker.com | sudo sh
```

Add the `ubuntu` user to the Docker group:

```bash
sudo usermod -aG docker ubuntu
```

Apply the new group membership:

```bash
newgrp docker
```

Verify the installation:

```bash
docker --version
```

---
# Phase 5: Verify Docker Compose

Check whether Docker Compose is available:

```bash
docker compose version
```

If Docker Compose is not installed:

```bash
sudo apt install -y docker-compose-plugin
```

Verify again:

```bash
docker compose version
```

---
# Phase 6: Clone the Repository

Navigate to your home directory:

```bash
cd ~
```

Clone your project repository:

```bash
git clone YOUR_GITHUB_REPOSITORY_URL
```

Navigate into the project:

```bash
cd project-folder
```

Verify the project structure:

```bash
ls
```

Expected structure:

```text
backend
frontend
docker-compose.yml
```

---
# Phase 7: Create Production Environment Variables

> **Important:** Never commit production secrets or environment files to GitHub.

Navigate to the backend directory:

```bash
cd backend
```

Create the production environment file:

```bash
nano .env.production
```

Example configuration:

```env
DEBUG=False
SECRET_KEY=YOUR_SECRET_KEY
ALLOWED_HOSTS=YOUR_SERVER_IP

DB_NAME=database_name
DB_USER=postgres
DB_PASSWORD=YOUR_DATABASE_PASSWORD
DB_HOST=project-name.cxy4g4e2mswh.ap-south-1.rds.amazonaws.com (if you used AWS RDS or any other RDS)
DB_PORT=5432

REDIS_URL=redis://redis:6379/0
```

Save and exit the editor.

---
# Phase 8: Create Production Docker Compose Configuration

If your project already has a Docker Compose configuration for the backend, for example:

```text
docker-compose-backend.yml
```

you do not necessarily need to create a completely new Docker Compose file from scratch.

Instead, you can create a production-specific configuration by copying the existing Compose file and modifying it for the production environment.

## Step 1: Navigate to the Project Root Directory

```bash
cd ~/project-folder
```

## Step 2: Check the Existing Docker Compose File

Verify that the existing backend Compose file is available:

```bash
ls
```

You should see something similar to:

```text
backend/
frontend/
docker-compose-backend.yml
```

## Step 3: Create the Production Compose File

Copy the existing backend Docker Compose configuration:

```bash
cp docker-compose-backend.yml docker-compose.prod.yml
```

This creates a new file:

```text
docker-compose.prod.yml
```

The original development or backend configuration remains unchanged.

## Step 4: Modify the Production Configuration

Open the production Compose file:

```bash
nano docker-compose.prod.yml
```

Update the configuration based on your production requirements.

Example:

```yaml
services:
  backend-web:
    build:
      context: .
      dockerfile: backend/Dockerfile

    env_file:
      - backend/.env.production

    ports:
      - "8000:8000"

    depends_on:
      - redis

  backend-worker:
    build:
      context: .
      dockerfile: backend/Dockerfile

    command: celery -A config worker -l info

    env_file:
      - backend/.env.production

    depends_on:
      - redis

  redis:
    image: redis:7-alpine
```

> Adjust the Celery application path (`config`) according to your Django project structure.

> **Note:** The production configuration may differ from your existing development configuration. For example, you may need to use production environment variables, remove development-only settings, configure restart policies, or adjust exposed ports.

After completing the production configuration, the project structure may look like:

```text
project-folder/
├── backend/
│   ├── Dockerfile
│   └── .env.production
│
├── frontend/
│
├── docker-compose-backend.yml
└── docker-compose.prod.yml
```

This approach allows you to keep your existing Docker Compose configuration while maintaining a separate configuration specifically for production deployment.

---
# Phase 9: Start the Backend Services

Build and start the containers:

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

Check the running containers:

```bash
docker ps
```

You should see containers similar to:

```text
backend-web
backend-worker
redis
```

To inspect logs:

```bash
docker compose -f docker-compose.prod.yml logs -f
```

---
# Phase 10: Verify the API

Test the backend from the EC2 instance:

```bash
curl http://localhost:8000
```

You can also test it using the EC2 public IP:

```bash
curl http://YOUR_EC2_PUBLIC_IP:8000
```

If the API responds successfully, the backend deployment is working.

```text
Backend deployment complete
```

> Stop at this point and test the backend thoroughly before continuing with the frontend and Nginx configuration.

---
# Phase 11: Build the Frontend

Navigate to the frontend directory:

```bash
cd ~/project-folder/frontend
```

Install dependencies:

```bash
npm install
```

Build the production version:

```bash
npm run build
```

The production build should be generated in:

```text
frontend/dist
```

> Depending on your frontend framework and build configuration, the output directory may differ.

---
# Phase 12: Install Nginx

Install Nginx:

```bash
sudo apt install nginx -y
```

Start the service:

```bash
sudo systemctl start nginx
```

Enable Nginx to start automatically:

```bash
sudo systemctl enable nginx
```

Check the status:

```bash
sudo systemctl status nginx
```

---


# Phase 13: Configure Nginx

Create a new Nginx configuration:

```bash
sudo nano /etc/nginx/sites-available/project-name
```

Use the following basic configuration:

```nginx
server {
    listen 80;

    server_name _;

    root /home/ubuntu/project-folder/frontend/dist;

    index index.html;

    location / {
        try_files $uri $uri/ /index.html;
    }

    location /api/ {
        proxy_pass http://127.0.0.1:8000;
    }

    location /ws/ {
        proxy_http_version 1.1;

        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection "upgrade";

        proxy_pass http://127.0.0.1:8000;
    }
}
```

Enable the configuration:

```bash
sudo ln -s \
  /etc/nginx/sites-available/project-name \
  /etc/nginx/sites-enabled/
```

Remove the default Nginx configuration:

```bash
sudo rm /etc/nginx/sites-enabled/default
```

Test the Nginx configuration:

```bash
sudo nginx -t
```

If the configuration test is successful, restart Nginx:

```bash
sudo systemctl restart nginx
```

---