# Server Management

This document contains commonly used commands for monitoring and managing a production server.

---

## Check Memory Usage

To check the current memory and swap usage:

```bash
free -h
````

This displays information about:

* Total memory
* Used memory
* Available memory
* Swap usage

---

## Check Disk Usage

To view the available disk space:

```bash
df -h
```

This displays the disk usage for the mounted file systems.

---

## Check Directory Size

To check the size of a specific directory:

```bash
du -sh directory-name
```

For example:

```bash
du -sh /home/ubuntu/project-folder
```

To check the size of directories inside the current directory:

```bash
du -sh *
```

---

## Check Open Ports

To view services currently listening on network ports:

```bash
sudo ss -tulpn
```

This can help identify which processes are using specific ports.

For example, you may check whether services such as:

* Nginx
* Docker
* Django
* Other application services

are listening on their expected ports.

---

## Check Running Processes

To view all running processes:

```bash
ps aux
```

To monitor running processes interactively:

```bash
top
```

Press:

```text
q
```

to exit.

---

## Use htop

If `htop` is installed, you can use:

```bash
htop
```

If it is not installed:

```bash
sudo apt install htop -y
```

Then run:

```bash
htop
```

This provides an interactive view of:

* CPU usage
* Memory usage
* Running processes
* System load

---

# Swap File Configuration

If your server has limited RAM, you can configure a swap file to provide additional virtual memory.

> Swap is slower than physical RAM, but it can help prevent memory-related issues on servers with limited resources.

---

## Check Existing Memory and Swap

Before creating a swap file, check the current memory usage:

```bash
free -h
```

You can also check whether swap is already enabled:

```bash
swapon --show
```

---

## Create a Swap File

The following example creates a `2 GB` swap file:

```bash
sudo fallocate -l 2G /swapfile
```

> Adjust the swap size according to your server resources and application requirements.

---

## Set Swap File Permissions

Set the correct permissions:

```bash
sudo chmod 600 /swapfile
```

---

## Format the File as Swap

```bash
sudo mkswap /swapfile
```

---

## Enable the Swap File

```bash
sudo swapon /swapfile
```

---

## Verify Swap

Check whether the swap file is active:

```bash
swapon --show
```

You can also verify it using:

```bash
free -h
```

The output should now show available swap memory.

---

## Make the Swap File Persistent

By default, the swap file may not remain active after a server restart.

To enable it automatically, open:

```bash
sudo nano /etc/fstab
```

Add the following line:

```text
/swapfile swap swap defaults 0 0
```

Save the file and exit.

Verify the configuration carefully before restarting the server.

> Be careful when modifying `/etc/fstab`. Incorrect entries can cause system boot issues.

---

## Check System Uptime

To check how long the server has been running:

```bash
uptime
```

This also displays the system load average.

---

## Check CPU Information

To view CPU information:

```bash
lscpu
```

---

## Check Operating System Information

```bash
cat /etc/os-release
```

---

## Check Kernel Information

```bash
uname -a
```

---