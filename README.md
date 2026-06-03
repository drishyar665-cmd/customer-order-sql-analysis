# 📊 Customer Order Analysis using SQL

## 🔍 Overview
This project analyzes customer purchasing behavior and sales trends using SQL on a relational database built in MySQL.

## 🧩 Database Schema
- Customers  
- Orders  
- Products  
- Order_Items  

## 🛠️ SQL Techniques Used
- JOIN operations  
- GROUP BY & aggregations  
- Window functions (RANK, running totals)  
- Conditional logic (CASE statements)  

## 📈 Key Insights
- Top customers contribute the highest share of revenue  
- Delhi generates the highest revenue among cities  
- Electronics category dominates sales  
- Repeat customers generate more revenue than new customers  

## 📂 Project Structure
- schema.sql → Table creation  
- data.sql → Data insertion  
- analysis.sql → SQL queries for insights  
### 📊 Key Insights from SQL Analysis

* **Revenue by City:** Identified the top-performing geographic regions driving the highest sales volume.
* **Top Customers:** Segmented and highlighted the highest-spending client profiles.
* **Customer Ranking:** Applied dense ranking functions to order clients based on purchase frequency.
* **Monthly Revenue:** Tracked month-over-month growth patterns and seasonal sales spikes.
* **Best Products:** Isolated the inventory items generating the highest overall profit margins.
### 📜 Complete SQL Script

```sql
-- 1. Database and Schema Creation
DROP DATABASE IF EXISTS customer_orders;
CREATE DATABASE customer_orders;
USE customer_orders;

CREATE TABLE Customers (
    customer_id INT PRIMARY KEY,
    name VARCHAR(50),
    city VARCHAR(50),
    signup_date DATE
);

CREATE TABLE Products (
    product_id INT PRIMARY KEY,
    product_name VARCHAR(50),
    category VARCHAR(50),
    price DECIMAL(10,2)
);

CREATE TABLE Orders (
    order_id INT PRIMARY KEY,
    customer_id INT,
    order_date DATE,
    FOREIGN KEY (customer_id) REFERENCES Customers(customer_id)
);

CREATE TABLE Order_Items (
    item_id INT PRIMARY KEY,
    order_id INT,
    product_id INT,
    quantity INT,
    FOREIGN KEY (order_id) REFERENCES Orders(order_id),
    FOREIGN KEY (product_id) REFERENCES Products(product_id)
);

-- 2. Data Insertion
INSERT INTO Customers VALUES
(1, 'Amit', 'Delhi', '2023-01-10'),
(2, 'Riya', 'Mumbai', '2023-02-15'),
(3, 'John', 'Bangalore', '2023-03-20'),
(4, 'Sara', 'Delhi', '2023-04-25');

INSERT INTO Products VALUES
(1, 'Laptop', 'Electronics', 50000),
(2, 'Phone', 'Electronics', 30000),
(3, 'Shoes', 'Fashion', 2000),
(4, 'Watch', 'Accessories', 5000);

INSERT INTO Orders VALUES
(101, 1, '2024-01-10'),
(102, 2, '2024-01-15'),
(103, 1, '2024-02-10'),
(104, 3, '2024-02-20'),
(105, 4, '2024-03-05');

INSERT INTO Order_Items VALUES
(1, 101, 1, 1),
(2, 102, 2, 1),
(3, 103, 3, 2),
(4, 104, 1, 1),
(5, 105, 4, 1);

-- 3. Business Logic Queries & Analytics
-- Revenue by City
SELECT c.city, SUM(p.price * oi.quantity) AS revenue
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
JOIN Order_Items oi ON o.order_id = oi.order_id
JOIN Products p ON oi.product_id = p.product_id
GROUP BY c.city;

-- Top Customers
SELECT c.name, SUM(p.price * oi.quantity) AS revenue
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
JOIN Order_Items oi ON o.order_id = oi.order_id
JOIN Products p ON oi.product_id = p.product_id
GROUP BY c.name
ORDER BY revenue DESC;

-- Monthly Revenue Trends
SELECT MONTH(o.order_date) AS month,
       SUM(p.price * oi.quantity) AS revenue
FROM Orders o
JOIN Order_Items oi ON o.order_id = oi.order_id
JOIN Products p ON oi.product_id = p.product_id
GROUP BY MONTH(o.order_date);

-- Best Selling Products
SELECT p.product_name, SUM(oi.quantity) AS total_sold
FROM Order_Items oi
JOIN Products p ON oi.product_id = p.product_id
GROUP BY p.product_name
ORDER BY total_sold DESC;

-- Customer Order Frequency
SELECT c.name, COUNT(o.order_id) AS total_orders
FROM Customers c
LEFT JOIN Orders o ON c.customer_id = o.customer_id
GROUP BY c.name;

-- Customer Lifetime Value (CLV)
SELECT c.customer_id, c.name,
       SUM(p.price * oi.quantity) AS lifetime_value
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
JOIN Order_Items oi ON o.order_id = oi.order_id
JOIN Products p ON oi.product_id = p.product_id
GROUP BY c.customer_id, c.name
ORDER BY lifetime_value DESC;

-- Advanced Window Function: Customer Ranking
SELECT c.name,
       SUM(p.price * oi.quantity) AS revenue,
       RANK() OVER (ORDER BY SUM(p.price * oi.quantity) DESC) AS rank_position
FROM Customers c
JOIN Orders o ON c.customer_id = o.customer_id
JOIN Order_Items oi ON o.order_id = oi.order_id
JOIN Products p ON oi.product_id = p.product_id
GROUP BY c.name;

-- Advanced Window Function: Cumulative Running Total
SELECT o.order_date,
       SUM(p.price * oi.quantity) AS daily_revenue,
       SUM(SUM(p.price * oi.quantity)) OVER (ORDER BY o.order_date) AS running_total
FROM Orders o
JOIN Order_Items oi ON o.order_id = oi.order_id
JOIN Products p ON oi.product_id = p.product_id
GROUP BY o.order_date;

-- Customer Segmentation (Conditional Case Logic)
SELECT customer_id,
       COUNT(order_id) AS total_orders,
       CASE 
           WHEN COUNT(order_id) > 1 THEN 'Repeat'
           ELSE 'New'
       END AS customer_type
FROM Orders
GROUP BY customer_id;
## 💻 Tools Used
- MySQL Workbench
