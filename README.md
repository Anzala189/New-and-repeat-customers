# New-and-repeat-customers

# SQL Portfolio – Customer Orders Analysis

This repository contains SQL tasks for analyzing customer orders:
- Find **new vs repeated customers**
- Count **orders per customer**
- Find **first order date per customer**
- Identify **customers with more than 3 orders**

---

##  Database & Table Setup

```sql
-- Create database and table
CREATE DATABASE new_repeated_customers;
USE new_repeated_customers;

CREATE TABLE CUSTOMER_ORDERS (
    ORDER_ID INTEGER, 
    CUSTOMER_ID INTEGER, 
    ORDER_DATE DATE, 
    ORDER_AMOUNT INTEGER
);

-- Insert sample data
INSERT INTO CUSTOMER_ORDERS VALUES
(1,100,'2022-01-01',2000),
(2,200,'2022-01-01',2500),
(3,300,'2022-01-01',2100),
(4,100,'2022-01-02',2000),
(5,400,'2022-01-02',2200),
(6,500,'2022-01-02',2700),
(7,100,'2022-01-03',3000),
(8,400,'2022-01-03',1000),
(9,600,'2022-01-03',3000);

-- View table
SELECT * FROM customer_orders;

```
--- OUTPUT

| order_id | customer_id | order_date  | order_amount |
|----------|------------|------------|--------------|
| 1        | 100        | 2022-01-01 | 2000         |
| 2        | 200        | 2022-01-01 | 2500         |
| 3        | 300        | 2022-01-01 | 2100         |
| 4        | 100        | 2022-01-02 | 2000         |
| 5        | 400        | 2022-01-02 | 2200         |
| 6        | 500        | 2022-01-02 | 2700         |
| 7        | 100        | 2022-01-03 | 3000         |
| 8        | 400        | 2022-01-03 | 1000         |
| 9        | 600        | 2022-01-03 | 3000         |

---

## 2️⃣ New vs Repeated Customers

```sql
---
WITH First_time AS (
    SELECT customer_id, MIN(order_date) AS First_time_order_dates
    FROM customer_orders
    GROUP BY customer_id
)
SELECT 
    co.order_date,
    SUM(CASE WHEN co.order_date = fv.First_time_order_dates THEN 1 ELSE 0 END) AS New_customer,
    SUM(CASE WHEN co.order_date != fv.First_time_order_dates THEN 1 ELSE 0 END) AS Repeated_customers
FROM customer_orders co
INNER JOIN First_time fv
ON co.customer_id = fv.customer_id
GROUP BY co.order_date
ORDER BY co.order_date;
```

| order_date  | new_customer | repeated_customer |
|------------|--------------|-------------------|
| 2022-01-01 | 3            | 0                 |
| 2022-01-02 | 2            | 1                 |
| 2022-01-03 | 1            | 2                 |

---

##  3️⃣ Count Orders Per Customer
```sql
SELECT customer_id, COUNT(customer_id) AS num_of_orders
FROM customer_orders
GROUP BY customer_id
ORDER BY customer_id ASC;
```
-- OUTPUT
| customer_id | num_of_orders |
|-------------|--------------|
| 100         | 3            |
| 200         | 1            |
| 300         | 1            |
| 400         | 2            |
| 500         | 1            |
| 600         | 1            |

![image](https://github.com/Anzala189/SQL-PRACTICE-QUESTIONS/blob/09a88529b956b4ba8e681862a4171453a767dc07/customers.png)

---
## 4️⃣ First Order Date Per Customer
```sql
SELECT customer_id, MIN(order_date) AS First_order_date
FROM customer_orders
GROUP BY customer_id
ORDER BY customer_id;
```
OUTPUT:

| customer_id | First_order_date |
|-------------|------------------|
| 100         | 2022-01-01       |
| 200         | 2022-01-01       |
| 300         | 2022-01-01       |
| 400         | 2022-01-02       |
| 500         | 2022-01-02       |
| 600         | 2022-01-03       |


![image](https://github.com/Anzala189/SQL-PRACTICE-QUESTIONS/blob/8b5835e2d2cb14b8c28c11cf1dd53ac8f9a5969b/first_visit_date.png)

---
##  5) Customers With More Than 3 Orders

```sql
SELECT customer_id, 
       COUNT(*) AS orders
FROM customer_orders
GROUP BY customer_id
HAVING COUNT(*) > 3;
```

OUTPUT:
| customer_id | orders |
|-------------|--------|
| NULL        | NULL   |

---

##  6) Number each order per customer

```sql
SELECT *,ROW_NUMBER() OVER (
PARTITION BY customer_id
ORDER BY order_date) AS Orders_per_customer
FROM customer_orders;
```

--- OUTPUT:
----
### 🔹 Orders With Running Count Per Customer

| order_id | customer_id | order_date  | order_amount | Order_per_cystomer|
|----------|------------|------------|--------------|--------------------|
| 1        | 100        | 2022-01-01 | 2000         | 1                  |
| 4        | 100        | 2022-01-02 | 2000         | 2                  |
| 7        | 100        | 2022-01-03 | 3000         | 3                  |
| 2        | 200        | 2022-01-01 | 2500         | 1                  |
| 3        | 300        | 2022-01-01 | 2100         | 1                  |
| 5        | 400        | 2022-01-02 | 2200         | 1                  |
| 8        | 400        | 2022-01-03 | 1000         | 2                  |
| 6        | 500        | 2022-01-02 | 2700         | 1                  |
| 9        | 600        | 2022-01-03 | 3000         | 1                  |


## 7) Total amount each customer spent

```sql
SELECT*, SUM(order_amount) OVER (PARTITION BY customer_id) AS total_amount
FROM customer_orders;
```
---OUTPUT:

### 🔹 Orders With Total Per Customer

| order_id | customer_id | order_date  | order_amount | total_amount_per_customer |
|----------|------------|------------|--------------|---------------------------|
| 1        | 100        | 2022-01-01 | 2000         | 7000                      |
| 4        | 100        | 2022-01-02 | 2000         | 7000                      |
| 7        | 100        | 2022-01-03 | 3000         | 7000                      |
| 2        | 200        | 2022-01-01 | 2500         | 2500                      |
| 3        | 300        | 2022-01-01 | 2100         | 2100                      |
| 5        | 400        | 2022-01-02 | 2200         | 3200                      |
| 8        | 400        | 2022-01-03 | 1000         | 3200                      |
| 6        | 500        | 2022-01-02 | 2700         | 2700                      |
| 9        | 600        | 2022-01-03 | 3000         | 3000                      |

---





