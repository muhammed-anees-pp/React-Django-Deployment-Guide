# DNS and Domain Verification

This document contains useful commands for checking and verifying DNS and domain configuration during deployment.

> Replace `your-domain.com` and `YOUR_SERVER_IP` with the values used in your own deployment.

---

## Check DNS Resolution

To check whether a domain resolves correctly:

```bash
nslookup your-domain.com
````

The output should show the IP address associated with the domain.

Example:

```text
Name:    your-domain.com
Address: YOUR_SERVER_IP
```

---

## Check the WWW Subdomain

If your application also uses a `www` subdomain:

```bash
nslookup www.your-domain.com
```

Verify that it resolves according to your DNS configuration.

---

## Check DNS Using `dig`

If the `dig` command is available, you can use:

```bash
dig your-domain.com
```

To display only the resolved IP address:

```bash
dig +short your-domain.com
```

For the `www` subdomain:

```bash
dig +short www.your-domain.com
```

---

## Check Domain Resolution Using Ping

You can also check whether the domain resolves to an IP address:

```bash
ping your-domain.com
```

Example:

```text
PING your-domain.com (YOUR_SERVER_IP)
```

> A failed ping does not necessarily mean that the domain configuration is incorrect. Some servers block ICMP requests.

---

## Common DNS Records

A typical deployment may require DNS records such as the following.

### A Record

An A record points a domain to an IPv4 address.

```text
Type: A
Name: @
Value: YOUR_SERVER_IP
```

Example structure:

```text
your-domain.com
        ↓
YOUR_SERVER_IP
```

---

### WWW Subdomain

Depending on your DNS configuration, the `www` subdomain can use a CNAME record:

```text
Type: CNAME
Name: www
Value: your-domain.com
```

This allows:

```text
www.your-domain.com
```

to point to:

```text
your-domain.com
```

---

## Verify the Domain Before SSL Configuration

Before configuring SSL, make sure the following are working correctly:

* The domain resolves to the correct server IP address.
* The DNS records are configured correctly.
* DNS propagation is complete.
* The application is accessible through HTTP.
* Nginx is running correctly.
* Port `80` is accessible.

You can verify DNS resolution using:

```bash
nslookup your-domain.com
```

You can also test whether the application is accessible:

```bash
curl http://your-domain.com
```

---

## Check DNS From the Server

You can also verify domain resolution directly from the production server:

```bash
nslookup your-domain.com
```

Or:

```bash
dig +short your-domain.com
```

The returned IP address should match the server's public IP address when using a direct A record configuration.

---

## Check the Server Public IP

From the EC2 instance, you can check the public IP address using:

```bash
curl ifconfig.me
```

You can then compare this IP address with the DNS result.

---

## DNS Propagation

After updating DNS records, the changes may not be immediately available everywhere.

The propagation time depends on factors such as:

* DNS provider
* Previous TTL configuration
* DNS caching
* Network location

You can continue checking the DNS configuration using:

```bash
nslookup your-domain.com
```

or:

```bash
dig +short your-domain.com
```

---

## Common DNS Troubleshooting

If the domain is not working correctly, check the following:

### Verify the A Record

Ensure that the domain points to the correct server IP address.

```text
your-domain.com
        ↓
YOUR_SERVER_IP
```

---

### Verify the WWW Record

Check whether the `www` subdomain is configured correctly.

For example:

```text
www.your-domain.com
        ↓
your-domain.com
```

or according to your preferred DNS configuration.

---

### Check DNS Resolution

```bash
nslookup your-domain.com
```

---

### Check the Web Server

Verify that Nginx is running:

```bash
sudo systemctl status nginx
```

---

### Check Nginx Configuration

```bash
sudo nginx -t
```

---

### Check HTTP Access

```bash
curl http://your-domain.com
```

---

### Check HTTPS Access

After SSL has been configured:

```bash
curl https://your-domain.com
```

---

## General DNS Verification Workflow

When configuring a domain for a deployed application:

```text
Configure DNS Records
        │
        ▼
Verify DNS Resolution
        │
        ▼
Confirm Domain Points to Server
        │
        ▼
Verify Nginx Configuration
        │
        ▼
Test HTTP Access
        │
        ▼
Configure SSL
        │
        ▼
Test HTTPS Access
```

---