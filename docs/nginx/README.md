# Nginx Commands

This document contains commonly used Nginx commands for managing, monitoring, and troubleshooting Nginx on a production server.

---

## Check Nginx Status

To check whether Nginx is currently running:

```bash
sudo systemctl status nginx
````

If Nginx is running correctly, you should see a status similar to:

```text
active (running)
```

Press:

```text
q
```

to exit the status screen.

---

## Start Nginx

To start the Nginx service:

```bash
sudo systemctl start nginx
```

---

## Stop Nginx

To stop the Nginx service:

```bash
sudo systemctl stop nginx
```

---

## Restart Nginx

To restart the Nginx service:

```bash
sudo systemctl restart nginx
```

This completely restarts the Nginx service.

---

## Reload Nginx

After making changes to the Nginx configuration, reload Nginx using:

```bash
sudo systemctl reload nginx
```

Reloading applies the updated configuration without completely stopping the service.

---

## Test the Nginx Configuration

Before reloading or restarting Nginx after making configuration changes, test the configuration:

```bash
sudo nginx -t
```

If the configuration is valid, you should see output similar to:

```text
syntax is ok
test is successful
```

If there is an error, fix the configuration before reloading Nginx.

---

## Recommended Configuration Workflow

After making changes to an Nginx configuration file:

### 1. Test the configuration

```bash
sudo nginx -t
```

### 2. Reload Nginx

If the configuration test is successful:

```bash
sudo systemctl reload nginx
```

If required, restart Nginx instead:

```bash
sudo systemctl restart nginx
```

---

## Enable Nginx on Server Startup

To automatically start Nginx when the server starts:

```bash
sudo systemctl enable nginx
```

---

## Disable Nginx on Server Startup

To disable automatic startup:

```bash
sudo systemctl disable nginx
```

---

## View Nginx Error Logs

To view the Nginx error logs:

```bash
sudo tail -f /var/log/nginx/error.log
```

To view the latest `100` error log entries:

```bash
sudo tail -n 100 /var/log/nginx/error.log
```

---

## View Nginx Access Logs

To continuously monitor incoming requests:

```bash
sudo tail -f /var/log/nginx/access.log
```

To view the latest `100` access log entries:

```bash
sudo tail -n 100 /var/log/nginx/access.log
```

---

## Check the Active Nginx Configuration

To display the complete active Nginx configuration:

```bash
sudo nginx -T
```

This can be useful when troubleshooting configuration issues.

---

## Check Nginx Version

```bash
nginx -v
```

For more detailed version and build information:

```bash
nginx -V
```

---