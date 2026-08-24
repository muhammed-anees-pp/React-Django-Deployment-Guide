# Docker Commands

This document contains commonly used Docker commands for managing and troubleshooting a containerized application deployed in a production environment.

> Replace placeholder values such as `your-container-name`, `your-service-name`, and `docker-compose.prod.yml` according to your project configuration.

---

## View Running Containers

To view all currently running Docker containers:

```bash
docker ps
````

This command displays information such as:

* Container ID
* Container name
* Image
* Status
* Port mappings

Before running commands that require a container name, use `docker ps` to identify the correct container.

---

## View All Containers

To view both running and stopped containers:

```bash
docker ps -a
```

---

## View Container Logs

To view logs from a specific container:

```bash
docker logs your-container-name
```

For example, to view only the latest `50` log entries:

```bash
docker logs your-container-name --tail 50
```

To view the latest `100` log entries:

```bash
docker logs your-container-name --tail 100
```

---

## Follow Container Logs

To continuously monitor logs from a container:

```bash
docker logs -f your-container-name
```

Press:

```text
Ctrl + C
```

to stop following the logs.

---

## View All Docker Compose Logs

If your application uses Docker Compose, you can view logs from all services using:

```bash
docker compose -f docker-compose.prod.yml logs
```

To continuously follow the logs:

```bash
docker compose -f docker-compose.prod.yml logs -f
```

---

## View Logs for a Specific Service

To view logs for a specific Docker Compose service:

```bash
docker compose -f docker-compose.prod.yml logs your-service-name
```

To continuously follow the service logs:

```bash
docker compose -f docker-compose.prod.yml logs -f your-service-name
```

---

## Check Docker Compose Service Status

To check the status of services defined in the Docker Compose configuration:

```bash
docker compose -f docker-compose.prod.yml ps
```

---

## Restart a Specific Container

To restart a container:

```bash
docker restart your-container-name
```

---

## Stop a Specific Container

```bash
docker stop your-container-name
```

---

## Start a Specific Container

```bash
docker start your-container-name
```

---

## Restart Application Services

Navigate to the project directory:

```bash
cd ~/project-folder
```

Stop the running services:

```bash
docker compose -f docker-compose.prod.yml down
```

Start the services again:

```bash
docker compose -f docker-compose.prod.yml up -d
```

---

## Rebuild and Restart Application Services

After making changes to the application, dependencies, Dockerfile, or Docker Compose configuration, rebuild the images and restart the services:

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

If required, stop the existing services first:

```bash
docker compose -f docker-compose.prod.yml down
```

Then rebuild and start the services:

```bash
docker compose -f docker-compose.prod.yml up -d --build
```

---

## Verify Container Status

After starting or restarting the application, verify that the required containers are running:

```bash
docker ps
```

If a container is not running, check its logs:

```bash
docker logs your-container-name --tail 100
```

---
