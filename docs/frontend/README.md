# Frontend Build and Deployment

This document contains useful commands and troubleshooting steps for building and managing a frontend application in a production environment.

The examples are primarily suitable for frontend applications built with tools such as:

- React
- Vite
- Other Node.js-based frontend frameworks

> Replace `project-folder`, build commands, and output directories according to your frontend project configuration.

---

## Navigate to the Frontend Directory

Navigate to your frontend project directory:

```bash
cd ~/project-folder/frontend
````

---

## Install Dependencies

Install the required project dependencies:

```bash
npm install
```

For projects that use a lock file and require reproducible installations, you may use:

```bash
npm ci
```

> `npm ci` is typically used when a valid `package-lock.json` file is available.

---

## Build the Frontend

Create a production build:

```bash
npm run build
```

The build command will generate the production-ready frontend files.

For many Vite-based React applications, the output directory is:

```text
dist/
```

The final structure may look similar to:

```text
frontend/
├── src/
├── public/
├── package.json
└── dist/
```

> Check your project's build configuration to confirm the correct output directory.

---

## Check the Build Output

After running the build command, verify that the output directory was created:

```bash
ls
```

For a Vite project:

```bash
ls dist
```

You should see the generated production files.

---

# Node.js Memory Issues During Build

Large frontend applications may encounter memory-related errors while running the production build.

For example, you may see errors related to:

```text
JavaScript heap out of memory
```

You can increase the maximum memory available to Node.js:

```bash
export NODE_OPTIONS="--max-old-space-size=2048"
```

Then run the build again:

```bash
npm run build
```

The value represents the maximum memory allocation in megabytes.

For example:

```bash
export NODE_OPTIONS="--max-old-space-size=4096"
```

Then:

```bash
npm run build
```

> Adjust the memory value based on your server's available RAM.

You can check available memory using:

```bash
free -h
```

---

## Build the Frontend with Increased Memory

You can run both commands together:

```bash
export NODE_OPTIONS="--max-old-space-size=2048" && npm run build
```

---

# Verify the Frontend

After building and configuring the web server, verify that the application is accessible.

For example:

```text
http://your-domain.com
```

Or, before domain configuration:

```text
http://YOUR_SERVER_IP
```

After SSL configuration:

```text
https://your-domain.com
```

---

## Check the Frontend Through Nginx

If Nginx is serving the frontend, verify the application response:

```bash
curl http://your-domain.com
```

After SSL is configured:

```bash
curl https://your-domain.com
```

You can also check the Nginx configuration:

```bash
sudo nginx -t
```

---

# Rebuild the Frontend After Changes

After making changes to the frontend application:

Navigate to the frontend directory:

```bash
cd ~/project-folder/frontend
```

Install any new dependencies if required:

```bash
npm install
```

Build the application:

```bash
npm run build
```

If the application is running inside Docker, rebuild the appropriate Docker services according to your project configuration.

For example:

```bash
cd ~/project-folder
```

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

---

# Common Frontend Build Checks

If the frontend does not build or load correctly, check the following:

* Node.js is installed correctly.
* npm dependencies are installed.
* The build command completes successfully.
* The correct build output directory is generated.
* Nginx points to the correct frontend build directory.
* Environment variables are configured correctly.
* API URLs are configured for the production environment.
* The domain and SSL configuration are correct.

---

# General Frontend Deployment Workflow

A typical frontend deployment process may look like:

```text
Frontend Source Code
        │
        ▼
Install Dependencies
        │
        ▼
Configure Environment Variables
        │
        ▼
Build Frontend
        │
        ▼
Generate Production Files
        │
        ▼
Configure Nginx
        │
        ▼
Verify Application
        │
        ▼
Configure Domain and SSL
```

---
