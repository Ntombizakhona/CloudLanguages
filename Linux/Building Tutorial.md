### 🏗️ Let’s Build

11 | Deploy Your Portfolio to the Cloud
---------------------------------------

### Prerequisites

1.  [**AWS Account**](https://medium.com/amazon-web-services-a8e57a9c6084)
2.  [**A Key Pair**](https://medium.com/abstraction-d1335bec022e)
3.  [**How To Launch An EC2 Instance**](https://medium.com/abstraction-d1335bec022e)

Throughout this series, you’ve built an impressive portfolio:

1.  HTML: Created the semantic structure
2.  CSS:Added professional styling
3.  JavaScript: Made it interactive with animations
4.  Python: Built a Flask backend API
5.  Git: Pushed everything to GitHub

**_Now it’s time to deploy your portfolio to a live Linux server in the cloud! This is the ultimate test of your cloud skills._**

### What We’re Deploying

```
portfolio-project/
├── index.html              ← Frontend (HTML/CSS/JavaScript)
├── styles/
│   └── main.css
├── scripts/
│   └── main.js
├── assets/
│   └── images/
└── python-backend/         ← Backend (Flask API)
    ├── app.py
    ├── portfolio_manager.py
    ├── requirements.txt
    └── data/
        └── portfolio.json
```

### Step 01: Launch Your Cloud Server

1.  Go to AWS Console → EC2 → Launch Instance
2.  **Name:** CloudLanguages
3.  **Amazon Machine Image:** Ubuntu 22.04 LTS (free tier eligible)
4.  Instance Type: **t3.micro** (free tier)
5.  **Key Pair** → Download .pem file or Select CloudGlossary Key Pair
6.  **Create Security Group:** CloudLanguagesSG
7.  **Description:** My Cloud Languages Security Group
8.  Inbound Security Group Rules

*   **SSH** (22): My IP
*   **HTTP** (80): Anywhere
*   **HTTPS** (443): Anywhere
*   **Custom TCP** (5000): Anywhere (for API)

**Launch instance.**

```
# Connect to your server
ssh -i CloudGlossary.pem ubuntu@your-server-ip
# You should see:
# Welcome to Ubuntu 22.04.3 LTS
# ubuntu@ip-172-31-25-42:~$
```

**🎉 Congratulations!** You’re now logged into a Linux server in the cloud!

### Step 02: Initial Server Setup

```
# Update the system (always do this first!)
sudo apt update && sudo apt upgrade -y
# Set your timezone
sudo timedatectl set-timezone UTC
# Install essential tools
sudo apt install -y git curl wget unzip
# Configure firewall
sudo ufw allow OpenSSH
sudo ufw allow 80/tcp
sudo ufw allow 443/tcp
sudo ufw allow 5000/tcp
sudo ufw --force enable
# Verify firewall
sudo ufw status
```

### Step 03: Install Required Software

```
# Install Nginx (web server)
sudo apt install nginx -y
sudo systemctl enable nginx
sudo systemctl start nginx
# Verify Nginx is running
sudo systemctl status nginx
# Visit http://your-server-ip in browser - you should see "Welcome to nginx!"
# Install Python 3 and pip
sudo apt install python3 python3-pip python3-venv -y
# Verify Python
python3 --version
# Output: Python 3.10.x
# Install Git (should already be installed)
  sudo apt install git -y
```

### Step 04: Clone Your Portfolio from GitHub

Remember when you pushed your portfolio to GitHub in the Git post? Now we’ll pull it down to the server!

```
# Create web directory
sudo mkdir -p /var/www/languagesportfolio
sudo chown ubuntu:ubuntu /var/www/languagesportfolio
cd /var/www/languagesportfolio
# Clone YOUR portfolio repository
git clone https://github.com/yourusername/languagesportfolio.git .
# Verify the files are there
ls -la
# You should see:
# index.html  styles/  scripts/  assets/  python-backend/
```

### Step 05: Set Up the Python Backend

```
# Navigate to backend directory
cd /var/www/languagesportfolio/python-backend
# Create virtual environment
python3 -m venv venv
# Activate virtual environment
source venv/bin/activate
# Install Flask and dependencies
pip install flask flask-cors gunicorn
# If you have a requirements.txt:
pip install -r requirements.txt
# Test the backend locally
python app.py
# You should see: Running on http://0.0.0.0:5000
# Press Ctrl+C to stop
```

### Step 06: Create Systemd Service for Flask API

Make your Python backend run automatically and restart if it crashes:

```
sudo nano /etc/systemd/system/languagesportfolio-api.service
```

Paste this configuration (replace paths as needed):

```
[Unit]
Description=Portfolio Flask API
After=network.target
[Service]
User=ubuntu
Group=ubuntu
WorkingDirectory=/var/www/languagesportfolio/python-backend
Environment="PATH=/var/www/languagesportfolio/python-backend/venv/bin"
ExecStart=/var/www/languagesportfolio/python-backend/venv/bin/gunicorn --workers 3 --bind 127.0.0.1:5000 app:app
Restart=always
RestartSec=10
[Install]
WantedBy=multi-user.target
```

Enable the service:

```
# Enable and start the service
sudo systemctl daemon-reload
sudo systemctl enable languagesportfolio-api
sudo systemctl start languagesportfolio-api
# Check status
sudo systemctl status languagesportfolio-api
# ● portfolio-api.service - Portfolio Flask API
#      Active: active (running) since...
```

### Step 07: Configure Nginx to Serve Your Portfolio

```
# Create Nginx configuration
sudo nano /etc/nginx/sites-available/languagesportfolio
```

Paste this configuration:

```
server {
    listen 80;
    server_name _;  # Accept any hostname
    # Serve static frontend files
    root /var/www/languagesportfolio;
    index index.html;
    # Handle frontend routes
    location / {
        try_files $uri $uri/ /index.html;
    }
    # Proxy API requests to Flask backend
    location /api {
        proxy_pass http://127.0.0.1:5000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
    # Cache static assets
    location ~* \.(css|js|jpg|jpeg|png|gif|ico|svg|woff|woff2)$ {
        expires 30d;
        add_header Cache-Control "public, immutable";
    }
}
```

Enable the site:

```
# Enable the site
sudo ln -s /etc/nginx/sites-available/languagesportfolio /etc/nginx/sites-enabled/
# Remove default site
sudo rm /etc/nginx/sites-enabled/default
# Test Nginx configuration
sudo nginx -t
# nginx: configuration file /etc/nginx/nginx.conf test is successful
# Reload Nginx
  sudo systemctl reload nginx
```

### Step 08: Verify Your Deployment

Check all services are running:

```
# Check Nginx
sudo systemctl status nginx
# ● nginx.service
#      Active: active (running)
# Check Flask API
sudo systemctl status languagesportfolio-api
# ● portfolio-api.service
#      Active: active (running)
# Test API locally
curl http://localhost/api/skills
# Should return JSON with your skills!
# Check the logs if anything goes wrong
sudo journalctl -u languagesportfolio-api -f
sudo tail -f /var/log/nginx/access.log
```

**Open your browser and visit**: [_http://your-server-ip_](http://your-server-ip)

**🎉 YOUR PORTFOLIO IS NOW LIVE ON THE CLOUD!**

### Step 09: Create a Deployment Script

Make future updates easy with a deployment script:

```
nano /var/www/languagesportfolio/deploy.sh
```

Paste this:

```
#!/bin/bash
# deploy.sh - Update portfolio from GitHub
set -e
# Colors
GREEN='\033[0;32m'
NC='\033[0m'
log() {
    echo -e "${GREEN}[DEPLOY]${NC} $1"
}
cd /var/www/languagesportfolio
log "Pulling latest changes from GitHub..."
git fetch origin
git reset --hard origin/main
log "Updating Python dependencies..."
cd python-backend
source venv/bin/activate
pip install -r requirements.txt --quiet
log "Restarting API service..."
sudo systemctl restart languagesportfolio-api
log "Reloading Nginx..."
sudo systemctl reload nginx
log "✅ Deployment complete!"
log "Visit: http://$(curl -s ifconfig.me)"
```

Run the script

```
chmod +x /var/www/languagesportfolio/deploy.sh
# Now whenever you push changes to GitHub, just run:
  ./deploy.sh
```

### Step 10: Update Your Portfolio Skills

Now that you’ve deployed to Linux, update your JavaScript to reflect your progress:

In your scripts/main.js, update the skill progress:

```
const skillProgress = {
    'english': 100,
    'mathematics': 100,
    'html': 100,
    'css': 100,
    'javascript': 100,
    'python': 100,
    'git': 100,
    'linux': 100,  // 🎉 You just mastered this!
    'sql': 0,
    'kubernetes': 0
};
```

Commit and deploy:

```
# On your local machine
git add .
git commit -m "Update skills progress - Linux complete!"
git push origin main
# On the server
./deploy.sh
```

### Step 11: SSH Hardening

```
# Edit SSH config
sudo nano /etc/ssh/sshd_config
# Make these changes:
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
MaxAuthTries 3
# Restart SSH
sudo systemctl restart sshd
```

### Step 12: Fail2ban (Intrusion Prevention)

```
# Install fail2ban
sudo apt install fail2ban -y
# Create local config
sudo cp /etc/fail2ban/jail.conf /etc/fail2ban/jail.local
sudo nano /etc/fail2ban/jail.local
``````
[sshd]
enabled = true
port = ssh
filter = sshd
logpath = /var/log/auth.log
maxretry = 3
bantime = 3600
``````
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
sudo fail2ban-client status sshd
```

### Step 13: Automatic Security Updates

```
# Install unattended-upgrades
sudo apt install unattended-upgrades -y
sudo dpkg-reconfigure -plow unattended-upgrades
# Enable automatic updates
sudo nano /etc/apt/apt.conf.d/20auto-upgrades
``````
APT::Periodic::Update-Package-Lists "1";
APT::Periodic::Unattended-Upgrade "1";
APT::Periodic::AutocleanInterval "7";
```

### ⛔ End of Building Tutorial⛔

12 | Building Tutorial Overview
-------------------------------

### What You’ve Accomplished

```
┌───────────────────────────────────────────────────────────────────┐
│              YOUR CLOUD PORTFOLIO ARCHITECTURE                    │
├───────────────────────────────────────────────────────────────────┤
│                                                                   │
│   Internet Users                                                  │
│         │                                                         │
│         ▼                                                         │
│   ┌─────────────────┐                                             │
│   │  Your Domain    │  ← Public IP / Domain Name                  │
│   │  (or Public IP) │                                             │
│   └────────┬────────┘                                             │
│            │                                                      │
│            ▼                                                      │
│   ┌─────────────────┐                                             │
│   │     Nginx       │  ← Web Server / Reverse Proxy               │
│   │   Port 80/443   │                                             │
│   └────────┬────────┘                                             │
│            │                                                      │
│      ┌─────┴─────┐                                                │
│      │           │                                                │
│      ▼           ▼                                                │
│  ┌────────┐  ┌────────────┐                                       │
│  │ Static │  │  Flask API │  ← Python Backend                     │
│  │ Files  │  │  Port 5000 │                                       │
│  │ HTML   │  │            │                                       │
│  │ CSS    │  │ /api/*     │                                       │
│  │ JS     │  │            │                                       │
│  └────────┘  └────────────┘                                       │
│                    │                                              │
│                    ▼                                              │
│            ┌────────────┐                                         │
│            │ portfolio  │  ← JSON Data Storage                    │
│            │   .json    │                                         │
│            └────────────┘                                         │
│                                                                   │
│   All running on Ubuntu 22.04 LTS in AWS EC2! 🚀                  │
│                                                                   │
└───────────────────────────────────────────────────────────────────┘
```

Take a moment to appreciate what you’ve built. Your portfolio isn’t just code on your laptop anymore, it’s a production application running on real cloud infrastructure.

The architecture you’ve created:

Internet users access your public IP address, which hits Nginx, your web server and reverse proxy.

Nginx serves your static files (HTML, CSS, JavaScript) directly for maximum performance.

When requests come in for /api/* endpoints, Nginx forwards them to your Flask application running on port 5000.

Flask processes the request, interacts with your JSON data storage, and returns the response.

All of this runs on Ubuntu 22.04 LTS in AWS EC2, protected by a UFW firewall, monitored by systemd, and deployable with a single script.

You’ve mastered:

1.  Launching and connecting to cloud servers
2.  Navigating the Linux filesystem
3.  Managing files and permissions
4.  Controlling processes and services
5.  Installing and updating software
6.  Configuring web servers and reverse proxies
7.  Running applications as system services
8.  Writing deployment automation scripts
9.  Securing servers against common attacks

**_This is real cloud engineering. These are the exact skills used by DevOps engineers, site reliability engineers, and cloud architects at companies of every size._**

---


# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [Linux in the Cloud](https://ntombizakhona.medium.com/linux-in-the-cloud-1274f654f972)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**03 February 2026**
