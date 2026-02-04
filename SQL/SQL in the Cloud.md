SQL in the Cloud
================

The Structured Query Language
-----------------------------


You’ve built a complete portfolio with [HTML](https://medium.com/html-in-the-cloud-dc23a04bfe51), [CSS](https://medium.com/css-8c98ee3ec762), [JavaScript](https://medium.com/javascript-in-the-cloud-6dd068fecb77), and [Python](https://medium.com/python-in-the-cloud-1462f61e3293). You’ve learned [Git](https://medium.com/git-in-the-cloud-d8fed43331fc) to track and deploy your code, and you’ve deployed it to a [Linux](https://medium.com/linux-in-the-cloud-1274f654f972) server in the cloud.

But here’s what’s missing: your application has no memory.

Think about it:

*   _A contact form submission?_ → **Lost when the server restarts**
*   _User preferences?_ → **Gone when they close the browser**
*   _Order history?_ → **Exists nowhere**
*   _Analytics data?_ → **Vanished into the void**

Your deployed portfolio can show information, but it can’t remember anything.

Every real-world application needs to store and retrieve data reliably. That’s where SQL comes in.

SQL (Structured Query Language) is the universal language for talking to databases. It’s been the industry standard for over 40 years, and it powers virtually every cloud database service you’ll encounter:

*   **AWS RDS, Aurora, Redshift** → SQL
*   **Azure SQL, Cosmos DB** → SQL
*   **Google Cloud SQL, BigQuery, Spanner** → SQL
*   **Your banking app, shopping cart, social media** → All SQL

_If data matters, SQL is involved. And in the cloud, data is everything._

Topics
------

1.  SQL fundamentals and database concepts
2.  Database design for scalable applications
3.  CRUD operations (Create, Read, Update, Delete)
4.  Joins, relationships, and data modeling
5.  Query optimization for cloud performance
6.  Cloud database services (RDS, Cloud SQL, Azure SQL)
7.  Adding a database to your portfolio project
8.  NoSQL vs SQL decision making

1 | Why SQL Powers the Cloud
----------------------------

### Every Cloud Application Needs Data

Here’s how data flows in cloud applications:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    CLOUD APPLICATION DATA FLOW                          │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   User                                                                  │
│    │                                                                    │
│    ▼                                                                    │
│   ┌─────────────────┐                                                   │
│   │    Frontend     │  ← HTML, CSS, JavaScript                          │
│   │   (Browser)     │                                                   │
│   └────────┬────────┘                                                   │
│            │                                                            │
│            ▼                                                            │
│   ┌─────────────────┐                                                   │
│   │    Backend      │  ← Python, Node.js, Java                          │
│   │   (API/Server)  │                                                   │
│   └────────┬────────┘                                                   │
│            │                                                            │
│            ▼                                                            │
│   ┌─────────────────┐                                                   │
│   │    DATABASE     │  ← SQL! This is where data lives permanently      │
│   │   (SQL Server)  │                                                   │
│   └─────────────────┘                                                   │
│                                                                         │
│   Without the database, nothing persists!                               │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

### SQL in the Cloud Ecosystem

Every major cloud provider offers managed SQL database services, and understanding the landscape helps you make informed decisions.

**Amazon Web Services** provides _RDS (Relational Database Service)_ and _Aurora_ for traditional web applications and enterprise systems. These services support MySQL, PostgreSQL, SQL Server, Oracle, and MariaDB, giving you flexibility in choosing your engine.

**Microsoft Azure** offers _Azure SQL Database_, which integrates seamlessly with the Microsoft ecosystem. If your organization uses .NET, Windows Server, or other Microsoft technologies, Azure SQL provides a natural fit.

**Google Cloud Platform** provides _Cloud SQL_ for standard relational database needs and Spanner for applications requiring global distribution and strong consistency. BigQuery handles analytics workloads with SQL syntax optimized for massive datasets.

**Across all providers**, _PostgreSQL_ and _MySQL_ remain the most portable choices. Applications built on these open-source databases can move between clouds with minimal changes, avoiding vendor lock-in.

### Why Managed Cloud Databases?

In the traditional approach, you’d manage your own database server. This meant installing and configuring software, handling backups manually, applying security patches, scaling hardware yourself, and waking up at an oddly specific time like 3:33 AM when something crashed.

Managed cloud databases flip this responsibility model entirely.

**Your responsibilities narrow to what actually matters for your application:** designing the schema, writing queries, optimizing performance, and choosing the right instance size.

**The cloud provider handles everything else:** hardware provisioning, automated backups, security patches, replication across availability zones, automatic failover when problems occur, and comprehensive monitoring.

This division of labour lets you focus on building features rather than maintaining infrastructure. The database just works, and when it doesn’t, the cloud provider’s engineering team responds, not you at 3:33 AM.

2 | Database Fundamentals
-------------------------

### What Is a Database?

Think of a database as a super-powered spreadsheet:

```
┌──────────────────────────────────────────────────────────────────────┐
│                        EXCEL SPREADSHEET                             │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│    Name       │  Email               │   Role                        │
│   ────────────┼──────────────────────┼────────────────               │
│    Anele      │  anele@example.com   │   Developer                   │
│    Bongani    │  bongani@example.com │   Designer                    │
│    Cynthia    │  cynthia@example.com │   Manager                     │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
                               ▼
                      BECOMES IN SQL:
┌──────────────────────────────────────────────────────────────────────┐
│                        DATABASE TABLE: users                         │
├──────────────────────────────────────────────────────────────────────┤
│                                                                      │
│  id  │ name    │ email               │ role       │ created_at       │
│ ─────┼─────────┼─────────────────────┼────────────┼───────────────── │
│  1   │ Anele   │ anele@example.com   │ Developer  │ 2024-01-15       │
│  2   │ Bongani │ bongani@example.com │ Designer   │ 2024-01-16       │
│  3   │ Cynthia │ cynthia@example.com │ Manager    │ 2024-01-17       │
│                                                                      │
│  + Auto-incrementing IDs                                             │
│  + Data type enforcement                                             │
│  + Relationships to other tables                                     │
│  + Constraints (unique emails, required fields)                      │
│  + Indexes for fast searching                                        │
│  + Concurrent access by thousands of users                           │
│                                                                      │
└──────────────────────────────────────────────────────────────────────┘
```

### Key Database Concepts

**Database:** A container that holds related tables. You might have an ecommerce_db containing all tables for your online store, or a portfolio_db for your personal website.

**Table:** A collection of related records organized into rows and columns. Common examples include users, products, and orders.

**Row (Record):** A single entry in a table representing one item. One user, one product, or one order.

**Column (Field):**A specific attribute that every record in the table contains. Examples include name, email, and price.

**Primary Key:** A unique identifier for each row, ensuring no two records are identical. Typically an auto-incrementing id column.

**Foreign Key:** A reference to another table’s primary key, creating relationships between tables. An orders table might have a user_id foreign key linking to the users table.

**Index:** A data structure that speeds up searches on specific columns. Think of it like a book’s index, instead of reading every page, you jump directly to what you need.

**Schema:** The overall structure and design of your database, including all tables, columns, and relationships.

### Relational Database Model

Tables connect to each other through relationships:

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    RELATIONAL DATABASE STRUCTURE                        │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   ┌─────────────┐         ┌─────────────┐         ┌─────────────┐       │
│   │   users     │         │   orders    │         │  products   │       │
│   ├─────────────┤         ├─────────────┤         ├─────────────┤       │
│   │ id (PK)     │◄────────│ user_id(FK) │         │ id (PK)     │       │
│   │ name        │         │ id (PK)     │         │ name        │       │
│   │ email       │         │ order_date  │         │ price       │       │
│   │ created_at  │         │ total       │         │ stock       │       │
│   └─────────────┘         └──────┬──────┘         └──────▲──────┘       │
│                                  │                        │             │
│                                  │    ┌─────────────┐     │             │
│                                  │    │ order_items │     │             │
│                                  │    ├─────────────┤     │             │
│                                  └───►│ order_id(FK)│     │             │
│                                       │ product_id(FK)────┘             │
│                                       │ quantity    │                   │
│                                       │ price       │                   │
│                                       └─────────────┘                   │
│                                                                         │
│   PK = Primary Key (unique identifier)                                  │
│   FK = Foreign Key (link to another table)                              │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘
```

3 | Setting Up Your SQL Environment
-----------------------------------

### Option A: Cloud Database (Recommended for production)

**AWS RDS MySQL:**

```
# Connect to AWS RDS MySQL instance
mysql -h your-rds-endpoint.amazonaws.com -u admin -p your_database
```

**Google Cloud SQL:**

```
# Connect via Cloud SQL proxy
gcloud sql connect my-instance --user=root
```

### Option B: Local Development Setup

**Using Docker (Fastest):**

```
# Run MySQL in Docker
docker run --name mysql-dev \
  -e MYSQL_ROOT_PASSWORD=password \
  -e MYSQL_DATABASE=portfolio_db \
  -p 3306:3306 \
  -d mysql:8.0
# Connect to database
mysql -h 127.0.0.1 -u root -p
# Or run PostgreSQL
docker run --name postgres-dev \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=portfolio_db \
  -p 5432:5432 \
  -d postgres:15
```

**Native Installation (Ubuntu):**

```
# Install MySQL
sudo apt update
sudo apt install mysql-server mysql-client -y
# Secure installation
sudo mysql_secure_installation
# Connect
sudo mysql -u root -p
```

### Option C: Online SQL Playgrounds

For quick practice without installation:

1.  [**DB Fiddle**](https://www.db-fiddle.com/)**:** Multiple database engines
2.  [**SQLiteOnline**](https://sqliteonline.com/)**:** Simple SQLite browser
3.  [**SQL Zoo**](https://sqlzoo.net/)**:** Interactive tutorials

4 | SQL Basics
--------------

### CRUD Operations

**Create Database and Tables**

```
-- Create database
CREATE DATABASE portfolio_db;
USE portfolio_db;
-- Create contacts table (for contact form submissions)
CREATE TABLE contacts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    email VARCHAR(255) NOT NULL,
    subject VARCHAR(200),
    message TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_read BOOLEAN DEFAULT FALSE,
    
    INDEX idx_email (email),
    INDEX idx_created_at (created_at)
);
-- Create projects table (for portfolio projects)
CREATE TABLE projects (
    id INT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(200) NOT NULL,
    description TEXT,
    technologies JSON,
    github_url VARCHAR(500),
    live_url VARCHAR(500),
    image_url VARCHAR(500),
    featured BOOLEAN DEFAULT FALSE,
    display_order INT DEFAULT 0,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP
);
-- Create skills table (to track learning progress)
CREATE TABLE skills (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL UNIQUE,
    category ENUM('frontend', 'backend', 'devops', 'cloud', 'other') NOT NULL,
    proficiency INT CHECK (proficiency BETWEEN 0 AND 100),
    icon_url VARCHAR(500),
    display_order INT DEFAULT 0,
    is_visible BOOLEAN DEFAULT TRUE
);
-- Create visitors table (for analytics)
CREATE TABLE visitors (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    ip_address VARCHAR(45),
    user_agent TEXT,
    page_visited VARCHAR(500),
    referrer VARCHAR(500),
    visited_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    
    INDEX idx_visited_at (visited_at)
);
```

**INSERT —** Adding Data:

```
-- Insert contact form submissions
INSERT INTO contacts (name, email, subject, message) VALUES
('John Doe', 'john@example.com', 'Job Opportunity', 'I saw your portfolio and would love to discuss...'),
('Jane Smith', 'jane@company.org', 'Collaboration', 'Your cloud projects look amazing!'),
('Bob Wilson', 'bob@startup.io', 'Question', 'How did you learn Kubernetes?');
-- Insert portfolio projects
INSERT INTO projects (title, description, technologies, github_url, featured, display_order) VALUES
(
    'Cloud Portfolio',
    'A full-stack portfolio showcasing cloud development skills',
    '["HTML", "CSS", "JavaScript", "Python", "Flask", "AWS"]',
    'https://github.com/username/portfolio',
    TRUE,
    1
),
(
    'E-Commerce API',
    'RESTful API for e-commerce platform built with Python',
    '["Python", "Flask", "PostgreSQL", "Docker"]',
    'https://github.com/username/ecommerce-api',
    TRUE,
    2
);
-- Insert skills with progress
INSERT INTO skills (name, category, proficiency, display_order) VALUES
('HTML', 'frontend', 100, 1),
('CSS', 'frontend', 100, 2),
('JavaScript', 'frontend', 90, 3),
('Python', 'backend', 85, 4),
('SQL', 'backend', 75, 5),
('Linux', 'devops', 80, 6),
('Docker', 'devops', 70, 7),
('AWS', 'cloud', 65, 8),
('Kubernetes', 'cloud', 50, 9);
```

**SELECT** — Reading Data:

```
-- Get all contacts (newest first)
SELECT * FROM contacts ORDER BY created_at DESC;
-- Get unread messages only
SELECT name, email, subject, created_at 
FROM contacts 
WHERE is_read = FALSE 
ORDER BY created_at DESC;
-- Count messages by email domain
SELECT 
    SUBSTRING_INDEX(email, '@', -1) AS domain,
    COUNT(*) AS message_count
FROM contacts
GROUP BY domain
ORDER BY message_count DESC;
-- Get featured projects
SELECT title, description, technologies 
FROM projects 
WHERE featured = TRUE 
ORDER BY display_order;
-- Get skills by category with progress bars
SELECT 
    name,
    category,
    proficiency,
    REPEAT('█', proficiency / 10) AS progress_bar
FROM skills
WHERE is_visible = TRUE
ORDER BY category, display_order;
-- Sample output:
-- | name       | category | proficiency | progress_bar |
-- |------------|----------|-------------|--------------|
-- | HTML       | frontend | 100         | ██████████   |
-- | CSS        | frontend | 100         | ██████████   |
-- | JavaScript | frontend | 90          | █████████    |
-- | Python     | backend  | 85          | ████████     |
```

**UPDATE —** Modifying Data

```
-- Mark a message as read
UPDATE contacts 
SET is_read = TRUE 
WHERE id = 1;
-- Mark all messages from today as read
UPDATE contacts 
SET is_read = TRUE 
WHERE DATE(created_at) = CURDATE();
-- Update skill proficiency
UPDATE skills 
SET proficiency = 80 
WHERE name = 'SQL';
-- Feature a project
UPDATE projects 
SET featured = TRUE, display_order = 1 
WHERE id = 3;
-- Update project with new technologies
UPDATE projects 
SET technologies = JSON_ARRAY_APPEND(technologies, '$', 'Kubernetes')
WHERE title = 'Cloud Portfolio';
```

**DELETE** — Removing Data

```
-- Delete a specific contact (hard delete)
DELETE FROM contacts WHERE id = 5;
-- Delete old read messages (older than 1 year)
DELETE FROM contacts 
WHERE is_read = TRUE 
  AND created_at < DATE_SUB(NOW(), INTERVAL 1 YEAR);
-- Delete visitor logs older than 90 days
DELETE FROM visitors 
WHERE visited_at < DATE_SUB(NOW(), INTERVAL 90 DAY);
-- ⚠️ CAREFUL! This deletes ALL data:
-- DELETE FROM contacts;  -- DANGEROUS!
-- TRUNCATE TABLE contacts;  -- Even faster, but no rollback
```

5 | Advanced SQL
----------------

### Understanding JOIN Types

JOINs combine data from multiple tables based on related columns. Each type returns different results depending on whether matches exist.

**INNER JOIN** returns only rows where matches exist in both tables. If a user has no orders, they won’t appear. If an order has no matching user (perhaps deleted), it won’t appear either. Use this when you need complete data from both sides.

```
SELECT users.name, orders.total
FROM users
INNER JOIN orders ON users.id = orders.user_id;
```

**LEFT JOIN** returns all rows from the left table, plus matching rows from the right table. Users without orders appear with NULL values for order columns. Use this when you need all records from your primary table regardless of whether related data exists.

```
SELECT users.name, orders.total
FROM users
LEFT JOIN orders ON users.id = orders.user_id;
```

**RIGHT JOIN** returns all rows from the right table, plus matching rows from the left table. This is the mirror of LEFT JOIN, orders appear even if their user was deleted. In practice, LEFT JOIN is more common and you can achieve the same result by swapping table order.

**FULL OUTER JOIN** returns all rows from both tables, with NULLs where no match exists. This is useful for finding orphaned records or reconciling data between systems.

Note that MySQL doesn’t support FULL OUTER JOIN directly, you’d need to combine LEFT and RIGHT JOINs with UNION.

### JOINs and Relationships

**Creating Related Tables**

Let’s add a more complex schema:

```
-- Users who can log in to manage portfolio
CREATE TABLE admin_users (
    id INT PRIMARY KEY AUTO_INCREMENT,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    role ENUM('admin', 'editor', 'viewer') DEFAULT 'viewer',
    last_login TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
-- Categories for projects
CREATE TABLE categories (
    id INT PRIMARY KEY AUTO_INCREMENT,
    name VARCHAR(100) NOT NULL,
    slug VARCHAR(100) UNIQUE NOT NULL,
    description TEXT,
    color VARCHAR(7) DEFAULT '#3B82F6'
);
-- Update projects to link to categories
ALTER TABLE projects 
ADD COLUMN category_id INT,
ADD FOREIGN KEY (category_id) REFERENCES categories(id);
-- Blog posts for portfolio
CREATE TABLE blog_posts (
    id INT PRIMARY KEY AUTO_INCREMENT,
    author_id INT NOT NULL,
    category_id INT,
    title VARCHAR(300) NOT NULL,
    slug VARCHAR(300) UNIQUE NOT NULL,
    content TEXT NOT NULL,
    excerpt VARCHAR(500),
    status ENUM('draft', 'published', 'archived') DEFAULT 'draft',
    view_count INT DEFAULT 0,
    published_at TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    
    FOREIGN KEY (author_id) REFERENCES admin_users(id),
    FOREIGN KEY (category_id) REFERENCES categories(id),
    
    INDEX idx_status (status),
    INDEX idx_slug (slug),
    FULLTEXT (title, content)
);
```

**JOIN Operations**

```
-- INNER JOIN: Get blog posts with author and category names
SELECT 
    bp.title,
    bp.status,
    au.username AS author,
    c.name AS category,
    bp.view_count,
    bp.published_at
FROM blog_posts bp
INNER JOIN admin_users au ON bp.author_id = au.id
INNER JOIN categories c ON bp.category_id = c.id
WHERE bp.status = 'published'
ORDER BY bp.published_at DESC;
-- LEFT JOIN: Get all categories with post counts (including empty categories)
SELECT 
    c.name AS category,
    c.color,
    COUNT(bp.id) AS post_count
FROM categories c
LEFT JOIN blog_posts bp ON c.id = bp.category_id AND bp.status = 'published'
GROUP BY c.id, c.name, c.color
ORDER BY post_count DESC;
-- Multiple JOINs: Get user activity summary
SELECT 
    au.username,
    au.role,
    COUNT(DISTINCT bp.id) AS posts_written,
    SUM(bp.view_count) AS total_views,
    MAX(bp.published_at) AS last_post_date
FROM admin_users au
LEFT JOIN blog_posts bp ON au.id = bp.author_id
GROUP BY au.id, au.username, au.role
ORDER BY posts_written DESC;
```

### Visual Guide to JOINs

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         SQL JOIN TYPES                                  │
├─────────────────────────────────────────────────────────────────────────┤
│                                                                         │
│   Table A             Table B                                           │
│   ┌─────┐             ┌─────┐                                           │
│   │  1  │             │  1  │                                           │
│   │  2  │             │  2  │                                           │
│   │  3  │             │  4  │                                           │
│   └─────┘             └─────┘                                           │
│                                                                         │
│   INNER JOIN          LEFT JOIN           RIGHT JOIN       FULL JOIN    │
│   (Matching only)     (All from A)        (All from B)     (All rows)   │
│                                                                         │
│   ┌─────┐             ┌─────┐             ┌─────┐          ┌─────┐      │
│   │  1  │             │  1  │             │  1  │          │  1  │      │
│   │  2  │             │  2  │             │  2  │          │  2  │      │
│   └─────┘             │  3  │──NULL       │  4  │          │  3  │      │
│                       └─────┘             └─────┘          │  4  │      │
│                                                            └─────┘      │
│                                                                         │
│   Returns: 1, 2       Returns: 1, 2, 3    Returns: 1, 2, 4  Returns all │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘

-- INNER JOIN: Only matching records
SELECT * FROM users u
INNER JOIN orders o ON u.id = o.user_id;
-- LEFT JOIN: All users, even with no orders
SELECT * FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
-- RIGHT JOIN: All orders, even with deleted users
SELECT * FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

6 | Query Optimization for Cloud Performance
--------------------------------------------

### Indexing Strategies

Indexes make queries fast, but too many slow down writes:

```
-- Check existing indexes
SHOW INDEX FROM contacts;
-- Create indexes for common query patterns
CREATE INDEX idx_contacts_email ON contacts(email);
CREATE INDEX idx_contacts_created_read ON contacts(created_at, is_read);
CREATE INDEX idx_projects_featured_order ON projects(featured, display_order);
-- Composite index for filtered + sorted queries
-- Supports: WHERE category = X ORDER BY display_order
CREATE INDEX idx_skills_category_order ON skills(category, display_order);
-- Full-text index for search
ALTER TABLE blog_posts ADD FULLTEXT INDEX ft_search (title, content);
-- Use full-text search
SELECT title, excerpt 
FROM blog_posts 
WHERE MATCH(title, content) AGAINST('kubernetes cloud' IN NATURAL LANGUAGE MODE)
ORDER BY view_count DESC
LIMIT 10;
```

**Query Analysis with EXPLAIN**

```
-- Analyze query performance
EXPLAIN SELECT * FROM contacts WHERE email = 'jabulani@example.com';
-- EXPLAIN output:
-- +----+-------------+----------+------+---------------+-----------+---------+-------+------+-------+
-- | id | select_type | table    | type | possible_keys | key       | key_len | ref   | rows | Extra |
-- +----+-------------+----------+------+---------------+-----------+---------+-------+------+-------+
-- |  1 | SIMPLE      | contacts | ref  | idx_email     | idx_email | 1022    | const |    1 | NULL  |
-- +----+-------------+----------+------+---------------+-----------+---------+-------+------+-------+
-- type: ref = using index ✓
-- rows: 1 = very efficient ✓
-- Compare to query without index:
-- type: ALL = full table scan ✗
-- rows: 10000 = scanning everything ✗
```

Pagination Best Practices

```
-- ❌ BAD: OFFSET pagination (slow for large datasets)
SELECT * FROM visitors ORDER BY id LIMIT 20 OFFSET 10000;
-- MySQL must read 10,020 rows and throw away 10,000
-- ✅ GOOD: Cursor-based pagination (fast at any depth)
SELECT * FROM visitors 
WHERE id > 10000  -- Last seen ID
ORDER BY id 
LIMIT 20;
-- Only reads 20 rows
-- ✅ GOOD: Keyset pagination for complex sorts
SELECT * FROM blog_posts
WHERE (view_count, id) < (500, 42)  -- Previous page last values
ORDER BY view_count DESC, id DESC
LIMIT 20;
```

**Query Optimization Tips**

```
-- ❌ BAD: Selecting everything
SELECT * FROM projects;
-- ✅ GOOD: Select only needed columns
SELECT id, title, image_url FROM projects WHERE featured = TRUE;
-- ❌ BAD: N+1 query problem
-- In a loop: SELECT * FROM categories WHERE id = ?
-- ✅ GOOD: Single query with JOIN
SELECT p.*, c.name AS category_name
FROM projects p
LEFT JOIN categories c ON p.category_id = c.id;
-- ❌ BAD: Functions on indexed columns prevent index use
SELECT * FROM contacts WHERE YEAR(created_at) = 2024;
-- ✅ GOOD: Use range for date filtering
SELECT * FROM contacts 
WHERE created_at >= '2024-01-01' AND created_at < '2025-01-01';
```

7 | Cloud Database Management
-----------------------------

### Backup and Recovery

```
-- MySQL backup (command line)
mysqldump -u username -p portfolio_db > backup_$(date +%Y%m%d).sql
-- Backup specific tables
mysqldump -u username -p portfolio_db contacts projects > partial_backup.sql
-- Restore from backup
mysql -u username -p portfolio_db < backup_20240122.sql
-- In AWS RDS, backups are automatic!
-- Configure retention period in RDS console (1-35 days)
```

**Database Monitoring Queries**

```
-- Check database and table sizes
SELECT 
    table_schema AS 'Database',
    table_name AS 'Table',
    ROUND(data_length / 1024 / 1024, 2) AS 'Data (MB)',
    ROUND(index_length / 1024 / 1024, 2) AS 'Index (MB)',
    table_rows AS 'Rows'
FROM information_schema.tables 
WHERE table_schema = 'portfolio_db'
ORDER BY data_length DESC;
-- Find slow queries (if slow query log is enabled)
SELECT 
    query_time,
    lock_time,
    rows_examined,
    sql_text
FROM mysql.slow_log
ORDER BY query_time DESC
LIMIT 10;
-- Check current connections
SHOW PROCESSLIST;
-- Check table status
SHOW TABLE STATUS FROM portfolio_db;
```

**User Management and Security**

```
-- Create application user with limited permissions
CREATE USER 'portfolio_app'@'%' IDENTIFIED BY 'secure_password_here';
GRANT SELECT, INSERT, UPDATE, DELETE ON portfolio_db.* TO 'portfolio_app'@'%';
-- Create read-only user for analytics
CREATE USER 'analytics_reader'@'%' IDENTIFIED BY 'another_secure_password';
GRANT SELECT ON portfolio_db.* TO 'analytics_reader'@'%';
-- Create admin user
CREATE USER 'db_admin'@'localhost' IDENTIFIED BY 'admin_password';
GRANT ALL PRIVILEGES ON portfolio_db.* TO 'db_admin'@'localhost';
-- Apply permission changes
FLUSH PRIVILEGES;
-- View user permissions
SHOW GRANTS FOR 'portfolio_app'@'%';
```

9 | SQL vs NoSQL
----------------

### Choosing the Right Tool

The SQL versus NoSQL debate isn’t about which is better, it’s about which fits your specific use case.

### Choose SQL When:

**Data integrity is critical.**

Financial transactions, healthcare records, and e-commerce orders require ACID compliance.

SQL databases guarantee that transactions are **atomic** (all or nothing), **consistent** (valid state to valid state), **isolated** (concurrent transactions don’t interfere), and **durable** (committed data survives crashes).

Relationships are complex. When users have orders, orders have items, items reference products, and products belong to categories, SQL’s JOIN operations elegantly query across these relationships.

You need powerful queries. Aggregations, groupings, window functions, and complex filtering come naturally to SQL. Reporting and analytics workloads leverage decades of SQL optimization.

Your schema is stable. When you know your data structure and it won’t change frequently, SQL’s rigid schema catches errors early and enforces consistency.

### Choose NoSQL When:

**Scale is massive.**

NoSQL databases like MongoDB, Cassandra, and DynamoDB distribute data across many servers more easily than traditional SQL databases.

Schema flexibility matters. Startups iterating rapidly, content management systems with varied document types, and applications integrating diverse data sources benefit from NoSQL’s flexible schemas.

Write volume is extreme. IoT sensors, real-time gaming, and chat applications generate high write throughput. Some NoSQL databases optimize specifically for this pattern.

Caching and sessions. Redis excels at storing session data, rate limiting counters, and caching frequently accessed data with sub-millisecond response times.

### The Hybrid Reality

Most production systems use both. SQL serves as the source of truth for critical business data, while NoSQL handles specialized access patterns. A blog might store posts in PostgreSQL but cache rendered HTML in Redis and index content in Elasticsearch for full-text search.

10 | Update Your Learning Journal
---------------------------------

```
## Day 9: SQL - The Language of Data
### What I Learned:
- SQL is the universal language for relational databases
- Cloud providers offer managed database services (RDS, Cloud SQL)
- CRUD operations: Create, Read, Update, Delete
- Database design with primary keys, foreign keys, and indexes
- JOINs to combine data from related tables
- Query optimization with EXPLAIN and proper indexing
- Security with user permissions and prepared statements
### Database Skills Mastered:
- Creating databases and tables with proper schemas
- Writing INSERT, SELECT, UPDATE, DELETE queries
- Using JOINs (INNER, LEFT, RIGHT) for related data
- Aggregations (COUNT, SUM, AVG, GROUP BY)
- Indexing strategies for performance
- Connection pooling in application code
### Portfolio Database Added:
- ✅ Installed MySQL on Linux server
- ✅ Created portfolio_db database
- ✅ Added contacts table for form submissions
- ✅ Added page_views table for analytics
- ✅ Updated Flask API with database integration
- ✅ Contact form now saves to database!
### Key SQL Concepts:
1. **ACID Compliance**: Transactions are Atomic, Consistent, Isolated, Durable
2. **Normalization**: Organizing data to reduce redundancy
3. **Indexes**: Speed up reads, but slow down writes
4. **Foreign Keys**: Enforce relationships between tables
5. **Prepared Statements**: Prevent SQL injection attacks
### Challenges Faced:
- Initially confused about JOIN types
  - Learned: Draw Venn diagrams to understand what each returns
- Query was slow on large dataset
  - Fixed: Added appropriate indexes, used EXPLAIN
- SQL injection vulnerability
  - Fixed: Always use parameterized queries
### My Portfolio Now Has:
- ✅ Frontend (HTML, CSS, JavaScript)
- ✅ Backend (Python Flask API)
- ✅ Version Control (Git + GitHub)
- ✅ Cloud Hosting (Linux + Nginx)
- ✅ Database (MySQL) - NEW!
- ⬜ Container Orchestration (Kubernetes)
### Next Steps:
Learn Kubernetes for container orchestration and scaling
```

Portfolio Project Progress Tracker
----------------------------------

✅ Completed: English, Mathematics, HTML, CSS, JavaScript, Python, Git, Linux, SQL
⬜ Next: Kubernetes for container orchestration

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
│ ✅ Linux        → Deployed to the cloud                        │
│ ✅ SQL          → DATABASE INTEGRATION! 🎉                     │
│ ⬜ Kubernetes   → Container orchestration (next!)              │
└────────────────────────────────────────────────────────────────┘
Progress: 9/10 skills (90% of foundational languages!)
```

### Final Thoughts

**You’ve Added Memory to Your Application**

Think about what you’ve accomplished. Before SQL, your portfolio was like a goldfish, beautiful but forgetful. Every time the server restarted, every piece of data vanished into nothing.

Now your portfolio remembers. Contact form submissions are saved permanently. Page views are tracked for analytics. You can query historical data anytime you want. This is the difference between a demo and a real application. Real applications persist data.

Concluding Remarks
------------------

Every cloud application you’ll ever work on will have a database. Whether you’re building APIs that store user data, analyzing logs and metrics, creating dashboards with historical data, or designing microservices architectures, SQL will be there.

The concepts you learned today apply across every major platform. AWS RDS, Aurora, and Redshift. Azure SQL and Cosmos DB. Google Cloud SQL, BigQuery, and Spanner.

Every traditional database you’ll encounter in your career. The syntax might vary slightly, but the fundamental concepts of tables, relationships, queries, and optimization remain constant.

### The Full Stack is Complete

Look at what you’ve built: frontend to backend to database to cloud server. Each layer connects to the next. Each piece serves a purpose. You now have a production-ready, full-stack application deployed to the cloud.

Not a tutorial project. Not a localhost demo. A real application that anyone in the world can visit, submit a contact form, and have their message saved permanently.

That’s not nothing. That’s everything.

![SQL — The Structured Query Language](https://miro.medium.com/v2/resize:fit:1400/format:webp/1*hL9QjJNH1TsM1vzeeq3u2Q.png)

Additional Resources
--------------------

### [Cloud Glossary](https://medium.com/list/cloud-glossary-528956a3c181)

> Cloud Computing Simplified: A Cloud Glossary For Beginners

### [Cloud Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34703)

> In a world where nearly every industry is moving toward cloud-first strategies, having a baseline understanding of these principles is no longer optional but essential.

### [AI Practitioner Exam Guide](https://dev.to/ntombizakhona/series/34979)

> Whether you’re an Executive, Developer, Engineer, or Project Manager, having a baseline understanding of AI principles is no longer a _nice to have_, it’s **absolutely essential.**

### [SQLBolt](https://sqlbolt.com/)

> The best interactive tutorial for beginners. Each lesson introduces a concept with clear explanations, then provides hands-on exercises with immediate feedback. You’ll progress from basic SELECT statements through JOINs, aggregations, and subqueries. Complete this before anything else.

### [Mode Analytics SQL Tutorial](https://mode.com/sql-tutorial/)

> The most comprehensive free guide for intermediate learners. After SQLBolt gives you fundamentals, Mode deepens your understanding with real-world scenarios, window functions, and performance optimization. Their practice problems use actual datasets.

### [LeetCode Database Problems](https://leetcode.com/problemset/database/)

> _The best way to prepare for technical interviews. These problems range from easy to hard, and solving them builds the pattern recognition you need to write efficient queries under pressure. Start with easy problems and work up._

# The Original

**Blog:** [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona)
<br>
**Article Link:** [SQL in the Cloud](https://medium.com/@ntombizakhona/sql-in-the-cloud-a2942dc80a32)
<br>
Originally Published by [Ntombizakhona Mabaso](https://medium.com/@ntombizakhona) 
<br>
**04 February 2026**
