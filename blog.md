---
title: "Blog"
---

# My Blog
## SQL Joins Demystified: INNER, LEFT, and OUTER in Action

### Problem Statement
In data science, combining data from multiple tables is common, but choosing the wrong SQL join can lead to incomplete or misleading results. This tutorial helps students understand how INNER, LEFT, and FULL OUTER joins affect outcomes when merging tables (e.g., customers and orders), and guides on selecting the right join for analysis tasks.

### Key Steps/Sections
#### 1. Setup: Two Simple Tables
Let's start with two basic tables to illustrate joins. Imagine we have a customers table (with customer details) and an orders table (with purchase records). We'll use a primary key (customer_id) in customers that acts as a foreign key in orders.
For this static tutorial, we'll define the tables directly as Markdown tables below. In a real SQL environment (like SQLite, MySQL, or PostgreSQL), you would create these tables using CREATE TABLE and INSERT statements.

**Customers Table:**

| customer_id | name     | email             |
|-------------|----------|-------------------|
| 1           | Alice    | alice@email.com   |
| 2           | Bob      | bob@email.com     |
| 3           | Charlie  | charlie@email.com |
| 4           | Dana     | dana@email.com    |
| 5           | Eve      | eve@email.com     |

**Orders Table:**

| order_id | customer_id | product  | amount |
|----------|-------------|----------|--------|
| 101      | 1           | Laptop   | 1200.0 |
| 102      | 2           | Phone    | 800.0  |
| 103      | 1           | Tablet   | 300.0  |
| 104      | 6           | Monitor  | 200.0  |
| 105      | 3           | Keyboard | 50.0   |

#### 2. INNER JOIN: Matching Only
An INNER JOIN returns only rows where there's a match in both tables based on the join condition (e.g., customer_id).

```
SELECT c.customer_id, c.name, o.order_id, o.product, o.amount
FROM customers c
INNER JOIN orders o ON c.customer_id = o.customer_id;
```

**Expected Result:**

| customer_id | name    | order_id | product  | amount |
|-------------|---------|----------|----------|--------|
| 1           | Alice   | 101      | Laptop   | 1200.0 |
| 1           | Alice   | 103      | Tablet   | 300.0  |
| 2           | Bob     | 102      | Phone    | 800.0  |
| 3           | Charlie | 105      | Keyboard | 50.0   |

Notice: Only matched customers (1,2,3) appear; Dana (4), Eve (5), and the unmatched order (104 for customer 6) are excluded.

#### LEFT JOIN: Keep All from Left
A LEFT JOIN keeps all rows from the left table (customers), with matching rows from the right (orders). Unmatched right-side columns get NULL.

```
SELECT c.customer_id, c.name, o.order_id, o.product, o.amount
FROM customers c
LEFT JOIN orders o ON c.customer_id = o.customer_id;
```

| customer_id | name    | order_id | product  | amount |
|-------------|---------|----------|----------|--------|
| 1           | Alice   | 101      | Laptop   | 1200.0 |
| 1           | Alice   | 103      | Tablet   | 300.0  |
| 2           | Bob     | 102      | Phone    | 800.0  |
| 3           | Charlie | 105      | Keyboard | 50.0   |
| 4           | Dana    | None     | None     | None   |
| 5           | Eve     | None     | None     | None   |

Here, all customers are included, with NULLS for Dana and Eve's orders. Unmatched orders (like 104) are dropped. 

#### 4. FULL OUTER JOIN: Everything, Everywhere
A FULL OUTER JOIN (or just OUTER JOIN in some dialects) includes all rows from both tables, with NULLs where there's no match. (Note: In SQLite, this is FULL OUTER JOIN; in MySQL, it's emulated with UNION of LEFT and RIGHT joins.)

```
SELECT c.customer_id, c.name, o.order_id, o.product, o.amount
FROM customers c
FULL OUTER JOIN orders o ON c.customer_id = o.customer_id;
```

| customer_id | name    | order_id | product  | amount |
|-------------|---------|----------|----------|--------|
| 1           | Alice   | 101      | Laptop   | 1200.0 |
| 1           | Alice   | 103      | Tablet   | 300.0  |
| 2           | Bob     | 102      | Phone    | 800.0  |
| 3           | Charlie | 105      | Keyboard | 50.0   |
| 4           | Dana    | None     | None     | None   |
| 5           | Eve     | None     | None     | None   |
| 6           | None    | 104      | Monitor  | 200.0  |

This shows everything: matched rows, plus unmatched customers (4,5) and the unmatched order (104). 

##### 5. Comparing Join Results

| Join Type       | Rows Returned | Includes Unmatched Left? | Includes Unmatched Right? | Use Case Example                  |
|-----------------|---------------|--------------------------|---------------------------|-----------------------------------|
| INNER JOIN      | 4             | No                       | No                        | Get only customers with orders    |
| LEFT JOIN       | 6             | Yes (with NULLs)         | No                        | Analyze all customers, including those without orders |
| FULL OUTER JOIN | 7             | Yes (with NULLs)         | Yes (with NULLs)          | Audit all data, spotting orphans in either table |

### CALL TO ACTION

