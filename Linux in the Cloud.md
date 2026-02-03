Linux in the Cloud
==================

The Operating System
--------------------


You’ve built your application with [HTML](https://medium.com/html-in-the-cloud-dc23a04bfe51), [CSS](https://medium.com/css-8c98ee3ec762), [JavaScript](https://medium.com/javascript-in-the-cloud-6dd068fecb77), and [Python](https://medium.com/python-in-the-cloud-1462f61e3293). You’ve learned [Git](https://medium.com/git-in-the-cloud-d8fed43331fc) to track and deploy your code.

But…**Linux** runs nearly everything.

Consider this:

*   **Your AWS EC2 instance?** → Running Linux
*   **The Docker containers you deploy?** → Built on Linux
*   **Kubernetes clusters?** → Linux nodes
*   **Cloud databases like RDS?** → Running on Linux servers
*   **Even serverless functions?** → Execute in Linux environments

Over 90% of the world’s cloud infrastructure runs on Linux.

If you want to work in cloud computing, you don’t just need to know Linux. you need to be _comfortable_ in it.

Topics
------

1.  Linux fundamentals and the command line
2.  File systems, permissions, and security
3.  Process and service management
4.  Package management across distributions
5.  Networking and firewall configuration
6.  Docker containers and orchestration
7.  Shell scripting for automation
8.  Deploying a complete cloud application

1 | Why Linux Dominates the Cloud
---------------------------------

### The Cloud Runs on Linux

Linux isn’t just popular in the cloud, it’s the standard:

```
┌─────────────────────────────────────────────────────────────────────┐
│                    CLOUD INFRASTRUCTURE                             │
├─────────────────────────────────────────────────────────────────────┤
│                                                                     │
│   ┌─────────────────┐   ┌─────────────────┐   ┌─────────────────┐   │
│   │    AWS          │   │    Azure        │   │    GCP          │   │
│   │                 │   │                 │   │                 │   │
│   │  Amazon Linux   │   │  Ubuntu         │   │  Container-     │   │
│   │  Ubuntu         │   │  CentOS         │   │  Optimized OS   │   │
│   │  RHEL           │   │  RHEL           │   │  Ubuntu         │   │
│   └────────┬────────┘   └────────┬────────┘   └────────┬────────┘   │
│            │                     │                     │            │
│            └─────────────────────┼─────────────────────┘            │
│                                  ▼                                  │
│                        ┌─────────────────┐                          │
│                        │     LINUX       │                          │
│                        │                 │                          │
│                        │  The Foundation │                          │
│                        └─────────────────┘                          │
│                                                                     │
└─────────────────────────────────────────────────────────────────────┘
```

### Why Cloud Providers Choose Linux

Cloud providers overwhelmingly choose Linux for several compelling reasons.

**It’s free and open source:** With no licensing costs at scale, organizations save enormous amounts of money as their infrastructure grows.

**It’s highly customizable:** Linux can be stripped down to bare essentials or specialized for specific workloads, giving engineers complete control over their environment.

**It’s stable and reliable:** Linux servers routinely run for years without requiring a reboot, making it ideal for production systems that need maximum uptime.

**Security patches come quickly:** The transparent, open-source codebase means vulnerabilities are identified and fixed rapidly by a global community of developers.

**Performance is exceptional:** Linux uses system resources efficiently, squeezing maximum value from cloud infrastructure.

**Everything is scriptable:** The command line interface makes automation straightforward, enabling infrastructure as code practices.

**Containers are native:** Docker and Kubernetes were built specifically for Linux, making it the natural choice for modern containerized applications.

### Linux Distributions in the Cloud

Not all Linux is the same. Different distributions (distros) are optimized for different purposes:

```
┌──────────────────────────────────────────────────────────────────┐
│                     LINUX DISTRIBUTIONS                          │
├──────────────────────────────────────────────────────────────────┤
│                                                                  │
│  Debian-Based                    Red Hat-Based                   │
│  ──────────────                  ──────────────                  │
│  ┌─────────────┐                ┌─────────────┐                  │
│  │   Ubuntu    │                │    RHEL     │                  │
│  │  Popular,   │                │  Enterprise │                  │
│  │  Beginner   │                │  Support    │                  │
│  │  Friendly   │                │             │                  │
│  └─────────────┘                └─────────────┘                  │
│        │                              │                          │
│        ▼                              ▼                          │
│  ┌─────────────┐                ┌─────────────┐                  │
│  │   Debian    │                │   CentOS    │                  │
│  │   Stable,   │                │  Free RHEL  │                  │
│  │   Minimal   │                │   Clone     │                  │
│  └─────────────┘                └─────────────┘                  │
│                                       │                          │
│                                       ▼                          │
│                                 ┌─────────────┐                  │
│  Cloud-Specific                 │  Fedora     │                  │
│  ──────────────                 │  Cutting    │                  │
│  ┌─────────────┐                │   Edge      │                  │
│  │   Amazon    │                └─────────────┘                  │
│  │   Linux     │                                                 │
│  └─────────────┘                                                 │
│                                                                  │
└──────────────────────────────────────────────────────────────────┘
```

Which should you learn?

*   **Ubuntu:** Best for beginners, excellent documentation
*   **Amazon Linux:** Optimized for AWS (similar to RHEL)
*   **CentOS Linux:** Enterprise-style, free RHEL alternative

2 | Getting Started with Linux
------------------------------

### Setting Up Your Linux Environment

Option A: **Cloud Instance** (Recommended)

```
# Launch an AWS EC2 instance with Ubuntu 22.04 LTS
# Connect via SSH
ssh -i your-key.pem ubuntu@your-instance-ip
```

Option B: **Windows Subsystem for Linux** (WSL2)

```
# Install WSL2 on Windows
wsl --install -d Ubuntu-22.04
# After restart, open Ubuntu from Start menu
```

Option C: **Local Virtual Machine**

*   Download [VirtualBox](https://www.virtualbox.org/)
*   Download [Ubuntu 22.04 LTS ISO](https://ubuntu.com/download/desktop)
*   Create VM: 2GB RAM, 25GB storage minimum

### The Linux Terminal

When you connect to a Linux server, you’ll see something like:

```
ubuntu@ip-172-31-25-42:~$
```

Let’s break this down:

```
ubuntu@ip-172-31-25-42:~$
  │           │         │ │
  │           │         │ └─ Prompt (waiting for command)
  │           │         └─── Current directory (~ = home)
  │           └───────────── Hostname (server name)
  └───────────────────────── Username
```

### The Linux Filesystem

Linux organizes files differently than Windows:

```
/                          ← Root (top of filesystem)
├── home/                  ← User home directories
│   ├── ubuntu/            ← Your files (~)
│   └── otheruser/
├── etc/                   ← System configuration files
├── var/                   ← Variable data (logs, databases)
│   ├── log/               ← System and application logs
│   └── www/               ← Web server files (Apache/Nginx)
├── usr/                   ← User programs and utilities
│   ├── bin/               ← Essential user commands
│   └── lib/               ← Libraries
├── opt/                   ← Optional/third-party software
├── tmp/                   ← Temporary files (cleared on reboot)
├── root/                  ← Root user's home directory
└── proc/                  ← Virtual filesystem (system info)
```

**Key differences from Windows:**

*   / instead of C:\
*   Forward slashes / instead of backslashes \
*   Case-sensitive: File.txt ≠ file.txt
*   No drive letters (everything under /)

3 | Essential Linux Commands
----------------------------

### Navigation and Files

Moving around:

```
# Where am I?
pwd
# Output: /home/ubuntu
# What's in this directory?
ls              # Simple list
ls -l           # Detailed list
ls -la          # Include hidden files
ls -lh          # Human-readable sizes
# Change directory
cd /var/log     # Go to absolute path
cd ..           # Go up one level
cd ~            # Go to home directory
cd -            # Go to previous directory
```

**Example ls -la output:**

```
$ ls -la
total 36
drwxr-xr-x 5 ubuntu ubuntu 4096 Jan 15 10:30 .
drwxr-xr-x 3 root   root   4096 Jan 10 08:00 ..
-rw-r--r-- 1 ubuntu ubuntu  220 Jan 10 08:00 .bashrc
drwxr-xr-x 2 ubuntu ubuntu 4096 Jan 15 10:30 projects
-rwxr-xr-x 1 ubuntu ubuntu  512 Jan 15 09:00 script.sh
# Breaking down: -rwxr-xr-x
# │ │  │  │
# │ │  │  └── Others: r-x (read, execute)
# │ │  └───── Group: r-x (read, execute)
# │ └──────── Owner: rwx (read, write, execute)
# └────────── File type: - (file) or d (directory)
```

### File Operations

```
# Create files and directories
touch newfile.txt              # Create empty file
mkdir new-directory            # Create directory
mkdir -p parent/child/grandchild  # Create nested directories
# Copy files
cp file.txt backup.txt         # Copy file
cp -r folder/ backup/          # Copy directory recursively
# Move/rename files
mv old.txt new.txt             # Rename file
mv file.txt /tmp/              # Move file to /tmp
# Delete files (⚠️ no recycle bin!)
rm file.txt                    # Delete file
rm -r directory/               # Delete directory
rm -rf directory/              # Force delete (dangerous!)
```

### Viewing File Contents

```
# View entire file
cat config.txt                 # Display all at once
# View large files (with navigation)
less /var/log/syslog           # Page through file
# Press: q=quit, /=search, n=next match, Space=next page
# View portions of files
head -n 20 file.txt            # First 20 lines
tail -n 20 file.txt            # Last 20 lines
tail -f /var/log/syslog        # Follow file in real-time (Ctrl+C to stop)
# Search within files
grep "error" /var/log/syslog         # Find lines containing "error"
grep -i "warning" file.txt           # Case-insensitive search
grep -r "TODO" /home/ubuntu/project/ # Recursive search
grep -n "pattern" file.txt           # Show line numbers
```

### Text Editing

Nano (beginner-friendly):

```
nano myfile.txt
# Inside nano:
# Ctrl+O → Save (Write Out)
# Ctrl+X → Exit
# Ctrl+K → Cut line
# Ctrl+U → Paste line
# Ctrl+W → Search
```

Vim (powerful, but learning curve):

```
vim myfile.txt
# Vim has modes:
# i          → Enter INSERT mode (type normally)
# Esc        → Return to NORMAL mode
# :w         → Save
# :q         → Quit
# :wq        → Save and quit
# :q!        → Quit without saving
# dd         → Delete line
# /pattern   → Search
```

4 | File Permissions and Security
---------------------------------

### Understanding Permissions

Linux permissions protect your files and system:

```
-rwxr-xr-x 1 ubuntu www-data 4096 Jan 15 10:30 script.sh
│└┬┘└┬┘└┬┘   └──┬──┘└───┬──┘
│ │  │  │       │       │
│ │  │  │       │       └── Group owner
│ │  │  │       └────────── File owner
│ │  │  └────────────────── Others permissions (r-x)
│ │  └───────────────────── Group permissions (r-x)
│ └──────────────────────── Owner permissions (rwx)
└────────────────────────── File type (- = file, d = directory)
```

**Permission meanings:**

Each permission symbol has a specific meaning, and that meaning changes slightly depending on whether you’re dealing with files or directories.

**r** (read): _For files, this allows viewing the contents. For directories, it allows listing what’s inside._

**w** (write): _For files, this allows modifying the contents. For directories, it allows creating or deleting files within._

**x** (execute): _For files, this allows running the file as a program. For directories, it allows entering the directory with cd._

**-** (none): _No permission granted for this action._

When you see a permission string like **rwxr-xr-x**, read it in groups of three: owner permissions, group permissions, and others permissions. The owner has full access **(rwx),** while the group and others can read and execute but not write **(r-x).**

### Changing Permissions

Symbolic method (easier to read):

```
chmod u+x script.sh    # Add execute for owner (user)
chmod g+w file.txt     # Add write for group
chmod o-r file.txt     # Remove read for others
chmod a+r file.txt     # Add read for all
chmod u+x,g+r script.sh # Multiple changes
```

Numeric method (faster):

```
r = 4
w = 2
x = 1
Common patterns:
755 = rwxr-xr-x (owner: all, others: read/execute)
644 = rw-r--r-- (owner: read/write, others: read only)
700 = rwx------ (owner only)
600 = rw------- (owner read/write only, for secrets)
chmod 755 script.sh    # rwxr-xr-x (typical for scripts)
chmod 644 config.txt   # rw-r--r-- (typical for files)
chmod 600 ~/.ssh/id_rsa # rw------- (SSH private key)
```

### Changing Ownership

```
# Change owner
sudo chown ubuntu file.txt
# Change owner and group
sudo chown ubuntu:www-data file.txt
# Recursive (for directories)
sudo chown -R ubuntu:ubuntu /var/www/myapp/  
```

### Cloud Security Best Practice

```
# Secure SSH key permissions
chmod 400 ~/.ssh/id_rsa        # Private key: owner read only
chmod 644 ~/.ssh/id_rsa.pub    # Public key: readable
# Secure sensitive config files
chmod 600 /etc/myapp/secrets.conf
# Web server files
sudo chown -R www-data:www-data /var/www/html/
chmod -R 755 /var/www/html/
```

5 | Process and Service Management
----------------------------------

### Understanding Processes

Every running program is a process:

```
# View all processes
ps aux
# Sample output:
USER       PID %CPU %MEM    VSZ   RSS TTY  STAT START   TIME COMMAND
root         1  0.0  0.1 169432 12984 ?    Ss   Jan10   0:05 /sbin/init
ubuntu    1234  0.1  0.5 725948 48276 ?    Ssl  10:30   0:12 nginx
ubuntu    5678  2.3  1.2 892456 98324 pts/0 Sl+ 11:45   1:23 python3 app.py
```

Key columns:

*   **PID**: Process ID (unique identifier)
*   **%CPU**: CPU usage percentage
*   **%MEM**: Memory usage percentage
*   **STAT:** Process state (S=sleeping, R=running, Z=zombie)
*   **COMMAND:** The command that started this process

### Managing Processes

```
# Find specific processes
ps aux | grep nginx
pgrep -l nginx              # Just PIDs and names
# Real-time process monitor
top                         # Basic monitor (q to quit)
htop                        # Enhanced monitor (install if needed)
# Kill processes
kill 1234                   # Graceful termination (SIGTERM)
kill -9 1234                # Force kill (SIGKILL)
pkill nginx                 # Kill by name
killall python3             # Kill all matching processes
# Run processes in background
python3 app.py &            # Run in background
nohup python3 app.py &      # Survives logout
jobs                        # List background jobs
fg %1                       # Bring job to foreground
```

### Systemd Services

Modern Linux uses systemd to manage services (daemons):

```
# Check service status
sudo systemctl status nginx
# Output:
● nginx.service - A high performance web server
     Loaded: loaded (/lib/systemd/system/nginx.service; enabled)
     Active: active (running) since Mon 2024-01-15 10:30:00 UTC
    Process: 1234 ExecStart=/usr/sbin/nginx
   Main PID: 1235 (nginx)
      Tasks: 2 (limit: 1137)
     Memory: 5.6M
        CPU: 123ms
# Control services
sudo systemctl start nginx      # Start service
sudo systemctl stop nginx       # Stop service
sudo systemctl restart nginx    # Restart service
sudo systemctl reload nginx     # Reload config (no downtime)
# Enable/disable on boot
sudo systemctl enable nginx     # Start on boot
sudo systemctl disable nginx    # Don't start on boot
# View logs
sudo journalctl -u nginx        # All nginx logs
sudo journalctl -u nginx -f     # Follow nginx logs
sudo journalctl -u nginx --since "1 hour ago"
```

### Creating Your Own Service

Create a service for your application:

```
# Create service file
sudo nano /etc/systemd/system/myapp.service
[Unit]
Description=My Python Application
After=network.target
[Service]
Type=simple
User=ubuntu
WorkingDirectory=/home/ubuntu/myapp
Environment="PATH=/home/ubuntu/myapp/venv/bin"
ExecStart=/home/ubuntu/myapp/venv/bin/python app.py
Restart=always
RestartSec=10
[Install]
WantedBy=multi-user.target
# Enable and start
sudo systemctl daemon-reload
sudo systemctl enable myapp
sudo systemctl start myapp
sudo systemctl status myapp
```

6 | System Monitoring and Resources
-----------------------------------

### Checking System Resources

```
# Memory usage
free -h
# Output:
              total        used        free      shared  buff/cache   available
Mem:          7.8Gi       2.1Gi       1.5Gi       256Mi       4.2Gi       5.2Gi
Swap:         2.0Gi          0B       2.0Gi
# Disk usage
df -h
# Output:
Filesystem      Size  Used Avail Use% Mounted on
/dev/xvda1       30G   12G   17G  42% /
tmpfs           3.9G     0  3.9G   0% /tmp
# Directory size
du -sh /var/log/          # Size of /var/log
du -sh *                   # Size of each item in current dir
du -h --max-depth=1 /      # Size of top-level directories
# CPU information
lscpu                      # CPU details
nproc                      # Number of processors
# System uptime and load
uptime
# Output: 10:45:32 up 5 days, 2:30, 1 user, load average: 0.08, 0.15, 0.12
```

### Log Analysis

Logs are crucial for troubleshooting:

```
# System logs
sudo journalctl -f                    # Follow all logs
sudo journalctl -p err                # Only errors
sudo journalctl --since "2024-01-15"  # Since date
# Traditional log files
sudo tail -f /var/log/syslog          # System log
sudo tail -f /var/log/auth.log        # Authentication log
sudo tail -f /var/log/nginx/access.log # Nginx access log
# Search logs
sudo grep -i "error" /var/log/syslog
sudo grep -i "failed" /var/log/auth.log
```

7 | Package Management
----------------------

### Ubuntu/Debian (APT)

```
# Update package lists (always do first!)
sudo apt update
# Upgrade installed packages
sudo apt upgrade                # Upgrade packages
sudo apt full-upgrade           # Upgrade + handle dependencies
# Install packages
sudo apt install nginx          # Install single package
sudo apt install nginx php mysql-server  # Install multiple
# Remove packages
sudo apt remove nginx           # Remove package
sudo apt purge nginx            # Remove + config files
sudo apt autoremove             # Remove unused dependencies
# Search packages
apt search docker               # Find packages
apt show nginx                  # Package details
apt list --installed            # List installed packages
```

### CentOS/RHEL (DNF/YUM)

```
# Update packages
sudo dnf update                 # CentOS 8+, Fedora
sudo yum update                 # CentOS 7
# Install packages
sudo dnf install nginx
sudo yum install nginx
# Remove packages
sudo dnf remove nginx
sudo yum remove nginx
# Search
dnf search docker
yum search docker
```

### Comparing Package Managers

Different Linux distributions use different package managers, but they all accomplish the same tasks. If you learn one, switching to another is straightforward.

**Updating your package** list refreshes the local database of available software. On Ubuntu and Debian, use apt update. On CentOS and RHEL, use dnf check-update.

**Upgrading installed packages** downloads and installs newer versions of everything on your system. Ubuntu uses apt upgrade, while CentOS uses dnf upgrade.

**Installing new software** is where you’ll spend most of your time. On Ubuntu, run apt install package-name. On CentOS, run dnf install package-name.

**Removing software** you no longer need keeps your system clean. Ubuntu uses apt remove package-name, and CentOS uses dnf remove package-name.

**Searching for packages** helps you find the right software when you’re not sure of the exact name. Use apt search term on Ubuntu or dnf search term on CentOS.

_The syntax differs slightly, but the concepts remain identical. Master one, and you’ll feel comfortable with any Linux distribution._

8 | Networking
--------------

### Network Information

```
# IP addresses
ip addr show                # All network interfaces
ip a                        # Short version
hostname -I                 # Just IP addresses
# Network connections
ss -tulpn                   # Active connections (modern)
netstat -tulpn              # Active connections (legacy)
# Sample output:
tcp  LISTEN 0  128  *:22    *:*    users:(("sshd",pid=1234,fd=3))
tcp  LISTEN 0  511  *:80    *:*    users:(("nginx",pid=5678,fd=6))
```

### Testing Connectivity

```
# Ping (test reachability)
ping google.com             # Ping until Ctrl+C
ping -c 4 google.com        # Ping 4 times
# DNS lookup
nslookup google.com
dig google.com
# HTTP requests
curl https://api.example.com/health    # GET request
curl -I https://example.com            # Just headers
curl -X POST -d '{"key":"value"}' https://api.example.com
# Download files
wget https://example.com/file.zip
curl -O https://example.com/file.zip
```

### Firewall Configuration (UFW)

UFW (Uncomplicated Firewall) is the standard on Ubuntu:

```
# Enable firewall
sudo ufw enable
# Allow common ports
sudo ufw allow 22/tcp       # SSH (always do this first!)
sudo ufw allow 80/tcp       # HTTP
sudo ufw allow 443/tcp      # HTTPS
sudo ufw allow 3000/tcp     # Custom app port
# Allow from specific IP
sudo ufw allow from 192.168.1.100
# Deny connections
sudo ufw deny 8080/tcp
# Check status
sudo ufw status verbose
# Sample output:
Status: active
To                         Action      From
--                         ------      ----
22/tcp                     ALLOW       Anywhere
80/tcp                     ALLOW       Anywhere
443/tcp                    ALLOW       Anywhere
```

### SSH Configuration

```
# Connect to remote server
ssh ubuntu@192.168.1.100
ssh -i ~/.ssh/mykey.pem ubuntu@ec2-instance.compute.amazonaws.com
# SSH config file (~/.ssh/config)
nano ~/.ssh/config

Host myserver
    HostName 192.168.1.100
    User ubuntu
    IdentityFile ~/.ssh/mykey.pem
    Port 22
Host production
    HostName ec2-12-34-56-78.compute.amazonaws.com
    User ec2-user
    IdentityFile ~/.ssh/aws-key.pem

# Now connect with just:
ssh myserver
ssh production
# Copy files over SSH
scp file.txt myserver:/home/ubuntu/
scp -r folder/ myserver:/home/ubuntu/
scp myserver:/var/log/app.log ./
# Sync directories
rsync -avz local-folder/ myserver:/remote-folder/
```

9 | Docker on Linux
-------------------

### Installing Docker

```
# Install Docker on Ubuntu
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
# Add your user to docker group (avoids sudo)
sudo usermod -aG docker $USER
# Log out and back in for this to take effect
# Verify installation
docker --version
docker run hello-world
```

### Docker Basics

```
# Pull images
docker pull ubuntu:22.04
docker pull nginx:latest
docker pull python:3.11-slim
# List images
docker images
# Run containers
docker run -it ubuntu:22.04 bash        # Interactive shell
docker run -d nginx                      # Detached (background)
docker run -d -p 80:80 nginx            # Map port 80
docker run -d -p 8080:80 --name webserver nginx  # Named container
# List containers
docker ps                                # Running containers
docker ps -a                             # All containers
# Container management
docker stop webserver                    # Stop container
docker start webserver                   # Start stopped container
docker restart webserver                 # Restart container
docker rm webserver                      # Remove container
docker rm -f webserver                   # Force remove running container
# Interact with containers
docker exec -it webserver bash           # Shell into container
docker logs webserver                    # View logs
docker logs -f webserver                 # Follow logs
```

### Building Docker Images

Create a Dockerfile:

```
# Dockerfile for Python Flask app
FROM python:3.11-slim
# Set working directory
WORKDIR /app
# Copy requirements first (for caching)
COPY requirements.txt .
RUN pip install --no-cache-dir -r requirements.txt
# Copy application code
COPY . .
# Expose port
EXPOSE 5000
# Set environment variables
ENV FLASK_APP=app.py
ENV FLASK_ENV=production
# Run application
CMD ["flask", "run", "--host=0.0.0.0"]
```

### Docker Compose

For multi-container applications, use Docker Compose:

```
# docker-compose.yml
version: '3.8'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    environment:
      - DATABASE_URL=postgresql://db:5432/myapp
    depends_on:
      - db
    restart: unless-stopped
  db:
    image: postgres:15
    environment:
      - POSTGRES_DB=myapp
      - POSTGRES_USER=user
      - POSTGRES_PASSWORD=password
    volumes:
      - postgres_data:/var/lib/postgresql/data
    restart: unless-stopped
  nginx:
    image: nginx:latest
    ports:
      - "80:80"
    volumes:
      - ./nginx.conf:/etc/nginx/nginx.conf
    depends_on:
      - web
    restart: unless-stopped
volumes:
  postgres_data:

# Start all services
docker-compose up -d
# View logs
docker-compose logs -f
# Stop all services
docker-compose down
# Rebuild after changes
docker-compose up -d --build
```

10 | Shell Scripting
--------------------

### Bash Script Basics

Shell scripts automate repetitive tasks:

```
#!/bin/bash
# save as: my-script.sh
# Variables
NAME="Cloud Developer"
DATE=$(date +%Y-%m-%d)
SERVER_COUNT=3
# Print statements
echo "Hello, $NAME!"
echo "Today is $DATE"
echo "Managing $SERVER_COUNT servers"
# User input
read -p "Enter your name: " USERNAME
echo "Welcome, $USERNAME!"

# Make executable and run
chmod +x my-script.sh
./my-script.sh
```

### Conditionals

```
#!/bin/bash
# If statements
if [ -f "/var/log/syslog" ]; then
    echo "Syslog exists"
else
    echo "Syslog not found"
fi
# Compare numbers
COUNT=5
if [ $COUNT -gt 10 ]; then
    echo "Count is greater than 10"
elif [ $COUNT -eq 5 ]; then
    echo "Count is exactly 5"
else
    echo "Count is less than 5"
fi
# Compare strings
STATUS="running"
if [ "$STATUS" = "running" ]; then
    echo "Service is running"
fi
# Check if command succeeded
if docker ps > /dev/null 2>&1; then
    echo "Docker is running"
else
    echo "Docker is not running"
fi
```

### Loops

```
#!/bin/bash
# For loop
for i in 1 2 3 4 5; do
    echo "Number: $i"
done
# For loop with range
for i in {1..10}; do
    echo "Count: $i"
done
# Loop through files
for file in /var/log/*.log; do
    echo "Processing: $file"
done
# While loop
COUNT=0
while [ $COUNT -lt 5 ]; do
    echo "Count is $COUNT"
    COUNT=$((COUNT + 1))
done
# Read file line by line
while read -r line; do
    echo "Line: $line"
done < /etc/hosts
```

### Functions

```
#!/bin/bash
# Define function
check_service() {
    local SERVICE=$1
    if systemctl is-active --quiet $SERVICE; then
        echo "✅ $SERVICE is running"
        return 0
    else
        echo "❌ $SERVICE is not running"
        return 1
    fi
}
# Call function
check_service nginx
check_service mysql
check_service redis
```

### Practical Deployment Script

```
#!/bin/bash
# deploy.sh - Automated deployment script
set -e  # Exit on any error
# Configuration
APP_NAME="myapp"
DEPLOY_DIR="/var/www/$APP_NAME"
REPO_URL="https://github.com/yourusername/myapp.git"
BRANCH="main"
# Colors for output
RED='\033[0;31m'
GREEN='\033[0;32m'
YELLOW='\033[1;33m'
NC='\033[0m' # No Color
# Logging function
log() {
    echo -e "${GREEN}[$(date '+%Y-%m-%d %H:%M:%S')]${NC} $1"
}
error() {
    echo -e "${RED}[ERROR]${NC} $1"
    exit 1
}
warn() {
    echo -e "${YELLOW}[WARNING]${NC} $1"
}
# Main deployment
main() {
    log "Starting deployment of $APP_NAME"
    # Check if directory exists
    if [ ! -d "$DEPLOY_DIR" ]; then
        log "Creating deploy directory"
        sudo mkdir -p "$DEPLOY_DIR"
        sudo chown $USER:$USER "$DEPLOY_DIR"
    fi
    cd "$DEPLOY_DIR"
    # Clone or pull
    if [ -d ".git" ]; then
        log "Pulling latest changes"
        git fetch origin
        git reset --hard origin/$BRANCH
    else
        log "Cloning repository"
        git clone -b $BRANCH $REPO_URL .
    fi
    # Install dependencies
    log "Installing dependencies"
    if [ -f "requirements.txt" ]; then
        pip install -r requirements.txt
    fi
    # Restart service
    log "Restarting service"
    sudo systemctl restart $APP_NAME
    # Health check
    sleep 5
    if systemctl is-active --quiet $APP_NAME; then
        log "✅ Deployment successful!"
    else
        error "Deployment failed - service not running"
    fi
}
# Run main function
main
```

12 | Update Your Learning Journal
---------------------------------

```
## Day 8: Linux - The Cloud Operating System
### What I Learned:
- Linux is the foundation of cloud computing (90%+ of cloud infrastructure)
- Command line navigation and file operations
- File permissions and security management
- Process and service management with systemd
- Package management with apt
- Firewall configuration with UFW
- Docker containers for application deployment
- Shell scripting for automation
### Portfolio Deployment Accomplished:
- Launched Ubuntu EC2 instance on AWS
- Configured Nginx as reverse proxy
- Deployed Flask backend as systemd service
- Connected frontend (HTML/CSS/JS) with Python API
- Created automated deployment script
- Secured server with UFW firewall
### Linux Commands Mastered:
- Navigation: pwd, ls, cd, mkdir, rm
- Files: cat, less, head, tail, grep
- Permissions: chmod, chown
- Processes: ps, top, kill, systemctl
- Packages: apt update, apt install
- Network: ip addr, curl, ssh, scp
- Docker: run, build, ps, logs
### My Portfolio is Now:
- ✅ Live on the internet!
- ✅ Running on Ubuntu 22.04 LTS
- ✅ Served by Nginx web server
- ✅ Powered by Python Flask backend
- ✅ Deployed with automated scripts
```

Portfolio Project Progress Tracker
----------------------------------

✅ **Completed:** English, Mathematics, HTML, CSS, JavaScript, Python, Git, Linux
✅ **Milestone:** Portfolio deployed to the cloud! 🚀
⬜ **Next:** SQL for database integration
⬜ **Coming:** Kubernetes for container orchestration

```
Portfolio Journey:
┌────────────────────────────────────────────────────────────────┐
│ ✅ English      → Technical communication                      │
│ ✅ Mathematics  → Logic and problem-solving                    │
│ ✅ HTML         → Portfolio structure                          │
│ ✅ CSS          → Professional styling                         │
│ ✅ JavaScript   → Interactive features                         │
│ ✅ Python       → Backend API                                  │
│ ✅ Git          → Version control & GitHub                     │
│ ✅ Linux        → DEPLOYED TO THE CLOUD! 🎉                    │  
│ ⬜ SQL          → Database integration (next!)                 │
│ ⬜ Kubernetes   → Container orchestration                      │
└────────────────────────────────────────────────────────────────┘
```

### Final Thoughts

Think about where you started: someone curious about cloud computing, perhaps wondering how websites actually get “onto the internet.”

Now look at what you’ve accomplished:

You’ve SSH’d into a remote server.

You’ve navigated the Linux filesystem with confidence.

You’ve installed software, configured firewalls, set up web servers, and created services that run automatically.

You’ve deployed a real application that anyone in the world can access!

Concluding Remarks
------------------

Linux is vast. What we covered is the foundation, but there’s always more to learn.

Additional Resources
--------------------

### [Cloud Glossary](https://medium.com/list/cloud-glossary-528956a3c181)

> Cloud Computing Simplified: A Cloud Glossary For Beginners

### [Cloud Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34703)

> In a world where nearly every industry is moving toward cloud-first strategies, having a baseline understanding of these principles is no longer optional but essential.

### [AI Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34979)

> Whether you’re an Executive, Developer, Engineer, or Project Manager, having a baseline understanding of AI principles is no longer a _nice to have_, it’s **absolutely essential.**

### [Linux for Beginners](https://www.youtube.com/watch?v=sWbUDq4S6Y8)

> A comprehensive six-hour course covering everything from basic commands to shell scripting. Completely free and well-paced for beginners.


# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Linux in the Cloud](https://ntombizakhona.medium.com/linux-in-the-cloud-1274f654f972)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**03 February 2026**
