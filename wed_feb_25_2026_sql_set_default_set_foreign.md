# Copilot Chat Conversation Export: ```sql -- SET DEFAULT: Set foreign

**User:** @meet41
**Thread URL:** https://github.com/copilot/c/6d4ea0ce-6d24-4133-a9f7-11c1ea7a5991

## @meet41

Week 4: Database (SQL)
1. Overview
This week focuses on building a strong foundation in SQL and relational databases, which are essential for almost every backend application. Interns will learn how databases store data, how to write queries to fetch/update records, how to connect tables using joins, and how to ensure performance and security using indexes and best practices. They will also get introduced to ORM concepts to understand how databases are used inside Python applications.
2. Topics (This Week)
1 Database Basics (SQL)
🔹 What is a Relational Database?
Concept of RDBMS
Structured data storage in tables
Relationships between tables
Examples: MySQL, PostgreSQL
🔹 Tables, Rows & Columns
Table structure
Records (rows)
Fields (columns)
Schema definition
2 Keys & Constraints
🔹 Primary Key
Unique identifier for each row
Auto-increment concept
🔹 Foreign Key
Relationship between two tables
Referential integrity
Type of FKs
On Delete Actions
🔹 Constraints
NOT NULL
UNIQUE
CHECK
DEFAULT
AUTO_INCREMENT (MySQL)
SERIAL / BIGSERIAL (PostgreSQL)
3 SQL Data Types
Common Data Types
INT, BIGINT
VARCHAR, TEXT
DATE, TIME, TIMESTAMP
BOOLEAN
FLOAT, DECIMAL
4 CRUD Operations
🔹 CREATE
Insert new records
Bulk insert
🔹 READ
SELECT queries
Filtering records
🔹 UPDATE
Update specific rows
Conditional updates
🔹 DELETE
Safe deletion using WHERE
Soft delete concept
5 Query Filtering & Sorting
WHERE
AND, OR
IN, BETWEEN
LIKE, ILIKE (PostgreSQL)
ORDER BY
LIMIT
OFFSET
6 Aggregate Functions
COUNT()
SUM()
AVG()
MIN()
MAX()
Grouping Data
GROUP BY
HAVING
7 Joins (Very Important)
INNER JOIN
LEFT JOIN
RIGHT JOIN
FULL JOIN
Self Join
Understanding:
One-to-One
One-to-Many
Many-to-Many relationships
8 Subqueries & Advanced Queries
Subqueries in SELECT
Subqueries in WHERE
Correlated subqueries
Common Table Expressions (CTE – PostgreSQL)
9 Indexes & Performance Basics
What is an Index?
How indexing improves performance
When not to use the index
Composite index
Unique index
EXPLAIN query analysis (PostgreSQL & MySQL)
10 Transactions & ACID Properties
ACID Concepts:
Atomicity
Consistency
Isolation
Durability
Transaction Commands
BEGIN
COMMIT
ROLLBACK
Isolation Levels
Read Uncommitted
Read Committed
Repeatable Read
Serializable
11 Stored Procedures & Functions
What are stored procedures?
Creating procedures (MySQL)
Functions in PostgreSQL
Triggers (PostgreSQL introduction)
12 Views
What is a View?
Creating and using views
Use cases for abstraction
Materialized Views (PostgreSQL)
13 Database Normalization
1NF
2NF
3NF
When to denormalize
14 SQL Injection & Security Basics
What is SQL Injection?
Unsafe query example
Parameterized queries
Prepared statements
ORM protection
Role-based access (PostgreSQL roles)
15 PostgreSQL
PostgreSQL Architecture Overview
Schemas
Extensions (e.g., pgcrypto, PostGIS – intro)
JSONB indexing
Full-text search
Window functions
Partitioning
VACUUM & Analyze
    PostgreSQL-Specific Data Types
JSON & JSONB
UUID
ARRAY
ENUM
HSTORE
16 ORM (Object Relational Mapping) 
What is ORM?
Mapping Python classes to database tables
Reducing raw SQL usage
Concepts
Models
Migrations
Relationships
Lazy vs Eager loading
Query optimisation in ORM
Examples
SQLAlchemy
Note: Please check the ORM basics. The practical concepts should be covered in both Flask and    FastAPI.


3. Learning Activities
Mini task: User Management
Practice goal: Daily 10–15 queries + 1 mini database task
4. Assessment / Quick Practice Test
Assessment Mode: SQL + ORM Test
Timeline: Will be conducted at the end of the week
Question 1: E-Commerce Order Analytics System
📌 Scenario
You are designing a small e-commerce database with the following tables:
users
products
orders
order_items
payments

🧠 Tasks
Part A – Database Design
Create all tables with:
Primary keys
Foreign keys
Proper data types
Constraints (NOT NULL, UNIQUE, DEFAULT, CHECK)
Ensure:
A user can place multiple orders.
Each order can have multiple products.
Payment must exist for every completed order.
Add proper indexes for performance.

Part B – Query Logic
Write queries for:
Get the top 5 users who spent the most money.
Find products that were never ordered.
Find users who placed more than 3 orders in the last 30 days.
Get total revenue per month.
Find the average order value per user.

Part C – Advanced
Use a subquery to find products priced above average.
Use GROUP BY + HAVING to find products sold more than 50 times.
Create a view called monthly_sales_summary.
Use a transaction to:
Create order
Insert order items
Insert payment
Rollback if payment fails.
Explain how SQL Injection can happen in the login system and how to prevent it.
Question 2: Employee Payroll & Department Analytics
📌 Scenario
Design a system for a company with tables:
employees
departments
attendance
salary
bonuses

🧠 Tasks
Part A – Design
Normalize the schema up to 3NF.
Add foreign keys between employees and departments.
Add unique constraints where necessary.
Create indexes on frequently searched columns.

Part B – Logical Queries
Find the department with the highest average salary.
Find employees who never taken leave.
Find employees earning more than their department average.
Get the monthly salary expenditure.
Find the top 3 highest-paid employees per department.

Part C – Advanced
Use a correlated subquery.
Use LEFT JOIN to find departments with no employees.
Create a stored procedure to:
Calculate monthly payroll
Insert into payroll_history table.
Create a trigger:
When salary is updated, log the old & new values.
Use EXPLAIN to optimise a slow query.
Question 3: Social Media Platform Analytics
📌 Scenario
You are building a simplified social media database:
users
posts
comments
likes
followers

🧠 Tasks
Part A – Design
Design tables with:
Proper constraints
Foreign keys
Default values
Ensure:
A user cannot like the same post twice.
A user cannot follow themselves.
Part B – Logical Queries
Find the top 5 most liked posts.
Find users who have more than 100 followers.
Find posts with zero comments.
Find the most active user (based on posts + comments).
Get the daily active users count.
Part C – Advanced
Use GROUP BY + HAVING.
Use a subquery to find users who liked all the posts of a specific user.
Create a view: user_engagement_summary.
Use transaction to:
Insert post
Insert hashtags
Rollback if hashtag insert fails.
Add a composite index and explain why.
5. Supporting Resources
MySQL Tutorial (W3Schools)
https://www.w3schools.com/MySQL/default.asp
PostgreSQL Official Documentation
https://www.postgresql.org/docs/
solve  the practise assignment task and give proper structure sql query for each one

## Copilot

I'll help you solve all three practice assignment tasks with properly structured SQL queries. Let me provide comprehensive solutions for each question.

# **Question 1: E-Commerce Order Analytics System**

## **Part A – Database Design**

```sql name=ecommerce_schema.sql
-- ============================================
-- E-COMMERCE DATABASE SCHEMA
-- ============================================

-- Users Table
CREATE TABLE users (
    user_id BIGSERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    full_name VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE,
    CONSTRAINT chk_email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$')
);

-- Products Table
CREATE TABLE products (
    product_id BIGSERIAL PRIMARY KEY,
    product_name VARCHAR(200) NOT NULL,
    description TEXT,
    price DECIMAL(10, 2) NOT NULL,
    stock_quantity INT NOT NULL DEFAULT 0,
    category VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE,
    CONSTRAINT chk_price_positive CHECK (price >= 0),
    CONSTRAINT chk_stock_non_negative CHECK (stock_quantity >= 0)
);

-- Orders Table
CREATE TABLE orders (
    order_id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    status VARCHAR(20) DEFAULT 'pending',
    total_amount DECIMAL(10, 2) NOT NULL DEFAULT 0,
    shipping_address TEXT NOT NULL,
    CONSTRAINT fk_user FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    CONSTRAINT chk_status CHECK (status IN ('pending', 'processing', 'shipped', 'delivered', 'cancelled')),
    CONSTRAINT chk_total_amount CHECK (total_amount >= 0)
);

-- Order Items Table (Junction table for Many-to-Many relationship)
CREATE TABLE order_items (
    order_item_id BIGSERIAL PRIMARY KEY,
    order_id BIGINT NOT NULL,
    product_id BIGINT NOT NULL,
    quantity INT NOT NULL,
    unit_price DECIMAL(10, 2) NOT NULL,
    subtotal DECIMAL(10, 2) NOT NULL,
    CONSTRAINT fk_order FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE,
    CONSTRAINT fk_product FOREIGN KEY (product_id) REFERENCES products(product_id) ON DELETE RESTRICT,
    CONSTRAINT chk_quantity_positive CHECK (quantity > 0),
    CONSTRAINT chk_unit_price_positive CHECK (unit_price >= 0),
    CONSTRAINT chk_subtotal_positive CHECK (subtotal >= 0)
);

-- Payments Table
CREATE TABLE payments (
    payment_id BIGSERIAL PRIMARY KEY,
    order_id BIGINT NOT NULL UNIQUE, -- One payment per order
    payment_method VARCHAR(50) NOT NULL,
    payment_status VARCHAR(20) DEFAULT 'pending',
    amount DECIMAL(10, 2) NOT NULL,
    transaction_id VARCHAR(100) UNIQUE,
    payment_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_payment_order FOREIGN KEY (order_id) REFERENCES orders(order_id) ON DELETE CASCADE,
    CONSTRAINT chk_payment_status CHECK (payment_status IN ('pending', 'completed', 'failed', 'refunded')),
    CONSTRAINT chk_payment_method CHECK (payment_method IN ('credit_card', 'debit_card', 'paypal', 'upi', 'net_banking')),
    CONSTRAINT chk_amount_positive CHECK (amount > 0)
);

-- ============================================
-- INDEXES FOR PERFORMANCE OPTIMIZATION
-- ============================================

-- User indexes
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_users_created_at ON users(created_at);

-- Product indexes
CREATE INDEX idx_products_category ON products(category);
CREATE INDEX idx_products_price ON products(price);
CREATE INDEX idx_products_name ON products(product_name);

-- Order indexes
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_orders_order_date ON orders(order_date);
CREATE INDEX idx_orders_status ON orders(status);
CREATE INDEX idx_orders_user_date ON orders(user_id, order_date); -- Composite index

-- Order items indexes
CREATE INDEX idx_order_items_order_id ON order_items(order_id);
CREATE INDEX idx_order_items_product_id ON order_items(product_id);

-- Payment indexes
CREATE INDEX idx_payments_order_id ON payments(order_id);
CREATE INDEX idx_payments_status ON payments(payment_status);
CREATE INDEX idx_payments_date ON payments(payment_date);
```

## **Part B – Query Logic**

```sql name=ecommerce_queries.sql
-- ============================================
-- QUERY 1: Top 5 users who spent the most money
-- ============================================
SELECT 
    u.user_id,
    u.username,
    u.full_name,
    u.email,
    COUNT(DISTINCT o.order_id) AS total_orders,
    SUM(p.amount) AS total_spent
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
INNER JOIN payments p ON o.order_id = p.order_id
WHERE p.payment_status = 'completed'
GROUP BY u.user_id, u.username, u.full_name, u.email
ORDER BY total_spent DESC
LIMIT 5;

-- ============================================
-- QUERY 2: Find products that were never ordered
-- ============================================
SELECT 
    p.product_id,
    p.product_name,
    p.price,
    p.category,
    p.stock_quantity
FROM products p
LEFT JOIN order_items oi ON p.product_id = oi.product_id
WHERE oi.product_id IS NULL
    AND p.is_active = TRUE
ORDER BY p.product_name;

-- Alternative using NOT EXISTS (More efficient for large datasets)
SELECT 
    p.product_id,
    p.product_name,
    p.price,
    p.category
FROM products p
WHERE NOT EXISTS (
    SELECT 1 
    FROM order_items oi 
    WHERE oi.product_id = p.product_id
)
AND p.is_active = TRUE;

-- ============================================
-- QUERY 3: Users who placed more than 3 orders in the last 30 days
-- ============================================
SELECT 
    u.user_id,
    u.username,
    u.full_name,
    u.email,
    COUNT(o.order_id) AS orders_count,
    MIN(o.order_date) AS first_order_date,
    MAX(o.order_date) AS last_order_date
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
WHERE o.order_date >= CURRENT_DATE - INTERVAL '30 days'
GROUP BY u.user_id, u.username, u.full_name, u.email
HAVING COUNT(o.order_id) > 3
ORDER BY orders_count DESC;

-- ============================================
-- QUERY 4: Total revenue per month
-- ============================================
SELECT 
    TO_CHAR(p.payment_date, 'YYYY-MM') AS month,
    EXTRACT(YEAR FROM p.payment_date) AS year,
    EXTRACT(MONTH FROM p.payment_date) AS month_number,
    COUNT(DISTINCT p.order_id) AS total_orders,
    SUM(p.amount) AS total_revenue,
    AVG(p.amount) AS average_order_value
FROM payments p
WHERE p.payment_status = 'completed'
GROUP BY 
    TO_CHAR(p.payment_date, 'YYYY-MM'),
    EXTRACT(YEAR FROM p.payment_date),
    EXTRACT(MONTH FROM p.payment_date)
ORDER BY year DESC, month_number DESC;

-- ============================================
-- QUERY 5: Average order value per user
-- ============================================
SELECT 
    u.user_id,
    u.username,
    u.full_name,
    COUNT(DISTINCT o.order_id) AS total_orders,
    SUM(p.amount) AS total_spent,
    AVG(p.amount) AS average_order_value,
    MIN(p.amount) AS min_order_value,
    MAX(p.amount) AS max_order_value
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
INNER JOIN payments p ON o.order_id = p.order_id
WHERE p.payment_status = 'completed'
GROUP BY u.user_id, u.username, u.full_name
ORDER BY average_order_value DESC;
```

## **Part C – Advanced**

```sql name=ecommerce_advanced.sql
-- ============================================
-- ADVANCED QUERY 1: Products priced above average (Subquery)
-- ============================================
SELECT 
    p.product_id,
    p.product_name,
    p.price,
    p.category,
    (SELECT ROUND(AVG(price)::numeric, 2) FROM products WHERE is_active = TRUE) AS avg_price,
    ROUND((p.price - (SELECT AVG(price) FROM products WHERE is_active = TRUE))::numeric, 2) AS price_difference
FROM products p
WHERE p.price > (SELECT AVG(price) FROM products WHERE is_active = TRUE)
    AND p.is_active = TRUE
ORDER BY p.price DESC;

-- ============================================
-- ADVANCED QUERY 2: Products sold more than 50 times (GROUP BY + HAVING)
-- ============================================
SELECT 
    p.product_id,
    p.product_name,
    p.category,
    p.price,
    SUM(oi.quantity) AS total_quantity_sold,
    COUNT(DISTINCT oi.order_id) AS number_of_orders,
    SUM(oi.subtotal) AS total_revenue
FROM products p
INNER JOIN order_items oi ON p.product_id = oi.product_id
INNER JOIN orders o ON oi.order_id = o.order_id
INNER JOIN payments pay ON o.order_id = pay.order_id
WHERE pay.payment_status = 'completed'
GROUP BY p.product_id, p.product_name, p.category, p.price
HAVING SUM(oi.quantity) > 50
ORDER BY total_quantity_sold DESC;

-- ============================================
-- ADVANCED QUERY 3: Create View - Monthly Sales Summary
-- ============================================
CREATE OR REPLACE VIEW monthly_sales_summary AS
SELECT 
    TO_CHAR(p.payment_date, 'YYYY-MM') AS month,
    EXTRACT(YEAR FROM p.payment_date) AS year,
    EXTRACT(MONTH FROM p.payment_date) AS month_number,
    COUNT(DISTINCT p.order_id) AS total_orders,
    COUNT(DISTINCT o.user_id) AS unique_customers,
    SUM(p.amount) AS total_revenue,
    AVG(p.amount) AS avg_order_value,
    SUM(oi.quantity) AS total_items_sold
FROM payments p
INNER JOIN orders o ON p.order_id = o.order_id
INNER JOIN order_items oi ON o.order_id = oi.order_id
WHERE p.payment_status = 'completed'
GROUP BY 
    TO_CHAR(p.payment_date, 'YYYY-MM'),
    EXTRACT(YEAR FROM p.payment_date),
    EXTRACT(MONTH FROM p.payment_date);

-- Query the view
SELECT * FROM monthly_sales_summary ORDER BY year DESC, month_number DESC;

-- ============================================
-- ADVANCED QUERY 4: Transaction Example
-- ============================================
-- Create order with transaction (Atomicity ensured)
DO $$
DECLARE
    v_user_id BIGINT := 1;
    v_order_id BIGINT;
    v_product_id BIGINT := 1;
    v_quantity INT := 2;
    v_unit_price DECIMAL(10, 2);
    v_subtotal DECIMAL(10, 2);
    v_total_amount DECIMAL(10, 2);
    v_payment_method VARCHAR(50) := 'credit_card';
BEGIN
    -- Start transaction (implicit in DO block)
    BEGIN
        -- Step 1: Create the order
        INSERT INTO orders (user_id, shipping_address, status)
        VALUES (v_user_id, '123 Main St, City, Country', 'pending')
        RETURNING order_id INTO v_order_id;
        
        RAISE NOTICE 'Order created: %', v_order_id;
        
        -- Step 2: Get product price
        SELECT price INTO v_unit_price
        FROM products
        WHERE product_id = v_product_id;
        
        -- Calculate subtotal
        v_subtotal := v_unit_price * v_quantity;
        
        -- Step 3: Insert order items
        INSERT INTO order_items (order_id, product_id, quantity, unit_price, subtotal)
        VALUES (v_order_id, v_product_id, v_quantity, v_unit_price, v_subtotal);
        
        RAISE NOTICE 'Order items added';
        
        -- Calculate total amount
        SELECT SUM(subtotal) INTO v_total_amount
        FROM order_items
        WHERE order_id = v_order_id;
        
        -- Update order total
        UPDATE orders
        SET total_amount = v_total_amount
        WHERE order_id = v_order_id;
        
        -- Step 4: Process payment
        INSERT INTO payments (order_id, payment_method, amount, payment_status)
        VALUES (v_order_id, v_payment_method, v_total_amount, 'completed');
        
        RAISE NOTICE 'Payment processed successfully';
        
        -- Update order status
        UPDATE orders
        SET status = 'processing'
        WHERE order_id = v_order_id;
        
        RAISE NOTICE 'Transaction completed successfully';
        
    EXCEPTION
        WHEN OTHERS THEN
            -- Rollback happens automatically
            RAISE NOTICE 'Transaction failed: %', SQLERRM;
            RAISE EXCEPTION 'Payment processing failed';
    END;
END $$;

-- ============================================
-- ADVANCED QUERY 5: SQL Injection Prevention
-- ============================================

-- ❌ UNSAFE: Vulnerable to SQL Injection
-- Example of vulnerable code (DO NOT USE):
/*
Python example:
username = request.form['username']
password = request.form['password']

# DANGEROUS - SQL Injection vulnerable
query = f"SELECT * FROM users WHERE username = '{username}' AND password = '{password}'"

Attack example:
username: admin' OR '1'='1' --
password: anything

Resulting query:
SELECT * FROM users WHERE username = 'admin' OR '1'='1' --' AND password = 'anything'
This returns all users!
*/

-- ✅ SAFE: Using Parameterized Queries
/*
Python example with parameterized query:

# Using psycopg2
cursor.execute(
    "SELECT * FROM users WHERE username = %s AND password = %s",
    (username, password)
)

# Using SQLAlchemy ORM
user = session.query(User).filter(
    User.username == username,
    User.password == password
).first()

Prevention Methods:
1. Parameterized Queries / Prepared Statements
2. ORM (SQLAlchemy automatically escapes)
3. Input Validation & Sanitization
4. Least Privilege Principle (database user permissions)
5. Stored Procedures
6. Web Application Firewall (WAF)
*/

-- PostgreSQL Prepared Statement Example
PREPARE get_user_safe (VARCHAR, VARCHAR) AS
    SELECT user_id, username, email, full_name
    FROM users
    WHERE username = $1 AND email = $2;

-- Execute safely
EXECUTE get_user_safe('john_doe', 'john@example.com');

-- Cleanup
DEALLOCATE get_user_safe;

-- Additional Security: Role-Based Access Control
CREATE ROLE ecommerce_app_user WITH LOGIN PASSWORD 'secure_password';
GRANT SELECT, INSERT, UPDATE ON users, products, orders, order_items, payments TO ecommerce_app_user;
REVOKE DELETE ON users FROM ecommerce_app_user; -- Prevent deletion
```

---

# **Question 2: Employee Payroll & Department Analytics**

## **Part A – Design**

```sql name=payroll_schema.sql
-- ============================================
-- EMPLOYEE PAYROLL DATABASE SCHEMA (3NF)
-- ============================================

-- Departments Table
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL UNIQUE,
    department_code VARCHAR(20) NOT NULL UNIQUE,
    location VARCHAR(100),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE
);

-- Employees Table
CREATE TABLE employees (
    employee_id BIGSERIAL PRIMARY KEY,
    employee_code VARCHAR(20) NOT NULL UNIQUE,
    first_name VARCHAR(50) NOT NULL,
    last_name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    phone VARCHAR(20),
    department_id INT NOT NULL,
    hire_date DATE NOT NULL,
    job_title VARCHAR(100) NOT NULL,
    is_active BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_department FOREIGN KEY (department_id) REFERENCES departments(department_id) ON DELETE RESTRICT,
    CONSTRAINT chk_email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$'),
    CONSTRAINT chk_hire_date CHECK (hire_date <= CURRENT_DATE)
);

-- Salary Table (Normalized - current salary per employee)
CREATE TABLE salary (
    salary_id BIGSERIAL PRIMARY KEY,
    employee_id BIGINT NOT NULL UNIQUE, -- One current salary per employee
    base_salary DECIMAL(12, 2) NOT NULL,
    effective_from DATE NOT NULL,
    currency VARCHAR(3) DEFAULT 'USD',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_salary_employee FOREIGN KEY (employee_id) REFERENCES employees(employee_id) ON DELETE CASCADE,
    CONSTRAINT chk_base_salary_positive CHECK (base_salary > 0)
);

-- Salary History Table (Track changes)
CREATE TABLE salary_history (
    history_id BIGSERIAL PRIMARY KEY,
    employee_id BIGINT NOT NULL,
    old_salary DECIMAL(12, 2),
    new_salary DECIMAL(12, 2) NOT NULL,
    change_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    changed_by VARCHAR(100),
    reason TEXT,
    CONSTRAINT fk_salary_history_employee FOREIGN KEY (employee_id) REFERENCES employees(employee_id) ON DELETE CASCADE
);

-- Attendance Table
CREATE TABLE attendance (
    attendance_id BIGSERIAL PRIMARY KEY,
    employee_id BIGINT NOT NULL,
    attendance_date DATE NOT NULL,
    check_in_time TIME,
    check_out_time TIME,
    status VARCHAR(20) NOT NULL,
    leave_type VARCHAR(30),
    remarks TEXT,
    CONSTRAINT fk_attendance_employee FOREIGN KEY (employee_id) REFERENCES employees(employee_id) ON DELETE CASCADE,
    CONSTRAINT chk_attendance_status CHECK (status IN ('present', 'absent', 'leave', 'half_day', 'holiday')),
    CONSTRAINT chk_leave_type CHECK (leave_type IN ('sick_leave', 'casual_leave', 'earned_leave', 'unpaid_leave', NULL)),
    CONSTRAINT uq_employee_date UNIQUE (employee_id, attendance_date)
);

-- Bonuses Table
CREATE TABLE bonuses (
    bonus_id BIGSERIAL PRIMARY KEY,
    employee_id BIGINT NOT NULL,
    bonus_amount DECIMAL(10, 2) NOT NULL,
    bonus_type VARCHAR(50) NOT NULL,
    bonus_date DATE NOT NULL,
    description TEXT,
    CONSTRAINT fk_bonus_employee FOREIGN KEY (employee_id) REFERENCES employees(employee_id) ON DELETE CASCADE,
    CONSTRAINT chk_bonus_amount_positive CHECK (bonus_amount > 0),
    CONSTRAINT chk_bonus_type CHECK (bonus_type IN ('performance', 'festive', 'annual', 'project_completion', 'referral'))
);

-- Payroll History Table
CREATE TABLE payroll_history (
    payroll_id BIGSERIAL PRIMARY KEY,
    employee_id BIGINT NOT NULL,
    month INT NOT NULL,
    year INT NOT NULL,
    base_salary DECIMAL(12, 2) NOT NULL,
    bonuses DECIMAL(10, 2) DEFAULT 0,
    deductions DECIMAL(10, 2) DEFAULT 0,
    net_salary DECIMAL(12, 2) NOT NULL,
    processed_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_payroll_employee FOREIGN KEY (employee_id) REFERENCES employees(employee_id) ON DELETE CASCADE,
    CONSTRAINT chk_month CHECK (month BETWEEN 1 AND 12),
    CONSTRAINT chk_year CHECK (year >= 2000),
    CONSTRAINT uq_employee_month_year UNIQUE (employee_id, month, year)
);

-- ============================================
-- INDEXES FOR PERFORMANCE
-- ============================================

-- Employee indexes
CREATE INDEX idx_employees_department ON employees(department_id);
CREATE INDEX idx_employees_email ON employees(email);
CREATE INDEX idx_employees_hire_date ON employees(hire_date);
CREATE INDEX idx_employees_active ON employees(is_active);

-- Salary indexes
CREATE INDEX idx_salary_employee ON salary(employee_id);

-- Attendance indexes
CREATE INDEX idx_attendance_employee ON attendance(employee_id);
CREATE INDEX idx_attendance_date ON attendance(attendance_date);
CREATE INDEX idx_attendance_employee_date ON attendance(employee_id, attendance_date);
CREATE INDEX idx_attendance_status ON attendance(status);

-- Bonus indexes
CREATE INDEX idx_bonuses_employee ON bonuses(employee_id);
CREATE INDEX idx_bonuses_date ON bonuses(bonus_date);

-- Payroll indexes
CREATE INDEX idx_payroll_employee ON payroll_history(employee_id);
CREATE INDEX idx_payroll_year_month ON payroll_history(year, month);
```

## **Part B – Logical Queries**

```sql name=payroll_queries.sql
-- ============================================
-- QUERY 1: Department with highest average salary
-- ============================================
SELECT 
    d.department_id,
    d.department_name,
    d.department_code,
    COUNT(e.employee_id) AS total_employees,
    ROUND(AVG(s.base_salary)::numeric, 2) AS average_salary,
    ROUND(MIN(s.base_salary)::numeric, 2) AS min_salary,
    ROUND(MAX(s.base_salary)::numeric, 2) AS max_salary
FROM departments d
INNER JOIN employees e ON d.department_id = e.department_id
INNER JOIN salary s ON e.employee_id = s.employee_id
WHERE e.is_active = TRUE
GROUP BY d.department_id, d.department_name, d.department_code
ORDER BY average_salary DESC
LIMIT 1;

-- ============================================
-- QUERY 2: Employees who never took leave
-- ============================================
SELECT 
    e.employee_id,
    e.employee_code,
    e.first_name || ' ' || e.last_name AS full_name,
    e.email,
    d.department_name,
    e.hire_date,
    CURRENT_DATE - e.hire_date AS days_employed,
    COUNT(a.attendance_id) AS total_attendance_records
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
LEFT JOIN attendance a ON e.employee_id = a.employee_id 
    AND a.status = 'leave'
WHERE e.is_active = TRUE
GROUP BY e.employee_id, e.employee_code, e.first_name, e.last_name, 
         e.email, d.department_name, e.hire_date
HAVING COUNT(CASE WHEN a.status = 'leave' THEN 1 END) = 0
ORDER BY days_employed DESC;

-- Alternative: Using NOT EXISTS (More efficient)
SELECT 
    e.employee_id,
    e.employee_code,
    e.first_name || ' ' || e.last_name AS full_name,
    e.email,
    d.department_name,
    e.hire_date
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
WHERE e.is_active = TRUE
    AND NOT EXISTS (
        SELECT 1 
        FROM attendance a 
        WHERE a.employee_id = e.employee_id 
            AND a.status = 'leave'
    )
ORDER BY e.hire_date;

-- ============================================
-- QUERY 3: Employees earning more than their department average
-- ============================================
WITH department_avg_salary AS (
    SELECT 
        e.department_id,
        AVG(s.base_salary) AS avg_dept_salary
    FROM employees e
    INNER JOIN salary s ON e.employee_id = s.employee_id
    WHERE e.is_active = TRUE
    GROUP BY e.department_id
)
SELECT 
    e.employee_id,
    e.employee_code,
    e.first_name || ' ' || e.last_name AS full_name,
    d.department_name,
    s.base_salary AS employee_salary,
    ROUND(das.avg_dept_salary::numeric, 2) AS department_avg_salary,
    ROUND((s.base_salary - das.avg_dept_salary)::numeric, 2) AS salary_difference,
    ROUND(((s.base_salary - das.avg_dept_salary) / das.avg_dept_salary * 100)::numeric, 2) AS percentage_above_avg
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
INNER JOIN salary s ON e.employee_id = s.employee_id
INNER JOIN department_avg_salary das ON e.department_id = das.department_id
WHERE s.base_salary > das.avg_dept_salary
    AND e.is_active = TRUE
ORDER BY salary_difference DESC;

-- ============================================
-- QUERY 4: Monthly salary expenditure
-- ============================================
SELECT 
    ph.year,
    ph.month,
    TO_CHAR(TO_DATE(ph.month::text, 'MM'), 'Month') AS month_name,
    COUNT(DISTINCT ph.employee_id) AS total_employees,
    SUM(ph.base_salary) AS total_base_salary,
    SUM(ph.bonuses) AS total_bonuses,
    SUM(ph.deductions) AS total_deductions,
    SUM(ph.net_salary) AS total_payroll_expenditure,
    ROUND(AVG(ph.net_salary)::numeric, 2) AS avg_employee_salary
FROM payroll_history ph
GROUP BY ph.year, ph.month
ORDER BY ph.year DESC, ph.month DESC;

-- Current month estimated expenditure (if payroll not yet processed)
SELECT 
    EXTRACT(YEAR FROM CURRENT_DATE) AS year,
    EXTRACT(MONTH FROM CURRENT_DATE) AS month,
    COUNT(e.employee_id) AS total_employees,
    SUM(s.base_salary) AS estimated_base_salary,
    COALESCE(SUM(b.bonus_amount), 0) AS current_month_bonuses,
    SUM(s.base_salary) + COALESCE(SUM(b.bonus_amount), 0) AS estimated_total_expenditure
FROM employees e
INNER JOIN salary s ON e.employee_id = s.employee_id
LEFT JOIN bonuses b ON e.employee_id = b.employee_id 
    AND EXTRACT(YEAR FROM b.bonus_date) = EXTRACT(YEAR FROM CURRENT_DATE)
    AND EXTRACT(MONTH FROM b.bonus_date) = EXTRACT(MONTH FROM CURRENT_DATE)
WHERE e.is_active = TRUE;

-- ============================================
-- QUERY 5: Top 3 highest-paid employees per department
-- ============================================
WITH ranked_employees AS (
    SELECT 
        e.employee_id,
        e.employee_code,
        e.first_name || ' ' || e.last_name AS full_name,
        e.job_title,
        d.department_name,
        s.base_salary,
        RANK() OVER (PARTITION BY d.department_id ORDER BY s.base_salary DESC) AS salary_rank,
        ROW_NUMBER() OVER (PARTITION BY d.department_id ORDER BY s.base_salary DESC) AS row_num
    FROM employees e
    INNER JOIN departments d ON e.department_id = d.department_id
    INNER JOIN salary s ON e.employee_id = s.employee_id
    WHERE e.is_active = TRUE
)
SELECT 
    department_name,
    employee_code,
    full_name,
    job_title,
    base_salary,
    salary_rank
FROM ranked_employees
WHERE row_num <= 3
ORDER BY department_name, salary_rank;
```

## **Part C – Advanced**

```sql name=payroll_advanced.sql
-- ============================================
-- ADVANCED QUERY 1: Correlated Subquery
-- Find employees whose salary is above average for their job title
-- ============================================
SELECT 
    e.employee_id,
    e.employee_code,
    e.first_name || ' ' || e.last_name AS full_name,
    e.job_title,
    d.department_name,
    s.base_salary,
    (
        SELECT ROUND(AVG(s2.base_salary)::numeric, 2)
        FROM employees e2
        INNER JOIN salary s2 ON e2.employee_id = s2.employee_id
        WHERE e2.job_title = e.job_title
            AND e2.is_active = TRUE
    ) AS avg_salary_for_title,
    ROUND((s.base_salary - (
        SELECT AVG(s2.base_salary)
        FROM employees e2
        INNER JOIN salary s2 ON e2.employee_id = s2.employee_id
        WHERE e2.job_title = e.job_title
            AND e2.is_active = TRUE
    ))::numeric, 2) AS difference_from_avg
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
INNER JOIN salary s ON e.employee_id = s.employee_id
WHERE e.is_active = TRUE
    AND s.base_salary > (
        SELECT AVG(s2.base_salary)
        FROM employees e2
        INNER JOIN salary s2 ON e2.employee_id = s2.employee_id
        WHERE e2.job_title = e.job_title
            AND e2.is_active = TRUE
    )
ORDER BY difference_from_avg DESC;

-- ============================================
-- ADVANCED QUERY 2: LEFT JOIN - Departments with no employees
-- ============================================
SELECT 
    d.department_id,
    d.department_name,
    d.department_code,
    d.location,
    d.created_at,
    COUNT(e.employee_id) AS employee_count
FROM departments d
LEFT JOIN employees e ON d.department_id = e.department_id 
    AND e.is_active = TRUE
WHERE d.is_active = TRUE
GROUP BY d.department_id, d.department_name, d.department_code, d.location, d.created_at
HAVING COUNT(e.employee_id) = 0
ORDER BY d.department_name;

-- ============================================
-- ADVANCED QUERY 3: Stored Procedure - Calculate Monthly Payroll
-- ============================================
CREATE OR REPLACE PROCEDURE calculate_monthly_payroll(
    p_year INT,
    p_month INT
)
LANGUAGE plpgsql
AS $$
DECLARE
    v_employee RECORD;
    v_base_salary DECIMAL(12, 2);
    v_bonus_total DECIMAL(10, 2);
    v_deductions DECIMAL(10, 2);
    v_net_salary DECIMAL(12, 2);
    v_absent_days INT;
    v_working_days INT := 22; -- Assume 22 working days per month
BEGIN
    -- Loop through all active employees
    FOR v_employee IN 
        SELECT e.employee_id, s.base_salary
        FROM employees e
        INNER JOIN salary s ON e.employee_id = s.employee_id
        WHERE e.is_active = TRUE
    LOOP
        -- Get base salary
        v_base_salary := v_employee.base_salary;
        
        -- Calculate bonuses for the month
        SELECT COALESCE(SUM(bonus_amount), 0) INTO v_bonus_total
        FROM bonuses
        WHERE employee_id = v_employee.employee_id
            AND EXTRACT(YEAR FROM bonus_date) = p_year
            AND EXTRACT(MONTH FROM bonus_date) = p_month;
        
        -- Calculate absent days (deductions)
        SELECT COUNT(*) INTO v_absent_days
        FROM attendance
        WHERE employee_id = v_employee.employee_id
            AND status = 'absent'
            AND EXTRACT(YEAR FROM attendance_date) = p_year
            AND EXTRACT(MONTH FROM attendance_date) = p_month;
        
        -- Calculate deductions (per day salary * absent days)
        v_deductions := (v_base_salary / v_working_days) * v_absent_days;
        
        -- Calculate net salary
        v_net_salary := v_base_salary + v_bonus_total - v_deductions;
        
        -- Insert into payroll history
        INSERT INTO payroll_history (
            employee_id, month, year, base_salary, 
            bonuses, deductions, net_salary
        ) VALUES (
            v_employee.employee_id, p_month, p_year, v_base_salary,
            v_bonus_total, v_deductions, v_net_salary
        )
        ON CONFLICT (employee_id, month, year) 
        DO UPDATE SET
            base_salary = EXCLUDED.base_salary,
            bonuses = EXCLUDED.bonuses,
            deductions = EXCLUDED.deductions,
            net_salary = EXCLUDED.net_salary,
            processed_date = CURRENT_TIMESTAMP;
    END LOOP;
    
    RAISE NOTICE 'Payroll processed for %-%', p_year, p_month;
END;
$$;

-- Execute the procedure
CALL calculate_monthly_payroll(2026, 2);

-- Query the results
SELECT * FROM payroll_history WHERE year = 2026 AND month = 2;

-- ============================================
-- ADVANCED QUERY 4: Trigger - Log Salary Changes
-- ============================================
CREATE OR REPLACE FUNCTION log_salary_change()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    -- Insert into salary history when salary is updated
    INSERT INTO salary_history (
        employee_id,
        old_salary,
        new_salary,
        changed_by,
        reason
    ) VALUES (
        NEW.employee_id,
        OLD.base_salary,
        NEW.base_salary,
        CURRENT_USER,
        'Salary updated via trigger'
    );
    
    RAISE NOTICE 'Salary change logged for employee_id: % (Old: %, New: %)', 
        NEW.employee_id, OLD.base_salary, NEW.base_salary;
    
    RETURN NEW;
END;
$$;

-- Create the trigger
CREATE TRIGGER trigger_salary_change
AFTER UPDATE OF base_salary ON salary
FOR EACH ROW
WHEN (OLD.base_salary IS DISTINCT FROM NEW.base_salary)
EXECUTE FUNCTION log_salary_change();

-- Test the trigger
UPDATE salary SET base_salary = base_salary + 5000 WHERE employee_id = 1;

-- Check the salary history
SELECT * FROM salary_history WHERE employee_id = 1 ORDER BY change_date DESC;

-- ============================================
-- ADVANCED QUERY 5: EXPLAIN - Query Optimization
-- ============================================

-- Example slow query (without optimization)
EXPLAIN ANALYZE
SELECT 
    e.employee_id,
    e.first_name || ' ' || e.last_name AS full_name,
    d.department_name,
    s.base_salary,
    COUNT(a.attendance_id) AS total_attendance
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
INNER JOIN salary s ON e.employee_id = s.employee_id
LEFT JOIN attendance a ON e.employee_id = a.employee_id
WHERE e.is_active = TRUE
GROUP BY e.employee_id, e.first_name, e.last_name, d.department_name, s.base_salary
ORDER BY s.base_salary DESC;

-- Optimization steps:
-- 1. Check if indexes exist
-- 2. Add missing indexes
-- 3. Use EXPLAIN ANALYZE to compare

-- Check index usage
SELECT
    schemaname,
    tablename,
    indexname,
    idx_scan AS index_scans,
    idx_tup_read AS tuples_read,
    idx_tup_fetch AS tuples_fetched
FROM pg_stat_user_indexes
WHERE schemaname = 'public'
ORDER BY idx_scan DESC;

-- Optimized query with CTE
EXPLAIN ANALYZE
WITH employee_attendance AS (
    SELECT 
        employee_id,
        COUNT(*) AS attendance_count
    FROM attendance
    GROUP BY employee_id
)
SELECT 
    e.employee_id,
    e.first_name || ' ' || e.last_name AS full_name,
    d.department_name,
    s.base_salary,
    COALESCE(ea.attendance_count, 0) AS total_attendance
FROM employees e
INNER JOIN departments d ON e.department_id = d.department_id
INNER JOIN salary s ON e.employee_id = s.employee_id
LEFT JOIN employee_attendance ea ON e.employee_id = ea.employee_id
WHERE e.is_active = TRUE
ORDER BY s.base_salary DESC;
```

---

# **Question 3: Social Media Platform Analytics**

## **Part A – Design**

```sql name=social_media_schema.sql
-- ============================================
-- SOCIAL MEDIA PLATFORM DATABASE SCHEMA
-- ============================================

-- Users Table
CREATE TABLE users (
    user_id BIGSERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL UNIQUE,
    full_name VARCHAR(100) NOT NULL,
    bio TEXT,
    profile_picture_url VARCHAR(255),
    date_of_birth DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    last_login TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE,
    is_verified BOOLEAN DEFAULT FALSE,
    CONSTRAINT chk_username_length CHECK (LENGTH(username) >= 3),
    CONSTRAINT chk_email_format CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$'),
    CONSTRAINT chk_age CHECK (date_of_birth <= CURRENT_DATE - INTERVAL '13 years')
);

-- Posts Table
CREATE TABLE posts (
    post_id BIGSERIAL PRIMARY KEY,
    user_id BIGINT NOT NULL,
    content TEXT NOT NULL,
    image_url VARCHAR(255),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_deleted BOOLEAN DEFAULT FALSE,
    CONSTRAINT fk_post_user FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    CONSTRAINT chk_content_length CHECK (LENGTH(content) > 0 AND LENGTH(content) <= 5000)
);

-- Comments Table
CREATE TABLE comments (
    comment_id BIGSERIAL PRIMARY KEY,
    post_id BIGINT NOT NULL,
    user_id BIGINT NOT NULL,
    comment_text TEXT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_deleted BOOLEAN DEFAULT FALSE,
    CONSTRAINT fk_comment_post FOREIGN KEY (post_id) REFERENCES posts(post_id) ON DELETE CASCADE,
    CONSTRAINT fk_comment_user FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    CONSTRAINT chk_comment_length CHECK (LENGTH(comment_text) > 0 AND LENGTH(comment_text) <= 1000)
);

-- Likes Table
CREATE TABLE likes (
    like_id BIGSERIAL PRIMARY KEY,
    post_id BIGINT NOT NULL,
    user_id BIGINT NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_like_post FOREIGN KEY (post_id) REFERENCES posts(post_id) ON DELETE CASCADE,
    CONSTRAINT fk_like_user FOREIGN KEY (user_id) REFERENCES users(user_id) ON DELETE CASCADE,
    CONSTRAINT uq_user_post_like UNIQUE (user_id, post_id) -- Prevent duplicate likes
);

-- Followers Table (Self-referencing many-to-many relationship)
CREATE TABLE followers (
    follow_id BIGSERIAL PRIMARY KEY,
    follower_id BIGINT NOT NULL, -- User who is following
    following_id BIGINT NOT NULL, -- User being followed
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT fk_follower FOREIGN KEY (follower_id) REFERENCES users(user_id) ON DELETE CASCADE,
    CONSTRAINT fk_following FOREIGN KEY (following_id) REFERENCES users(user_id) ON DELETE CASCADE,
    CONSTRAINT uq_follower_following UNIQUE (follower_id, following_id), -- Prevent duplicate follows
    CONSTRAINT chk_no_self_follow CHECK (follower_id != following_id) -- Prevent self-following
);

-- Hashtags Table
CREATE TABLE hashtags (
    hashtag_id SERIAL PRIMARY KEY,
    hashtag_name VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    CONSTRAINT chk_hashtag_format CHECK (hashtag_name ~ '^[a-zA-Z0-9_]+$')
);

-- Post Hashtags Junction Table (Many-to-Many)
CREATE TABLE post_hashtags (
    post_hashtag_id BIGSERIAL PRIMARY KEY,
    post_id BIGINT NOT NULL,
    hashtag_id INT NOT NULL,
    CONSTRAINT fk_post_hashtag_post FOREIGN KEY (post_id) REFERENCES posts(post_id) ON DELETE CASCADE,
    CONSTRAINT fk_post_hashtag_hashtag FOREIGN KEY (hashtag_id) REFERENCES hashtags(hashtag_id) ON DELETE CASCADE,
    CONSTRAINT uq_post_hashtag UNIQUE (post_id, hashtag_id)
);

-- ============================================
-- INDEXES FOR PERFORMANCE
-- ============================================

-- User indexes
CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_users_email ON users(email);
CREATE INDEX idx_users_created_at ON users(created_at);

-- Post indexes
CREATE INDEX idx_posts_user_id ON posts(user_id);
CREATE INDEX idx_posts_created_at ON posts(created_at);
CREATE INDEX idx_posts_user_created ON posts(user_id, created_at);

-- Comment indexes
CREATE INDEX idx_comments_post_id ON comments(post_id);
CREATE INDEX idx_comments_user_id ON comments(user_id);
CREATE INDEX idx_comments_created_at ON comments(created_at);

-- Like indexes
CREATE INDEX idx_likes_post_id ON likes(post_id);
CREATE INDEX idx_likes_user_id ON likes(user_id);

-- Follower indexes
CREATE INDEX idx_followers_follower_id ON followers(follower_id);
CREATE INDEX idx_followers_following_id ON followers(following_id);

-- Hashtag indexes
CREATE INDEX idx_hashtags_name ON hashtags(hashtag_name);
CREATE INDEX idx_post_hashtags_post ON post_hashtags(post_id);
CREATE INDEX idx_post_hashtags_hashtag ON post_hashtags(hashtag_id);

-- Composite indexes for common queries
CREATE INDEX idx_posts_user_deleted ON posts(user_id, is_deleted);
CREATE INDEX idx_comments_post_deleted ON comments(post_id, is_deleted);
```

## **Part B – Logical Queries**

```sql name=social_media_queries.sql
-- ============================================
-- QUERY 1: Top 5 most liked posts
-- ============================================
SELECT 
    p.post_id,
    p.content,
    u.username AS author_username,
    u.full_name AS author_name,
    p.created_at AS post_date,
    COUNT(l.like_id) AS total_likes,
    (SELECT COUNT(*) FROM comments c WHERE c.post_id = p.post_id AND c.is_deleted = FALSE) AS total_comments
FROM posts p
INNER JOIN users u ON p.user_id = u.user_id
LEFT JOIN likes l ON p.post_id = l.post_id
WHERE p.is_deleted = FALSE
GROUP BY p.post_id, p.content, u.username, u.full_name, p.created_at
ORDER BY total_likes DESC, p.created_at DESC
LIMIT 5;

-- ============================================
-- QUERY 2: Users with more than 100 followers
-- ============================================
SELECT 
    u.user_id,
    u.username,
    u.full_name,
    u.is_verified,
    COUNT(f.follower_id) AS follower_count,
    (SELECT COUNT(*) FROM followers f2 WHERE f2.follower_id = u.user_id) AS following_count,
    (SELECT COUNT(*) FROM posts p WHERE p.user_id = u.user_id AND p.is_deleted = FALSE) AS total_posts
FROM users u
INNER JOIN followers f ON u.user_id = f.following_id
WHERE u.is_active = TRUE
GROUP BY u.user_id, u.username, u.full_name, u.is_verified
HAVING COUNT(f.follower_id) > 100
ORDER BY follower_count DESC;

-- ============================================
-- QUERY 3: Posts with zero comments
-- ============================================
SELECT 
    p.post_id,
    p.content,
    u.username AS author,
    u.full_name,
    p.created_at,
    COUNT(l.like_id) AS likes_count,
    EXTRACT(DAY FROM (CURRENT_TIMESTAMP - p.created_at)) AS days_since_posted
FROM posts p
INNER JOIN users u ON p.user_id = u.user_id
LEFT JOIN comments c ON p.post_id = c.post_id AND c.is_deleted = FALSE
LEFT JOIN likes l ON p.post_id = l.post_id
WHERE p.is_deleted = FALSE
    AND c.comment_id IS NULL
GROUP BY p.post_id, p.content, u.username, u.full_name, p.created_at
ORDER BY p.created_at DESC;

-- Alternative using NOT EXISTS
SELECT 
    p.post_id,
    p.content,
    u.username,
    p.created_at
FROM posts p
INNER JOIN users u ON p.user_id = u.user_id
WHERE p.is_deleted = FALSE
    AND NOT EXISTS (
        SELECT 1 
        FROM comments c 
        WHERE c.post_id = p.post_id 
            AND c.is_deleted = FALSE
    )
ORDER BY p.created_at DESC;

-- ============================================
-- QUERY 4: Most active user (posts + comments)
-- ============================================
WITH user_activity AS (
    SELECT 
        u.user_id,
        u.username,
        u.full_name,
        COUNT(DISTINCT p.post_id) AS total_posts,
        COUNT(DISTINCT c.comment_id) AS total_comments,
        COUNT(DISTINCT p.post_id) + COUNT(DISTINCT c.comment_id) AS total_activity
    FROM users u
    LEFT JOIN posts p ON u.user_id = p.user_id AND p.is_deleted = FALSE
    LEFT JOIN comments c ON u.user_id = c.user_id AND c.is_deleted = FALSE
    WHERE u.is_active = TRUE
    GROUP BY u.user_id, u.username, u.full_name
)
SELECT 
    user_id,
    username,
    full_name,
    total_posts,
    total_comments,
    total_activity,
    (SELECT COUNT(*) FROM followers WHERE following_id = user_id) AS followers,
    (SELECT COUNT(*) FROM likes WHERE user_id = user_activity.user_id) AS likes_given
FROM user_activity
ORDER BY total_activity DESC
LIMIT 1;

-- ============================================
-- QUERY 5: Daily active users count (last 30 days)
-- ============================================
WITH date_series AS (
    SELECT generate_series(
        CURRENT_DATE - INTERVAL '30 days',
        CURRENT_DATE,
        '1 day'::interval
    )::date AS activity_date
),
daily_activity AS (
    SELECT 
        DATE(created_at) AS activity_date,
        user_id
    FROM posts
    WHERE created_at >= CURRENT_DATE - INTERVAL '30 days'
        AND is_deleted = FALSE
    
    UNION
    
    SELECT 
        DATE(created_at) AS activity_date,
        user_id
    FROM comments
    WHERE created_at >= CURRENT_DATE - INTERVAL '30 days'
        AND is_deleted = FALSE
    
    UNION
    
    SELECT 
        DATE(created_at) AS activity_date,
        user_id
    FROM likes
    WHERE created_at >= CURRENT_DATE - INTERVAL '30 days'
)
SELECT 
    ds.activity_date,
    TO_CHAR(ds.activity_date, 'Day') AS day_name,
    COUNT(DISTINCT da.user_id) AS active_users
FROM date_series ds
LEFT JOIN daily_activity da ON ds.activity_date = da.activity_date
GROUP BY ds.activity_date
ORDER BY ds.activity_date DESC;
```

## **Part C – Advanced**

```sql name=social_media_advanced.sql
-- ============================================
-- ADVANCED QUERY 1: GROUP BY + HAVING
-- Find hashtags used in more than 10 posts
-- ============================================
SELECT 
    h.hashtag_id,
    h.hashtag_name,
    COUNT(ph.post_id) AS usage_count,
    COUNT(DISTINCT ph.post_id) AS unique_posts,
    MIN(p.created_at) AS first_used,
    MAX(p.created_at) AS last_used
FROM hashtags h
INNER JOIN post_hashtags ph ON h.hashtag_id = ph.hashtag_id
INNER JOIN posts p ON ph.post_id = p.post_id
WHERE p.is_deleted = FALSE
GROUP BY h.hashtag_id, h.hashtag_name
HAVING COUNT(ph.post_id) > 10
ORDER BY usage_count DESC;

-- ============================================
-- ADVANCED QUERY 2: Subquery - Users who liked all posts of a specific user
-- ============================================
-- Find users who liked ALL posts by user_id = 1
WITH target_user_posts AS (
    SELECT post_id
    FROM posts
    WHERE user_id = 1
        AND is_deleted = FALSE
),
user_likes AS (
    SELECT 
        l.user_id,
        COUNT(DISTINCT l.post_id) AS liked_posts_count
    FROM likes l
    INNER JOIN target_user_posts tup ON l.post_id = tup.post_id
    GROUP BY l.user_id
)
SELECT 
    u.user_id,
    u.username,
    u.full_name,
    ul.liked_posts_count,
    (SELECT COUNT(*) FROM target_user_posts) AS total_posts
FROM users u
INNER JOIN user_likes ul ON u.user_id = ul.user_id
WHERE ul.liked_posts_count = (SELECT COUNT(*) FROM target_user_posts)
    AND u.user_id != 1 -- Exclude the target user themselves
ORDER BY u.username;

-- Alternative using NOT EXISTS (find users who did NOT miss any post)
SELECT 
    u.user_id,
    u.username,
    u.full_name
FROM users u
WHERE u.user_id != 1
    AND NOT EXISTS (
        -- Find posts by user 1 that this user didn't like
        SELECT 1
        FROM posts p
        WHERE p.user_id = 1
            AND p.is_deleted = FALSE
            AND NOT EXISTS (
                SELECT 1
                FROM likes l
                WHERE l.post_id = p.post_id
                    AND l.user_id = u.user_id
            )
    )
    AND EXISTS (
        -- Ensure the user liked at least one post
        SELECT 1
        FROM likes l
        INNER JOIN posts p ON l.post_id = p.post_id
        WHERE p.user_id = 1
            AND l.user_id = u.user_id
    );

-- ============================================
-- ADVANCED QUERY 3: Create View - User Engagement Summary
-- ============================================
CREATE OR REPLACE VIEW user_engagement_summary AS
SELECT 
    u.user_id,
    u.username,
    u.full_name,
    u.is_verified,
    u.created_at AS joined_date,
    -- Posts metrics
    COUNT(DISTINCT p.post_id) AS total_posts,
    COALESCE(SUM((SELECT COUNT(*) FROM likes l WHERE l.post_id = p.post_id)), 0) AS total_likes_received,
    COALESCE(SUM((SELECT COUNT(*) FROM comments c WHERE c.post_id = p.post_id AND c.is_deleted = FALSE)), 0) AS total_comments_received,
    -- Comments made
    (SELECT COUNT(*) FROM comments c WHERE c.user_id = u.user_id AND c.is_deleted = FALSE) AS total_comments_made,
    -- Likes given
    (SELECT COUNT(*) FROM likes l WHERE l.user_id = u.user_id) AS total_likes_given,
    -- Follower metrics
    (SELECT COUNT(*) FROM followers f WHERE f.following_id = u.user_id) AS followers_count,
    (SELECT COUNT(*) FROM followers f WHERE f.follower_id = u.user_id) AS following_count,
    -- Engagement rate
    CASE 
        WHEN COUNT(DISTINCT p.post_id) > 0 THEN
            ROUND((COALESCE(SUM((SELECT COUNT(*) FROM likes l WHERE l.post_id = p.post_id)), 0) + 
                   COALESCE(SUM((SELECT COUNT(*) FROM comments c WHERE c.post_id = p.post_id AND c.is_deleted = FALSE)), 0))::numeric / 
                  COUNT(DISTINCT p.post_id), 2)
        ELSE 0
    END AS avg_engagement_per_post,
    -- Last activity
    GREATEST(
        COALESCE(MAX(p.created_at), u.created_at),
        (SELECT MAX(created_at) FROM comments WHERE user_id = u.user_id),
        (SELECT MAX(created_at) FROM likes WHERE user_id = u.user_id)
    ) AS last_activity
FROM users u
LEFT JOIN posts p ON u.user_id = p.user_id AND p.is_deleted = FALSE
WHERE u.is_active = TRUE
GROUP BY u.user_id, u.username, u.full_name, u.is_verified, u.created_at;

-- Query the view
SELECT * FROM user_engagement_summary ORDER BY avg_engagement_per_post DESC LIMIT 10;

-- ============================================
-- ADVANCED QUERY 4: Transaction - Insert Post with Hashtags
-- ============================================
DO $$
DECLARE
    v_user_id BIGINT := 1;
    v_post_content TEXT := 'Learning #SQL and #PostgreSQL is amazing! #Database #Learning';
    v_post_id BIGINT;
    v_hashtag_id INT;
    v_hashtags TEXT[] := ARRAY['SQL', 'PostgreSQL', 'Database', 'Learning'];
    v_hashtag TEXT;
BEGIN
    -- Start transaction (implicit in DO block)
    BEGIN
        -- Step 1: Insert the post
        INSERT INTO posts (user_id, content)
        VALUES (v_user_id, v_post_content)
        RETURNING post_id INTO v_post_id;
        
        RAISE NOTICE 'Post created with ID: %', v_post_id;
        
        -- Step 2: Process hashtags
        FOREACH v_hashtag IN ARRAY v_hashtags
        LOOP
            -- Insert hashtag if it doesn't exist
            INSERT INTO hashtags (hashtag_name)
            VALUES (v_hashtag)
            ON CONFLICT (hashtag_name) DO NOTHING;
            
            -- Get hashtag_id
            SELECT hashtag_id INTO v_hashtag_id
            FROM hashtags
            WHERE hashtag_name = v_hashtag;
            
            -- Link post to hashtag
            INSERT INTO post_hashtags (post_id, hashtag_id)
            VALUES (v_post_id, v_hashtag_id)
            ON CONFLICT (post_id, hashtag_id) DO NOTHING;
            
            RAISE NOTICE 'Linked hashtag: % (ID: %)', v_hashtag, v_hashtag_id;
        END LOOP;
        
        RAISE NOTICE 'Transaction completed successfully';
        
    EXCEPTION
        WHEN OTHERS THEN
            -- Rollback happens automatically
            RAISE NOTICE 'Transaction failed: %', SQLERRM;
            RAISE EXCEPTION 'Hashtag processing failed';
    END;
END $$;

-- Verify the result
SELECT 
    p.post_id,
    p.content,
    ARRAY_AGG(h.hashtag_name) AS hashtags
FROM posts p
LEFT JOIN post_hashtags ph ON p.post_id = ph.post_id
LEFT JOIN hashtags h ON ph.hashtag_id = h.hashtag_id
WHERE p.post_id = (SELECT MAX(post_id) FROM posts)
GROUP BY p.post_id, p.content;

-- ============================================
-- ADVANCED QUERY 5: Composite Index and Explanation
-- ============================================

-- Create composite index for common query pattern
-- Scenario: Frequently query posts by user and creation date
CREATE INDEX idx_posts_user_created_deleted ON posts(user_id, created_at DESC, is_deleted)
WHERE is_deleted = FALSE;

/*
WHY THIS COMPOSITE INDEX?

1. **Query Pattern**: We frequently query:
   - Posts by a specific user (user_id)
   - Ordered by creation date (created_at DESC)
   - Only active posts (is_deleted = FALSE)

2. **Index Column Order**:
   - user_id: First for equality filtering
   - created_at DESC: Second for sorting
   - is_deleted: Third for additional filtering

3. **Partial Index**: WHERE is_deleted = FALSE
   - Reduces index size
   - Improves query performance
   - Most queries only need active posts

4. **Benefits**:
   - Index-only scans possible
   - No table access needed for covered columns
   - Faster sorting operations
   - Reduced I/O operations

5. **Use Case Example**:
*/

-- Query that benefits from the composite index
EXPLAIN ANALYZE
SELECT post_id, content, created_at
FROM posts
WHERE user_id = 1
    AND is_deleted = FALSE
ORDER BY created_at DESC
LIMIT 20;

/*
Without the composite index:
- Sequential scan on posts table
- Sort operation on all matching rows
- Higher execution time

With the composite index:
- Index scan on idx_posts_user_created_deleted
- No additional sort needed (index is already sorted)
- Much faster execution
*/

-- Another example: Timeline query
CREATE INDEX idx_posts_created_deleted_composite ON posts(created_at DESC, is_deleted)
WHERE is_deleted = FALSE;

-- Query: Get recent posts from followed users
EXPLAIN ANALYZE
SELECT 
    p.post_id,
    p.content,
    p.created_at,
    u.username
FROM posts p
INNER JOIN users u ON p.user_id = u.user_id
WHERE p.user_id IN (
    SELECT following_id 
    FROM followers 
    WHERE follower_id = 1
)
    AND p.is_deleted = FALSE
ORDER BY p.created_at DESC
LIMIT 50;

-- Composite index for user engagement queries
CREATE INDEX idx_likes_user_created_composite ON likes(user_id, created_at DESC);
CREATE INDEX idx_comments_user_created_composite ON comments(user_id, created_at DESC)
WHERE is_deleted = FALSE;

/*
KEY TAKEAWAYS:
1. Composite indexes should match query patterns
2. Order matters: most selective column first
3. Include sort columns for ORDER BY optimization
4. Use partial indexes to reduce size
5. Monitor with EXPLAIN ANALYZE
6. Balance between query performance and write overhead
*/
```

---

## **Summary of Key SQL Concepts Covered**

### **Question 1 - E-Commerce:**
- ✅ Database schema design with proper constraints
- ✅ Foreign keys with cascading actions
- ✅ Check constraints for data validation
- ✅ Aggregate functions (COUNT, SUM, AVG)
- ✅ JOINs (INNER, LEFT)
- ✅ Subqueries
- ✅ GROUP BY and HAVING
- ✅ Views
- ✅ Transactions with error handling
- ✅ SQL Injection prevention
- ✅ Composite indexes

### **Question 2 - Payroll:**
- ✅ Database normalization (3NF)
- ✅ Correlated subqueries
- ✅ Window functions (RANK, ROW_NUMBER)
- ✅ CTEs (Common Table Expressions)
- ✅ Stored procedures
- ✅ Triggers
- ✅ EXPLAIN ANALYZE for optimization
- ✅ Self-joins
- ✅ Advanced aggregate queries

### **Question 3 - Social Media:**
- ✅ Self-referencing relationships
- ✅ Many-to-many relationships
- ✅ Unique constraints preventing duplicates
- ✅ Complex subqueries
- ✅ NOT EXISTS patterns
- ✅ Date series generation
- ✅ UNION queries
- ✅ Views for engagement metrics
- ✅ Transaction with multiple inserts
- ✅ Composite indexes with explanation

---

These solutions demonstrate **production-ready SQL code** with proper:
- Error handling
- Data validation
- Performance optimization
- Security considerations
- Documentation

Good luck with your Week 4 assessment! 🚀

## @meet41

can you also provide me well formed structured comprehensive detailed notes on all topics mentioned above such that to master it from start to complete depth proper (and become master in SQL/PostgreSQL)

## Copilot

# **Complete SQL & PostgreSQL Mastery Guide** 📚

A comprehensive guide from fundamentals to advanced concepts.

---

## **Table of Contents**

1. [Database Basics](#1-database-basics)
2. [Keys & Constraints](#2-keys--constraints)
3. [SQL Data Types](#3-sql-data-types)
4. [CRUD Operations](#4-crud-operations)
5. [Query Filtering & Sorting](#5-query-filtering--sorting)
6. [Aggregate Functions](#6-aggregate-functions)
7. [Joins](#7-joins)
8. [Subqueries & Advanced Queries](#8-subqueries--advanced-queries)
9. [Indexes & Performance](#9-indexes--performance)
10. [Transactions & ACID](#10-transactions--acid)
11. [Stored Procedures & Functions](#11-stored-procedures--functions)
12. [Views](#12-views)
13. [Database Normalization](#13-database-normalization)
14. [SQL Injection & Security](#14-sql-injection--security)
15. [PostgreSQL Advanced Features](#15-postgresql-advanced-features)
16. [ORM Concepts](#16-orm-concepts)

---

# **1. Database Basics**

## **1.1 What is a Relational Database?**

A **relational database** is a type of database that stores and organizes data in structured tables with relationships between them.

### **Key Characteristics:**
- **Structured Data**: Data is organized in tables (relations)
- **Rows & Columns**: Tables consist of rows (records) and columns (attributes)
- **Relationships**: Tables can be linked through keys
- **SQL**: Uses Structured Query Language for operations
- **ACID Compliance**: Ensures data integrity

### **RDBMS (Relational Database Management System)**

Popular RDBMSs include:
- **PostgreSQL** - Advanced, open-source, feature-rich
- **MySQL** - Popular, fast, widely used
- **Oracle Database** - Enterprise-grade
- **Microsoft SQL Server** - Windows-centric
- **SQLite** - Lightweight, embedded

---

## **1.2 Tables, Rows & Columns**

```sql name=basic_table_structure.sql
-- ============================================
-- BASIC TABLE STRUCTURE
-- ============================================

-- A table represents an entity (e.g., users, products)
CREATE TABLE employees (
    -- Columns define attributes
    employee_id INT,           -- Column name and data type
    first_name VARCHAR(50),
    last_name VARCHAR(50),
    email VARCHAR(100),
    hire_date DATE,
    salary DECIMAL(10, 2)
);

-- Inserting rows (records)
INSERT INTO employees VALUES 
    (1, 'John', 'Doe', 'john@example.com', '2024-01-15', 75000.00),
    (2, 'Jane', 'Smith', 'jane@example.com', '2024-02-01', 80000.00);

-- Each row represents a complete record
-- Each column represents an attribute of that entity
```

### **Schema Definition**

A **schema** is the structure/blueprint of your database:

```sql name=schema_definition.sql
-- ============================================
-- SCHEMA DEFINITION
-- ============================================

-- In PostgreSQL, schemas are namespaces
CREATE SCHEMA company;

-- Create table within schema
CREATE TABLE company.departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL,
    location VARCHAR(100)
);

-- Set default schema
SET search_path TO company, public;

-- View schema information
SELECT table_schema, table_name, column_name, data_type
FROM information_schema.columns
WHERE table_schema = 'company';
```

---

# **2. Keys & Constraints**

## **2.1 Primary Key**

A **Primary Key** uniquely identifies each record in a table.

### **Characteristics:**
- Must be **UNIQUE**
- Cannot be **NULL**
- Only **ONE** primary key per table
- Usually auto-incrementing

```sql name=primary_keys.sql
-- ============================================
-- PRIMARY KEY EXAMPLES
-- ============================================

-- Method 1: Inline definition
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL
);

-- Method 2: Separate constraint
CREATE TABLE products (
    product_id BIGSERIAL,
    product_name VARCHAR(200),
    price DECIMAL(10, 2),
    CONSTRAINT pk_products PRIMARY KEY (product_id)
);

-- Composite Primary Key (multiple columns)
CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    PRIMARY KEY (order_id, product_id)
);

-- Auto-increment in PostgreSQL
CREATE TABLE posts (
    post_id BIGSERIAL PRIMARY KEY,  -- Auto-incrementing
    title VARCHAR(200),
    content TEXT
);

-- Auto-increment in MySQL
CREATE TABLE posts (
    post_id BIGINT AUTO_INCREMENT PRIMARY KEY,
    title VARCHAR(200),
    content TEXT
);
```

---

## **2.2 Foreign Key**

A **Foreign Key** creates a relationship between two tables.

### **Types of Relationships:**

```sql name=foreign_keys.sql
-- ============================================
-- FOREIGN KEY RELATIONSHIPS
-- ============================================

-- ONE-TO-MANY RELATIONSHIP
-- One department has many employees
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(100) NOT NULL
);

CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    employee_name VARCHAR(100) NOT NULL,
    department_id INT NOT NULL,
    CONSTRAINT fk_department 
        FOREIGN KEY (department_id) 
        REFERENCES departments(department_id)
);

-- ONE-TO-ONE RELATIONSHIP
-- One user has one profile
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50) NOT NULL
);

CREATE TABLE user_profiles (
    profile_id SERIAL PRIMARY KEY,
    user_id INT UNIQUE NOT NULL,  -- UNIQUE ensures one-to-one
    bio TEXT,
    avatar_url VARCHAR(255),
    CONSTRAINT fk_user 
        FOREIGN KEY (user_id) 
        REFERENCES users(user_id)
);

-- MANY-TO-MANY RELATIONSHIP
-- Students enroll in many courses, courses have many students
CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    student_name VARCHAR(100)
);

CREATE TABLE courses (
    course_id SERIAL PRIMARY KEY,
    course_name VARCHAR(100)
);

-- Junction/Bridge table
CREATE TABLE enrollments (
    enrollment_id SERIAL PRIMARY KEY,
    student_id INT NOT NULL,
    course_id INT NOT NULL,
    enrollment_date DATE,
    CONSTRAINT fk_student 
        FOREIGN KEY (student_id) 
        REFERENCES students(student_id),
    CONSTRAINT fk_course 
        FOREIGN KEY (course_id) 
        REFERENCES courses(course_id),
    CONSTRAINT uq_student_course 
        UNIQUE (student_id, course_id)
);
```

### **ON DELETE Actions**

```sql name=on_delete_actions.sql
-- ============================================
-- ON DELETE & ON UPDATE ACTIONS
-- ============================================

-- CASCADE: Delete child records when parent is deleted
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT NOT NULL,
    CONSTRAINT fk_user 
        FOREIGN KEY (user_id) 
        REFERENCES users(user_id) 
        ON DELETE CASCADE  -- If user deleted, delete their orders
);

-- RESTRICT: Prevent deletion if child records exist
CREATE TABLE categories (
    category_id SERIAL PRIMARY KEY,
    category_name VARCHAR(100)
);

CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    category_id INT,
    CONSTRAINT fk_category 
        FOREIGN KEY (category_id) 
        REFERENCES categories(category_id) 
        ON DELETE RESTRICT  -- Cannot delete category if products exist
);

-- SET NULL: Set foreign key to NULL when parent deleted
CREATE TABLE articles (
    article_id SERIAL PRIMARY KEY,
    author_id INT,
    CONSTRAINT fk_author 
        FOREIGN KEY (author_id) 
        REFERENCES users(user_id) 
        ON DELETE SET NULL  -- Set author_id to NULL if user deleted
);

-- SET DEFAULT: Set to default value
CREATE TABLE tasks (
    task_id SERIAL PRIMARY KEY,
    assigned_to INT DEFAULT 1,
    CONSTRAINT fk_assigned 
        FOREIGN KEY (assigned_to) 
        REFERENCES users(user_id) 
        ON DELETE SET DEFAULT
);

-- NO ACTION: Similar to RESTRICT (PostgreSQL default)
CREATE TABLE comments (
    comment_id SERIAL PRIMARY KEY,
    post_id INT,
    CONSTRAINT fk_post 
        FOREIGN KEY (post_id) 
        REFERENCES posts(post_id) 
        ON DELETE NO ACTION
);
```

---

## **2.3 Constraints**

Constraints enforce rules on data.

```sql name=constraints.sql
-- ============================================
-- ALL CONSTRAINTS EXAMPLES
-- ============================================

CREATE TABLE users (
    -- PRIMARY KEY constraint
    user_id SERIAL PRIMARY KEY,
    
    -- NOT NULL constraint (required field)
    username VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL,
    
    -- UNIQUE constraint (no duplicates)
    username VARCHAR(50) UNIQUE,
    email VARCHAR(100) UNIQUE,
    
    -- CHECK constraint (validation rule)
    age INT CHECK (age >= 18 AND age <= 120),
    
    -- DEFAULT constraint
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    is_active BOOLEAN DEFAULT TRUE,
    status VARCHAR(20) DEFAULT 'pending',
    
    -- Multiple constraints on one column
    phone VARCHAR(20) NOT NULL UNIQUE CHECK (phone ~ '^\+?[0-9]{10,15}$')
);

-- ============================================
-- NAMED CONSTRAINTS (Better for maintenance)
-- ============================================

CREATE TABLE products (
    product_id SERIAL,
    product_name VARCHAR(200) NOT NULL,
    price DECIMAL(10, 2) NOT NULL,
    stock INT NOT NULL,
    category VARCHAR(50),
    
    -- Named constraints
    CONSTRAINT pk_products PRIMARY KEY (product_id),
    CONSTRAINT uq_product_name UNIQUE (product_name),
    CONSTRAINT chk_price_positive CHECK (price > 0),
    CONSTRAINT chk_stock_non_negative CHECK (stock >= 0),
    CONSTRAINT chk_category_valid CHECK (category IN ('electronics', 'clothing', 'food', 'books'))
);

-- ============================================
-- ADD/DROP CONSTRAINTS (ALTER TABLE)
-- ============================================

-- Add constraint to existing table
ALTER TABLE users 
ADD CONSTRAINT chk_email_format 
CHECK (email ~* '^[A-Za-z0-9._%+-]+@[A-Za-z0-9.-]+\.[A-Za-z]{2,}$');

-- Drop constraint
ALTER TABLE users DROP CONSTRAINT chk_email_format;

-- Add NOT NULL
ALTER TABLE users ALTER COLUMN phone SET NOT NULL;

-- Remove NOT NULL
ALTER TABLE users ALTER COLUMN phone DROP NOT NULL;

-- Add UNIQUE constraint
ALTER TABLE users ADD CONSTRAINT uq_phone UNIQUE (phone);

-- Add DEFAULT
ALTER TABLE users ALTER COLUMN status SET DEFAULT 'active';

-- ============================================
-- COMPOSITE CONSTRAINTS
-- ============================================

CREATE TABLE bookings (
    booking_id SERIAL PRIMARY KEY,
    room_number INT NOT NULL,
    booking_date DATE NOT NULL,
    guest_name VARCHAR(100),
    
    -- Composite unique constraint
    -- Same room cannot be booked twice on same date
    CONSTRAINT uq_room_date UNIQUE (room_number, booking_date),
    
    -- Complex check constraint
    CONSTRAINT chk_future_booking CHECK (booking_date >= CURRENT_DATE)
);
```

### **PostgreSQL-Specific Auto-Increment**

```sql name=postgresql_auto_increment.sql
-- ============================================
-- SERIAL TYPES (PostgreSQL)
-- ============================================

-- SERIAL = INTEGER with auto-increment (1 to 2,147,483,647)
CREATE TABLE small_table (
    id SERIAL PRIMARY KEY
);

-- BIGSERIAL = BIGINT with auto-increment (1 to 9,223,372,036,854,775,807)
CREATE TABLE large_table (
    id BIGSERIAL PRIMARY KEY
);

-- SMALLSERIAL = SMALLINT with auto-increment (1 to 32,767)
CREATE TABLE tiny_table (
    id SMALLSERIAL PRIMARY KEY
);

-- Under the hood, SERIAL creates a sequence
-- Equivalent to:
CREATE SEQUENCE users_user_id_seq;

CREATE TABLE users (
    user_id INT DEFAULT nextval('users_user_id_seq') PRIMARY KEY
);

-- Manually managing sequences
CREATE SEQUENCE custom_seq START 1000 INCREMENT 1;

CREATE TABLE orders (
    order_id INT DEFAULT nextval('custom_seq') PRIMARY KEY
);

-- Reset sequence
ALTER SEQUENCE custom_seq RESTART WITH 5000;

-- Get current value
SELECT currval('custom_seq');

-- Get next value
SELECT nextval('custom_seq');
```

---

# **3. SQL Data Types**

## **3.1 Common Data Types**

```sql name=data_types.sql
-- ============================================
-- NUMERIC DATA TYPES
-- ============================================

CREATE TABLE numeric_examples (
    -- Integer types
    small_int SMALLINT,           -- -32,768 to 32,767
    normal_int INT,               -- -2.1B to 2.1B
    big_int BIGINT,               -- -9.2E18 to 9.2E18
    
    -- Decimal types (exact precision)
    price DECIMAL(10, 2),         -- 10 digits, 2 after decimal
    salary NUMERIC(12, 2),        -- Same as DECIMAL
    
    -- Floating-point (approximate)
    float_val REAL,               -- 6 decimal digits precision
    double_val DOUBLE PRECISION,  -- 15 decimal digits precision
    
    -- Serial (auto-increment)
    id SERIAL,
    big_id BIGSERIAL
);

-- ============================================
-- STRING/CHARACTER DATA TYPES
-- ============================================

CREATE TABLE string_examples (
    -- Fixed length (padded with spaces)
    country_code CHAR(2),          -- Always 2 characters
    
    -- Variable length (recommended)
    username VARCHAR(50),          -- Max 50 characters
    email VARCHAR(100),
    
    -- Unlimited text
    description TEXT,              -- No length limit
    bio TEXT
);

-- ============================================
-- DATE AND TIME DATA TYPES
-- ============================================

CREATE TABLE datetime_examples (
    -- Date only
    birth_date DATE,               -- YYYY-MM-DD
    
    -- Time only
    appointment_time TIME,         -- HH:MM:SS
    
    -- Date and time
    created_at TIMESTAMP,          -- YYYY-MM-DD HH:MM:SS
    
    -- Timestamp with timezone (recommended)
    updated_at TIMESTAMPTZ,        -- Timezone-aware
    
    -- Time interval
    duration INTERVAL              -- '1 day', '2 hours', etc.
);

-- ============================================
-- BOOLEAN DATA TYPE
-- ============================================

CREATE TABLE boolean_examples (
    is_active BOOLEAN,             -- TRUE, FALSE, NULL
    is_verified BOOL,              -- Alias for BOOLEAN
    has_access BOOLEAN DEFAULT FALSE
);

-- ============================================
-- OTHER COMMON DATA TYPES
-- ============================================

CREATE TABLE other_types (
    -- Binary data
    file_data BYTEA,               -- Binary data
    
    -- UUID (Universally Unique Identifier)
    uuid_col UUID,
    
    -- JSON
    metadata JSON,
    metadata_indexed JSONB,        -- Binary JSON (indexable)
    
    -- Array (PostgreSQL)
    tags TEXT[],
    scores INT[],
    
    -- Enum (PostgreSQL)
    status status_enum,
    
    -- IP Address (PostgreSQL)
    ip_address INET,
    
    -- Money (PostgreSQL)
    amount MONEY
);
```

### **Choosing the Right Data Type**

```sql name=data_type_best_practices.sql
-- ============================================
-- BEST PRACTICES FOR DATA TYPES
-- ============================================

CREATE TABLE best_practices (
    -- ✅ Use BIGSERIAL for IDs (future-proof)
    id BIGSERIAL PRIMARY KEY,
    
    -- ✅ Use VARCHAR with reasonable limits
    username VARCHAR(50) NOT NULL,  -- Not VARCHAR without limit
    
    -- ✅ Use TEXT for long content
    article_content TEXT,
    
    -- ✅ Use DECIMAL for money (not FLOAT)
    price DECIMAL(10, 2),          -- Exact precision
    
    -- ✅ Use TIMESTAMPTZ (timezone-aware)
    created_at TIMESTAMPTZ DEFAULT CURRENT_TIMESTAMP,
    
    -- ✅ Use BOOLEAN for true/false
    is_active BOOLEAN DEFAULT TRUE,
    
    -- ✅ Use SMALLINT for small ranges
    age SMALLINT CHECK (age BETWEEN 0 AND 150),
    
    -- ✅ Use ENUM for fixed sets (PostgreSQL)
    status status_type NOT NULL DEFAULT 'pending',
    
    -- ❌ AVOID: CHAR unless fixed-length needed
    -- ❌ AVOID: FLOAT/REAL for money
    -- ❌ AVOID: VARCHAR without limit in production
);

-- Create ENUM type
CREATE TYPE status_type AS ENUM ('pending', 'active', 'inactive', 'deleted');
```

### **Data Type Conversion (CAST)**

```sql name=type_casting.sql
-- ============================================
-- TYPE CASTING / CONVERSION
-- ============================================

-- Method 1: CAST function
SELECT CAST('123' AS INTEGER);
SELECT CAST('2024-01-01' AS DATE);
SELECT CAST(123.45 AS VARCHAR);

-- Method 2: :: operator (PostgreSQL)
SELECT '123'::INTEGER;
SELECT '2024-01-01'::DATE;
SELECT 123.45::VARCHAR;

-- Practical examples
SELECT 
    '100'::INT + '200'::INT AS total,
    '2024-01-15'::DATE + INTERVAL '1 month' AS next_month,
    123.456::NUMERIC(5,2) AS rounded;

-- Type conversion in queries
SELECT 
    user_id,
    created_at::DATE AS date_only,
    created_at::TIME AS time_only,
    age::VARCHAR || ' years old' AS age_text
FROM users;
```

---

# **4. CRUD Operations**

CRUD = **C**reate, **R**ead, **U**pdate, **D**elete

## **4.1 CREATE (INSERT)**

```sql name=insert_operations.sql
-- ============================================
-- INSERT - SINGLE ROW
-- ============================================

-- Method 1: Specify all columns
INSERT INTO users (username, email, age, created_at)
VALUES ('john_doe', 'john@example.com', 25, CURRENT_TIMESTAMP);

-- Method 2: Without column names (must match order)
INSERT INTO users 
VALUES (DEFAULT, 'jane_doe', 'jane@example.com', 30, CURRENT_TIMESTAMP, TRUE);

-- Method 3: Partial columns (others get DEFAULT or NULL)
INSERT INTO users (username, email)
VALUES ('bob_smith', 'bob@example.com');

-- ============================================
-- BULK INSERT (Multiple Rows)
-- ============================================

INSERT INTO products (product_name, price, stock)
VALUES 
    ('Laptop', 999.99, 50),
    ('Mouse', 29.99, 200),
    ('Keyboard', 79.99, 150),
    ('Monitor', 299.99, 75);

-- ============================================
-- INSERT ... RETURNING (PostgreSQL)
-- ============================================

-- Return inserted data
INSERT INTO users (username, email)
VALUES ('alice', 'alice@example.com')
RETURNING user_id, username, created_at;

-- Useful for getting auto-generated IDs
INSERT INTO orders (user_id, total_amount)
VALUES (1, 150.00)
RETURNING order_id;

-- ============================================
-- INSERT ... ON CONFLICT (UPSERT)
-- ============================================

-- Insert or update if conflict
INSERT INTO users (username, email, age)
VALUES ('john_doe', 'john_new@example.com', 26)
ON CONFLICT (username) 
DO UPDATE SET 
    email = EXCLUDED.email,
    age = EXCLUDED.age,
    updated_at = CURRENT_TIMESTAMP;

-- Insert or ignore if conflict
INSERT INTO tags (tag_name)
VALUES ('python'), ('sql'), ('python')
ON CONFLICT (tag_name) DO NOTHING;

-- ============================================
-- INSERT FROM SELECT
-- ============================================

-- Copy data from another table
INSERT INTO archived_users (user_id, username, email)
SELECT user_id, username, email
FROM users
WHERE last_login < CURRENT_DATE - INTERVAL '1 year';

-- ============================================
-- INSERT WITH DEFAULT VALUES
-- ============================================

INSERT INTO logs DEFAULT VALUES;  -- All columns get defaults

-- ============================================
-- BEST PRACTICES
-- ============================================

-- ✅ Always specify column names
INSERT INTO users (username, email) VALUES ('test', 'test@example.com');

-- ❌ Avoid implicit column order
INSERT INTO users VALUES ('test', 'test@example.com');  -- Fragile

-- ✅ Use RETURNING for auto-generated values
-- ✅ Use ON CONFLICT for idempotency
-- ✅ Use transactions for bulk inserts
```

---

## **4.2 READ (SELECT)**

```sql name=select_operations.sql
-- ============================================
-- SELECT - BASIC QUERIES
-- ============================================

-- Select all columns
SELECT * FROM users;

-- Select specific columns
SELECT user_id, username, email FROM users;

-- Select with alias
SELECT 
    user_id AS id,
    username AS name,
    email AS contact
FROM users;

-- ============================================
-- SELECT DISTINCT
-- ============================================

-- Get unique values
SELECT DISTINCT city FROM users;

-- Distinct on multiple columns
SELECT DISTINCT city, state FROM users;

-- Count distinct
SELECT COUNT(DISTINCT city) AS unique_cities FROM users;

-- ============================================
-- SELECT WITH EXPRESSIONS
-- ============================================

SELECT 
    username,
    age,
    age + 10 AS age_in_10_years,
    price * 1.1 AS price_with_tax,
    UPPER(email) AS email_uppercase,
    CONCAT(first_name, ' ', last_name) AS full_name
FROM users;

-- ============================================
-- SELECT WITH CASE
-- ============================================

SELECT 
    username,
    age,
    CASE 
        WHEN age < 18 THEN 'Minor'
        WHEN age BETWEEN 18 AND 64 THEN 'Adult'
        ELSE 'Senior'
    END AS age_group,
    CASE 
        WHEN price > 100 THEN 'Expensive'
        WHEN price > 50 THEN 'Moderate'
        ELSE 'Cheap'
    END AS price_category
FROM users;

-- ============================================
-- SELECT TOP N RECORDS
-- ============================================

-- PostgreSQL, MySQL 8+
SELECT * FROM users LIMIT 10;

-- With offset (pagination)
SELECT * FROM users LIMIT 10 OFFSET 20;  -- Skip first 20

-- SQL Server
SELECT TOP 10 * FROM users;

-- ============================================
-- SELECT INTO (Create new table)
-- ============================================

-- PostgreSQL
CREATE TABLE active_users AS
SELECT * FROM users WHERE is_active = TRUE;

-- Or
SELECT * INTO active_users FROM users WHERE is_active = TRUE;
```

---

## **4.3 UPDATE**

```sql name=update_operations.sql
-- ============================================
-- UPDATE - SINGLE COLUMN
-- ============================================

-- ⚠️ WITHOUT WHERE - Updates ALL rows
UPDATE users SET email = 'newemail@example.com';

-- ✅ WITH WHERE - Updates specific rows
UPDATE users 
SET email = 'john_new@example.com'
WHERE user_id = 1;

-- ============================================
-- UPDATE - MULTIPLE COLUMNS
-- ============================================

UPDATE users 
SET 
    email = 'updated@example.com',
    age = 30,
    updated_at = CURRENT_TIMESTAMP
WHERE username = 'john_doe';

-- ============================================
-- UPDATE WITH CALCULATIONS
-- ============================================

-- Increment value
UPDATE products 
SET stock = stock + 100
WHERE product_id = 5;

-- Apply percentage
UPDATE products 
SET price = price * 1.10  -- 10% increase
WHERE category = 'electronics';

-- ============================================
-- UPDATE FROM ANOTHER TABLE
-- ============================================

-- PostgreSQL
UPDATE orders o
SET status = 'shipped'
FROM shipments s
WHERE o.order_id = s.order_id
    AND s.shipped_date IS NOT NULL;

-- Standard SQL
UPDATE orders
SET status = 'shipped'
WHERE order_id IN (
    SELECT order_id 
    FROM shipments 
    WHERE shipped_date IS NOT NULL
);

-- ============================================
-- UPDATE ... RETURNING (PostgreSQL)
-- ============================================

UPDATE users 
SET last_login = CURRENT_TIMESTAMP
WHERE user_id = 1
RETURNING user_id, username, last_login;

-- ============================================
-- CONDITIONAL UPDATE
-- ============================================

UPDATE products
SET 
    stock = CASE 
        WHEN stock < 10 THEN stock + 100
        WHEN stock < 50 THEN stock + 50
        ELSE stock
    END,
    status = CASE 
        WHEN stock = 0 THEN 'out_of_stock'
        WHEN stock < 10 THEN 'low_stock'
        ELSE 'in_stock'
    END;

-- ============================================
-- SAFE UPDATE PRACTICES
-- ============================================

-- ✅ Always use WHERE clause
UPDATE users SET status = 'inactive' WHERE last_login < CURRENT_DATE - INTERVAL '1 year';

-- ✅ Test with SELECT first
SELECT * FROM users WHERE last_login < CURRENT_DATE - INTERVAL '1 year';

-- ✅ Use transactions for critical updates
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;
COMMIT;

-- ✅ Backup before mass updates
CREATE TABLE users_backup AS SELECT * FROM users;
```

---

## **4.4 DELETE**

```sql name=delete_operations.sql
-- ============================================
-- DELETE - BASIC
-- ============================================

-- ⚠️ WITHOUT WHERE - Deletes ALL rows
DELETE FROM users;

-- ✅ WITH WHERE - Deletes specific rows
DELETE FROM users WHERE user_id = 10;

-- Delete with condition
DELETE FROM logs 
WHERE created_at < CURRENT_DATE - INTERVAL '30 days';

-- ============================================
-- DELETE ... RETURNING (PostgreSQL)
-- ============================================

DELETE FROM users 
WHERE is_active = FALSE
RETURNING user_id, username, email;

-- ============================================
-- DELETE WITH SUBQUERY
-- ============================================

DELETE FROM orders
WHERE user_id IN (
    SELECT user_id 
    FROM users 
    WHERE is_deleted = TRUE
);

-- ============================================
-- DELETE WITH JOIN (PostgreSQL)
-- ============================================

DELETE FROM order_items oi
USING orders o
WHERE oi.order_id = o.order_id
    AND o.status = 'cancelled';

-- ============================================
-- SOFT DELETE (Recommended)
-- ============================================

-- Instead of deleting, mark as deleted
ALTER TABLE users ADD COLUMN deleted_at TIMESTAMPTZ;

-- Soft delete
UPDATE users 
SET deleted_at = CURRENT_TIMESTAMP
WHERE user_id = 10;

-- Query only active records
SELECT * FROM users WHERE deleted_at IS NULL;

-- Create view for active users
CREATE VIEW active_users AS
SELECT * FROM users WHERE deleted_at IS NULL;

-- ============================================
-- TRUNCATE (Fast delete all)
-- ============================================

-- Faster than DELETE for removing all rows
TRUNCATE TABLE logs;

-- Truncate with cascade
TRUNCATE TABLE users CASCADE;  -- Also truncates dependent tables

-- Truncate with restart identity
TRUNCATE TABLE users RESTART IDENTITY;  -- Reset auto-increment

-- ============================================
-- DELETE SAFETY
-- ============================================

-- ✅ Always test with SELECT first
SELECT * FROM users WHERE last_login < '2020-01-01';

-- ✅ Use transactions
BEGIN;
DELETE FROM users WHERE last_login < '2020-01-01';
-- Check results
SELECT COUNT(*) FROM users;
ROLLBACK;  -- Or COMMIT if satisfied

-- ✅ Prefer soft deletes for user data
-- ✅ Use CASCADE carefully
-- ✅ Backup before mass deletes
```

---

# **5. Query Filtering & Sorting**

## **5.1 WHERE Clause**

```sql name=where_clause.sql
-- ============================================
-- WHERE - BASIC FILTERING
-- ============================================

-- Equality
SELECT * FROM users WHERE age = 25;

-- Inequality
SELECT * FROM users WHERE age != 25;
SELECT * FROM users WHERE age <> 25;  -- Same as !=

-- Comparison operators
SELECT * FROM products WHERE price > 100;
SELECT * FROM products WHERE price >= 100;
SELECT * FROM products WHERE price < 50;
SELECT * FROM products WHERE price <= 50;

-- NULL checks
SELECT * FROM users WHERE email IS NULL;
SELECT * FROM users WHERE email IS NOT NULL;

-- String matching (case-sensitive)
SELECT * FROM users WHERE username = 'john_doe';

-- ============================================
-- AND, OR, NOT
-- ============================================

-- AND (all conditions must be true)
SELECT * FROM users 
WHERE age >= 18 AND age <= 65 AND is_active = TRUE;

-- OR (at least one condition must be true)
SELECT * FROM products 
WHERE category = 'electronics' OR category = 'computers';

-- NOT
SELECT * FROM users WHERE NOT is_active;
SELECT * FROM users WHERE is_active = FALSE;  -- Same as above

-- Combining AND & OR (use parentheses)
SELECT * FROM products
WHERE (category = 'electronics' OR category = 'computers')
    AND price < 500
    AND stock > 0;

-- ============================================
-- IN Operator
-- ============================================

-- Check if value in list
SELECT * FROM users 
WHERE city IN ('New York', 'Los Angeles', 'Chicago');

-- Equivalent to multiple OR
SELECT * FROM users 
WHERE city = 'New York' OR city = 'Los Angeles' OR city = 'Chicago';

-- NOT IN
SELECT * FROM products 
WHERE category NOT IN ('clothing', 'shoes');

-- IN with subquery
SELECT * FROM orders
WHERE user_id IN (SELECT user_id FROM users WHERE is_premium = TRUE);

-- ============================================
-- BETWEEN Operator
-- ============================================

-- Inclusive range
SELECT * FROM products 
WHERE price BETWEEN 50 AND 100;

-- Equivalent to
SELECT * FROM products 
WHERE price >= 50 AND price <= 100;

-- Date range
SELECT * FROM orders 
WHERE order_date BETWEEN '2024-01-01' AND '2024-12-31';

-- NOT BETWEEN
SELECT * FROM products 
WHERE price NOT BETWEEN 50 AND 100;

-- ============================================
-- LIKE Operator (Pattern Matching)
-- ============================================

-- % = any number of characters
-- _ = exactly one character

-- Starts with
SELECT * FROM users WHERE username LIKE 'john%';

-- Ends with
SELECT * FROM users WHERE email LIKE '%@gmail.com';

-- Contains
SELECT * FROM products WHERE product_name LIKE '%laptop%';

-- Exactly one character
SELECT * FROM codes WHERE code LIKE 'A_B';  -- A1B, A2B, etc.

-- NOT LIKE
SELECT * FROM users WHERE email NOT LIKE '%@spam.com';

-- Case-insensitive LIKE (PostgreSQL)
SELECT * FROM users WHERE username ILIKE 'JOHN%';

-- ============================================
-- Regular Expressions (PostgreSQL)
-- ============================================

-- ~ operator (case-sensitive regex)
SELECT * FROM users WHERE email ~ '^[a-z]+@[a-z]+\.com$';

-- ~* operator (case-insensitive regex)
SELECT * FROM users WHERE username ~* '^john';

-- !~ (does not match)
SELECT * FROM users WHERE phone !~ '^\+1';

-- ============================================
-- MULTIPLE CONDITIONS WITH CASE
-- ============================================

SELECT 
    user_id,
    username,
    CASE 
        WHEN age < 18 THEN 'Minor'
        WHEN age BETWEEN 18 AND 30 THEN 'Young Adult'
        WHEN age BETWEEN 31 AND 50 THEN 'Adult'
        ELSE 'Senior'
    END AS age_group
FROM users
WHERE is_active = TRUE;
```

---

## **5.2 ORDER BY**

```sql name=order_by.sql
-- ============================================
-- ORDER BY - SORTING RESULTS
-- ============================================

-- Ascending order (default)
SELECT * FROM products ORDER BY price;
SELECT * FROM products ORDER BY price ASC;

-- Descending order
SELECT * FROM products ORDER BY price DESC;

-- Multiple columns
SELECT * FROM users 
ORDER BY city ASC, age DESC;

-- Order by calculated column
SELECT 
    username,
    age,
    age * 365 AS days_lived
FROM users
ORDER BY days_lived DESC;

-- Order by alias
SELECT 
    username,
    first_name || ' ' || last_name AS full_name
FROM users
ORDER BY full_name;

-- ============================================
-- ORDER BY with NULLS
-- ============================================

-- PostgreSQL
SELECT * FROM users ORDER BY last_login NULLS FIRST;
SELECT * FROM users ORDER BY last_login NULLS LAST;

-- Default behavior:
-- ASC: NULLS LAST
-- DESC: NULLS FIRST

-- ============================================
-- ORDER BY CASE
-- ============================================

-- Custom sort order
SELECT * FROM orders
ORDER BY 
    CASE status
        WHEN 'urgent' THEN 1
        WHEN 'high' THEN 2
        WHEN 'medium' THEN 3
        WHEN 'low' THEN 4
    END;

-- ============================================
-- ORDER BY with LIMIT
-- ============================================

-- Top 10 highest-paid employees
SELECT * FROM employees 
ORDER BY salary DESC 
LIMIT 10;

-- Page 3 (records 21-30)
SELECT * FROM products 
ORDER BY created_at DESC 
LIMIT 10 OFFSET 20;

-- ============================================
-- ORDER BY RANDOM
-- ============================================

-- PostgreSQL
SELECT * FROM users ORDER BY RANDOM() LIMIT 5;

-- MySQL
SELECT * FROM users ORDER BY RAND() LIMIT 5;
```

---

## **5.3 LIMIT & OFFSET (Pagination)**

```sql name=limit_offset.sql
-- ============================================
-- LIMIT & OFFSET
-- ============================================

-- First 10 records
SELECT * FROM products LIMIT 10;

-- Skip first 20, get next 10 (page 3)
SELECT * FROM products LIMIT 10 OFFSET 20;

-- ============================================
-- PAGINATION FORMULA
-- ============================================

/*
page_size = 10
page_number = 3

OFFSET = (page_number - 1) * page_size
LIMIT = page_size

Page 1: LIMIT 10 OFFSET 0
Page 2: LIMIT 10 OFFSET 10
Page 3: LIMIT 10 OFFSET 20
*/

-- Page 1
SELECT * FROM products ORDER BY product_id LIMIT 10 OFFSET 0;

-- Page 2
SELECT * FROM products ORDER BY product_id LIMIT 10 OFFSET 10;

-- ============================================
-- EFFICIENT PAGINATION (Cursor-based)
-- ============================================

-- Instead of OFFSET (slow for large datasets)
-- Use WHERE with last seen ID

-- First page
SELECT * FROM products 
WHERE product_id > 0 
ORDER BY product_id 
LIMIT 10;

-- Next page (assume last ID was 10)
SELECT * FROM products 
WHERE product_id > 10 
ORDER BY product_id 
LIMIT 10;

-- ============================================
-- COUNT TOTAL WITH PAGINATION
-- ============================================

-- Get total count
SELECT COUNT(*) AS total FROM products WHERE category = 'electronics';

-- Get paginated results
SELECT * FROM products 
WHERE category = 'electronics'
ORDER BY product_id 
LIMIT 10 OFFSET 0;

-- Combined with window function (PostgreSQL)
SELECT 
    *,
    COUNT(*) OVER() AS total_count
FROM products 
WHERE category = 'electronics'
ORDER BY product_id 
LIMIT 10 OFFSET 0;
```

---

# **6. Aggregate Functions**

## **6.1 Basic Aggregate Functions**

```sql name=aggregate_functions.sql
-- ============================================
-- COUNT
-- ============================================

-- Count all rows
SELECT COUNT(*) AS total_users FROM users;

-- Count non-NULL values
SELECT COUNT(email) AS users_with_email FROM users;

-- Count distinct values
SELECT COUNT(DISTINCT city) AS unique_cities FROM users;

-- Count with condition
SELECT COUNT(*) AS active_users FROM users WHERE is_active = TRUE;

-- ============================================
-- SUM
-- ============================================

-- Total sum
SELECT SUM(price) AS total_revenue FROM orders;

-- Sum with condition
SELECT SUM(amount) AS total_refunds 
FROM transactions 
WHERE type = 'refund';

-- Sum with FILTER (PostgreSQL 9.4+)
SELECT 
    SUM(amount) FILTER (WHERE type = 'sale') AS total_sales,
    SUM(amount) FILTER (WHERE type = 'refund') AS total_refunds
FROM transactions;

-- ============================================
-- AVG (Average)
-- ============================================

-- Simple average
SELECT AVG(age) AS average_age FROM users;

-- Round average
SELECT ROUND(AVG(price)::numeric, 2) AS avg_price FROM products;

-- Average with condition
SELECT AVG(salary) AS avg_salary 
FROM employees 
WHERE department = 'Engineering';

-- ============================================
-- MIN & MAX
-- ============================================

-- Minimum value
SELECT MIN(price) AS cheapest_product FROM products;

-- Maximum value
SELECT MAX(salary) AS highest_salary FROM employees;

-- Both together
SELECT 
    MIN(price) AS min_price,
    MAX(price) AS max_price,
    AVG(price) AS avg_price
FROM products;

-- Min/Max dates
SELECT 
    MIN(created_at) AS first_user,
    MAX(created_at) AS latest_user
FROM users;

-- ============================================
-- MULTIPLE AGGREGATES
-- ============================================

SELECT 
    COUNT(*) AS total_products,
    COUNT(DISTINCT category) AS unique_categories,
    SUM(stock) AS total_stock,
    AVG(price) AS avg_price,
    MIN(price) AS min_price,
    MAX(price) AS max_price
FROM products;
```

---

## **6.2 GROUP BY**

```sql name=group_by.sql
-- ============================================
-- GROUP BY - BASIC
-- ============================================

-- Count users by city
SELECT 
    city,
    COUNT(*) AS user_count
FROM users
GROUP BY city;

-- Average salary by department
SELECT 
    department,
    AVG(salary) AS avg_salary
FROM employees
GROUP BY department;

-- Total revenue by month
SELECT 
    DATE_TRUNC('month', order_date) AS month,
    SUM(total_amount) AS monthly_revenue
FROM orders
GROUP BY DATE_TRUNC('month', order_date)
ORDER BY month DESC;

-- ============================================
-- GROUP BY MULTIPLE COLUMNS
-- ============================================

SELECT 
    city,
    state,
    COUNT(*) AS user_count
FROM users
GROUP BY city, state
ORDER BY user_count DESC;

-- Sales by category and month
SELECT 
    category,
    DATE_TRUNC('month', sale_date) AS month,
    SUM(amount) AS total_sales
FROM sales
GROUP BY category, DATE_TRUNC('month', sale_date)
ORDER BY category, month;

-- ============================================
-- GROUP BY with HAVING (Filter Groups)
-- ============================================

-- Cities with more than 100 users
SELECT 
    city,
    COUNT(*) AS user_count
FROM users
GROUP BY city
HAVING COUNT(*) > 100
ORDER BY user_count DESC;

-- Products sold more than 50 times
SELECT 
    product_id,
    product_name,
    COUNT(*) AS times_sold,
    SUM(quantity) AS total_quantity
FROM order_items
GROUP BY product_id, product_name
HAVING COUNT(*) > 50
ORDER BY times_sold DESC;

-- Departments with avg salary > 75000
SELECT 
    department,
    COUNT(*) AS employee_count,
    AVG(salary) AS avg_salary
FROM employees
GROUP BY department
HAVING AVG(salary) > 75000;

-- ============================================
-- WHERE vs HAVING
-- ============================================

/*
WHERE: Filters rows BEFORE grouping
HAVING: Filters groups AFTER grouping
*/

-- Example combining both
SELECT 
    category,
    COUNT(*) AS product_count,
    AVG(price) AS avg_price
FROM products
WHERE is_active = TRUE              -- Filter rows first
GROUP BY category
HAVING AVG(price) > 100             -- Filter groups after
ORDER BY avg_price DESC;

-- ============================================
-- GROUP BY with ROLLUP (Subtotals) - PostgreSQL
-- ============================================

-- Get totals with subtotals
SELECT 
    category,
    COUNT(*) AS product_count,
    SUM(stock) AS total_stock
FROM products
GROUP BY ROLLUP(category);

-- Multiple levels
SELECT 
    city,
    state,
    COUNT(*) AS user_count
FROM users
GROUP BY ROLLUP(state, city)
ORDER BY state, city;

-- ============================================
-- GROUP BY ALL (PostgreSQL 16+)
-- ============================================

-- Automatically group by non-aggregate columns
SELECT 
    category,
    COUNT(*) AS count
FROM products
GROUP BY ALL;  -- Groups by 'category' automatically
```

---

## **6.3 Advanced Aggregation**

```sql name=advanced_aggregation.sql
-- ============================================
-- STRING AGGREGATION
-- ============================================

-- PostgreSQL: STRING_AGG
SELECT 
    department,
    STRING_AGG(employee_name, ', ') AS employees
FROM employees
GROUP BY department;

-- With ordering
SELECT 
    department,
    STRING_AGG(employee_name, ', ' ORDER BY employee_name) AS employees
FROM employees
GROUP BY department;

-- MySQL: GROUP_CONCAT
SELECT 
    department,
    GROUP_CONCAT(employee_name ORDER BY employee_name SEPARATOR ', ') AS employees
FROM employees
GROUP BY department;

-- ============================================
-- ARRAY AGGREGATION (PostgreSQL)
-- ============================================

SELECT 
    department,
    ARRAY_AGG(employee_name ORDER BY employee_name) AS employee_array
FROM employees
GROUP BY department;

-- ============================================
-- JSON AGGREGATION (PostgreSQL)
-- ============================================

-- JSON object aggregation
SELECT 
    department,
    JSON_AGG(
        JSON_BUILD_OBJECT(
            'id', employee_id,
            'name', employee_name,
            'salary', salary
        )
    ) AS employees
FROM employees
GROUP BY department;

-- ============================================
-- CONDITIONAL AGGREGATION
-- ============================================

SELECT 
    department,
    COUNT(*) AS total_employees,
    COUNT(*) FILTER (WHERE salary > 100000) AS high_earners,
    COUNT(*) FILTER (WHERE age < 30) AS young_employees,
    SUM(salary) AS total_payroll,
    SUM(salary) FILTER (WHERE is_manager) AS manager_payroll
FROM employees
GROUP BY department;

-- Using CASE in aggregates
SELECT 
    department,
    SUM(CASE WHEN salary > 100000 THEN 1 ELSE 0 END) AS high_earners,
    SUM(CASE WHEN age < 30 THEN 1 ELSE 0 END) AS young_employees
FROM employees
GROUP BY department;

-- ============================================
-- PERCENTILES & STATISTICS (PostgreSQL)
-- ============================================

SELECT 
    department,
    PERCENTILE_CONT(0.5) WITHIN GROUP (ORDER BY salary) AS median_salary,
    PERCENTILE_CONT(0.25) WITHIN GROUP (ORDER BY salary) AS q1_salary,
    PERCENTILE_CONT(0.75) WITHIN GROUP (ORDER BY salary) AS q3_salary,
    MODE() WITHIN GROUP (ORDER BY city) AS most_common_city,
    STDDEV(salary) AS salary_stddev,
    VARIANCE(salary) AS salary_variance
FROM employees
GROUP BY department;
```

---

# **7. Joins**

Joins combine rows from two or more tables based on related columns.

## **7.1 INNER JOIN**

```sql name=inner_join.sql
-- ============================================
-- INNER JOIN - Returns matching rows only
-- ============================================

-- Basic INNER JOIN
SELECT 
    users.user_id,
    users.username,
    orders.order_id,
    orders.total_amount
FROM users
INNER JOIN orders ON users.user_id = orders.user_id;

-- With table aliases (recommended)
SELECT 
    u.user_id,
    u.username,
    o.order_id,
    o.total_amount,
    o.order_date
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id;

-- Multiple conditions
SELECT 
    u.username,
    o.order_id,
    o.total_amount
FROM users u
INNER JOIN orders o 
    ON u.user_id = o.user_id 
    AND o.status = 'completed'
WHERE u.is_active = TRUE;

-- ============================================
-- JOINING MULTIPLE TABLES
-- ============================================

SELECT 
    u.username,
    o.order_id,
    o.order_date,
    p.product_name,
    oi.quantity,
    oi.unit_price
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
INNER JOIN order_items oi ON o.order_id = oi.order_id
INNER JOIN products p ON oi.product_id = p.product_id
WHERE o.order_date >= '2024-01-01';

-- ============================================
-- INNER JOIN with AGGREGATES
-- ============================================

SELECT 
    u.user_id,
    u.username,
    COUNT(o.order_id) AS total_orders,
    SUM(o.total_amount) AS total_spent,
    AVG(o.total_amount) AS avg_order_value
FROM users u
INNER JOIN orders o ON u.user_id = o.user_id
GROUP BY u.user_id, u.username
HAVING COUNT(o.order_id) > 5
ORDER BY total_spent DESC;
```

---

## **7.2 LEFT JOIN (LEFT OUTER JOIN)**

```sql name=left_join.sql
-- ============================================
-- LEFT JOIN - Returns all left table rows + matching right rows
-- ============================================

-- Get all users with their orders (if any)
SELECT 
    u.user_id,
    u.username,
    o.order_id,
    o.total_amount
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id;

/*
Result includes:
- All users who have orders (with order details)
- All users who have NO orders (with NULL for order columns)
*/

-- ============================================
-- FIND USERS WITH NO ORDERS
-- ============================================

SELECT 
    u.user_id,
    u.username,
    u.email
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE o.order_id IS NULL;

-- ============================================
-- LEFT JOIN with COUNT
-- ============================================

SELECT 
    u.user_id,
    u.username,
    COUNT(o.order_id) AS order_count,
    COALESCE(SUM(o.total_amount), 0) AS total_spent
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
GROUP BY u.user_id, u.username
ORDER BY order_count DESC;

-- ============================================
-- LEFT JOIN MULTIPLE TABLES
-- ============================================

SELECT 
    u.username,
    COUNT(DISTINCT o.order_id) AS total_orders,
    COUNT(DISTINCT p.post_id) AS total_posts,
    COUNT(DISTINCT c.comment_id) AS total_comments
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
LEFT JOIN posts p ON u.user_id = p.user_id
LEFT JOIN comments c ON u.user_id = c.user_id
GROUP BY u.user_id, u.username;

-- ============================================
-- LEFT JOIN vs INNER JOIN Comparison
-- ============================================

-- INNER JOIN (only users who have orders)
SELECT COUNT(*) FROM users u
INNER JOIN orders o ON u.user_id = o.user_id;

-- LEFT JOIN (all users)
SELECT COUNT(*) FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id;

-- LEFT JOIN (only users WITHOUT orders)
SELECT COUNT(*) FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
WHERE o.order_id IS NULL;
```

---

## **7.3 RIGHT JOIN (RIGHT OUTER JOIN)**

```sql name=right_join.sql
-- ============================================
-- RIGHT JOIN - Returns all right table rows + matching left rows
-- ============================================

-- Get all orders with user info (even if user deleted)
SELECT 
    o.order_id,
    o.total_amount,
    o.order_date,
    u.username,
    u.email
FROM users u
RIGHT JOIN orders o ON u.user_id = o.user_id;

/*
Result includes:
- All orders with user information (if user exists)
- All orders where user was deleted (user columns are NULL)
*/

-- ============================================
-- RIGHT JOIN is rarely used
-- ============================================

-- This RIGHT JOIN:
SELECT * FROM users u
RIGHT JOIN orders o ON u.user_id = o.user_id;

-- Is equivalent to this LEFT JOIN:
SELECT * FROM orders o
LEFT JOIN users u ON o.user_id = u.user_id;

-- Most developers prefer LEFT JOIN for readability
```

---

## **7.4 FULL OUTER JOIN**

```sql name=full_outer_join.sql
-- ============================================
-- FULL OUTER JOIN - Returns all rows from both tables
-- ============================================

-- Get all users AND all orders (matched + unmatched)
SELECT 
    u.user_id,
    u.username,
    o.order_id,
    o.total_amount
FROM users u
FULL OUTER JOIN orders o ON u.user_id = o.user_id;

/*
Result includes:
- Users with orders (matched rows)
- Users without orders (NULL for order columns)
- Orders without users (NULL for user columns)
*/

-- ============================================
-- FIND UNMATCHED ROWS ON BOTH SIDES
-- ============================================

-- Users without orders OR orders without users
SELECT 
    u.user_id,
    u.username,
    o.order_id,
    o.total_amount,
    CASE 
        WHEN u.user_id IS NULL THEN 'Orphan Order'
        WHEN o.order_id IS NULL THEN 'User Without Orders'
        ELSE 'Matched'
    END AS match_status
FROM users u
FULL OUTER JOIN orders o ON u.user_id = o.user_id
WHERE u.user_id IS NULL OR o.order_id IS NULL;
```

---

## **7.5 CROSS JOIN**

```sql name=cross_join.sql
-- ============================================
-- CROSS JOIN - Cartesian product (all combinations)
-- ============================================

-- Every user paired with every product
SELECT 
    u.username,
    p.product_name
FROM users u
CROSS JOIN products p;

-- If users has 100 rows and products has 50 rows
-- Result will have 100 × 50 = 5,000 rows

-- ============================================
-- PRACTICAL USE CASE - Generate date series
-- ============================================

-- Create report with all dates, even if no sales
WITH date_series AS (
    SELECT generate_series(
        '2024-01-01'::date,
        '2024-01-31'::date,
        '1 day'::interval
    )::date AS report_date
)
SELECT 
    ds.report_date,
    COALESCE(SUM(s.amount), 0) AS daily_sales
FROM date_series ds
LEFT JOIN sales s ON DATE(s.sale_date) = ds.report_date
GROUP BY ds.report_date
ORDER BY ds.report_date;

-- ============================================
-- Generate all combinations for testing
-- ============================================

-- All combinations of sizes and colors
SELECT 
    s.size_name,
    c.color_name
FROM sizes s
CROSS JOIN colors c;
```

---

## **7.6 SELF JOIN**

```sql name=self_join.sql
-- ============================================
-- SELF JOIN - Join table to itself
-- ============================================

-- Example: Employee hierarchy (manager-employee relationship)
CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    employee_name VARCHAR(100),
    manager_id INT,
    FOREIGN KEY (manager_id) REFERENCES employees(employee_id)
);

-- Find employees and their managers
SELECT 
    e.employee_name AS employee,
    m.employee_name AS manager
FROM employees e
LEFT JOIN employees m ON e.manager_id = m.employee_id;

-- ============================================
-- FIND COLLEAGUES (Same Manager)
-- ============================================

SELECT 
    e1.employee_name AS employee1,
    e2.employee_name AS employee2,
    m.employee_name AS common_manager
FROM employees e1
INNER JOIN employees e2 ON e1.manager_id = e2.manager_id
INNER JOIN employees m ON e1.manager_id = m.employee_id
WHERE e1.employee_id < e2.employee_id  -- Avoid duplicates
ORDER BY common_manager, employee1;

-- ============================================
-- HIERARCHICAL QUERY (Recursive CTE)
-- ============================================

-- Get full org chart hierarchy
WITH RECURSIVE org_chart AS (
    -- Base case: top-level managers
    SELECT 
        employee_id,
        employee_name,
        manager_id,
        1 AS level,
        employee_name::TEXT AS path
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: their reports
    SELECT 
        e.employee_id,
        e.employee_name,
        e.manager_id,
        oc.level + 1,
        oc.path || ' > ' || e.employee_name
    FROM employees e
    INNER JOIN org_chart oc ON e.manager_id = oc.employee_id
)
SELECT 
    REPEAT('  ', level - 1) || employee_name AS hierarchy,
    level,
    path
FROM org_chart
ORDER BY path;

-- ============================================
-- FOLLOWERS EXAMPLE (Social Media)
-- ============================================

CREATE TABLE followers (
    follower_id INT,
    following_id INT,
    FOREIGN KEY (follower_id) REFERENCES users(user_id),
    FOREIGN KEY (following_id) REFERENCES users(user_id)
);

-- Find mutual followers (users who follow each other)
SELECT 
    u1.username AS user1,
    u2.username AS user2
FROM followers f1
INNER JOIN followers f2 
    ON f1.follower_id = f2.following_id 
    AND f1.following_id = f2.follower_id
INNER JOIN users u1 ON f1.follower_id = u1.user_id
INNER JOIN users u2 ON f1.following_id = u2.user_id
WHERE f1.follower_id < f1.following_id;  -- Avoid duplicates
```

---

## **7.7 Join Types Visual Summary**

```sql name=join_comparison.sql
-- ============================================
-- UNDERSTANDING RELATIONSHIPS
-- ============================================

-- ONE-TO-ONE
-- One user has one profile
CREATE TABLE users (user_id SERIAL PRIMARY KEY, username VARCHAR(50));
CREATE TABLE profiles (
    profile_id SERIAL PRIMARY KEY,
    user_id INT UNIQUE,  -- UNIQUE makes it one-to-one
    bio TEXT,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- ONE-TO-MANY
-- One user has many orders
CREATE TABLE users (user_id SERIAL PRIMARY KEY, username VARCHAR(50));
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT,  -- No UNIQUE, so one user can have many orders
    total_amount DECIMAL(10, 2),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- MANY-TO-MANY
-- Students enroll in many courses, courses have many students
CREATE TABLE students (student_id SERIAL PRIMARY KEY, name VARCHAR(100));
CREATE TABLE courses (course_id SERIAL PRIMARY KEY, title VARCHAR(100));
CREATE TABLE enrollments (  -- Junction table
    enrollment_id SERIAL PRIMARY KEY,
    student_id INT,
    course_id INT,
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id),
    UNIQUE (student_id, course_id)
);

-- Query many-to-many
SELECT 
    s.name AS student_name,
    c.title AS course_title,
    e.enrollment_date
FROM students s
INNER JOIN enrollments e ON s.student_id = e.student_id
INNER JOIN courses c ON e.course_id = c.course_id;
```

---

# **8. Subqueries & Advanced Queries**

## **8.1 Subqueries in SELECT**

```sql name=subqueries_select.sql
-- ============================================
-- SCALAR SUBQUERY (Returns single value)
-- ============================================

SELECT 
    username,
    age,
    (SELECT AVG(age) FROM users) AS avg_age,
    age - (SELECT AVG(age) FROM users) AS age_diff_from_avg
FROM users;

-- ============================================
-- CORRELATED SUBQUERY in SELECT
-- ============================================

-- Get each employee's salary compared to dept average
SELECT 
    e.employee_name,
    e.department,
    e.salary,
    (
        SELECT AVG(e2.salary)
        FROM employees e2
        WHERE e2.department = e.department
    ) AS dept_avg_salary
FROM employees e;

-- Count orders per user
SELECT 
    u.username,
    u.email,
    (
        SELECT COUNT(*)
        FROM orders o
        WHERE o.user_id = u.user_id
    ) AS order_count
FROM users u;
```

---

## **8.2 Subqueries in WHERE**

```sql name=subqueries_where.sql
-- ============================================
-- SUBQUERY WITH COMPARISON OPERATORS
-- ============================================

-- Find users older than average
SELECT * FROM users
WHERE age > (SELECT AVG(age) FROM users);

-- Products more expensive than average
SELECT product_name, price
FROM products
WHERE price > (SELECT AVG(price) FROM products);

-- ============================================
-- SUBQUERY WITH IN
-- ============================================

-- Find orders from premium users
SELECT * FROM orders
WHERE user_id IN (
    SELECT user_id FROM users WHERE is_premium = TRUE
);

-- Products in specific categories
SELECT * FROM products
WHERE category_id IN (
    SELECT category_id FROM categories 
    WHERE category_name IN ('Electronics', 'Computers')
);

-- ============================================
-- SUBQUERY WITH NOT IN
-- ============================================

-- Users who never placed an order
SELECT * FROM users
WHERE user_id NOT IN (
    SELECT DISTINCT user_id FROM orders
);

-- ⚠️ Warning: NOT IN with NULL values can cause issues
-- Safer alternative:
SELECT * FROM users u
WHERE NOT EXISTS (
    SELECT 1 FROM orders o WHERE o.user_id = u.user_id
);

-- ============================================
-- SUBQUERY WITH EXISTS
-- ============================================

-- Users who have at least one order
SELECT * FROM users u
WHERE EXISTS (
    SELECT 1 FROM orders o WHERE o.user_id = u.user_id
);

-- Products that were never ordered
SELECT * FROM products p
WHERE NOT EXISTS (
    SELECT 1 FROM order_items oi WHERE oi.product_id = p.product_id
);

-- ============================================
-- SUBQUERY WITH ANY/ALL
-- ============================================

-- Employees earning more than ANY employee in Sales dept
SELECT * FROM employees
WHERE salary > ANY (
    SELECT salary FROM employees WHERE department = 'Sales'
);

-- Employees earning more than ALL employees in Sales dept
SELECT * FROM employees
WHERE salary > ALL (
    SELECT salary FROM employees WHERE department = 'Sales'
);
```

---

## **8.3 Correlated Subqueries**

```sql name=correlated_subqueries.sql
-- ============================================
-- CORRELATED SUBQUERY (References outer query)
-- ============================================

-- Find employees earning above department average
SELECT 
    e1.employee_name,
    e1.department,
    e1.salary
FROM employees e1
WHERE e1.salary > (
    SELECT AVG(e2.salary)
    FROM employees e2
    WHERE e2.department = e1.department  -- Correlation
);

-- ============================================
-- FINDING TOP N PER GROUP
-- ============================================

-- Top 3 highest-paid employees per department
SELECT 
    e1.department,
    e1.employee_name,
    e1.salary
FROM employees e1
WHERE (
    SELECT COUNT(DISTINCT e2.salary)
    FROM employees e2
    WHERE e2.department = e1.department
        AND e2.salary >= e1.salary
) <= 3
ORDER BY e1.department, e1.salary DESC;

-- ============================================
-- ROW NUMBER SIMULATION
-- ============================================

-- Get row number within each department
SELECT 
    employee_name,
    department,
    salary,
    (
        SELECT COUNT(*)
        FROM employees e2
        WHERE e2.department = e1.department
            AND e2.salary >= e1.salary
    ) AS rank_in_dept
FROM employees e1
ORDER BY department, salary DESC;
```

---

## **8.4 Common Table Expressions (CTE)**

```sql name=cte_examples.sql
-- ============================================
-- BASIC CTE (WITH clause)
-- ============================================

-- More readable than subqueries
WITH active_users AS (
    SELECT user_id, username, email
    FROM users
    WHERE is_active = TRUE
)
SELECT 
    au.username,
    COUNT(o.order_id) AS order_count
FROM active_users au
LEFT JOIN orders o ON au.user_id = o.user_id
GROUP BY au.user_id, au.username;

-- ============================================
-- MULTIPLE CTEs
-- ============================================

WITH 
premium_users AS (
    SELECT user_id, username
    FROM users
    WHERE is_premium = TRUE
),
recent_orders AS (
    SELECT order_id, user_id, total_amount
    FROM orders
    WHERE order_date >= CURRENT_DATE - INTERVAL '30 days'
)
SELECT 
    pu.username,
    COUNT(ro.order_id) AS recent_order_count,
    SUM(ro.total_amount) AS recent_spending
FROM premium_users pu
LEFT JOIN recent_orders ro ON pu.user_id = ro.user_id
GROUP BY pu.user_id, pu.username;

-- ============================================
-- RECURSIVE CTE
-- ============================================

-- Generate series
WITH RECURSIVE numbers AS (
    SELECT 1 AS n
    UNION ALL
    SELECT n + 1 FROM numbers WHERE n < 10
)
SELECT * FROM numbers;

-- Organizational hierarchy
WITH RECURSIVE employee_hierarchy AS (
    -- Base case: CEO (no manager)
    SELECT 
        employee_id,
        employee_name,
        manager_id,
        1 AS level,
        ARRAY[employee_id] AS path
    FROM employees
    WHERE manager_id IS NULL
    
    UNION ALL
    
    -- Recursive case: employees reporting to previous level
    SELECT 
        e.employee_id,
        e.employee_name,
        e.manager_id,
        eh.level + 1,
        eh.path || e.employee_id
    FROM employees e
    INNER JOIN employee_hierarchy eh ON e.manager_id = eh.employee_id
)
SELECT 
    REPEAT('  ', level - 1) || employee_name AS hierarchy,
    level
FROM employee_hierarchy
ORDER BY path;

-- ============================================
-- CTE for Complex Aggregations
-- ============================================

WITH monthly_sales AS (
    SELECT 
        DATE_TRUNC('month', order_date) AS month,
        SUM(total_amount) AS total_sales
    FROM orders
    GROUP BY DATE_TRUNC('month', order_date)
),
sales_with_growth AS (
    SELECT 
        month,
        total_sales,
        LAG(total_sales) OVER (ORDER BY month) AS prev_month_sales,
        total_sales - LAG(total_sales) OVER (ORDER BY month) AS growth
    FROM monthly_sales
)
SELECT 
    month,
    total_sales,
    prev_month_sales,
    growth,
    ROUND((growth / NULLIF(prev_month_sales, 0) * 100)::numeric, 2) AS growth_percentage
FROM sales_with_growth
ORDER BY month;
```

---

## **8.5 Window Functions**

```sql name=window_functions.sql
-- ============================================
-- ROW_NUMBER, RANK, DENSE_RANK
-- ============================================

SELECT 
    employee_name,
    department,
    salary,
    ROW_NUMBER() OVER (PARTITION BY department ORDER BY salary DESC) AS row_num,
    RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS rank,
    DENSE_RANK() OVER (PARTITION BY department ORDER BY salary DESC) AS dense_rank
FROM employees;

/*
Difference:
ROW_NUMBER: 1, 2, 3, 4, 5, ... (unique, no ties)
RANK: 1, 2, 2, 4, 5, ... (skip numbers after tie)
DENSE_RANK: 1, 2, 2, 3, 4, ... (no gaps)
*/

-- ============================================
-- LAG and LEAD
-- ============================================

SELECT 
    order_date,
    total_amount,
    LAG(total_amount) OVER (ORDER BY order_date) AS prev_day_sales,
    LEAD(total_amount) OVER (ORDER BY order_date) AS next_day_sales,
    total_amount - LAG(total_amount) OVER (ORDER BY order_date) AS day_over_day_change
FROM daily_sales
ORDER BY order_date;

-- ============================================
-- FIRST_VALUE and LAST_VALUE
-- ============================================

SELECT 
    order_date,
    total_amount,
    FIRST_VALUE(total_amount) OVER (ORDER BY order_date) AS first_day_sales,
    LAST_VALUE(total_amount) OVER (
        ORDER BY order_date 
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS last_day_sales
FROM daily_sales;

-- ============================================
-- SUM, AVG, COUNT with OVER
-- ============================================

-- Running total
SELECT 
    order_date,
    daily_sales,
    SUM(daily_sales) OVER (ORDER BY order_date) AS running_total,
    AVG(daily_sales) OVER (ORDER BY order_date ROWS BETWEEN 6 PRECEDING AND CURRENT ROW) AS moving_avg_7days
FROM daily_sales;

-- Percentage of total
SELECT 
    category,
    total_sales,
    SUM(total_sales) OVER () AS grand_total,
    ROUND((total_sales * 100.0 / SUM(total_sales) OVER ())::numeric, 2) AS percentage_of_total
FROM category_sales;

-- ============================================
-- NTILE (Divide into buckets)
-- ============================================

-- Divide into quartiles
SELECT 
    employee_name,
    salary,
    NTILE(4) OVER (ORDER BY salary) AS salary_quartile
FROM employees;
```

---

*(Continuing in next message due to length...)*

## @meet41

contine from where it  was left

## Copilot

# **9. Indexes & Performance**

## **9.1 What is an Index?**

An **index** is a database object that improves the speed of data retrieval operations. Think of it like a book's index - instead of scanning every page, you can jump directly to the relevant section.

### **How Indexes Work**

```sql name=index_basics.sql
-- ============================================
-- INDEX FUNDAMENTALS
-- ============================================

-- Without index: Full table scan (slow)
-- Database must check every row
SELECT * FROM users WHERE email = 'john@example.com';
-- Scans: 1,000,000 rows → finds 1 row

-- With index on email: Direct lookup (fast)
CREATE INDEX idx_users_email ON users(email);
SELECT * FROM users WHERE email = 'john@example.com';
-- Index lookup: ~log(n) operations → finds 1 row

-- ============================================
-- B-TREE INDEX (Default in PostgreSQL)
-- ============================================

/*
B-Tree Structure (simplified):
                [M]
              /     \
          [C-L]     [N-Z]
         /  |  \    /  |  \
      [A] [D] [G] [N] [P] [T]
      
- Balanced tree structure
- Great for equality and range queries
- Used for: =, <, >, <=, >=, BETWEEN, IN, LIKE 'abc%'
*/

-- Create B-Tree index (default)
CREATE INDEX idx_users_age ON users(age);

-- B-Tree supports range queries
SELECT * FROM users WHERE age BETWEEN 25 AND 35;
```

---

## **9.2 Types of Indexes**

```sql name=index_types.sql
-- ============================================
-- 1. SINGLE-COLUMN INDEX
-- ============================================

CREATE INDEX idx_users_username ON users(username);
CREATE INDEX idx_orders_order_date ON orders(order_date);
CREATE INDEX idx_products_price ON products(price);

-- ============================================
-- 2. COMPOSITE INDEX (Multi-column)
-- ============================================

-- Index on multiple columns
CREATE INDEX idx_users_city_state ON users(city, state);

-- Order matters!
-- This index helps:
-- ✅ WHERE city = 'NYC' AND state = 'NY'
-- ✅ WHERE city = 'NYC'
-- ❌ WHERE state = 'NY' (can't use index efficiently)

-- Best practice: Most selective column first
CREATE INDEX idx_orders_user_date ON orders(user_id, order_date);

-- ============================================
-- 3. UNIQUE INDEX
-- ============================================

-- Enforces uniqueness AND improves performance
CREATE UNIQUE INDEX idx_users_email_unique ON users(email);

-- Equivalent to UNIQUE constraint
ALTER TABLE users ADD CONSTRAINT uq_email UNIQUE (email);
-- This automatically creates a unique index

-- ============================================
-- 4. PARTIAL INDEX (Filtered)
-- ============================================

-- Index only rows matching condition
CREATE INDEX idx_active_users ON users(username) 
WHERE is_active = TRUE;

-- Benefits:
-- - Smaller index size
-- - Faster index maintenance
-- - Only indexes relevant data

-- More examples
CREATE INDEX idx_pending_orders ON orders(order_date)
WHERE status = 'pending';

CREATE INDEX idx_recent_posts ON posts(created_at)
WHERE created_at > CURRENT_DATE - INTERVAL '30 days';

-- ============================================
-- 5. EXPRESSION INDEX (Functional Index)
-- ============================================

-- Index on computed values
CREATE INDEX idx_users_lower_email ON users(LOWER(email));

-- Now this query can use the index:
SELECT * FROM users WHERE LOWER(email) = 'john@example.com';

-- More examples
CREATE INDEX idx_users_full_name ON users(
    (first_name || ' ' || last_name)
);

CREATE INDEX idx_products_discounted_price ON products(
    (price * 0.9)
);

-- ============================================
-- 6. COVERING INDEX (Include columns)
-- ============================================

-- Index includes non-key columns (PostgreSQL 11+)
CREATE INDEX idx_users_username_covering ON users(username) 
INCLUDE (email, full_name);

-- Benefits: Index-only scan (no table access needed)
SELECT username, email, full_name 
FROM users 
WHERE username = 'john_doe';
-- All needed data is in the index!

-- ============================================
-- 7. GIN INDEX (Generalized Inverted Index)
-- ============================================

-- For full-text search, JSONB, arrays
CREATE INDEX idx_posts_content_gin ON posts 
USING GIN(to_tsvector('english', content));

-- Full-text search
SELECT * FROM posts 
WHERE to_tsvector('english', content) @@ to_tsquery('postgresql');

-- JSONB index
CREATE INDEX idx_metadata_gin ON products USING GIN(metadata);

-- Query JSON
SELECT * FROM products 
WHERE metadata @> '{"color": "red"}';

-- Array index
CREATE INDEX idx_tags_gin ON posts USING GIN(tags);

SELECT * FROM posts WHERE tags @> ARRAY['postgresql', 'database'];

-- ============================================
-- 8. GiST INDEX (Generalized Search Tree)
-- ============================================

-- For geometric data, full-text search, ranges
CREATE INDEX idx_locations_gist ON stores USING GiST(location);

-- Range types
CREATE INDEX idx_bookings_dates ON bookings USING GiST(date_range);

-- ============================================
-- 9. HASH INDEX (PostgreSQL)
-- ============================================

-- Only for equality comparisons (=)
-- Not crash-safe before PostgreSQL 10
CREATE INDEX idx_users_id_hash ON users USING HASH(user_id);

-- Use case: Large tables with only = lookups
-- B-Tree is usually better!

-- ============================================
-- 10. BTREE vs HASH vs GIN vs GiST
-- ============================================

/*
B-Tree (default):
✅ =, <, >, <=, >=, BETWEEN, IN, LIKE 'abc%'
✅ Sorting operations (ORDER BY)
✅ Most common, usually best choice

Hash:
✅ = only
❌ No range queries
❌ No sorting
⚠️ Rarely needed

GIN:
✅ Full-text search
✅ JSONB queries
✅ Array containment
✅ Multiple values per row

GiST:
✅ Geometric data
✅ Range types
✅ Full-text search (alternative to GIN)
✅ Custom data types
*/
```

---

## **9.3 Creating and Managing Indexes**

```sql name=index_management.sql
-- ============================================
-- CREATE INDEX
-- ============================================

-- Basic syntax
CREATE INDEX index_name ON table_name(column_name);

-- With specific method
CREATE INDEX idx_name ON table_name USING BTREE(column);

-- Concurrent index creation (doesn't lock table)
CREATE INDEX CONCURRENTLY idx_users_email ON users(email);

-- With storage parameters
CREATE INDEX idx_large_table ON large_table(col1)
WITH (fillfactor = 70);

-- ============================================
-- DROP INDEX
-- ============================================

DROP INDEX idx_users_email;

-- Drop if exists
DROP INDEX IF EXISTS idx_users_email;

-- Drop concurrently (PostgreSQL 9.2+)
DROP INDEX CONCURRENTLY idx_users_email;

-- ============================================
-- RENAME INDEX
-- ============================================

ALTER INDEX idx_old_name RENAME TO idx_new_name;

-- ============================================
-- REINDEX (Rebuild index)
-- ============================================

-- Rebuild single index
REINDEX INDEX idx_users_email;

-- Rebuild all indexes on table
REINDEX TABLE users;

-- Rebuild all indexes in database
REINDEX DATABASE mydb;

-- Concurrent reindex (PostgreSQL 12+)
REINDEX INDEX CONCURRENTLY idx_users_email;

-- ============================================
-- VIEW INDEXES
-- ============================================

-- List all indexes on a table
SELECT 
    indexname,
    indexdef
FROM pg_indexes
WHERE tablename = 'users';

-- Detailed index information
SELECT 
    schemaname,
    tablename,
    indexname,
    indexdef
FROM pg_indexes
WHERE schemaname = 'public'
ORDER BY tablename, indexname;

-- Index size
SELECT 
    indexname,
    pg_size_pretty(pg_relation_size(indexname::regclass)) AS index_size
FROM pg_indexes
WHERE tablename = 'users';

-- ============================================
-- INDEX USAGE STATISTICS
-- ============================================

-- Check if indexes are being used
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan AS index_scans,
    idx_tup_read AS tuples_read,
    idx_tup_fetch AS tuples_fetched
FROM pg_stat_user_indexes
WHERE schemaname = 'public'
ORDER BY idx_scan ASC;

-- Unused indexes (candidates for removal)
SELECT 
    schemaname,
    tablename,
    indexname,
    idx_scan
FROM pg_stat_user_indexes
WHERE schemaname = 'public'
    AND idx_scan = 0
    AND indexname NOT LIKE 'pg_toast_%'
ORDER BY pg_relation_size(indexname::regclass) DESC;
```

---

## **9.4 When to Use Indexes**

```sql name=index_best_practices.sql
-- ============================================
-- WHEN TO CREATE INDEXES ✅
-- ============================================

-- 1. Primary Keys (automatic)
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY  -- Index created automatically
);

-- 2. Foreign Keys (should be indexed)
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT NOT NULL,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
-- ✅ Add index on foreign key
CREATE INDEX idx_orders_user_id ON orders(user_id);

-- 3. Frequently queried columns
-- If you often query: WHERE email = '...'
CREATE INDEX idx_users_email ON users(email);

-- 4. Columns used in JOINs
SELECT * FROM orders o
INNER JOIN products p ON o.product_id = p.product_id;
-- ✅ Index on products.product_id (if not PK)

-- 5. Columns used in ORDER BY
SELECT * FROM posts ORDER BY created_at DESC LIMIT 20;
-- ✅ Index on created_at
CREATE INDEX idx_posts_created ON posts(created_at DESC);

-- 6. Columns used in GROUP BY
SELECT category, COUNT(*) FROM products GROUP BY category;
-- ✅ Index on category
CREATE INDEX idx_products_category ON products(category);

-- 7. Columns used in WHERE with high selectivity
-- High selectivity = returns few rows
CREATE INDEX idx_users_premium ON users(is_premium)
WHERE is_premium = TRUE;  -- Only 5% of users

-- ============================================
-- WHEN NOT TO CREATE INDEXES ❌
-- ============================================

-- 1. Small tables (< 1000 rows)
-- Full table scan is faster than index lookup

-- 2. Columns with low selectivity
-- Example: gender (M/F) - only 2 values
-- Index won't help much

-- 3. Frequently updated columns
-- Indexes slow down INSERT, UPDATE, DELETE

-- 4. Tables with frequent writes
-- Each index adds overhead to write operations

-- 5. Large text columns
-- Use full-text search indexes instead

-- ============================================
-- COMPOSITE INDEX STRATEGIES
-- ============================================

-- ✅ Good: Most selective column first
CREATE INDEX idx_orders_user_status_date ON orders(
    user_id,      -- High selectivity
    status,       -- Medium selectivity
    order_date    -- Low selectivity
);

-- This index helps:
SELECT * FROM orders WHERE user_id = 123;
SELECT * FROM orders WHERE user_id = 123 AND status = 'pending';
SELECT * FROM orders WHERE user_id = 123 AND status = 'pending' 
    AND order_date > '2024-01-01';

-- ❌ Bad: Can't use index efficiently
SELECT * FROM orders WHERE status = 'pending';  -- Skips user_id
SELECT * FROM orders WHERE order_date > '2024-01-01';  -- Skips user_id

-- ============================================
-- COVERING INDEX PATTERN
-- ============================================

-- Query needs: username, email, full_name
CREATE INDEX idx_users_covering ON users(username)
INCLUDE (email, full_name);

-- Index-only scan (fastest)
SELECT username, email, full_name
FROM users
WHERE username = 'john_doe';

-- ============================================
-- PARTIAL INDEX PATTERN
-- ============================================

-- Only index active records (90% of queries)
CREATE INDEX idx_active_products ON products(product_name)
WHERE is_active = TRUE AND deleted_at IS NULL;

-- Benefits:
-- - 50% smaller index size
-- - Faster index maintenance
-- - Faster queries on active products
```

---

## **9.5 Query Performance Optimization**

```sql name=query_optimization.sql
-- ============================================
-- EXPLAIN - Analyze Query Plan
-- ============================================

-- See query execution plan
EXPLAIN 
SELECT * FROM users WHERE email = 'john@example.com';

/*
Output:
Seq Scan on users  (cost=0.00..35.50 rows=1 width=244)
  Filter: (email = 'john@example.com'::text)
  
Seq Scan = Sequential Scan (full table scan) = SLOW
*/

-- ============================================
-- EXPLAIN ANALYZE - Actual Execution
-- ============================================

EXPLAIN ANALYZE
SELECT * FROM users WHERE email = 'john@example.com';

/*
Output includes:
- Actual execution time
- Rows returned
- Index usage
- Join methods
*/

-- ============================================
-- Reading EXPLAIN Output
-- ============================================

/*
Common Scan Types (fastest to slowest):

1. Index Only Scan - Best! Data from index only
2. Index Scan - Good! Uses index, fetches from table
3. Bitmap Index Scan - Good for multiple indexes
4. Seq Scan - Slowest! Reads entire table

Cost format: (startup_cost..total_cost rows=X width=Y)
*/

-- Example: Slow query (Seq Scan)
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 123;

-- Add index
CREATE INDEX idx_orders_user ON orders(user_id);

-- Fast query (Index Scan)
EXPLAIN ANALYZE
SELECT * FROM orders WHERE user_id = 123;

-- ============================================
-- ANALYZE - Update Statistics
-- ============================================

-- Update table statistics for query planner
ANALYZE users;

-- Analyze entire database
ANALYZE;

-- Auto-analyze (PostgreSQL does this automatically)
-- But run manually after bulk data changes

-- ============================================
-- VACUUM - Clean Up
-- ============================================

-- Remove dead rows (from UPDATEs/DELETEs)
VACUUM users;

-- Vacuum and analyze together
VACUUM ANALYZE users;

-- Full vacuum (more aggressive, locks table)
VACUUM FULL users;

-- Auto-vacuum runs automatically in PostgreSQL

-- ============================================
-- QUERY OPTIMIZATION TECHNIQUES
-- ============================================

-- ❌ Slow: SELECT *
SELECT * FROM users WHERE user_id = 123;

-- ✅ Fast: Select only needed columns
SELECT user_id, username, email FROM users WHERE user_id = 123;

-- ❌ Slow: Function on indexed column
SELECT * FROM users WHERE LOWER(email) = 'john@example.com';

-- ✅ Fast: Store lowercase OR use expression index
CREATE INDEX idx_email_lower ON users(LOWER(email));

-- ❌ Slow: LIKE with leading wildcard
SELECT * FROM users WHERE email LIKE '%@gmail.com';

-- ✅ Fast: LIKE without leading wildcard
SELECT * FROM users WHERE email LIKE 'john%';

-- ❌ Slow: OR conditions on different columns
SELECT * FROM users WHERE username = 'john' OR email = 'john@example.com';

-- ✅ Fast: UNION with separate indexes
SELECT * FROM users WHERE username = 'john'
UNION
SELECT * FROM users WHERE email = 'john@example.com';

-- ❌ Slow: NOT IN with subquery
SELECT * FROM users WHERE user_id NOT IN (
    SELECT user_id FROM deleted_users
);

-- ✅ Fast: NOT EXISTS or LEFT JOIN
SELECT * FROM users u
WHERE NOT EXISTS (
    SELECT 1 FROM deleted_users d WHERE d.user_id = u.user_id
);

-- ============================================
-- LIMIT vs COUNT(*)
-- ============================================

-- ❌ Slow: Count all matching rows
SELECT COUNT(*) FROM orders WHERE status = 'pending';
-- Scans all matching rows

-- ✅ Fast: Just check if any exist
SELECT EXISTS(SELECT 1 FROM orders WHERE status = 'pending' LIMIT 1);

-- ============================================
-- JOIN Optimization
-- ============================================

-- ✅ Ensure indexes on join columns
CREATE INDEX idx_orders_user_id ON orders(user_id);
CREATE INDEX idx_users_user_id ON users(user_id);  -- PK is auto-indexed

-- ✅ Join on indexed columns
SELECT * FROM orders o
INNER JOIN users u ON o.user_id = u.user_id;

-- ✅ Filter before joining (if possible)
SELECT * FROM orders o
INNER JOIN (
    SELECT user_id, username FROM users WHERE is_active = TRUE
) u ON o.user_id = u.user_id;
```

---

## **9.6 Index Maintenance**

```sql name=index_maintenance.sql
-- ============================================
-- MONITORING INDEX HEALTH
-- ============================================

-- Index bloat (wasted space)
SELECT 
    schemaname,
    tablename,
    indexname,
    pg_size_pretty(pg_relation_size(indexname::regclass)) AS index_size,
    idx_scan AS number_of_scans,
    idx_tup_read AS tuples_read,
    idx_tup_fetch AS tuples_fetched
FROM pg_stat_user_indexes
ORDER BY pg_relation_size(indexname::regclass) DESC;

-- Duplicate indexes (waste of space)
SELECT 
    pg_size_pretty(SUM(pg_relation_size(idx))::BIGINT) AS size,
    (array_agg(idx))[1] AS idx1,
    (array_agg(idx))[2] AS idx2,
    (array_agg(idx))[3] AS idx3,
    (array_agg(idx))[4] AS idx4
FROM (
    SELECT 
        indexrelid::regclass AS idx,
        (indrelid::text ||E'\n'|| indclass::text ||E'\n'|| 
         indkey::text ||E'\n'|| COALESCE(indexprs::text,'')||E'\n' || 
         COALESCE(indpred::text,'')) AS key
    FROM pg_index
) sub
GROUP BY key 
HAVING COUNT(*) > 1
ORDER BY SUM(pg_relation_size(idx)) DESC;

-- ============================================
-- INDEX REBUILD SCHEDULE
-- ============================================

-- When to rebuild:
-- 1. After bulk data changes
-- 2. Index bloat detected
-- 3. Performance degradation

-- Rebuild concurrently (no downtime)
REINDEX INDEX CONCURRENTLY idx_users_email;

-- Or create new index and drop old
CREATE INDEX CONCURRENTLY idx_users_email_new ON users(email);
DROP INDEX idx_users_email;
ALTER INDEX idx_users_email_new RENAME TO idx_users_email;
```

---

# **10. Transactions & ACID Properties**

## **10.1 What is a Transaction?**

A **transaction** is a sequence of operations performed as a single logical unit of work. Either ALL operations succeed, or ALL fail.

```sql name=transaction_basics.sql
-- ============================================
-- BASIC TRANSACTION SYNTAX
-- ============================================

-- Start transaction
BEGIN;
-- or
START TRANSACTION;

-- Execute operations
UPDATE accounts SET balance = balance - 100 WHERE account_id = 1;
UPDATE accounts SET balance = balance + 100 WHERE account_id = 2;

-- Commit (save changes)
COMMIT;

-- Or rollback (undo changes)
ROLLBACK;

-- ============================================
-- TRANSACTION EXAMPLE: Bank Transfer
-- ============================================

BEGIN;

-- Deduct from sender
UPDATE accounts 
SET balance = balance - 500 
WHERE account_id = 101;

-- Add to receiver
UPDATE accounts 
SET balance = balance + 500 
WHERE account_id = 202;

-- Check if both succeeded
-- If any error occurs, ROLLBACK
-- If all good, COMMIT
COMMIT;

-- ============================================
-- TRANSACTION WITH ERROR HANDLING (PostgreSQL)
-- ============================================

DO $$
BEGIN
    -- Start transaction block
    BEGIN
        UPDATE accounts SET balance = balance - 500 WHERE account_id = 101;
        UPDATE accounts SET balance = balance + 500 WHERE account_id = 202;
        
        -- Log the transaction
        INSERT INTO transaction_log (from_account, to_account, amount)
        VALUES (101, 202, 500);
        
        RAISE NOTICE 'Transaction successful';
    EXCEPTION
        WHEN OTHERS THEN
            -- Automatic rollback on error
            RAISE NOTICE 'Transaction failed: %', SQLERRM;
    END;
END $$;

-- ============================================
-- SAVEPOINTS (Nested Transactions)
-- ============================================

BEGIN;

UPDATE users SET balance = balance - 100 WHERE user_id = 1;

SAVEPOINT sp1;

UPDATE users SET balance = balance + 100 WHERE user_id = 2;

-- Oops, error! Rollback to savepoint
ROLLBACK TO sp1;

-- user_id = 1 still has -100 balance
-- user_id = 2 change was undone

-- Now do the correct operation
UPDATE users SET balance = balance + 100 WHERE user_id = 3;

COMMIT;  -- Commits changes to user 1 and 3
```

---

## **10.2 ACID Properties**

**ACID** ensures database reliability.

### **A - Atomicity**

```sql name=acid_atomicity.sql
-- ============================================
-- ATOMICITY: All or Nothing
-- ============================================

/*
Either all operations in a transaction succeed,
or none of them do. No partial updates.
*/

-- Example: Transfer money
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- If second UPDATE fails:
-- - First UPDATE is automatically rolled back
-- - Database returns to original state
-- - No money is lost or created

-- ❌ Without transaction (BAD!)
UPDATE accounts SET balance = balance - 100 WHERE id = 1;  -- Success
UPDATE accounts SET balance = balance + 100 WHERE id = 2;  -- Fails!
-- Result: Account 1 lost $100, Account 2 didn't receive it!

-- ✅ With transaction (GOOD!)
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;
-- Result: Either both succeed or both fail
```

---

### **C - Consistency**

```sql name=acid_consistency.sql
-- ============================================
-- CONSISTENCY: Valid State to Valid State
-- ============================================

/*
Transaction must take database from one valid state
to another valid state. All constraints must be satisfied.
*/

-- Example: Enforce business rules
BEGIN;

-- This will FAIL if constraint violated
INSERT INTO orders (user_id, total_amount)
VALUES (999, 100.00);  -- If user 999 doesn't exist, ROLLBACK

-- Check constraint
CREATE TABLE accounts (
    account_id SERIAL PRIMARY KEY,
    balance DECIMAL(10, 2) CHECK (balance >= 0)  -- Can't be negative
);

BEGIN;
UPDATE accounts SET balance = balance - 1000 WHERE account_id = 1;
-- If balance would become negative, transaction rolls back
COMMIT;

-- ============================================
-- CONSISTENCY EXAMPLE: Inventory Management
-- ============================================

BEGIN;

-- Reduce inventory
UPDATE products 
SET stock = stock - 5 
WHERE product_id = 10;

-- Create order
INSERT INTO orders (product_id, quantity)
VALUES (10, 5);

-- If stock goes negative, rollback
-- Ensures consistency: can't sell what you don't have

COMMIT;
```

---

### **I - Isolation**

```sql name=acid_isolation.sql
-- ============================================
-- ISOLATION: Concurrent Transactions
-- ============================================

/*
Transactions execute in isolation.
One transaction should not see intermediate states
of another transaction.
*/

-- Problem without isolation:

-- Transaction A:
BEGIN;
UPDATE accounts SET balance = 1000 WHERE id = 1;
-- Not yet committed

-- Transaction B (in parallel):
SELECT balance FROM accounts WHERE id = 1;
-- Should it see 1000 (uncommitted) or old value?

-- ============================================
-- ISOLATION LEVELS (See next section)
-- ============================================

-- Set isolation level for transaction
BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;
-- operations
COMMIT;
```

---

### **D - Durability**

```sql name=acid_durability.sql
-- ============================================
-- DURABILITY: Permanent Changes
-- ============================================

/*
Once a transaction is committed,
changes are permanent (even after system crash).
*/

BEGIN;
UPDATE accounts SET balance = balance + 1000 WHERE id = 1;
COMMIT;  -- Changes written to disk

-- Even if:
-- - Power failure
-- - Server crash
-- - Database restart
-- The committed change persists!

-- PostgreSQL uses Write-Ahead Logging (WAL)
-- Changes logged before applied
-- Can replay log after crash to recover
```

---

## **10.3 Isolation Levels**

```sql name=isolation_levels.sql
-- ============================================
-- ISOLATION LEVELS
-- ============================================

/*
Four standard isolation levels (weakest to strongest):

1. READ UNCOMMITTED (not in PostgreSQL)
2. READ COMMITTED (PostgreSQL default)
3. REPEATABLE READ
4. SERIALIZABLE
*/

-- ============================================
-- 1. READ UNCOMMITTED
-- ============================================

/*
NOT SUPPORTED IN POSTGRESQL
(Treated as READ COMMITTED)

Allows dirty reads:
- Can read uncommitted changes from other transactions
- Lowest isolation, highest concurrency
*/

-- ============================================
-- 2. READ COMMITTED (Default)
-- ============================================

/*
- Can only see committed data
- Default in PostgreSQL
- No dirty reads
- But: non-repeatable reads possible
*/

-- Transaction A:
BEGIN TRANSACTION ISOLATION LEVEL READ COMMITTED;
SELECT balance FROM accounts WHERE id = 1;  -- Returns 1000
-- Transaction B commits a change here (balance = 2000)
SELECT balance FROM accounts WHERE id = 1;  -- Returns 2000 (different!)
COMMIT;

-- Use case: Most applications (good balance)

-- ============================================
-- 3. REPEATABLE READ
-- ============================================

/*
- Sees snapshot of database at transaction start
- Same query returns same results
- No dirty reads, no non-repeatable reads
- But: phantom reads possible
*/

-- Transaction A:
BEGIN TRANSACTION ISOLATION LEVEL REPEATABLE READ;
SELECT balance FROM accounts WHERE id = 1;  -- Returns 1000
-- Transaction B commits a change (balance = 2000)
SELECT balance FROM accounts WHERE id = 1;  -- Still returns 1000 (snapshot!)
COMMIT;

-- Use case: Reports, analytics

-- Example:
BEGIN ISOLATION LEVEL REPEATABLE READ;

-- Generate report based on consistent snapshot
SELECT SUM(balance) FROM accounts;
-- Even if other transactions modify accounts,
-- this transaction sees consistent data

COMMIT;

-- ============================================
-- 4. SERIALIZABLE (Strongest)
-- ============================================

/*
- Strictest isolation
- Transactions appear to run serially (one after another)
- No dirty reads, no non-repeatable reads, no phantom reads
- May cause more transaction failures (serialization errors)
*/

BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;

-- Critical operations requiring full isolation
UPDATE accounts SET balance = balance + 100 WHERE id = 1;

COMMIT;

-- Use case: Financial transactions, critical operations

-- ============================================
-- ISOLATION LEVEL COMPARISON
-- ============================================

/*
┌─────────────────────┬────────┬──────────┬────────────────┐
│ Isolation Level     │ Dirty  │ Non-Rep  │ Phantom Reads  │
│                     │ Read   │ Read     │                │
├─────────────────────┼────────┼──────────┼────────────────┤
│ READ UNCOMMITTED    │ Yes    │ Yes      │ Yes            │
│ READ COMMITTED      │ No     │ Yes      │ Yes            │
│ REPEATABLE READ     │ No     │ No       │ Yes (Postgres: No) │
│ SERIALIZABLE        │ No     │ No       │ No             │
└─────────────────────┴────────┴──────────┴────────────────┘

Dirty Read: Read uncommitted data
Non-Repeatable Read: Same query, different result
Phantom Read: New rows appear in range query
*/

-- ============================================
-- SETTING ISOLATION LEVEL
-- ============================================

-- For single transaction
BEGIN TRANSACTION ISOLATION LEVEL SERIALIZABLE;
-- operations
COMMIT;

-- For session
SET SESSION CHARACTERISTICS AS TRANSACTION ISOLATION LEVEL REPEATABLE READ;

-- Check current level
SHOW TRANSACTION ISOLATION LEVEL;

-- ============================================
-- PRACTICAL EXAMPLES
-- ============================================

-- READ COMMITTED: E-commerce checkout
BEGIN;
-- User sees current product price
SELECT price FROM products WHERE id = 123;
-- If admin changes price, user sees new price on next SELECT
-- This is usually acceptable
COMMIT;

-- REPEATABLE READ: Financial report
BEGIN ISOLATION LEVEL REPEATABLE READ;
-- Generate month-end report
SELECT 
    department,
    SUM(amount) AS total
FROM transactions
WHERE transaction_date BETWEEN '2024-01-01' AND '2024-01-31'
GROUP BY department;
-- Consistent snapshot throughout report generation
COMMIT;

-- SERIALIZABLE: Bank transfer
BEGIN ISOLATION LEVEL SERIALIZABLE;
-- Ensure no concurrent modifications affect this transfer
UPDATE accounts SET balance = balance - 500 WHERE id = 1;
UPDATE accounts SET balance = balance + 500 WHERE id = 2;
COMMIT;
```

---

## **10.4 Transaction Best Practices**

```sql name=transaction_best_practices.sql
-- ============================================
-- BEST PRACTICES
-- ============================================

-- ✅ Keep transactions short
BEGIN;
UPDATE accounts SET balance = balance - 100 WHERE id = 1;
UPDATE accounts SET balance = balance + 100 WHERE id = 2;
COMMIT;

-- ❌ Don't do this (transaction too long)
BEGIN;
-- Send email (slow)
-- Call external API (slow)
-- Complex calculations (slow)
COMMIT;

-- ✅ Explicit transaction control
BEGIN;
-- operations
COMMIT;

-- ❌ Auto-commit for critical operations
UPDATE accounts SET balance = balance - 100 WHERE id = 1;  -- Auto-commits
UPDATE accounts SET balance = balance + 100 WHERE id = 2;  -- Separate transaction!

-- ✅ Handle errors properly
DO $$
BEGIN
    BEGIN
        -- Critical operations
        UPDATE accounts SET balance = balance - 100 WHERE id = 1;
        UPDATE accounts SET balance = balance + 100 WHERE id = 2;
    EXCEPTION
        WHEN OTHERS THEN
            RAISE NOTICE 'Error: %', SQLERRM;
            -- Automatic rollback
    END;
END $$;

-- ✅ Use appropriate isolation level
-- Default (READ COMMITTED) is usually fine
BEGIN;  -- Uses default isolation level

-- Only use SERIALIZABLE when necessary
BEGIN ISOLATION LEVEL SERIALIZABLE;

-- ✅ Avoid long-running transactions
-- They hold locks and block other transactions

-- ✅ Use SAVEPOINT for partial rollbacks
BEGIN;
UPDATE table1 SET col = val WHERE id = 1;
SAVEPOINT sp1;
UPDATE table2 SET col = val WHERE id = 2;
-- Error! Rollback only table2 change
ROLLBACK TO sp1;
-- Fix and retry
UPDATE table2 SET col = correct_val WHERE id = 2;
COMMIT;

-- ============================================
-- DEADLOCK HANDLING
-- ============================================

/*
Deadlock occurs when two transactions wait for each other:

Transaction A:
1. Locks row 1
2. Waits for row 2 (locked by B)

Transaction B:
1. Locks row 2
2. Waits for row 1 (locked by A)

Result: Deadlock! Database detects and aborts one transaction.
*/

-- ✅ Prevent deadlocks: Always access resources in same order
-- Transaction A and B both do:
BEGIN;
UPDATE accounts SET balance = balance + 10 WHERE id = 1;  -- Always lock 1 first
UPDATE accounts SET balance = balance + 10 WHERE id = 2;  -- Then lock 2
COMMIT;

-- ❌ This can deadlock:
-- Transaction A:
BEGIN;
UPDATE accounts SET balance = balance + 10 WHERE id = 1;
UPDATE accounts SET balance = balance + 10 WHERE id = 2;
COMMIT;

-- Transaction B (parallel):
BEGIN;
UPDATE accounts SET balance = balance + 10 WHERE id = 2;  -- Different order!
UPDATE accounts SET balance = balance + 10 WHERE id = 1;
COMMIT;

-- ============================================
-- LOCK TIMEOUT
-- ============================================

-- Set timeout for waiting on locks
SET lock_timeout = '5s';

BEGIN;
-- If can't acquire lock within 5 seconds, error
UPDATE accounts SET balance = balance + 100 WHERE id = 1;
COMMIT;

-- Statement timeout
SET statement_timeout = '30s';  -- Query must complete in 30s
```

---

# **11. Stored Procedures & Functions**

## **11.1 PostgreSQL Functions**

```sql name=postgresql_functions.sql
-- ============================================
-- BASIC FUNCTION
-- ============================================

-- Simple function returning scalar value
CREATE OR REPLACE FUNCTION get_user_count()
RETURNS INTEGER
LANGUAGE SQL
AS $$
    SELECT COUNT(*)::INTEGER FROM users;
$$;

-- Call function
SELECT get_user_count();

-- ============================================
-- FUNCTION WITH PARAMETERS
-- ============================================

CREATE OR REPLACE FUNCTION get_user_orders(p_user_id INT)
RETURNS TABLE(order_id INT, order_date TIMESTAMP, total_amount DECIMAL)
LANGUAGE SQL
AS $$
    SELECT order_id, order_date, total_amount
    FROM orders
    WHERE user_id = p_user_id
    ORDER BY order_date DESC;
$$;

-- Call function
SELECT * FROM get_user_orders(123);

-- ============================================
-- PLPGSQL FUNCTION (Procedural)
-- ============================================

CREATE OR REPLACE FUNCTION calculate_discount(
    p_price DECIMAL,
    p_discount_percent INT
)
RETURNS DECIMAL
LANGUAGE plpgsql
AS $$
DECLARE
    v_discount DECIMAL;
    v_final_price DECIMAL;
BEGIN
    -- Calculate discount amount
    v_discount := p_price * (p_discount_percent / 100.0);
    
    -- Calculate final price
    v_final_price := p_price - v_discount;
    
    -- Return result
    RETURN v_final_price;
END;
$$;

-- Usage
SELECT calculate_discount(100.00, 20);  -- Returns 80.00

-- ============================================
-- FUNCTION WITH CONDITIONAL LOGIC
-- ============================================

CREATE OR REPLACE FUNCTION get_user_status(p_user_id INT)
RETURNS TEXT
LANGUAGE plpgsql
AS $$
DECLARE
    v_order_count INT;
    v_total_spent DECIMAL;
    v_status TEXT;
BEGIN
    -- Get order statistics
    SELECT 
        COUNT(*),
        COALESCE(SUM(total_amount), 0)
    INTO v_order_count, v_total_spent
    FROM orders
    WHERE user_id = p_user_id;
    
    -- Determine status
    IF v_total_spent >= 10000 THEN
        v_status := 'VIP';
    ELSIF v_total_spent >= 5000 THEN
        v_status := 'Premium';
    ELSIF v_order_count >= 10 THEN
        v_status := 'Regular';
    ELSE
        v_status := 'New';
    END IF;
    
    RETURN v_status;
END;
$$;

-- Usage
SELECT username, get_user_status(user_id) AS status
FROM users;

-- ============================================
-- FUNCTION RETURNING TABLE
-- ============================================

CREATE OR REPLACE FUNCTION get_top_products(p_limit INT DEFAULT 10)
RETURNS TABLE(
    product_id INT,
    product_name VARCHAR,
    total_sold BIGINT,
    revenue DECIMAL
)
LANGUAGE plpgsql
AS $$
BEGIN
    RETURN QUERY
    SELECT 
        p.product_id,
        p.product_name,
        COUNT(oi.order_item_id) AS total_sold,
        SUM(oi.subtotal) AS revenue
    FROM products p
    INNER JOIN order_items oi ON p.product_id = oi.product_id
    GROUP BY p.product_id, p.product_name
    ORDER BY revenue DESC
    LIMIT p_limit;
END;
$$;

-- Usage
SELECT * FROM get_top_products(5);

-- ============================================
-- FUNCTION WITH LOOPS
-- ============================================

CREATE OR REPLACE FUNCTION generate_fibonacci(n INT)
RETURNS TABLE(position INT, value BIGINT)
LANGUAGE plpgsql
AS $$
DECLARE
    a BIGINT := 0;
    b BIGINT := 1;
    temp BIGINT;
    i INT;
BEGIN
    FOR i IN 1..n LOOP
        position := i;
        value := a;
        RETURN NEXT;
        
        temp := a + b;
        a := b;
        b := temp;
    END LOOP;
END;
$$;

-- Usage
SELECT * FROM generate_fibonacci(10);

-- ============================================
-- FUNCTION WITH EXCEPTION HANDLING
-- ============================================

CREATE OR REPLACE FUNCTION safe_divide(a DECIMAL, b DECIMAL)
RETURNS DECIMAL
LANGUAGE plpgsql
AS $$
BEGIN
    RETURN a / b;
EXCEPTION
    WHEN division_by_zero THEN
        RAISE NOTICE 'Division by zero attempted';
        RETURN NULL;
    WHEN OTHERS THEN
        RAISE NOTICE 'Error: %', SQLERRM;
        RETURN NULL;
END;
$$;

-- Usage
SELECT safe_divide(100, 0);  -- Returns NULL, doesn't error

-- ============================================
-- DROP FUNCTION
-- ============================================

DROP FUNCTION IF EXISTS get_user_count();
DROP FUNCTION IF EXISTS calculate_discount(DECIMAL, INT);
```

---

## **11.2 Stored Procedures (PostgreSQL 11+)**

```sql name=stored_procedures.sql
-- ============================================
-- BASIC PROCEDURE
-- ============================================

-- Procedures can have transactions, functions cannot
CREATE OR REPLACE PROCEDURE update_user_status()
LANGUAGE plpgsql
AS $$
BEGIN
    -- Update inactive users
    UPDATE users 
    SET is_active = FALSE
    WHERE last_login < CURRENT_DATE - INTERVAL '1 year';
    
    RAISE NOTICE 'Updated % users', FOUND;
END;
$$;

-- Call procedure
CALL update_user_status();

-- ============================================
-- PROCEDURE WITH PARAMETERS
-- ============================================

CREATE OR REPLACE PROCEDURE create_order(
    p_user_id INT,
    p_product_ids INT[],
    p_quantities INT[]
)
LANGUAGE plpgsql
AS $$
DECLARE
    v_order_id INT;
    v_total DECIMAL := 0;
    v_price DECIMAL;
    i INT;
BEGIN
    -- Create order
    INSERT INTO orders (user_id, order_date, status)
    VALUES (p_user_id, CURRENT_TIMESTAMP, 'pending')
    RETURNING order_id INTO v_order_id;
    
    -- Add order items
    FOR i IN 1..array_length(p_product_ids, 1) LOOP
        -- Get product price
        SELECT price INTO v_price
        FROM products
        WHERE product_id = p_product_ids[i];
        
        -- Insert order item
        INSERT INTO order_items (order_id, product_id, quantity, unit_price, subtotal)
        VALUES (
            v_order_id,
            p_product_ids[i],
            p_quantities[i],
            v_price,
            v_price * p_quantities[i]
        );
        
        v_total := v_total + (v_price * p_quantities[i]);
    END LOOP;
    
    -- Update order total
    UPDATE orders SET total_amount = v_total WHERE order_id = v_order_id;
    
    RAISE NOTICE 'Order created: ID = %, Total = $%', v_order_id, v_total;
END;
$$;

-- Call procedure
CALL create_order(
    123,                    -- user_id
    ARRAY[1, 2, 3],        -- product_ids
    ARRAY[2, 1, 5]         -- quantities
);

-- ============================================
-- PROCEDURE WITH TRANSACTION CONTROL
-- ============================================

CREATE OR REPLACE PROCEDURE process_monthly_payroll(p_month INT, p_year INT)
LANGUAGE plpgsql
AS $$
DECLARE
    v_employee RECORD;
    v_salary DECIMAL;
    v_bonuses DECIMAL;
    v_deductions DECIMAL;
    v_net DECIMAL;
BEGIN
    -- Loop through all employees
    FOR v_employee IN 
        SELECT employee_id, base_salary 
        FROM employees 
        WHERE is_active = TRUE
    LOOP
        BEGIN
            -- Calculate bonuses
            SELECT COALESCE(SUM(bonus_amount), 0) INTO v_bonuses
            FROM bonuses
            WHERE employee_id = v_employee.employee_id
                AND EXTRACT(MONTH FROM bonus_date) = p_month
                AND EXTRACT(YEAR FROM bonus_date) = p_year;
            
            -- Calculate deductions (absences)
            v_deductions := 0;  -- Calculate based on attendance
            
            -- Calculate net salary
            v_net := v_employee.base_salary + v_bonuses - v_deductions;
            
            -- Insert payroll record
            INSERT INTO payroll_history (
                employee_id, month, year,
                base_salary, bonuses, deductions, net_salary
            ) VALUES (
                v_employee.employee_id, p_month, p_year,
                v_employee.base_salary, v_bonuses, v_deductions, v_net
            );
            
            COMMIT;  -- Commit after each employee
            
        EXCEPTION
            WHEN OTHERS THEN
                RAISE NOTICE 'Error processing employee %: %', 
                    v_employee.employee_id, SQLERRM;
                ROLLBACK;
        END;
    END LOOP;
    
    RAISE NOTICE 'Payroll processed for %-%', p_year, p_month;
END;
$$;

-- Call procedure
CALL process_monthly_payroll(2, 2026);

-- ============================================
-- PROCEDURE WITH OUT PARAMETERS
-- ============================================

CREATE OR REPLACE PROCEDURE get_order_summary(
    p_order_id INT,
    OUT p_item_count INT,
    OUT p_total_amount DECIMAL,
    OUT p_status TEXT
)
LANGUAGE plpgsql
AS $$
BEGIN
    SELECT 
        COUNT(oi.order_item_id),
        o.total_amount,
        o.status
    INTO p_item_count, p_total_amount, p_status
    FROM orders o
    LEFT JOIN order_items oi ON o.order_id = oi.order_id
    WHERE o.order_id = p_order_id
    GROUP BY o.order_id, o.total_amount, o.status;
END;
$$;

-- Call procedure with OUT parameters
CALL get_order_summary(123, NULL, NULL, NULL);

-- ============================================
-- DROP PROCEDURE
-- ============================================

DROP PROCEDURE IF EXISTS update_user_status();
DROP PROCEDURE IF EXISTS process_monthly_payroll(INT, INT);
```

---

## **11.3 Triggers**

```sql name=triggers.sql
-- ============================================
-- BASIC TRIGGER
-- ============================================

-- Trigger function
CREATE OR REPLACE FUNCTION update_timestamp()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    NEW.updated_at := CURRENT_TIMESTAMP;
    RETURN NEW;
END;
$$;

-- Create trigger
CREATE TRIGGER trg_users_updated
BEFORE UPDATE ON users
FOR EACH ROW
EXECUTE FUNCTION update_timestamp();

-- Test trigger
UPDATE users SET email = 'newemail@example.com' WHERE user_id = 1;
-- updated_at is automatically set

-- ============================================
-- AUDIT TRIGGER
-- ============================================

-- Audit table
CREATE TABLE salary_audit (
    audit_id SERIAL PRIMARY KEY,
    employee_id INT,
    old_salary DECIMAL,
    new_salary DECIMAL,
    changed_by TEXT,
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Trigger function
CREATE OR REPLACE FUNCTION audit_salary_changes()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    IF OLD.base_salary IS DISTINCT FROM NEW.base_salary THEN
        INSERT INTO salary_audit (employee_id, old_salary, new_salary, changed_by)
        VALUES (NEW.employee_id, OLD.base_salary, NEW.base_salary, CURRENT_USER);
    END IF;
    RETURN NEW;
END;
$$;

-- Create trigger
CREATE TRIGGER trg_salary_audit
AFTER UPDATE ON salary
FOR EACH ROW
EXECUTE FUNCTION audit_salary_changes();

-- Test
UPDATE salary SET base_salary = 100000 WHERE employee_id = 1;
SELECT * FROM salary_audit;

-- ============================================
-- VALIDATION TRIGGER
-- ============================================

CREATE OR REPLACE FUNCTION validate_order()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
DECLARE
    v_user_exists BOOLEAN;
BEGIN
    -- Check if user exists
    SELECT EXISTS(SELECT 1 FROM users WHERE user_id = NEW.user_id)
    INTO v_user_exists;
    
    IF NOT v_user_exists THEN
        RAISE EXCEPTION 'User % does not exist', NEW.user_id;
    END IF;
    
    -- Validate amount
    IF NEW.total_amount < 0 THEN
        RAISE EXCEPTION 'Total amount cannot be negative';
    END IF;
    
    RETURN NEW;
END;
$$;

CREATE TRIGGER trg_validate_order
BEFORE INSERT OR UPDATE ON orders
FOR EACH ROW
EXECUTE FUNCTION validate_order();

-- ============================================
-- TRIGGER TO MAINTAIN DENORMALIZED DATA
-- ============================================

-- Update order total when order item is added/updated
CREATE OR REPLACE FUNCTION update_order_total()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
DECLARE
    v_order_total DECIMAL;
BEGIN
    -- Calculate new total
    SELECT COALESCE(SUM(subtotal), 0) INTO v_order_total
    FROM order_items
    WHERE order_id = COALESCE(NEW.order_id, OLD.order_id);
    
    -- Update order
    UPDATE orders
    SET total_amount = v_order_total
    WHERE order_id = COALESCE(NEW.order_id, OLD.order_id);
    
    RETURN COALESCE(NEW, OLD);
END;
$$;

CREATE TRIGGER trg_update_order_total_insert
AFTER INSERT ON order_items
FOR EACH ROW
EXECUTE FUNCTION update_order_total();

CREATE TRIGGER trg_update_order_total_update
AFTER UPDATE ON order_items
FOR EACH ROW
EXECUTE FUNCTION update_order_total();

CREATE TRIGGER trg_update_order_total_delete
AFTER DELETE ON order_items
FOR EACH ROW
EXECUTE FUNCTION update_order_total();

-- ============================================
-- INSTEAD OF TRIGGER (For Views)
-- ============================================

-- Create view
CREATE VIEW active_users_view AS
SELECT user_id, username, email
FROM users
WHERE is_active = TRUE;

-- Make view updatable
CREATE OR REPLACE FUNCTION update_active_users_view()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE users
    SET username = NEW.username,
        email = NEW.email
    WHERE user_id = NEW.user_id;
    RETURN NEW;
END;
$$;

CREATE TRIGGER trg_update_active_users
INSTEAD OF UPDATE ON active_users_view
FOR EACH ROW
EXECUTE FUNCTION update_active_users_view();

-- ============================================
-- DISABLE/ENABLE TRIGGERS
-- ============================================

-- Disable trigger
ALTER TABLE users DISABLE TRIGGER trg_users_updated;

-- Enable trigger
ALTER TABLE users ENABLE TRIGGER trg_users_updated;

-- Disable all triggers on table
ALTER TABLE users DISABLE TRIGGER ALL;

-- ============================================
-- DROP TRIGGER
-- ============================================

DROP TRIGGER IF EXISTS trg_users_updated ON users;
DROP FUNCTION IF EXISTS update_timestamp();
```

---

# **12. Views**

## **12.1 Basic Views**

```sql name=basic_views.sql
-- ============================================
-- CREATE VIEW
-- ============================================

-- Simple view
CREATE VIEW active_users AS
SELECT user_id, username, email, created_at
FROM users
WHERE is_active = TRUE;

-- Query view like a table
SELECT * FROM active_users;

-- ============================================
-- VIEW WITH JOINS
-- ============================================

CREATE VIEW user_orders_summary AS
SELECT 
    u.user_id,
    u.username,
    u.email,
    COUNT(o.order_id) AS total_orders,
    COALESCE(SUM(o.total_amount), 0) AS total_spent,
    MAX(o.order_date) AS last_order_date
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
GROUP BY u.user_id, u.username, u.email;

-- Usage
SELECT * FROM user_orders_summary
WHERE total_orders > 5
ORDER BY total_spent DESC;

-- ============================================
-- VIEW WITH COMPLEX LOGIC
-- ============================================

CREATE VIEW user_engagement AS
SELECT 
    u.user_id,
    u.username,
    COUNT(DISTINCT p.post_id) AS total_posts,
    COUNT(DISTINCT c.comment_id) AS total_comments,
    COUNT(DISTINCT l.like_id) AS total_likes,
    CASE 
        WHEN COUNT(DISTINCT p.post_id) > 100 THEN 'Power User'
        WHEN COUNT(DISTINCT p.post_id) > 10 THEN 'Active User'
        ELSE 'Casual User'
    END AS user_category
FROM users u
LEFT JOIN posts p ON u.user_id = p.user_id
LEFT JOIN comments c ON u.user_id = c.user_id
LEFT JOIN likes l ON u.user_id = l.user_id
GROUP BY u.user_id, u.username;

-- ============================================
-- UPDATE VIEW
-- ============================================

-- Replace existing view
CREATE OR REPLACE VIEW active_users AS
SELECT user_id, username, email, created_at, last_login
FROM users
WHERE is_active = TRUE AND deleted_at IS NULL;

-- ============================================
-- DROP VIEW
-- ============================================

DROP VIEW IF EXISTS active_users;

-- Drop with cascade (drops dependent objects)
DROP VIEW IF EXISTS user_orders_summary CASCADE;
```

---

## **12.2 Updatable Views**

```sql name=updatable_views.sql
-- ============================================
-- SIMPLE UPDATABLE VIEW
-- ============================================

-- View is automatically updatable if:
-- - Based on single table
-- - No aggregates, DISTINCT, GROUP BY, HAVING
-- - No UNION, INTERSECT, EXCEPT

CREATE VIEW premium_users AS
SELECT user_id, username, email, is_premium
FROM users
WHERE is_premium = TRUE;

-- Can UPDATE through view
UPDATE premium_users
SET email = 'newemail@example.com'
WHERE user_id = 1;

-- Can INSERT through view
INSERT INTO premium_users (username, email, is_premium)
VALUES ('newuser', 'new@example.com', TRUE);

-- Can DELETE through view
DELETE FROM premium_users WHERE user_id = 10;

-- ============================================
-- WITH CHECK OPTION
-- ============================================

-- Ensure inserted/updated rows remain visible in view
CREATE OR REPLACE VIEW premium_users AS
SELECT user_id, username, email, is_premium
FROM users
WHERE is_premium = TRUE
WITH CHECK OPTION;

-- This will FAIL (violates check option)
UPDATE premium_users SET is_premium = FALSE WHERE user_id = 1;
-- Error: new row violates check option for view

-- ============================================
-- INSTEAD OF TRIGGERS (Complex Views)
-- ============================================

-- See Triggers section above for examples
```

---

## **12.3 Materialized Views**

```sql name=materialized_views.sql
-- ============================================
-- MATERIALIZED VIEW (PostgreSQL)
-- ============================================

/*
- Stores query results physically
- Much faster than regular views
- Must be refreshed to update data
- Great for expensive queries/reports
*/

-- Create materialized view
CREATE MATERIALIZED VIEW monthly_sales_report AS
SELECT 
    DATE_TRUNC('month', order_date) AS month,
    COUNT(DISTINCT user_id) AS unique_customers,
    COUNT(order_id) AS total_orders,
    SUM(total_amount) AS total_revenue,
    AVG(total_amount) AS avg_order_value
FROM orders
WHERE order_date >= '2024-01-01'
GROUP BY DATE_TRUNC('month', order_date);

-- Create index on materialized view
CREATE INDEX idx_monthly_sales_month ON monthly_sales_report(month);

-- Query materialized view (fast!)
SELECT * FROM monthly_sales_report ORDER BY month DESC;

-- ============================================
-- REFRESH MATERIALIZED VIEW
-- ============================================

-- Refresh (locks view during refresh)
REFRESH MATERIALIZED VIEW monthly_sales_report;

-- Refresh concurrently (no lock, requires UNIQUE index)
CREATE UNIQUE INDEX idx_monthly_sales_month_unique 
ON monthly_sales_report(month);

REFRESH MATERIALIZED VIEW CONCURRENTLY monthly_sales_report;

-- ============================================
-- AUTOMATED REFRESH (Using pg_cron or cron)
-- ============================================

-- Schedule refresh every night at 2 AM
-- Using pg_cron extension:
SELECT cron.schedule('refresh-sales-report', '0 2 * * *', 
    'REFRESH MATERIALIZED VIEW monthly_sales_report');

-- ============================================
-- DROP MATERIALIZED VIEW
-- ============================================

DROP MATERIALIZED VIEW IF EXISTS monthly_sales_report;

-- ============================================
-- USE CASES FOR MATERIALIZED VIEWS
-- ============================================

-- 1. Dashboard reports
CREATE MATERIALIZED VIEW dashboard_stats AS
SELECT 
    (SELECT COUNT(*) FROM users WHERE created_at > CURRENT_DATE - 30) AS new_users_30d,
    (SELECT COUNT(*) FROM orders WHERE order_date > CURRENT_DATE - 7) AS orders_7d,
    (SELECT SUM(total_amount) FROM orders WHERE order_date > CURRENT_DATE - 30) AS revenue_30d;

-- 2. Complex aggregations
CREATE MATERIALIZED VIEW product_analytics AS
SELECT 
    p.product_id,
    p.product_name,
    COUNT(DISTINCT oi.order_id) AS times_ordered,
    SUM(oi.quantity) AS total_quantity_sold,
    SUM(oi.subtotal) AS total_revenue,
    AVG(oi.unit_price) AS avg_selling_price
FROM products p
LEFT JOIN order_items oi ON p.product_id = oi.product_id
GROUP BY p.product_id, p.product_name;

-- 3. Data warehouse queries
CREATE MATERIALIZED VIEW customer_lifetime_value AS
SELECT 
    u.user_id,
    u.username,
    COUNT(o.order_id) AS total_orders,
    SUM(o.total_amount) AS lifetime_value,
    MIN(o.order_date) AS first_order,
    MAX(o.order_date) AS last_order,
    AVG(o.total_amount) AS avg_order_value
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
GROUP BY u.user_id, u.username;
```

---

# **13. Database Normalization**

## **13.1 What is Normalization?**

**Normalization** is the process of organizing database tables to reduce redundancy and improve data integrity.

### **Benefits:**
- Eliminates duplicate data
- Reduces storage space
- Prevents update anomalies
- Improves data consistency

### **Drawbacks:**
- More complex queries (more joins)
- Can be slower (need to join tables)

---

## **13.2 First Normal Form (1NF)**

```sql name=normalization_1nf.sql
-- ============================================
-- FIRST NORMAL FORM (1NF)
-- ============================================

/*
Rules:
1. Each column contains atomic (indivisible) values
2. Each column contains values of single type
3. Each column has unique name
4. Order doesn't matter
*/

-- ❌ NOT in 1NF (multiple values in one column)
CREATE TABLE users_bad (
    user_id INT PRIMARY KEY,
    username VARCHAR(50),
    phone_numbers VARCHAR(200)  -- "555-1234, 555-5678, 555-9012"
);

-- ✅ IN 1NF (atomic values)
CREATE TABLE users (
    user_id INT PRIMARY KEY,
    username VARCHAR(50)
);

CREATE TABLE user_phones (
    phone_id SERIAL PRIMARY KEY,
    user_id INT NOT NULL,
    phone_number VARCHAR(20) NOT NULL,
    phone_type VARCHAR(20),  -- 'mobile', 'home', 'work'
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

-- ❌ NOT in 1NF (repeating groups)
CREATE TABLE orders_bad (
    order_id INT PRIMARY KEY,
    user_id INT,
    product1 VARCHAR(100),
    product2 VARCHAR(100),
    product3 VARCHAR(100)
);

-- ✅ IN 1NF (no repeating groups)
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT NOT NULL,
    order_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE TABLE order_items (
    order_item_id SERIAL PRIMARY KEY,
    order_id INT NOT NULL,
    product_name VARCHAR(100),
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);
```

---

## **13.3 Second Normal Form (2NF)**

```sql name=normalization_2nf.sql
-- ============================================
-- SECOND NORMAL FORM (2NF)
-- ============================================

/*
Rules:
1. Must be in 1NF
2. No partial dependencies (all non-key columns depend on entire primary key)
3. Only applies to tables with composite keys
*/

-- ❌ NOT in 2NF (partial dependency)
CREATE TABLE order_items_bad (
    order_id INT,
    product_id INT,
    quantity INT,
    product_name VARCHAR(100),     -- Depends only on product_id
    product_price DECIMAL(10, 2),  -- Depends only on product_id
    PRIMARY KEY (order_id, product_id)
);
-- product_name and product_price depend only on product_id, not full key!

-- ✅ IN 2NF (no partial dependencies)
CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR(100),
    product_price DECIMAL(10, 2)
);

CREATE TABLE order_items (
    order_id INT,
    product_id INT,
    quantity INT,
    unit_price DECIMAL(10, 2),  -- Price at time of order
    PRIMARY KEY (order_id, product_id),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- ❌ NOT in 2NF
CREATE TABLE student_courses_bad (
    student_id INT,
    course_id INT,
    student_name VARCHAR(100),    -- Depends only on student_id
    course_name VARCHAR(100),     -- Depends only on course_id
    grade CHAR(2),
    PRIMARY KEY (student_id, course_id)
);

-- ✅ IN 2NF
CREATE TABLE students (
    student_id SERIAL PRIMARY KEY,
    student_name VARCHAR(100)
);

CREATE TABLE courses (
    course_id SERIAL PRIMARY KEY,
    course_name VARCHAR(100)
);

CREATE TABLE enrollments (
    student_id INT,
    course_id INT,
    grade CHAR(2),
    PRIMARY KEY (student_id, course_id),
    FOREIGN KEY (student_id) REFERENCES students(student_id),
    FOREIGN KEY (course_id) REFERENCES courses(course_id)
);
```

---

## **13.4 Third Normal Form (3NF)**

```sql name=normalization_3nf.sql
-- ============================================
-- THIRD NORMAL FORM (3NF)
-- ============================================

/*
Rules:
1. Must be in 2NF
2. No transitive dependencies (non-key columns don't depend on other non-key columns)
*/

-- ❌ NOT in 3NF (transitive dependency)
CREATE TABLE employees_bad (
    employee_id SERIAL PRIMARY KEY,
    employee_name VARCHAR(100),
    department_id INT,
    department_name VARCHAR(100),    -- Depends on department_id
    department_location VARCHAR(100) -- Depends on department_id
);
-- department_name and department_location depend on department_id, not employee_id!

-- ✅ IN 3NF (no transitive dependencies)
CREATE TABLE departments (
    department_id SERIAL PRIMARY KEY,
    department_name VARCHAR(100),
    department_location VARCHAR(100)
);

CREATE TABLE employees (
    employee_id SERIAL PRIMARY KEY,
    employee_name VARCHAR(100),
    department_id INT,
    FOREIGN KEY (department_id) REFERENCES departments(department_id)
);

-- ❌ NOT in 3NF
CREATE TABLE orders_bad (
    order_id SERIAL PRIMARY KEY,
    user_id INT,
    user_email VARCHAR(100),      -- Depends on user_id
    user_city VARCHAR(50),        -- Depends on user_id
    order_total DECIMAL(10, 2)
);

-- ✅ IN 3NF
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    user_email VARCHAR(100),
    user_city VARCHAR(50)
);

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT,
    order_total DECIMAL(10, 2),
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);
```

---

## **13.5 When to Denormalize**

```sql name=denormalization.sql
-- ============================================
-- DENORMALIZATION (Intentional Redundancy)
-- ============================================

/*
When to denormalize:
1. Read-heavy applications (more SELECTs than INSERTs/UPDATEs)
2. Performance is critical
3. Complex joins are too slow
4. Data doesn't change often
*/

-- Example: E-commerce order history
-- ✅ Normalized (slow for reports)
CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT,
    order_date TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(user_id)
);

CREATE TABLE order_items (
    order_item_id SERIAL PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    unit_price DECIMAL(10, 2),
    FOREIGN KEY (order_id) REFERENCES orders(order_id),
    FOREIGN KEY (product_id) REFERENCES products(product_id)
);

-- ❌ To get order details, need 3-way join
SELECT 
    o.order_id,
    u.username,
    p.product_name,
    oi.quantity
FROM orders o
JOIN users u ON o.user_id = u.user_id
JOIN order_items oi ON o.order_id = oi.order_id
JOIN products p ON oi.product_id = p.product_id;

-- ✅ Denormalized (fast for reports, but redundant)
CREATE TABLE order_items_denormalized (
    order_item_id SERIAL PRIMARY KEY,
    order_id INT,
    product_id INT,
    product_name VARCHAR(200),  -- Redundant!
    quantity INT,
    unit_price DECIMAL(10, 2),
    username VARCHAR(50),       -- Redundant!
    FOREIGN KEY (order_id) REFERENCES orders(order_id)
);

-- Fast query (no joins needed)
SELECT order_id, product_name, quantity, username
FROM order_items_denormalized
WHERE order_id = 123;

-- ============================================
-- DENORMALIZATION PATTERNS
-- ============================================

-- 1. Calculated columns (cached aggregates)
ALTER TABLE users ADD COLUMN total_orders INT DEFAULT 0;
ALTER TABLE users ADD COLUMN total_spent DECIMAL(10, 2) DEFAULT 0;

-- Keep updated with triggers
CREATE OR REPLACE FUNCTION update_user_stats()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE users
    SET 
        total_orders = (SELECT COUNT(*) FROM orders WHERE user_id = NEW.user_id),
        total_spent = (SELECT COALESCE(SUM(total_amount), 0) FROM orders WHERE user_id = NEW.user_id)
    WHERE user_id = NEW.user_id;
    RETURN NEW;
END;
$$;

CREATE TRIGGER trg_update_user_stats
AFTER INSERT OR UPDATE OR DELETE ON orders
FOR EACH ROW
EXECUTE FUNCTION update_user_stats();

-- 2. Counter caches
ALTER TABLE posts ADD COLUMN comment_count INT DEFAULT 0;
ALTER TABLE posts ADD COLUMN like_count INT DEFAULT 0;

-- 3. Materialized views (for complex reports)
CREATE MATERIALIZED VIEW user_analytics AS
SELECT 
    u.user_id,
    u.username,
    COUNT(DISTINCT o.order_id) AS total_orders,
    COUNT(DISTINCT p.post_id) AS total_posts,
    COUNT(DISTINCT c.comment_id) AS total_comments
FROM users u
LEFT JOIN orders o ON u.user_id = o.user_id
LEFT JOIN posts p ON u.user_id = p.user_id
LEFT JOIN comments c ON u.user_id = c.user_id
GROUP BY u.user_id, u.username;
```

---

*(Continuing in next message...)*

## @meet41

continue

## Copilot

# **14. SQL Injection & Security**

## **14.1 What is SQL Injection?**

**SQL Injection** is a code injection technique where attackers insert malicious SQL code into application queries to manipulate the database.

```sql name=sql_injection_examples.sql
-- ============================================
-- HOW SQL INJECTION WORKS
-- ============================================

-- ❌ VULNERABLE CODE EXAMPLE (Python)
/*
username = request.form['username']
password = request.form['password']

# String concatenation - DANGEROUS!
query = f"SELECT * FROM users WHERE username = '{username}' AND password = '{password}'"
cursor.execute(query)
*/

-- Normal input:
-- username: "john_doe"
-- password: "mypassword123"
-- Query: SELECT * FROM users WHERE username = 'john_doe' AND password = 'mypassword123'

-- ❌ MALICIOUS INPUT (SQL Injection Attack):
-- username: "admin' OR '1'='1' --"
-- password: "anything"
-- Query becomes: 
-- SELECT * FROM users WHERE username = 'admin' OR '1'='1' --' AND password = 'anything'
--                                                      ↑ Comment starts here
-- Result: Returns admin user WITHOUT password!

-- ============================================
-- SQL INJECTION ATTACK TYPES
-- ============================================

-- 1. Authentication Bypass
-- Input: username = "admin' --"
-- Query: SELECT * FROM users WHERE username = 'admin' --' AND password = 'xxx'
-- Result: Password check is commented out!

-- 2. Data Extraction
-- Input: username = "' OR 1=1 --"
-- Query: SELECT * FROM users WHERE username = '' OR 1=1 --'
-- Result: Returns ALL users!

-- 3. Data Modification
-- Input: username = "'; UPDATE users SET is_admin = TRUE WHERE username = 'hacker'; --"
-- Result: Makes hacker an admin!

-- 4. Data Deletion
-- Input: username = "'; DROP TABLE users; --"
-- Result: Deletes entire users table!

-- 5. Union-based Injection
-- Input: "' UNION SELECT credit_card, cvv, expiry FROM payment_info --"
-- Result: Extracts sensitive payment data

-- ============================================
-- REAL-WORLD ATTACK SCENARIOS
-- ============================================

-- Login Form Attack
/*
Normal query:
SELECT * FROM users WHERE username = 'john' AND password = 'pass123'

Attack input:
username: admin' OR '1'='1
password: (anything)

Resulting query:
SELECT * FROM users WHERE username = 'admin' OR '1'='1' AND password = 'anything'
Returns: First user (usually admin)
*/

-- Search Field Attack
/*
Normal query:
SELECT * FROM products WHERE product_name LIKE '%laptop%'

Attack input:
search: %' OR 1=1 --

Resulting query:
SELECT * FROM products WHERE product_name LIKE '%%' OR 1=1 --%'
Returns: ALL products
*/

-- URL Parameter Attack
/*
URL: example.com/user?id=123

Normal query:
SELECT * FROM users WHERE user_id = 123

Attack URL: example.com/user?id=123; DROP TABLE users; --

Resulting query:
SELECT * FROM users WHERE user_id = 123; DROP TABLE users; --
Result: User table is DELETED!
*/
```

---

## **14.2 Preventing SQL Injection**

```sql name=sql_injection_prevention.sql
-- ============================================
-- METHOD 1: PARAMETERIZED QUERIES (BEST)
-- ============================================

-- ❌ UNSAFE (String concatenation)
/*
Python example:
query = f"SELECT * FROM users WHERE username = '{username}'"
cursor.execute(query)
*/

-- ✅ SAFE (Parameterized query)
/*
Python with psycopg2:
query = "SELECT * FROM users WHERE username = %s AND password = %s"
cursor.execute(query, (username, password))

Python with psycopg3:
query = "SELECT * FROM users WHERE username = $1 AND password = $2"
cursor.execute(query, (username, password))

Node.js with pg:
const query = 'SELECT * FROM users WHERE username = $1 AND password = $2';
client.query(query, [username, password]);

PHP with PDO:
$stmt = $pdo->prepare('SELECT * FROM users WHERE username = ? AND password = ?');
$stmt->execute([$username, $password]);
*/

-- ============================================
-- METHOD 2: PREPARED STATEMENTS (PostgreSQL)
-- ============================================

-- Create prepared statement
PREPARE get_user (VARCHAR, VARCHAR) AS
    SELECT user_id, username, email
    FROM users
    WHERE username = $1 AND password = $2;

-- Execute with parameters (safe from injection)
EXECUTE get_user('john_doe', 'password123');

-- Deallocate when done
DEALLOCATE get_user;

-- ============================================
-- METHOD 3: ORM (Object-Relational Mapping)
-- ============================================

/*
ORMs automatically parameterize queries

Python SQLAlchemy example:
user = session.query(User).filter(
    User.username == username,
    User.password == password
).first()

Django ORM:
user = User.objects.filter(username=username, password=password).first()

Even with malicious input, ORM treats it as literal string:
username = "admin' OR '1'='1' --"
# Query: SELECT * FROM users WHERE username = 'admin'' OR ''1''=''1'' --'
# Single quotes are escaped automatically!
*/

-- ============================================
-- METHOD 4: INPUT VALIDATION & SANITIZATION
-- ============================================

/*
1. Whitelist Validation
- Only allow expected characters
- Example: Username should be alphanumeric only

Python example:
import re
if not re.match(r'^[a-zA-Z0-9_]+$', username):
    raise ValueError("Invalid username")

2. Length Validation
if len(username) > 50:
    raise ValueError("Username too long")

3. Type Validation
user_id = int(request.args.get('id'))  # Raises error if not integer

4. Escape Special Characters (Last resort)
- Use database-specific escaping functions
- Example: psycopg2 automatically escapes
*/

-- ============================================
-- METHOD 5: STORED PROCEDURES
-- ============================================

-- Define stored procedure
CREATE OR REPLACE PROCEDURE authenticate_user(
    p_username VARCHAR,
    p_password VARCHAR,
    OUT p_user_id INT,
    OUT p_success BOOLEAN
)
LANGUAGE plpgsql
AS $$
BEGIN
    SELECT user_id INTO p_user_id
    FROM users
    WHERE username = p_username
        AND password = p_password  -- Should be hashed!
    LIMIT 1;
    
    p_success := (p_user_id IS NOT NULL);
END;
$$;

-- Call from application
-- Even if input contains SQL, it's treated as literal string
CALL authenticate_user('admin'' OR ''1''=''1', 'pass', NULL, NULL);

-- ============================================
-- METHOD 6: LEAST PRIVILEGE PRINCIPLE
-- ============================================

-- Create application user with limited permissions
CREATE ROLE app_user WITH LOGIN PASSWORD 'secure_password';

-- Grant only necessary permissions
GRANT SELECT, INSERT, UPDATE ON users TO app_user;
GRANT SELECT ON products TO app_user;

-- Revoke dangerous permissions
REVOKE DELETE ON users FROM app_user;
REVOKE DROP, CREATE ON DATABASE mydb FROM app_user;

-- Even if SQL injection occurs, damage is limited
-- Can't DROP tables, can't DELETE from users, etc.

-- ============================================
-- METHOD 7: WEB APPLICATION FIREWALL (WAF)
-- ============================================

/*
Use WAF to detect and block SQL injection attempts
- ModSecurity
- AWS WAF
- Cloudflare WAF

Common patterns blocked:
- ' OR 1=1
- UNION SELECT
- DROP TABLE
- xp_cmdshell
- etc.
*/
```

---

## **14.3 Secure Password Handling**

```sql name=password_security.sql
-- ============================================
-- PASSWORD HASHING (Never store plain text!)
-- ============================================

-- ❌ NEVER DO THIS
CREATE TABLE users_bad (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50),
    password VARCHAR(100)  -- Plain text password!
);

-- ✅ ALWAYS HASH PASSWORDS
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,  -- Hashed password
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- ============================================
-- USING PGCRYPTO EXTENSION (PostgreSQL)
-- ============================================

-- Enable extension
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- Hash password during insert
INSERT INTO users (username, password_hash)
VALUES ('john_doe', crypt('mypassword123', gen_salt('bf')));
-- 'bf' = Blowfish algorithm (bcrypt)

-- Verify password during login
SELECT user_id, username
FROM users
WHERE username = 'john_doe'
    AND password_hash = crypt('mypassword123', password_hash);
-- Returns row if password matches, empty if not

-- ============================================
-- BETTER: HASH PASSWORDS IN APPLICATION
-- ============================================

/*
Python with bcrypt:
import bcrypt

# Hash password
password = "mypassword123"
hashed = bcrypt.hashpw(password.encode('utf-8'), bcrypt.gensalt())

# Store hashed password in database
cursor.execute(
    "INSERT INTO users (username, password_hash) VALUES (%s, %s)",
    (username, hashed.decode('utf-8'))
)

# Verify password
cursor.execute(
    "SELECT password_hash FROM users WHERE username = %s",
    (username,)
)
stored_hash = cursor.fetchone()[0].encode('utf-8')
if bcrypt.checkpw(password.encode('utf-8'), stored_hash):
    print("Login successful")
*/

-- ============================================
-- PASSWORD SECURITY BEST PRACTICES
-- ============================================

-- 1. Use strong hashing algorithms
-- ✅ bcrypt, scrypt, Argon2
-- ❌ MD5, SHA1 (too fast, easily cracked)

-- 2. Salt passwords (prevent rainbow table attacks)
-- Bcrypt automatically includes salt

-- 3. Use key stretching (slow down brute force)
-- Bcrypt has configurable work factor

-- 4. Store additional security info
CREATE TABLE users (
    user_id SERIAL PRIMARY KEY,
    username VARCHAR(50) UNIQUE NOT NULL,
    email VARCHAR(100) UNIQUE NOT NULL,
    password_hash VARCHAR(255) NOT NULL,
    password_changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    failed_login_attempts INT DEFAULT 0,
    locked_until TIMESTAMP,
    last_login TIMESTAMP,
    two_factor_secret VARCHAR(32),  -- For 2FA
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- 5. Implement account lockout
CREATE OR REPLACE FUNCTION handle_failed_login(p_username VARCHAR)
RETURNS VOID
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE users
    SET failed_login_attempts = failed_login_attempts + 1,
        locked_until = CASE 
            WHEN failed_login_attempts >= 4 THEN 
                CURRENT_TIMESTAMP + INTERVAL '30 minutes'
            ELSE locked_until
        END
    WHERE username = p_username;
END;
$$;

-- 6. Reset failed attempts on successful login
CREATE OR REPLACE FUNCTION handle_successful_login(p_username VARCHAR)
RETURNS VOID
LANGUAGE plpgsql
AS $$
BEGIN
    UPDATE users
    SET failed_login_attempts = 0,
        locked_until = NULL,
        last_login = CURRENT_TIMESTAMP
    WHERE username = p_username;
END;
$$;
```

---

## **14.4 Additional Security Measures**

```sql name=database_security.sql
-- ============================================
-- ROW-LEVEL SECURITY (PostgreSQL)
-- ============================================

-- Enable RLS on table
ALTER TABLE documents ENABLE ROW LEVEL SECURITY;

-- Create policy: Users can only see their own documents
CREATE POLICY user_documents_policy ON documents
    FOR SELECT
    USING (user_id = current_user_id());

-- Create function to get current user
CREATE OR REPLACE FUNCTION current_user_id()
RETURNS INT
LANGUAGE plpgsql
AS $$
BEGIN
    -- Get user_id from session variable
    RETURN current_setting('app.current_user_id')::INT;
END;
$$;

-- Set user context in application
SET app.current_user_id = 123;

-- Now queries automatically filter by user
SELECT * FROM documents;
-- Only returns documents where user_id = 123

-- ============================================
-- COLUMN-LEVEL ENCRYPTION
-- ============================================

-- Enable pgcrypto
CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- Create table with encrypted column
CREATE TABLE sensitive_data (
    id SERIAL PRIMARY KEY,
    user_id INT,
    credit_card_encrypted BYTEA,  -- Encrypted data
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert encrypted data
INSERT INTO sensitive_data (user_id, credit_card_encrypted)
VALUES (
    123,
    pgp_sym_encrypt('4111111111111111', 'encryption_key_here')
);

-- Retrieve and decrypt
SELECT 
    id,
    user_id,
    pgp_sym_decrypt(credit_card_encrypted, 'encryption_key_here') AS credit_card
FROM sensitive_data
WHERE user_id = 123;

-- ============================================
-- AUDIT LOGGING
-- ============================================

-- Create audit table
CREATE TABLE audit_log (
    audit_id BIGSERIAL PRIMARY KEY,
    table_name VARCHAR(50),
    operation VARCHAR(10),  -- INSERT, UPDATE, DELETE
    old_data JSONB,
    new_data JSONB,
    changed_by VARCHAR(50),
    changed_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    ip_address INET
);

-- Create audit trigger function
CREATE OR REPLACE FUNCTION audit_trigger_function()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    IF (TG_OP = 'DELETE') THEN
        INSERT INTO audit_log (table_name, operation, old_data, changed_by)
        VALUES (TG_TABLE_NAME, TG_OP, row_to_json(OLD), current_user);
        RETURN OLD;
    ELSIF (TG_OP = 'UPDATE') THEN
        INSERT INTO audit_log (table_name, operation, old_data, new_data, changed_by)
        VALUES (TG_TABLE_NAME, TG_OP, row_to_json(OLD), row_to_json(NEW), current_user);
        RETURN NEW;
    ELSIF (TG_OP = 'INSERT') THEN
        INSERT INTO audit_log (table_name, operation, new_data, changed_by)
        VALUES (TG_TABLE_NAME, TG_OP, row_to_json(NEW), current_user);
        RETURN NEW;
    END IF;
END;
$$;

-- Apply audit trigger to sensitive table
CREATE TRIGGER audit_users
AFTER INSERT OR UPDATE OR DELETE ON users
FOR EACH ROW
EXECUTE FUNCTION audit_trigger_function();

-- ============================================
-- DATABASE USER MANAGEMENT
-- ============================================

-- Create roles with specific permissions
CREATE ROLE readonly;
GRANT CONNECT ON DATABASE mydb TO readonly;
GRANT SELECT ON ALL TABLES IN SCHEMA public TO readonly;

CREATE ROLE readwrite;
GRANT CONNECT ON DATABASE mydb TO readwrite;
GRANT SELECT, INSERT, UPDATE ON ALL TABLES IN SCHEMA public TO readwrite;

-- Create users and assign roles
CREATE USER report_user WITH PASSWORD 'secure_pass';
GRANT readonly TO report_user;

CREATE USER app_user WITH PASSWORD 'secure_pass';
GRANT readwrite TO app_user;

-- Revoke public permissions
REVOKE ALL ON SCHEMA public FROM PUBLIC;

-- ============================================
-- CONNECTION SECURITY
-- ============================================

-- Require SSL connections (postgresql.conf)
-- ssl = on
-- ssl_cert_file = 'server.crt'
-- ssl_key_file = 'server.key'

-- Configure pg_hba.conf to require SSL
-- hostssl all all 0.0.0.0/0 md5

-- ============================================
-- PREVENTING PRIVILEGE ESCALATION
-- ============================================

-- Don't use SECURITY DEFINER unless necessary
CREATE OR REPLACE FUNCTION public_function()
RETURNS TEXT
LANGUAGE plpgsql
SECURITY INVOKER  -- Runs with caller's privileges
AS $$
BEGIN
    RETURN 'Safe function';
END;
$$;

-- ✅ If SECURITY DEFINER needed, validate input thoroughly
CREATE OR REPLACE FUNCTION admin_function(p_user_id INT)
RETURNS VOID
LANGUAGE plpgsql
SECURITY DEFINER  -- Runs with creator's privileges
AS $$
BEGIN
    -- Validate input
    IF p_user_id IS NULL OR p_user_id <= 0 THEN
        RAISE EXCEPTION 'Invalid user ID';
    END IF;
    
    -- Check permissions
    IF NOT is_admin(current_user) THEN
        RAISE EXCEPTION 'Permission denied';
    END IF;
    
    -- Safe to proceed
    UPDATE sensitive_table SET col = val WHERE user_id = p_user_id;
END;
$$;

-- ============================================
-- RATE LIMITING
-- ============================================

-- Track login attempts
CREATE TABLE login_attempts (
    id SERIAL PRIMARY KEY,
    ip_address INET,
    username VARCHAR(50),
    attempt_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    success BOOLEAN
);

-- Function to check rate limit
CREATE OR REPLACE FUNCTION is_rate_limited(
    p_ip_address INET,
    p_max_attempts INT DEFAULT 5,
    p_time_window INTERVAL DEFAULT '15 minutes'
)
RETURNS BOOLEAN
LANGUAGE plpgsql
AS $$
DECLARE
    v_attempt_count INT;
BEGIN
    SELECT COUNT(*) INTO v_attempt_count
    FROM login_attempts
    WHERE ip_address = p_ip_address
        AND attempt_time > CURRENT_TIMESTAMP - p_time_window
        AND success = FALSE;
    
    RETURN v_attempt_count >= p_max_attempts;
END;
$$;
```

---

# **15. PostgreSQL Advanced Features**

## **15.1 PostgreSQL-Specific Data Types**

```sql name=postgresql_data_types.sql
-- ============================================
-- JSON & JSONB
-- ============================================

-- JSON: Stores as text, validates JSON format
-- JSONB: Binary format, indexable, faster queries

CREATE TABLE products (
    product_id SERIAL PRIMARY KEY,
    product_name VARCHAR(200),
    metadata JSON,          -- Stores exact text
    specifications JSONB    -- Parsed and stored efficiently
);

-- Insert JSON data
INSERT INTO products (product_name, specifications)
VALUES (
    'Laptop',
    '{"brand": "Dell", "ram": "16GB", "storage": {"type": "SSD", "capacity": "512GB"}}'::JSONB
);

-- Query JSON data
SELECT * FROM products
WHERE specifications->>'brand' = 'Dell';

-- Extract nested values
SELECT 
    product_name,
    specifications->'storage'->>'capacity' AS storage_capacity
FROM products;

-- JSONB operators
SELECT * FROM products WHERE specifications @> '{"brand": "Dell"}';  -- Contains
SELECT * FROM products WHERE specifications ? 'brand';                -- Has key
SELECT * FROM products WHERE specifications ?| ARRAY['brand', 'ram']; -- Has any key
SELECT * FROM products WHERE specifications ?& ARRAY['brand', 'ram']; -- Has all keys

-- Update JSON field
UPDATE products
SET specifications = jsonb_set(
    specifications,
    '{ram}',
    '"32GB"'::JSONB
)
WHERE product_id = 1;

-- Add new key
UPDATE products
SET specifications = specifications || '{"warranty": "2 years"}'::JSONB;

-- Remove key
UPDATE products
SET specifications = specifications - 'warranty';

-- Create GIN index for fast JSON queries
CREATE INDEX idx_products_specs ON products USING GIN(specifications);

-- ============================================
-- UUID (Universally Unique Identifier)
-- ============================================

-- Enable extension
CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

CREATE TABLE sessions (
    session_id UUID DEFAULT uuid_generate_v4() PRIMARY KEY,
    user_id INT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP
);

-- Insert with auto-generated UUID
INSERT INTO sessions (user_id, expires_at)
VALUES (123, CURRENT_TIMESTAMP + INTERVAL '1 hour');

-- Generate UUID
SELECT uuid_generate_v4();  -- Random UUID
SELECT uuid_generate_v1();  -- Time-based UUID

-- ============================================
-- ARRAY
-- ============================================

CREATE TABLE posts (
    post_id SERIAL PRIMARY KEY,
    title VARCHAR(200),
    tags TEXT[],           -- Array of text
    view_counts INT[],     -- Array of integers
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert array data
INSERT INTO posts (title, tags)
VALUES ('PostgreSQL Guide', ARRAY['database', 'postgresql', 'sql']);

-- Or using array literal
INSERT INTO posts (title, tags)
VALUES ('Python Tutorial', '{"python", "programming", "tutorial"}');

-- Query arrays
SELECT * FROM posts WHERE 'postgresql' = ANY(tags);
SELECT * FROM posts WHERE tags @> ARRAY['database'];  -- Contains
SELECT * FROM posts WHERE tags && ARRAY['python', 'sql'];  -- Overlaps

-- Array functions
SELECT 
    title,
    array_length(tags, 1) AS tag_count,
    array_to_string(tags, ', ') AS tags_string
FROM posts;

-- Unnest array (expand to rows)
SELECT 
    post_id,
    title,
    unnest(tags) AS tag
FROM posts;

-- Array aggregation
SELECT array_agg(title) AS all_titles FROM posts;

-- Update array
UPDATE posts
SET tags = array_append(tags, 'beginner')
WHERE post_id = 1;

UPDATE posts
SET tags = array_remove(tags, 'database')
WHERE post_id = 1;

-- GIN index for array searches
CREATE INDEX idx_posts_tags ON posts USING GIN(tags);

-- ============================================
-- ENUM
-- ============================================

-- Create ENUM type
CREATE TYPE order_status AS ENUM (
    'pending',
    'processing',
    'shipped',
    'delivered',
    'cancelled'
);

CREATE TABLE orders (
    order_id SERIAL PRIMARY KEY,
    user_id INT,
    status order_status DEFAULT 'pending',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert with ENUM
INSERT INTO orders (user_id, status)
VALUES (123, 'processing');

-- Query with ENUM
SELECT * FROM orders WHERE status = 'pending';

-- Get all ENUM values
SELECT enum_range(NULL::order_status);

-- Add new ENUM value
ALTER TYPE order_status ADD VALUE 'returned';

-- Drop ENUM type
DROP TYPE order_status CASCADE;

-- ============================================
-- HSTORE (Key-Value Store)
-- ============================================

-- Enable extension
CREATE EXTENSION IF NOT EXISTS hstore;

CREATE TABLE user_preferences (
    user_id INT PRIMARY KEY,
    settings HSTORE
);

-- Insert hstore data
INSERT INTO user_preferences (user_id, settings)
VALUES (
    123,
    'theme => dark, language => en, notifications => true'::HSTORE
);

-- Query hstore
SELECT * FROM user_preferences
WHERE settings->'theme' = 'dark';

-- Get all keys
SELECT skeys(settings) FROM user_preferences WHERE user_id = 123;

-- Get all values
SELECT svals(settings) FROM user_preferences WHERE user_id = 123;

-- Update hstore
UPDATE user_preferences
SET settings = settings || 'theme => light'::HSTORE
WHERE user_id = 123;

-- ============================================
-- INET & CIDR (IP Addresses)
-- ============================================

CREATE TABLE access_log (
    log_id SERIAL PRIMARY KEY,
    ip_address INET,
    network CIDR,
    access_time TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Insert IP addresses
INSERT INTO access_log (ip_address, network)
VALUES 
    ('192.168.1.100', '192.168.1.0/24'),
    ('2001:db8::1', '2001:db8::/32');

-- Query IP ranges
SELECT * FROM access_log WHERE ip_address << '192.168.1.0/24'::INET;  -- Within network

-- IP functions
SELECT 
    ip_address,
    host(ip_address) AS ip_only,
    netmask(network) AS mask,
    broadcast(network) AS broadcast_addr
FROM access_log;

-- ============================================
-- RANGE TYPES
-- ============================================

CREATE TABLE bookings (
    booking_id SERIAL PRIMARY KEY,
    room_number INT,
    booking_period TSRANGE,  -- Timestamp range
    EXCLUDE USING GIST (room_number WITH =, booking_period WITH &&)
    -- Prevents overlapping bookings for same room
);

-- Insert range data
INSERT INTO bookings (room_number, booking_period)
VALUES (
    101,
    '[2026-03-01 14:00:00, 2026-03-03 10:00:00)'  -- [inclusive, exclusive)
);

-- This will FAIL (overlapping booking)
INSERT INTO bookings (room_number, booking_period)
VALUES (
    101,
    '[2026-03-02 14:00:00, 2026-03-04 10:00:00)'
);

-- Query ranges
SELECT * FROM bookings
WHERE booking_period @> '2026-03-02 15:00:00'::TIMESTAMP;  -- Contains timestamp

-- Other range types
CREATE TABLE price_ranges (
    id SERIAL PRIMARY KEY,
    price_bracket INT4RANGE,    -- Integer range
    date_range DATERANGE,        -- Date range
    numeric_range NUMRANGE       -- Numeric range
);

-- ============================================
-- MONEY
-- ============================================

CREATE TABLE accounts (
    account_id SERIAL PRIMARY KEY,
    balance MONEY
);

-- Insert money values
INSERT INTO accounts (balance) VALUES ('$1,234.56'), (1234.56);

-- Money calculations
SELECT balance, balance * 1.1 AS with_interest FROM accounts;

-- Note: MONEY is locale-dependent, NUMERIC is often better for currency
```

---

## **15.2 Full-Text Search**

```sql name=full_text_search.sql
-- ============================================
-- FULL-TEXT SEARCH
-- ============================================

-- Add tsvector column
ALTER TABLE posts ADD COLUMN search_vector TSVECTOR;

-- Populate tsvector
UPDATE posts
SET search_vector = 
    setweight(to_tsvector('english', COALESCE(title, '')), 'A') ||
    setweight(to_tsvector('english', COALESCE(content, '')), 'B');

-- Create GIN index for fast search
CREATE INDEX idx_posts_search ON posts USING GIN(search_vector);

-- Full-text search query
SELECT 
    post_id,
    title,
    ts_rank(search_vector, query) AS rank
FROM posts, to_tsquery('english', 'postgresql & database') AS query
WHERE search_vector @@ query
ORDER BY rank DESC;

-- Highlight search results
SELECT 
    post_id,
    ts_headline('english', content, to_tsquery('postgresql & database')) AS highlighted
FROM posts
WHERE search_vector @@ to_tsquery('postgresql & database');

-- ============================================
-- AUTO-UPDATE SEARCH VECTOR (Trigger)
-- ============================================

CREATE OR REPLACE FUNCTION update_search_vector()
RETURNS TRIGGER
LANGUAGE plpgsql
AS $$
BEGIN
    NEW.search_vector := 
        setweight(to_tsvector('english', COALESCE(NEW.title, '')), 'A') ||
        setweight(to_tsvector('english', COALESCE(NEW.content, '')), 'B');
    RETURN NEW;
END;
$$;

CREATE TRIGGER trg_update_search_vector
BEFORE INSERT OR UPDATE ON posts
FOR EACH ROW
EXECUTE FUNCTION update_search_vector();

-- ============================================
-- ADVANCED SEARCH QUERIES
-- ============================================

-- AND operator
SELECT * FROM posts
WHERE search_vector @@ to_tsquery('postgresql & database');

-- OR operator
SELECT * FROM posts
WHERE search_vector @@ to_tsquery('postgresql | mysql');

-- NOT operator
SELECT * FROM posts
WHERE search_vector @@ to_tsquery('postgresql & !oracle');

-- Phrase search
SELECT * FROM posts
WHERE search_vector @@ phraseto_tsquery('database management system');

-- Prefix search
SELECT * FROM posts
WHERE search_vector @@ to_tsquery('post:*');  -- Matches post, posts, posting, etc.

-- ============================================
-- SEARCH CONFIGURATION
-- ============================================

-- Create custom search configuration
CREATE TEXT SEARCH CONFIGURATION custom_english (COPY = english);

-- Modify stopwords (words to ignore)
ALTER TEXT SEARCH CONFIGURATION custom_english
    ALTER MAPPING FOR asciiword, word
    WITH english_stem;
```

---

## **15.3 Window Functions**

```sql name=window_functions_advanced.sql
-- ============================================
-- ADVANCED WINDOW FUNCTIONS
-- ============================================

-- Moving averages
SELECT 
    order_date,
    total_amount,
    AVG(total_amount) OVER (
        ORDER BY order_date 
        ROWS BETWEEN 6 PRECEDING AND CURRENT ROW
    ) AS moving_avg_7_days
FROM orders;

-- Cumulative sum
SELECT 
    order_date,
    total_amount,
    SUM(total_amount) OVER (ORDER BY order_date) AS cumulative_total
FROM orders;

-- Percent of total
SELECT 
    category,
    sales,
    ROUND(100.0 * sales / SUM(sales) OVER (), 2) AS percent_of_total
FROM category_sales;

-- First and last value
SELECT 
    employee_name,
    salary,
    FIRST_VALUE(salary) OVER (ORDER BY salary DESC) AS highest_salary,
    LAST_VALUE(salary) OVER (
        ORDER BY salary DESC
        ROWS BETWEEN UNBOUNDED PRECEDING AND UNBOUNDED FOLLOWING
    ) AS lowest_salary
FROM employees;

-- NTILE (divide into buckets)
SELECT 
    employee_name,
    salary,
    NTILE(4) OVER (ORDER BY salary) AS quartile
FROM employees;

-- Conditional aggregation
SELECT 
    department,
    AVG(salary) FILTER (WHERE age < 30) OVER (PARTITION BY department) AS avg_young_salary
FROM employees;
```

---

## **15.4 Partitioning**

```sql name=table_partitioning.sql
-- ============================================
-- TABLE PARTITIONING (PostgreSQL 10+)
-- ============================================

/*
Benefits:
- Improved query performance
- Faster data archival/deletion
- Better index management
*/

-- ============================================
-- RANGE PARTITIONING (By Date)
-- ============================================

-- Create partitioned table
CREATE TABLE orders_partitioned (
    order_id BIGSERIAL,
    user_id INT NOT NULL,
    order_date DATE NOT NULL,
    total_amount DECIMAL(10, 2),
    PRIMARY KEY (order_id, order_date)
) PARTITION BY RANGE (order_date);

-- Create partitions
CREATE TABLE orders_2024_q1 PARTITION OF orders_partitioned
    FOR VALUES FROM ('2024-01-01') TO ('2024-04-01');

CREATE TABLE orders_2024_q2 PARTITION OF orders_partitioned
    FOR VALUES FROM ('2024-04-01') TO ('2024-07-01');

CREATE TABLE orders_2024_q3 PARTITION OF orders_partitioned
    FOR VALUES FROM ('2024-07-01') TO ('2024-10-01');

CREATE TABLE orders_2024_q4 PARTITION OF orders_partitioned
    FOR VALUES FROM ('2024-10-01') TO ('2025-01-01');

-- Insert automatically goes to correct partition
INSERT INTO orders_partitioned (user_id, order_date, total_amount)
VALUES (123, '2024-03-15', 150.00);
-- Goes to orders_2024_q1

-- Query partitioned table
SELECT * FROM orders_partitioned
WHERE order_date BETWEEN '2024-03-01' AND '2024-03-31';
-- Only scans orders_2024_q1 (partition pruning)

-- Drop old partition (fast delete)
DROP TABLE orders_2024_q1;

-- ============================================
-- LIST PARTITIONING (By Category)
-- ============================================

CREATE TABLE products_partitioned (
    product_id SERIAL,
    product_name VARCHAR(200),
    category VARCHAR(50),
    PRIMARY KEY (product_id, category)
) PARTITION BY LIST (category);

CREATE TABLE products_electronics PARTITION OF products_partitioned
    FOR VALUES IN ('electronics', 'computers');

CREATE TABLE products_clothing PARTITION OF products_partitioned
    FOR VALUES IN ('clothing', 'shoes');

CREATE TABLE products_other PARTITION OF products_partitioned
    DEFAULT;  -- Catch-all partition

-- ============================================
-- HASH PARTITIONING (Distribute evenly)
-- ============================================

CREATE TABLE logs_partitioned (
    log_id BIGSERIAL,
    user_id INT,
    message TEXT,
    created_at TIMESTAMP,
    PRIMARY KEY (log_id, user_id)
) PARTITION BY HASH (user_id);

CREATE TABLE logs_part_0 PARTITION OF logs_partitioned
    FOR VALUES WITH (MODULUS 4, REMAINDER 0);

CREATE TABLE logs_part_1 PARTITION OF logs_partitioned
    FOR VALUES WITH (MODULUS 4, REMAINDER 1);

CREATE TABLE logs_part_2 PARTITION OF logs_partitioned
    FOR VALUES WITH (MODULUS 4, REMAINDER 2);

CREATE TABLE logs_part_3 PARTITION OF logs_partitioned
    FOR VALUES WITH (MODULUS 4, REMAINDER 3);
```

---

## **15.5 Extensions**

```sql name=postgresql_extensions.sql
-- ============================================
-- USEFUL POSTGRESQL EXTENSIONS
-- ============================================

-- View available extensions
SELECT * FROM pg_available_extensions ORDER BY name;

-- View installed extensions
SELECT * FROM pg_extension;

-- ============================================
-- 1. UUID-OSSP (UUID Generation)
-- ============================================

CREATE EXTENSION IF NOT EXISTS "uuid-ossp";

SELECT uuid_generate_v4();

-- ============================================
-- 2. PGCRYPTO (Encryption)
-- ============================================

CREATE EXTENSION IF NOT EXISTS pgcrypto;

-- Hashing
SELECT digest('hello', 'sha256');
SELECT crypt('password', gen_salt('bf'));

-- Encryption
SELECT pgp_sym_encrypt('secret data', 'encryption_key');
SELECT pgp_sym_decrypt(encrypted_data, 'encryption_key') FROM table;

-- ============================================
-- 3. PG_TRGM (Similarity Search)
-- ============================================

CREATE EXTENSION IF NOT EXISTS pg_trgm;

-- Fuzzy search
SELECT * FROM products
WHERE product_name % 'laptp';  -- Finds "laptop" despite typo

-- Similarity score
SELECT 
    product_name,
    similarity(product_name, 'laptop') AS score
FROM products
WHERE product_name % 'laptop'
ORDER BY score DESC;

-- Create GIN index for fast fuzzy search
CREATE INDEX idx_products_name_trgm ON products USING GIN(product_name gin_trgm_ops);

-- ============================================
-- 4. HSTORE (Key-Value Store)
-- ============================================

CREATE EXTENSION IF NOT EXISTS hstore;
-- See section 15.1 for usage examples

-- ============================================
-- 5. LTREE (Hierarchical Data)
-- ============================================

CREATE EXTENSION IF NOT EXISTS ltree;

CREATE TABLE categories (
    id SERIAL PRIMARY KEY,
    path LTREE,
    name VARCHAR(100)
);

INSERT INTO categories (path, name) VALUES
    ('Top', 'Top'),
    ('Top.Electronics', 'Electronics'),
    ('Top.Electronics.Computers', 'Computers'),
    ('Top.Electronics.Phones', 'Phones'),
    ('Top.Clothing', 'Clothing');

-- Find all descendants
SELECT * FROM categories WHERE path <@ 'Top.Electronics';

-- Find all ancestors
SELECT * FROM categories WHERE path @> 'Top.Electronics.Computers';

-- Create index
CREATE INDEX idx_categories_path ON categories USING GIST(path);

-- ============================================
-- 6. POSTGIS (Geographic Data)
-- ============================================

CREATE EXTENSION IF NOT EXISTS postgis;

CREATE TABLE stores (
    store_id SERIAL PRIMARY KEY,
    store_name VARCHAR(100),
    location GEOGRAPHY(POINT, 4326)
);

-- Insert location (longitude, latitude)
INSERT INTO stores (store_name, location)
VALUES ('Store 1', ST_GeogFromText('POINT(-73.9857 40.7484)'));

-- Find stores within 5km
SELECT 
    store_name,
    ST_Distance(location, ST_GeogFromText('POINT(-73.9857 40.7484)')) AS distance_meters
FROM stores
WHERE ST_DWithin(
    location,
    ST_GeogFromText('POINT(-73.9857 40.7484)'),
    5000  -- 5000 meters = 5km
)
ORDER BY distance_meters;

-- ============================================
-- 7. PG_STAT_STATEMENTS (Query Performance)
-- ============================================

CREATE EXTENSION IF NOT EXISTS pg_stat_statements;

-- View slow queries
SELECT 
    query,
    calls,
    total_exec_time,
    mean_exec_time,
    max_exec_time
FROM pg_stat_statements
ORDER BY mean_exec_time DESC
LIMIT 10;
```

---

# **16. ORM (Object-Relational Mapping)**

## **16.1 What is ORM?**

**ORM** maps database tables to programming language objects, allowing you to work with databases using object-oriented paradigms instead of writing raw SQL.

### **Benefits:**
- Write less SQL code
- Database agnostic (easier to switch databases)
- Built-in protection against SQL injection
- Automatic schema migrations
- Relationships handled automatically

### **Drawbacks:**
- Performance overhead
- Less control over queries
- Learning curve
- Complex queries can be harder

---

## **16.2 ORM Concepts**

```python name=orm_concepts.py
# ============================================
# SQLALCHEMY EXAMPLE (Python)
# ============================================

from sqlalchemy import create_engine, Column, Integer, String, ForeignKey, DateTime
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import relationship, sessionmaker
from datetime import datetime

Base = declarative_base()

# ============================================
# 1. MODELS (Map to Tables)
# ============================================

class User(Base):
    __tablename__ = 'users'
    
    user_id = Column(Integer, primary_key=True)
    username = Column(String(50), unique=True, nullable=False)
    email = Column(String(100), unique=True, nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)
    
    # Relationship
    orders = relationship('Order', back_populates='user')
    
    def __repr__(self):
        return f"<User(username='{self.username}')>"

class Order(Base):
    __tablename__ = 'orders'
    
    order_id = Column(Integer, primary_key=True)
    user_id = Column(Integer, ForeignKey('users.user_id'), nullable=False)
    total_amount = Column(Integer)
    order_date = Column(DateTime, default=datetime.utcnow)
    
    # Relationship
    user = relationship('User', back_populates='orders')
    
    def __repr__(self):
        return f"<Order(order_id={self.order_id}, amount={self.total_amount})>"

# ============================================
# 2. DATABASE CONNECTION
# ============================================

# Create engine
engine = create_engine('postgresql://user:password@localhost/dbname')

# Create tables
Base.metadata.create_all(engine)

# Create session
Session = sessionmaker(bind=engine)
session = Session()

# ============================================
# 3. CRUD OPERATIONS WITH ORM
# ============================================

# CREATE
new_user = User(username='john_doe', email='john@example.com')
session.add(new_user)
session.commit()

# Bulk insert
users = [
    User(username='alice', email='alice@example.com'),
    User(username='bob', email='bob@example.com'),
]
session.add_all(users)
session.commit()

# READ
# Get all users
users = session.query(User).all()

# Filter
user = session.query(User).filter(User.username == 'john_doe').first()

# Multiple conditions
users = session.query(User).filter(
    User.username.like('%john%'),
    User.email.endswith('@example.com')
).all()

# UPDATE
user = session.query(User).filter(User.username == 'john_doe').first()
user.email = 'newemail@example.com'
session.commit()

# Bulk update
session.query(User).filter(User.created_at < '2024-01-01').update({
    'is_active': False
})
session.commit()

# DELETE
user = session.query(User).filter(User.user_id == 1).first()
session.delete(user)
session.commit()

# Bulk delete
session.query(User).filter(User.is_active == False).delete()
session.commit()

# ============================================
# 4. RELATIONSHIPS (Joins)
# ============================================

# One-to-many: User has many orders
user = session.query(User).filter(User.username == 'john_doe').first()
print(user.orders)  # Automatically fetches orders

# Access related data
for order in user.orders:
    print(f"Order #{order.order_id}: ${order.total_amount}")

# Explicit join
results = session.query(User, Order).join(Order).filter(
    Order.total_amount > 100
).all()

# ============================================
# 5. LAZY VS EAGER LOADING
# ============================================

# Lazy loading (default): Fetches related data when accessed
user = session.query(User).first()
print(user.orders)  # Triggers separate query

# Eager loading: Fetches everything at once
from sqlalchemy.orm import joinedload

user = session.query(User).options(joinedload(User.orders)).first()
print(user.orders)  # No additional query!

# ============================================
# 6. AGGREGATIONS
# ============================================

from sqlalchemy import func

# Count
user_count = session.query(func.count(User.user_id)).scalar()

# Group by
results = session.query(
    User.user_id,
    func.count(Order.order_id).label('order_count')
).join(Order).group_by(User.user_id).all()

# Having
results = session.query(
    User.username,
    func.sum(Order.total_amount).label('total_spent')
).join(Order).group_by(User.username).having(
    func.sum(Order.total_amount) > 1000
).all()

# ============================================
# 7. TRANSACTIONS
# ============================================

try:
    # Start transaction (implicit)
    user = User(username='test', email='test@example.com')
    session.add(user)
    
    order = Order(user_id=user.user_id, total_amount=100)
    session.add(order)
    
    session.commit()  # Commit transaction
except Exception as e:
    session.rollback()  # Rollback on error
    print(f"Error: {e}")
finally:
    session.close()

# ============================================
# 8. RAW SQL (When needed)
# ============================================

# Execute raw SQL
result = session.execute("SELECT * FROM users WHERE username = :username", 
                          {'username': 'john_doe'})
for row in result:
    print(row)

# ============================================
# 9. MIGRATIONS (Alembic)
# ============================================

"""
Install: pip install alembic

Initialize:
alembic init alembic

Create migration:
alembic revision --autogenerate -m "Add users table"

Apply migration:
alembic upgrade head

Rollback:
alembic downgrade -1
"""
```

---

## **16.3 ORM Query Optimization**

```python name=orm_optimization.py
# ============================================
# QUERY OPTIMIZATION IN ORM
# ============================================

# ❌ N+1 Problem (BAD!)
users = session.query(User).all()
for user in users:
    print(user.orders)  # Separate query for EACH user!
# Total queries: 1 + N (where N = number of users)

# ✅ Eager Loading (GOOD!)
from sqlalchemy.orm import joinedload

users = session.query(User).options(joinedload(User.orders)).all()
for user in users:
    print(user.orders)  # No additional queries!
# Total queries: 1 (single join query)

# ============================================
# SELECT SPECIFIC COLUMNS (Don't use *)
# ============================================

# ❌ Load all columns
users = session.query(User).all()

# ✅ Load only needed columns
users = session.query(User.user_id, User.username).all()

# ============================================
# PAGINATION
# ============================================

# ✅ Efficient pagination
page = 1
page_size = 20
offset = (page - 1) * page_size

users = session.query(User).limit(page_size).offset(offset).all()

# ============================================
# BULK OPERATIONS
# ============================================

# ❌ Slow (individual updates)
users = session.query(User).all()
for user in users:
    user.is_active = False
session.commit()

# ✅ Fast (bulk update)
session.query(User).update({'is_active': False})
session.commit()

# ============================================
# INDEXES IN ORM
# ============================================

from sqlalchemy import Index

class User(Base):
    __tablename__ = 'users'
    
    user_id = Column(Integer, primary_key=True)
    username = Column(String(50), index=True)  # Single column index
    email = Column(String(100))
    created_at = Column(DateTime)
    
    # Composite index
    __table_args__ = (
        Index('idx_username_email', 'username', 'email'),
    )

# ============================================
# EXPLAIN QUERIES
# ============================================

from sqlalchemy import text

query = session.query(User).filter(User.username == 'john')
print(str(query))  # See generated SQL

# Explain in PostgreSQL
result = session.execute(text(f"EXPLAIN {query}"))
for row in result:
    print(row)
```

---

## **Summary: SQL Mastery Checklist** ✅

### **Fundamentals:**
- [x] Database basics & RDBMS concepts
- [x] Tables, rows, columns, schemas
- [x] Primary keys & foreign keys
- [x] Constraints (NOT NULL, UNIQUE, CHECK, DEFAULT)
- [x] Data types (INT, VARCHAR, TEXT, DATE, etc.)

### **Core Operations:**
- [x] CRUD (INSERT, SELECT, UPDATE, DELETE)
- [x] WHERE, AND, OR, IN, BETWEEN, LIKE
- [x] ORDER BY, LIMIT, OFFSET
- [x] Aggregate functions (COUNT, SUM, AVG, MIN, MAX)
- [x] GROUP BY, HAVING

### **Advanced Queries:**
- [x] All JOIN types (INNER, LEFT, RIGHT, FULL, CROSS, SELF)
- [x] Subqueries (SELECT, WHERE, FROM)
- [x] Correlated subqueries
- [x] Common Table Expressions (CTEs)
- [x] Recursive CTEs
- [x] Window functions

### **Performance:**
- [x] Indexes (B-Tree, GIN, GiST, Hash)
- [x] Composite indexes
- [x] Partial indexes
- [x] EXPLAIN ANALYZE
- [x] Query optimization

### **Transactions:**
- [x] ACID properties
- [x] Isolation levels
- [x] BEGIN, COMMIT, ROLLBACK
- [x] SAVEPOINT

### **Advanced Features:**
- [x] Stored procedures & functions
- [x] Triggers
- [x] Views & materialized views
- [x] Normalization (1NF, 2NF, 3NF)

### **Security:**
- [x] SQL injection prevention
- [x] Parameterized queries
- [x] Password hashing
- [x] Row-level security

### **PostgreSQL-Specific:**
- [x] JSONB, Arrays, UUIDs, ENUMs
- [x] Full-text search
- [x] Extensions
- [x] Partitioning

### **ORM:**
- [x] Models & relationships
- [x] Migrations
- [x] Lazy vs eager loading
- [x] Query optimization

---

**🎉 Congratulations!** You now have comprehensive notes covering SQL from fundamentals to advanced PostgreSQL features. Practice these concepts with real-world projects to solidify your mastery! 🚀

## @meet41

is there any left other than topics which i had mentioned? also can you only provide reference link or text data as theory for each topics to get better understanding also.

## Copilot

# **Additional SQL/PostgreSQL Topics & Comprehensive Learning Resources**

## **Missing/Additional Topics Not Covered:**

```sql name=additional_topics_overview.sql
-- ============================================
-- ADDITIONAL IMPORTANT TOPICS
-- ============================================

/*
1. Database Design & ER Diagrams
2. Backup & Restore Strategies
3. Replication & High Availability
4. Connection Pooling
5. Concurrency Control & Locking
6. Database Monitoring & Maintenance
7. Performance Tuning Deep Dive
8. Data Import/Export (COPY, pg_dump, pg_restore)
9. Query Execution Plans (Deep Dive)
10. Constraints Advanced (EXCLUDE, DEFERRABLE)
11. Foreign Data Wrappers (FDW)
12. Logical Replication
13. Sharding Strategies
14. OLTP vs OLAP
15. Data Warehousing Basics
16. Temporal Tables (System Versioning)
17. Database Migration Strategies
18. Testing Strategies for Databases
19. CAP Theorem & Distributed Databases
20. PostgreSQL Configuration Tuning
*/
```

---

# **📚 Comprehensive Reference Links & Theory**

## **1. Database Basics (SQL)**

### **Theory:**
A relational database stores data in tables (relations) with rows and columns. The relational model was introduced by E.F. Codd in 1970. Key concepts include:
- **Tables (Relations)**: Structured data storage
- **Tuples (Rows)**: Individual records
- **Attributes (Columns)**: Properties of entities
- **Schema**: Structure definition
- **Relationships**: Connections between tables

### **Reference Links:**
- **Official PostgreSQL Documentation**: https://www.postgresql.org/docs/current/tutorial-concepts.html
- **SQL Tutorial (W3Schools)**: https://www.w3schools.com/sql/
- **Database Systems Concepts**: https://db-book.com/ (Classic textbook)
- **SQLBolt (Interactive)**: https://sqlbolt.com/
- **Khan Academy SQL**: https://www.khanacademy.org/computing/computer-programming/sql
- **Relational Model Basics**: https://en.wikipedia.org/wiki/Relational_model

---

## **2. Keys & Constraints**

### **Theory:**
- **Primary Key**: Uniquely identifies each record. Properties: UNIQUE, NOT NULL, only one per table
- **Foreign Key**: Establishes relationships between tables, maintains referential integrity
- **Constraints**: Rules enforced on data (NOT NULL, UNIQUE, CHECK, DEFAULT, EXCLUSION)
- **Referential Integrity**: Ensures foreign keys point to existing records

### **Reference Links:**
- **PostgreSQL Constraints**: https://www.postgresql.org/docs/current/ddl-constraints.html
- **Primary Keys Explained**: https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-PRIMARY-KEYS
- **Foreign Keys Deep Dive**: https://www.postgresql.org/docs/current/ddl-constraints.html#DDL-CONSTRAINTS-FK
- **Constraint Types**: https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-constraints/
- **Cascading Actions**: https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-foreign-key/

---

## **3. SQL Data Types**

### **Theory:**
Choosing correct data types is crucial for:
- Storage efficiency
- Query performance
- Data integrity
- Application logic

Key considerations:
- Use smallest data type that fits requirements
- DECIMAL for money (exact precision)
- TIMESTAMPTZ for timezone-aware dates
- TEXT for variable-length strings
- JSONB for semi-structured data

### **Reference Links:**
- **PostgreSQL Data Types (Official)**: https://www.postgresql.org/docs/current/datatype.html
- **Numeric Types**: https://www.postgresql.org/docs/current/datatype-numeric.html
- **Character Types**: https://www.postgresql.org/docs/current/datatype-character.html
- **Date/Time Types**: https://www.postgresql.org/docs/current/datatype-datetime.html
- **JSON Types**: https://www.postgresql.org/docs/current/datatype-json.html
- **Type Conversion**: https://www.postgresql.org/docs/current/typeconv.html
- **Data Type Best Practices**: https://wiki.postgresql.org/wiki/Don%27t_Do_This#Don.27t_use_varchar.28n.29_by_default

---

## **4. CRUD Operations**

### **Theory:**
CRUD represents the four basic database operations:
- **Create (INSERT)**: Add new records
- **Read (SELECT)**: Retrieve data
- **Update (UPDATE)**: Modify existing records
- **Delete (DELETE)**: Remove records

Best practices:
- Always use WHERE in UPDATE/DELETE
- Use RETURNING for feedback
- Consider soft deletes for audit trails
- Use transactions for related operations

### **Reference Links:**
- **INSERT Documentation**: https://www.postgresql.org/docs/current/sql-insert.html
- **SELECT Documentation**: https://www.postgresql.org/docs/current/sql-select.html
- **UPDATE Documentation**: https://www.postgresql.org/docs/current/sql-update.html
- **DELETE Documentation**: https://www.postgresql.org/docs/current/sql-delete.html
- **DML Tutorial**: https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-insert/
- **INSERT ... ON CONFLICT (UPSERT)**: https://www.postgresql.org/docs/current/sql-insert.html#SQL-ON-CONFLICT

---

## **5. Query Filtering & Sorting**

### **Theory:**
- **WHERE Clause**: Filters rows before grouping
- **HAVING Clause**: Filters groups after aggregation
- **ORDER BY**: Sorts results (ASC/DESC)
- **LIMIT/OFFSET**: Pagination support
- **Pattern Matching**: LIKE, ILIKE, regex (~, ~*)

Performance tips:
- Index columns in WHERE clauses
- Avoid functions on indexed columns in WHERE
- Use LIMIT for large result sets

### **Reference Links:**
- **WHERE Clause**: https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-WHERE
- **Pattern Matching**: https://www.postgresql.org/docs/current/functions-matching.html
- **ORDER BY**: https://www.postgresql.org/docs/current/queries-order.html
- **LIMIT/OFFSET**: https://www.postgresql.org/docs/current/queries-limit.html
- **Query Tutorial**: https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-where/

---

## **6. Aggregate Functions**

### **Theory:**
Aggregate functions perform calculations on sets of rows:
- **COUNT()**: Count rows
- **SUM()**: Total of values
- **AVG()**: Average value
- **MIN()/MAX()**: Minimum/maximum value
- **GROUP BY**: Groups rows for aggregation
- **HAVING**: Filters aggregated results

### **Reference Links:**
- **Aggregate Functions**: https://www.postgresql.org/docs/current/functions-aggregate.html
- **GROUP BY**: https://www.postgresql.org/docs/current/queries-table-expressions.html#QUERIES-GROUP
- **HAVING Clause**: https://www.postgresql.org/docs/current/tutorial-agg.html
- **FILTER Clause**: https://www.postgresql.org/docs/current/sql-expressions.html#SYNTAX-AGGREGATES
- **String Aggregation**: https://www.postgresql.org/docs/current/functions-aggregate.html#FUNCTIONS-AGGREGATE-STATISTICS-TABLE

---

## **7. Joins (Very Important)**

### **Theory:**
Joins combine rows from multiple tables:

**Join Types:**
- **INNER JOIN**: Returns matching rows only
- **LEFT JOIN**: All left rows + matching right rows
- **RIGHT JOIN**: All right rows + matching left rows
- **FULL OUTER JOIN**: All rows from both tables
- **CROSS JOIN**: Cartesian product
- **SELF JOIN**: Table joined to itself

**Relationship Types:**
- **One-to-One**: Single record relates to single record
- **One-to-Many**: Single record relates to multiple records
- **Many-to-Many**: Multiple records relate to multiple records (requires junction table)

### **Reference Links:**
- **PostgreSQL Joins**: https://www.postgresql.org/docs/current/tutorial-join.html
- **Join Visual Explanation**: https://blog.codinghorror.com/a-visual-explanation-of-sql-joins/
- **Advanced Joins**: https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-joins/
- **Self Joins**: https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-self-join/
- **Join Performance**: https://www.postgresql.org/docs/current/explicit-joins.html

---

## **8. Subqueries & Advanced Queries**

### **Theory:**
**Subquery**: Query nested inside another query

**Types:**
- **Scalar subquery**: Returns single value
- **Row subquery**: Returns single row
- **Table subquery**: Returns multiple rows/columns
- **Correlated subquery**: References outer query

**CTE (Common Table Expression):**
- More readable than nested subqueries
- Can be recursive
- Reusable within query

### **Reference Links:**
- **Subqueries**: https://www.postgresql.org/docs/current/functions-subquery.html
- **WITH Queries (CTEs)**: https://www.postgresql.org/docs/current/queries-with.html
- **Recursive CTEs**: https://www.postgresql.org/docs/current/queries-with.html#QUERIES-WITH-RECURSIVE
- **Subquery Tutorial**: https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-subquery/
- **CTE Examples**: https://www.postgresqltutorial.com/postgresql-tutorial/postgresql-cte/

---

## **9. Indexes & Performance Basics**

### **Theory:**
**Index**: Database object that speeds up data retrieval

**How indexes work:**
- B-tree structure (default): Binary search, O(log n)
- Speeds up SELECT, WHERE, JOIN, ORDER BY
- Slows down INSERT, UPDATE, DELETE (index must be updated)

**When to use:**
- Columns in WHERE clauses
- Foreign keys
- Columns in JOIN conditions
- Columns in ORDER BY

**When NOT to use:**
- Small tables (< 1000 rows)
- Columns rarely queried
- High write frequency tables

### **Reference Links:**
- **PostgreSQL Indexes**: https://www.postgresql.org/docs/current/indexes.html
- **Index Types**: https://www.postgresql.org/docs/current/indexes-types.html
- **EXPLAIN Documentation**: https://www.postgresql.org/docs/current/sql-explain.html
- **Query Performance Tips**: https://wiki.postgresql.org/wiki/Performance_Optimization
- **Index Maintenance**: https://www.postgresql.org/docs/current/routine-reindex.html
- **Use The Index, Luke!**: https://use-the-index-luke.com/ (Excellent resource)

---

## **10. Transactions & ACID Properties**

### **Theory:**
**Transaction**: Sequence of operations as single logical unit

**ACID Properties:**
- **Atomicity**: All or nothing
- **Consistency**: Valid state to valid state
- **Isolation**: Concurrent transactions don't interfere
- **Durability**: Committed changes are permanent

**Isolation Levels:**
1. **READ UNCOMMITTED**: Lowest isolation (not in PostgreSQL)
2. **READ COMMITTED**: Default, prevents dirty reads
3. **REPEATABLE READ**: Prevents non-repeatable reads
4. **SERIALIZABLE**: Highest isolation, full serializability

### **Reference Links:**
- **Transaction Control**: https://www.postgresql.org/docs/current/tutorial-transactions.html
- **Isolation Levels**: https://www.postgresql.org/docs/current/transaction-iso.html
- **ACID Properties**: https://en.wikipedia.org/wiki/ACID
- **Concurrency Control**: https://www.postgresql.org/docs/current/mvcc.html
- **Deadlocks**: https://www.postgresql.org/docs/current/explicit-locking.html
- **Transaction Best Practices**: https://wiki.postgresql.org/wiki/Transactional_DDL_in_PostgreSQL:_A_Competitive_Analysis

---

## **11. Stored Procedures & Functions**

### **Theory:**
**Functions**: Return value, can be used in SELECT
**Procedures**: Don't return value, can manage transactions

**Benefits:**
- Code reusability
- Reduced network traffic
- Enhanced security (controlled data access)
- Business logic in database

**Triggers**: Automatically execute on INSERT/UPDATE/DELETE

### **Reference Links:**
- **PL/pgSQL Language**: https://www.postgresql.org/docs/current/plpgsql.html
- **Functions**: https://www.postgresql.org/docs/current/sql-createfunction.html
- **Procedures**: https://www.postgresql.org/docs/current/sql-createprocedure.html
- **Triggers**: https://www.postgresql.org/docs/current/trigger-definition.html
- **PL/pgSQL Tutorial**: https://www.postgresqltutorial.com/postgresql-plpgsql/
- **Trigger Examples**: https://www.postgresqltutorial.com/postgresql-triggers/

---

## **12. Views**

### **Theory:**
**View**: Virtual table based on query result

**Types:**
- **Simple View**: Based on single table, updatable
- **Complex View**: Joins/aggregations, usually read-only
- **Materialized View**: Stores query result physically, must be refreshed

**Use Cases:**
- Simplify complex queries
- Security (restrict column/row access)
- Abstraction layer
- Backward compatibility

### **Reference Links:**
- **Views**: https://www.postgresql.org/docs/current/sql-createview.html
- **Materialized Views**: https://www.postgresql.org/docs/current/sql-creatematerializedview.html
- **Updatable Views**: https://www.postgresql.org/docs/current/sql-createview.html#SQL-CREATEVIEW-UPDATABLE-VIEWS
- **View Tutorial**: https://www.postgresqltutorial.com/postgresql-views/
- **Materialized View Guide**: https://www.postgresqltutorial.com/postgresql-views/postgresql-materialized-views/

---

## **13. Database Normalization**

### **Theory:**
**Normalization**: Process of organizing data to reduce redundancy

**Normal Forms:**

**1NF (First Normal Form):**
- Atomic values (no arrays/lists in columns)
- Each column has unique name
- Order doesn't matter

**2NF (Second Normal Form):**
- Must be in 1NF
- No partial dependencies (non-key columns depend on entire key)

**3NF (Third Normal Form):**
- Must be in 2NF
- No transitive dependencies (non-key columns don't depend on other non-key columns)

**BCNF (Boyce-Codd Normal Form):**
- Stricter version of 3NF

**When to Denormalize:**
- Performance critical
- Read-heavy applications
- Data rarely changes

### **Reference Links:**
- **Database Normalization**: https://en.wikipedia.org/wiki/Database_normalization
- **Normal Forms Tutorial**: https://www.studytonight.com/dbms/database-normalization.php
- **Normalization Examples**: https://www.guru99.com/database-normalization.html
- **Denormalization**: https://en.wikipedia.org/wiki/Denormalization
- **When to Denormalize**: https://www.sqlshack.com/what-is-denormalization-in-sql-server/

---

## **14. SQL Injection & Security**

### **Theory:**
**SQL Injection**: Malicious SQL code inserted into queries

**Attack Types:**
- Authentication bypass
- Data extraction (UNION attacks)
- Data modification/deletion
- Command execution

**Prevention Methods:**
1. **Parameterized Queries** (Best)
2. **Prepared Statements**
3. **ORM** (automatic escaping)
4. **Input Validation**
5. **Least Privilege Principle**
6. **Web Application Firewall**

**Password Security:**
- Never store plain text
- Use bcrypt, scrypt, or Argon2
- Salt passwords
- Implement account lockout

### **Reference Links:**
- **SQL Injection (OWASP)**: https://owasp.org/www-community/attacks/SQL_Injection
- **PostgreSQL Security**: https://www.postgresql.org/docs/current/security.html
- **SQL Injection Prevention**: https://cheatsheetseries.owasp.org/cheatsheets/SQL_Injection_Prevention_Cheat_Sheet.html
- **Row-Level Security**: https://www.postgresql.org/docs/current/ddl-rowsecurity.html
- **pgcrypto Extension**: https://www.postgresql.org/docs/current/pgcrypto.html
- **Database Security Best Practices**: https://www.postgresql.org/docs/current/sql-security.html

---

## **15. PostgreSQL Advanced Features**

### **Theory:**

**JSONB:**
- Binary JSON format
- Indexable with GIN
- Supports operators (@>, ?, ->, ->>)
- Ideal for semi-structured data

**Arrays:**
- Store multiple values in single column
- Supports GIN indexing
- Use functions: array_agg, unnest, array_length

**Full-Text Search:**
- tsvector, tsquery types
- Language-specific stemming
- Ranking and highlighting
- GIN/GiST indexes

**Extensions:**
- Modular functionality
- uuid-ossp, pgcrypto, pg_trgm, PostGIS, etc.

### **Reference Links:**
- **JSON Types**: https://www.postgresql.org/docs/current/datatype-json.html
- **JSON Functions**: https://www.postgresql.org/docs/current/functions-json.html
- **Array Types**: https://www.postgresql.org/docs/current/arrays.html
- **Full-Text Search**: https://www.postgresql.org/docs/current/textsearch.html
- **Extensions**: https://www.postgresql.org/docs/current/contrib.html
- **Window Functions**: https://www.postgresql.org/docs/current/tutorial-window.html
- **Partitioning**: https://www.postgresql.org/docs/current/ddl-partitioning.html
- **PostgreSQL Wiki**: https://wiki.postgresql.org/wiki/Main_Page

---

## **16. ORM (Object-Relational Mapping)**

### **Theory:**
**ORM**: Maps database tables to programming objects

**Concepts:**
- **Models**: Classes representing tables
- **Migrations**: Version control for schema
- **Relationships**: one-to-one, one-to-many, many-to-many
- **Lazy Loading**: Fetch related data on access
- **Eager Loading**: Fetch all data at once (prevents N+1)

**Popular ORMs:**
- **Python**: SQLAlchemy, Django ORM, Peewee
- **Node.js**: Sequelize, TypeORM, Prisma
- **Java**: Hibernate, JPA
- **PHP**: Doctrine, Eloquent
- **Ruby**: ActiveRecord

### **Reference Links:**
- **SQLAlchemy Documentation**: https://docs.sqlalchemy.org/
- **Django ORM**: https://docs.djangoproject.com/en/stable/topics/db/
- **Sequelize (Node.js)**: https://sequelize.org/docs/
- **TypeORM**: https://typeorm.io/
- **Prisma**: https://www.prisma.io/docs/
- **ORM Patterns**: https://en.wikipedia.org/wiki/Object%E2%80%93relational_mapping
- **N+1 Query Problem**: https://stackoverflow.com/questions/97197/what-is-the-n1-selects-problem

---

# **📖 Additional Topics Not Originally Covered**

## **17. Database Design & ER Diagrams**

### **Theory:**
**Entity-Relationship Modeling:**
- **Entities**: Objects (tables)
- **Attributes**: Properties (columns)
- **Relationships**: Connections between entities
- **Cardinality**: One-to-one, one-to-many, many-to-many

**Design Process:**
1. Requirements analysis
2. Conceptual design (ER diagram)
3. Logical design (normalize)
4. Physical design (indexes, partitions)

### **Reference Links:**
- **ER Diagrams**: https://www.lucidchart.com/pages/er-diagrams
- **Database Design Tutorial**: https://www.tutorialspoint.com/dbms/dbms_data_models.htm
- **Design Patterns**: https://en.wikipedia.org/wiki/Database_design
- **Cardinality**: https://www.guru99.com/er-modeling.html
- **Tools**: https://dbdiagram.io/ (ER diagram tool)

---

## **18. Backup & Restore**

### **Theory:**
**Backup Types:**
- **Logical Backup**: SQL dump (pg_dump)
- **Physical Backup**: File-level copy
- **Incremental Backup**: Only changes
- **Point-in-Time Recovery (PITR)**: Restore to specific time

**Strategies:**
- Full backup daily
- Incremental/differential backups hourly
- Test restores regularly
- Off-site storage

### **Reference Links:**
- **PostgreSQL Backup**: https://www.postgresql.org/docs/current/backup.html
- **pg_dump**: https://www.postgresql.org/docs/current/app-pgdump.html
- **pg_restore**: https://www.postgresql.org/docs/current/app-pgrestore.html
- **PITR**: https://www.postgresql.org/docs/current/continuous-archiving.html
- **Backup Best Practices**: https://wiki.postgresql.org/wiki/Backup_and_Recovery

---

## **19. Replication & High Availability**

### **Theory:**
**Replication Types:**
- **Streaming Replication**: Continuous WAL streaming
- **Logical Replication**: Replicate specific tables/databases
- **Synchronous Replication**: Wait for replica confirmation
- **Asynchronous Replication**: Don't wait (faster, less safe)

**High Availability:**
- **Primary-Replica**: Read scalability
- **Failover**: Automatic promotion of replica
- **Load Balancing**: Distribute read queries

### **Reference Links:**
- **Replication**: https://www.postgresql.org/docs/current/replication.html
- **Streaming Replication**: https://www.postgresql.org/docs/current/warm-standby.html
- **Logical Replication**: https://www.postgresql.org/docs/current/logical-replication.html
- **High Availability**: https://www.postgresql.org/docs/current/high-availability.html
- **Patroni (HA Tool)**: https://github.com/zalando/patroni

---

## **20. Connection Pooling**

### **Theory:**
**Connection Pool**: Reusable database connections

**Benefits:**
- Reduced connection overhead
- Better resource utilization
- Handle connection spikes

**Popular Poolers:**
- **PgBouncer**: Lightweight pooler
- **Pgpool-II**: Advanced features
- **Application-level**: SQLAlchemy, HikariCP

### **Reference Links:**
- **Connection Pooling**: https://en.wikipedia.org/wiki/Connection_pool
- **PgBouncer**: https://www.pgbouncer.org/
- **Pgpool-II**: https://www.pgpool.net/
- **PostgreSQL Connection Limits**: https://www.postgresql.org/docs/current/runtime-config-connection.html

---

## **21. Performance Tuning Deep Dive**

### **Theory:**
**Performance Factors:**
- Query optimization
- Index strategy
- Hardware (CPU, RAM, disk)
- Configuration parameters
- Connection pooling
- Caching strategies

**Key Configurations:**
- `shared_buffers`: Memory for caching
- `work_mem`: Memory per operation
- `maintenance_work_mem`: For VACUUM, CREATE INDEX
- `effective_cache_size`: OS cache estimate

### **Reference Links:**
- **Performance Tips**: https://wiki.postgresql.org/wiki/Performance_Optimization
- **Configuration Tuning**: https://www.postgresql.org/docs/current/runtime-config.html
- **PGTune**: https://pgtune.leopard.in.ua/ (Config generator)
- **EXPLAIN Tutorial**: https://www.postgresql.org/docs/current/using-explain.html
- **Slow Query Analysis**: https://www.postgresql.org/docs/current/pgstatstatements.html

---

## **22. Data Import/Export**

### **Theory:**
**COPY Command**: Fast bulk data transfer
- **COPY TO**: Export to file
- **COPY FROM**: Import from file
- Formats: CSV, text, binary

**Other Methods:**
- pg_dump/pg_restore
- INSERT statements
- ETL tools (Apache Airflow, Talend)

### **Reference Links:**
- **COPY Command**: https://www.postgresql.org/docs/current/sql-copy.html
- **Import/Export Guide**: https://www.postgresqltutorial.com/postgresql-tutorial/import-csv-file-into-posgresql-table/
- **Large Data Loading**: https://www.postgresql.org/docs/current/populate.html

---

## **23. Concurrency Control & Locking**

### **Theory:**
**Locks Prevent:**
- Dirty reads
- Non-repeatable reads
- Phantom reads
- Lost updates

**Lock Types:**
- **Table Locks**: ACCESS SHARE, ROW SHARE, ROW EXCLUSIVE, etc.
- **Row Locks**: FOR UPDATE, FOR SHARE
- **Advisory Locks**: Application-defined

**MVCC (Multi-Version Concurrency Control):**
- PostgreSQL's concurrency model
- Each transaction sees snapshot
- Minimal blocking

### **Reference Links:**
- **Locking**: https://www.postgresql.org/docs/current/explicit-locking.html
- **MVCC**: https://www.postgresql.org/docs/current/mvcc-intro.html
- **Lock Monitoring**: https://wiki.postgresql.org/wiki/Lock_Monitoring
- **Deadlock Detection**: https://www.postgresql.org/docs/current/runtime-config-locks.html

---

## **24. Monitoring & Maintenance**

### **Theory:**
**Monitoring:**
- Query performance (pg_stat_statements)
- Index usage (pg_stat_user_indexes)
- Table bloat
- Connection counts
- Cache hit ratios

**Maintenance:**
- **VACUUM**: Remove dead tuples
- **ANALYZE**: Update statistics
- **REINDEX**: Rebuild indexes
- **Autovacuum**: Automatic maintenance

### **Reference Links:**
- **Monitoring**: https://www.postgresql.org/docs/current/monitoring.html
- **Statistics Views**: https://www.postgresql.org/docs/current/monitoring-stats.html
- **VACUUM**: https://www.postgresql.org/docs/current/sql-vacuum.html
- **Routine Maintenance**: https://www.postgresql.org/docs/current/maintenance.html
- **pg_stat_statements**: https://www.postgresql.org/docs/current/pgstatstatements.html
- **pgAdmin**: https://www.pgadmin.org/ (GUI tool)

---

## **25. OLTP vs OLAP**

### **Theory:**
**OLTP (Online Transaction Processing):**
- Many small transactions
- Normalized schema
- Row-oriented storage
- PostgreSQL, MySQL

**OLAP (Online Analytical Processing):**
- Complex analytical queries
- Denormalized (star/snowflake schema)
- Column-oriented storage
- Data warehouses (Redshift, BigQuery)

### **Reference Links:**
- **OLTP vs OLAP**: https://www.guru99.com/oltp-vs-olap.html
- **Data Warehousing**: https://en.wikipedia.org/wiki/Data_warehouse
- **Star Schema**: https://en.wikipedia.org/wiki/Star_schema

---

# **🎯 Best Learning Path**

## **Beginner Level (Weeks 1-2):**
1. Database Basics
2. CRUD Operations
3. Basic Filtering (WHERE, ORDER BY)
4. Simple Joins (INNER, LEFT)

## **Intermediate Level (Weeks 3-4):**
1. Aggregate Functions & GROUP BY
2. All Join Types
3. Subqueries
4. Indexes Basics
5. Transactions

## **Advanced Level (Weeks 5-8):**
1. CTEs & Window Functions
2. Stored Procedures & Triggers
3. Performance Optimization
4. Normalization
5. Security & SQL Injection
6. PostgreSQL Advanced Features

## **Expert Level (Weeks 9-12):**
1. Query Execution Plans
2. Replication & HA
3. Performance Tuning
4. Database Design
5. ORM Mastery

---

# **🔗 Additional Curated Resources**

## **Interactive Learning:**
- **PostgreSQL Exercises**: https://pgexercises.com/
- **SQLZoo**: https://sqlzoo.net/
- **LeetCode Database**: https://leetcode.com/problemset/database/
- **HackerRank SQL**: https://www.hackerrank.com/domains/sql
- **Mode Analytics SQL Tutorial**: https://mode.com/sql-tutorial/

## **Books:**
- **"PostgreSQL: Up and Running"** by Regina Obe & Leo Hsu
- **"SQL Performance Explained"** by Markus Winand
- **"The Art of PostgreSQL"** by Dimitri Fontaine
- **"Database Systems: The Complete Book"** by Hector Garcia-Molina

## **Video Courses:**
- **freeCodeCamp PostgreSQL**: https://www.youtube.com/watch?v=qw--VYLpxG4
- **Udemy - Complete SQL Bootcamp**: https://www.udemy.com/course/the-complete-sql-bootcamp/
- **Coursera - SQL for Data Science**: https://www.coursera.org/learn/sql-for-data-science

## **Community & Forums:**
- **PostgreSQL Mailing Lists**: https://www.postgresql.org/list/
- **Stack Overflow**: https://stackoverflow.com/questions/tagged/postgresql
- **Reddit r/PostgreSQL**: https://www.reddit.com/r/PostgreSQL/
- **PostgreSQL Slack**: https://postgres-slack.herokuapp.com/

## **Blogs & Newsletters:**
- **PostgreSQL Planet**: https://planet.postgresql.org/
- **Cybertec PostgreSQL Blog**: https://www.cybertec-postgresql.com/en/blog/
- **2ndQuadrant Blog**: https://www.2ndquadrant.com/en/blog/
- **Postgres Weekly**: https://postgresweekly.com/

---

**🎓 Study Tips:**
1. **Practice Daily**: Write 10-15 queries every day
2. **Read Documentation**: PostgreSQL docs are excellent
3. **Build Projects**: Create real applications
4. **Explain to Others**: Teaching solidifies knowledge
5. **Join Communities**: Learn from experienced developers
6. **Analyze Real Queries**: Use EXPLAIN ANALYZE
7. **Set Up Local Database**: Experiment freely
8. **Contribute**: Answer questions on Stack Overflow

Good luck on your SQL mastery journey! 🚀
