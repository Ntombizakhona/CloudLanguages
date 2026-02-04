### 🏗️ Let’s Build

8 | Add a Database to Your Portfolio
------------------------------------

Throughout this series, you’ve built:

*   **HTML/CSS/JavaScript:** Frontend interface
*   **Python:** Backend API with Flask
*   **Git:** Version control and GitHub
*   **Linux:** Deployed to the cloud

Now let’s add persistent storage so your portfolio can remember things!

**What We’re Adding**

```
┌─────────────────────────────────────────────────────────────────────────┐
│                  PORTFOLIO WITH DATABASE ARCHITECTURE                   │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Internet Users                                                        │
│         │                                                               │
│         ▼                                                               │
│   ┌─────────────────┐                                                   │
│   │     Nginx       │  ← Reverse Proxy                                  │
│   │   Port 80/443   │                                                   │
│   └────────┬────────┘                                                   │
│            │                                                            │
│      ┌─────┴─────┐                                                      │
│      │           │                                                      │
│      ▼           ▼                                                      │
│  ┌────────┐  ┌────────────┐                                             │
│  │ Static │  │  Flask API │                                             │
│  │ Files  │  │  Port 5000 │                                             │
│  │ (HTML/ │  │            │                                             │
│  │ CSS/JS)│  │ /api/*     │                                             │
│  └────────┘  └──────┬─────┘                                             │
│                     │                                                   │
│                     ▼                                                   │
│              ┌────────────┐                                             │
│              │   MySQL    │  ← NEW! Database                            │
│              │  Port 3306 │                                             │
│              │            │                                             │
│              │ - contacts │                                             │
│              │ - projects │                                             │
│              │ - skills   │                                             │
│              │ - visitors │                                             │
│              └────────────┘                                             │
│                                                                         │
│   Contact form → Saved to database → Never lost!                        │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### Step 01: Install MySQL on Your Server

SSH into your Linux server:

```
# Connect to your server
ssh -i your-key.pem ubuntu@your-server-ip
# Install MySQL Server
sudo apt update
sudo apt install mysql-server -y
# Start and enable MySQL
sudo systemctl start mysql
sudo systemctl enable mysql
# Secure installation
sudo mysql_secure_installation
# - Set root password
# - Remove anonymous users: Y
# - Disallow root login remotely: Y
# - Remove test database: Y
# - Reload privilege tables: Y
# Verify MySQL is running
sudo systemctl status mysql
```

### Step 02: Create Database and User

```
# Login to MySQL
sudo mysql -u root -p

-- Create the portfolio database
CREATE DATABASE portfolio_db CHARACTER SET utf8mb4 COLLATE utf8mb4_unicode_ci;
-- Create application user
CREATE USER 'portfolio_app'@'localhost' IDENTIFIED BY 'your_secure_password';
GRANT ALL PRIVILEGES ON portfolio_db.* TO 'portfolio_app'@'localhost';
FLUSH PRIVILEGES;
-- Switch to the database
USE portfolio_db;
-- Create contacts table
CREATE TABLE contacts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL,
    subject VARCHAR(200),
    message TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_read BOOLEAN DEFAULT FALSE,
    ip_address VARCHAR(45),
    
    INDEX idx_created_at (created_at),
    INDEX idx_is_read (is_read)
);
-- Create page views table for analytics
CREATE TABLE page_views (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    page_path VARCHAR(500) NOT NULL,
    referrer VARCHAR(500),
    user_agent TEXT,
    ip_address VARCHAR(45),
    viewed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_viewed_at (viewed_at),
    INDEX idx_page_path (page_path)
);
-- Exit MySQL
EXIT;
```

### Step 03: Update Python Backend

**Update your Flask application to use the database:**

```
# Navigate to your backend
cd /var/www/languagesportfolio/python-backend
# Activate virtual environment
source venv/bin/activate
# Install MySQL connector
pip install mysql-connector-python
pip freeze > requirements.txt
```

**Update app.py to connect to MySQL:**

```
# app.py
from flask import Flask, request, jsonify
from flask_cors import CORS
import mysql.connector
from mysql.connector import pooling
from datetime import datetime
import os
app = Flask(__name__)
CORS(app)
# Database connection pool
db_config = {
    'host': 'localhost',
    'user': 'portfolio_app',
    'password': os.environ.get('DB_PASSWORD', 'your_secure_password'),
    'database': 'portfolio_db',
    'pool_name': 'portfolio_pool',
    'pool_size': 5
}
connection_pool = pooling.MySQLConnectionPool(**db_config)
def get_db_connection():
    return connection_pool.get_connection()
# Contact form endpoint
@app.route('/api/contact', methods=['POST'])
def submit_contact():
    try:
        data = request.json
        
        # Validate required fields
        if not all(key in data for key in ['name', 'email', 'message']):
            return jsonify({'error': 'Missing required fields'}), 400
        
        conn = get_db_connection()
        cursor = conn.cursor()
        
        query = """
            INSERT INTO contacts (name, email, subject, message, ip_address)
            VALUES (%s, %s, %s, %s, %s)
        """
        cursor.execute(query, (
            data['name'],
            data['email'],
            data.get('subject', ''),
            data['message'],
            request.remote_addr
        ))
        
        conn.commit()
        cursor.close()
        conn.close()
        
        return jsonify({
            'success': True,
            'message': 'Thank you! Your message has been received.'
        }), 201
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500
# Get all contacts (admin only - add authentication in production!)
@app.route('/api/contacts', methods=['GET'])
def get_contacts():
    try:
        conn = get_db_connection()
        cursor = conn.cursor(dictionary=True)
        
        cursor.execute("""
            SELECT id, name, email, subject, message, created_at, is_read
            FROM contacts
            ORDER BY created_at DESC
            LIMIT 50
        """)
        
        contacts = cursor.fetchall()
        
        # Convert datetime to string for JSON
        for contact in contacts:
            contact['created_at'] = contact['created_at'].isoformat()
        
        cursor.close()
        conn.close()
        
        return jsonify(contacts)
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500
# Track page view
@app.route('/api/pageview', methods=['POST'])
def track_pageview():
    try:
        data = request.json
        
        conn = get_db_connection()
        cursor = conn.cursor()
        
        query = """
            INSERT INTO page_views (page_path, referrer, user_agent, ip_address)
            VALUES (%s, %s, %s, %s)
        """
        cursor.execute(query, (
            data.get('path', '/'),
            data.get('referrer', ''),
            request.headers.get('User-Agent', ''),
            request.remote_addr
        ))
        
        conn.commit()
        cursor.close()
        conn.close()
        
        return jsonify({'success': True}), 201
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500
# Analytics endpoint
@app.route('/api/analytics', methods=['GET'])
def get_analytics():
    try:
        conn = get_db_connection()
        cursor = conn.cursor(dictionary=True)
        
        # Total views last 7 days
        cursor.execute("""
            SELECT 
                DATE(viewed_at) AS date,
                COUNT(*) AS views
            FROM page_views
            WHERE viewed_at >= DATE_SUB(NOW(), INTERVAL 7 DAY)
            GROUP BY DATE(viewed_at)
            ORDER BY date
        """)
        daily_views = cursor.fetchall()
        
        # Top pages
        cursor.execute("""
            SELECT page_path, COUNT(*) AS views
            FROM page_views
            WHERE viewed_at >= DATE_SUB(NOW(), INTERVAL 30 DAY)
            GROUP BY page_path
            ORDER BY views DESC
            LIMIT 10
        """)
        top_pages = cursor.fetchall()
        
        cursor.close()
        conn.close()
        
        # Convert dates for JSON
        for row in daily_views:
            row['date'] = row['date'].isoformat()
        
        return jsonify({
            'daily_views': daily_views,
            'top_pages': top_pages
        })
        
    except Exception as e:
        return jsonify({'error': str(e)}), 500
# Health check
@app.route('/api/health', methods=['GET'])
def health_check():
    try:
        conn = get_db_connection()
        cursor = conn.cursor()
        cursor.execute("SELECT 1")
        cursor.fetchone()
        cursor.close()
        conn.close()
        return jsonify({'status': 'healthy', 'database': 'connected'})
    except Exception as e:
        return jsonify({'status': 'unhealthy', 'error': str(e)}), 500
if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000, debug=False)
```

### Step 04: Update Frontend Contact Form

Update your JavaScript to send data to the API:

```
// scripts/main.js
// Contact form submission
const contactForm = document.getElementById('contact-form');
if (contactForm) {
    contactForm.addEventListener('submit', async (e) => {
        e.preventDefault();
        
        const formData = {
            name: document.getElementById('name').value,
            email: document.getElementById('email').value,
            subject: document.getElementById('subject').value,
            message: document.getElementById('message').value
        };
        
        try {
            const response = await fetch('/api/contact', {
                method: 'POST',
                headers: {
                    'Content-Type': 'application/json'
                },
                body: JSON.stringify(formData)
            });
            
            const result = await response.json();
            
            if (response.ok) {
                showNotification('success', result.message);
                contactForm.reset();
            } else {
                showNotification('error', result.error || 'Something went wrong');
            }
        } catch (error) {
            showNotification('error', 'Failed to send message. Please try again.');
        }
    });
}
// Track page views
async function trackPageView() {
    try {
        await fetch('/api/pageview', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/json'
            },
            body: JSON.stringify({
                path: window.location.pathname,
                referrer: document.referrer
            })
        });
    } catch (error) {
        console.log('Analytics tracking failed:', error);
    }
}
// Track on page load
document.addEventListener('DOMContentLoaded', trackPageView);
// Notification helper
function showNotification(type, message) {
    const notification = document.createElement('div');
    notification.className = `notification notification-${type}`;
    notification.textContent = message;
    document.body.appendChild(notification);
    
    setTimeout(() => {
        notification.classList.add('fade-out');
        setTimeout(() => notification.remove(), 300);
    }, 3000);
}
```

### Step 05: Deploy And Test

```
# Restart the Flask service
sudo systemctl restart languagesportfolio-api
# Check service status
sudo systemctl status languagesportfolio-api
# Test the API
curl http://localhost:5000/api/health
# Expected output:
# {"database":"connected","status":"healthy"}
# Test contact submission
curl -X POST http://localhost:5000/api/contact \
  -H "Content-Type: application/json" \
  -d '{"name":"Test User","email":"test@example.com","message":"Hello from SQL!"}'
# Verify data was saved
sudo mysql -u root -p -e "SELECT * FROM portfolio_db.contacts;"
```

### Step 06: Update Your Skills

Your portfolio now has real database integration! Update your skills section:

```
const skillProgress = {
    'english': 100,
    'mathematics': 100,
    'html': 100,
    'css': 100,
    'javascript': 100,
    'python': 100,
    'git': 100,
    'linux': 100,
    'sql': 100,  // 🎉 You just mastered this!
    'kubernetes': 0
};
```

### ⛔ End of Building Tutorial⛔

---

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [SQL in the Cloud](https://medium.com/@ntombizakhona/sql-in-the-cloud-a2942dc80a32)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**04 February 2026**
