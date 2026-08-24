This document contains commonly used Docker and Docker Compose commands for managing, monitoring, and troubleshooting a containerized production application.

> Replace container names, service names, and Compose file names according to your project configuration.

---

## View Running Containers

To view all currently running containers:

```bash
docker ps

To view all containers, including stopped containers:

docker ps -a
View Container Logs

To view logs from a specific container:

docker logs your-container-name

To view the latest logs:

docker logs your-container-name --tail 100

For example:

docker logs your-container-name --tail 50
Follow Container Logs

To continuously follow container logs:

docker logs -f your-container-name

Press:

Ctrl + C

to stop following the logs.

View All Docker Compose Logs

If your application uses Docker Compose:

docker compose -f docker-compose.prod.yml logs -f

To view logs without continuously following them:

docker compose -f docker-compose.prod.yml logs
View Logs for a Specific Service
docker compose -f docker-compose.prod.yml logs service-name

For example:

docker compose -f docker-compose.prod.yml logs backend-web

To follow the logs:

docker compose -f docker-compose.prod.yml logs -f backend-web
Restart a Container

To restart a specific container:

docker restart your-container-name
Stop a Container
docker stop your-container-name
Start a Container
docker start your-container-name
Restart All Application Services

Navigate to the project directory:

cd ~/project-folder

Stop the services:

docker compose -f docker-compose.prod.yml down

Start the services again:

docker compose -f docker-compose.prod.yml up -d
Rebuild and Restart Services

After making changes to the Dockerfile, dependencies, or application configuration:

docker compose -f docker-compose.prod.yml up -d --build

If required, first stop the existing services:

docker compose -f docker-compose.prod.yml down

Then rebuild and start:

docker compose -f docker-compose.prod.yml up -d --build
Check Docker Compose Service Status
docker compose -f docker-compose.prod.yml ps
Check Docker Disk Usage

To check Docker disk usage:

docker system df
Important Notes

When troubleshooting a containerized application, the following commands are commonly useful:

docker ps
docker logs your-container-name --tail 100
docker compose -f docker-compose.prod.yml logs -f

Always check the actual container names using:

docker ps

before running commands that require a container name.