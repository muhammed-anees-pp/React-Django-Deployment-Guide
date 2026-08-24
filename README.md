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
