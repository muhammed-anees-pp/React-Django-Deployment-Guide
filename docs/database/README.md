# Database Connectivity

This document contains useful commands for checking and troubleshooting the connection between an application server and a database server.

The examples below are applicable to self-hosted databases or managed database services such as AWS RDS.

> Replace placeholder values such as `your-database-endpoint`, `your-database-name`, and `your-database-user` with your actual database configuration.

---

## Check Database Network Connectivity

Before troubleshooting application-level database errors, verify that the server can reach the database.

For a PostgreSQL database:

```bash
nc -vz your-database-endpoint 5432
````

For example:

```bash
nc -vz your-rds-endpoint.amazonaws.com 5432
```

If the connection is successful, the application server can reach the PostgreSQL database port.

---

## PostgreSQL Default Port

The default PostgreSQL port is:

```text
5432
```

If your database uses a different port, replace `5432` with the appropriate value.

---

## MySQL Default Port

The default MySQL port is:

```text
3306
```

To test connectivity:

```bash
nc -vz your-database-endpoint 3306
```

---

## Check Database Environment Variables

Database configuration is commonly stored in environment variables.

For example, a PostgreSQL configuration may look like:

```env
DB_NAME=your_database_name
DB_USER=your_database_user
DB_PASSWORD=your_database_password
DB_HOST=your_database_endpoint
DB_PORT=5432
```

Make sure that:

* The database name is correct.
* The database user is correct.
* The database password is correct.
* The database host or endpoint is correct.
* The database port is correct.

> Never commit database passwords or other sensitive credentials to a public GitHub repository.

---

## Test PostgreSQL Connection Using psql

If the PostgreSQL client is installed on the server, you can test the connection directly.

Install the PostgreSQL client:

```bash
sudo apt install postgresql-client -y
```

Connect to the database:

```bash
psql \
  -h your-database-endpoint \
  -p 5432 \
  -U your-database-user \
  -d your-database-name
```

You will be prompted to enter the database password.

If the connection is successful, you should enter the PostgreSQL shell.

To exit:

```text
\q
```

---

## Check Database Connectivity From a Docker Container

If your backend application is running inside Docker, the connection should also be verified from inside the container.

First, check the running containers:

```bash
docker ps
```

Open a shell inside the backend container:

```bash
docker exec -it your-backend-container sh
```

If the container supports Bash:

```bash
docker exec -it your-backend-container bash
```

You can then test connectivity from inside the container.

---

## Check Backend Logs for Database Errors

If the application cannot connect to the database, check the backend container logs:

```bash
docker logs your-backend-container --tail 100
```

To continuously follow the logs:

```bash
docker logs -f your-backend-container
```

Look for errors related to:

* Database connection failures
* Incorrect credentials
* Connection timeouts
* Database host errors
* Database port errors
* Migration errors

---

## Common Database Connection Checks

If the application cannot connect to the database, verify the following:

### Database Endpoint

Ensure that the configured database host is correct:

```env
DB_HOST=your-database-endpoint
```

---

### Database Port

Ensure that the configured port matches the database configuration:

```env
DB_PORT=5432
```

---

### Database Credentials

Verify:

```env
DB_NAME=your_database_name
DB_USER=your_database_user
DB_PASSWORD=your_database_password
```

---

### Security Groups and Firewall Rules

If you are using a cloud-hosted database, verify that:

* The database security group allows inbound connections.
* The application server is allowed to access the database.
* The correct database port is open.
* Firewall or network rules are not blocking the connection.

For PostgreSQL:

```text
Port: 5432
```

For MySQL:

```text
Port: 3306
```

---

## Run Database Migrations

For Django applications, database migrations may need to be applied after deployment.

If Django is running inside a Docker container:

```bash
docker exec -it your-backend-container python manage.py migrate
```

Alternatively, if your Docker Compose service supports it:

```bash
docker compose -f docker-compose.prod.yml exec backend-web python manage.py migrate
```

Replace `backend-web` with your actual service name.

---

## Check Migration Status

For Django applications:

```bash
docker exec -it your-backend-container python manage.py showmigrations
```

This can help verify whether all required migrations have been applied.

---

## General Database Troubleshooting Workflow

When a database connection issue occurs, check the following:

```text
Application Cannot Connect
        │
        ▼
Check Backend Logs
        │
        ▼
Check Environment Variables
        │
        ▼
Check Database Endpoint
        │
        ▼
Check Network Connectivity
        │
        ▼
Check Database Port
        │
        ▼
Check Security Groups / Firewall Rules
        │
        ▼
Check Database Credentials
        │
        ▼
Check Database Migration Status
```

Start by checking the backend logs:

```bash
docker logs your-backend-container --tail 100
```

Then test network connectivity:

```bash
nc -vz your-database-endpoint 5432
```

If the network connection is successful but the application still cannot connect, review the database credentials, environment variables, and application configuration.

---