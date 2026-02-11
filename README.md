# New-and-repeat-customers
# SQL-PRACTICE-QUESTIONS

-- Script 3 :
-- New and repeated customers

CREATE DATABASE new_repeated_customers;
USE new_repeated_customers;
SELECT*FROM customer_orders;
-- For new customers: First_time: co.Order_date= MIN(order_date)
-- For repeated customers : co.order_date!= Min(order_date) = repeated

SOLUTION:

WITH First_time AS(
SELECT customer_id, MIN(order_date) AS First_time_order_dates
FROM customer_orders
GROUP BY customer_Id)
-- Fv= first_visit
-- CO= Customer_orders
SELECT co.order_date, 
SUM(CASE WHEN co.order_date= fv.First_time_order_dates THEN 1 ELSE 0 END) AS New_customer,
SUM(CASE WHEN co.order_date != fv.First_time_order_dates THEN 1 ELSE 0 END) AS Repeated_customers
FROM customer_orders co INNER JOIN First_time fv
ON co.customer_id=fv.customer_id
GROUP BY co.order_date
order by co.order_date
LIMIT 100;

FIND OUT ORDERS PER CUSTOMERS:

-- Count Orders Per Customer

SOLUTION
SELECT customer_id,COUNT(customer_Id) AS num_of_orders FROM customer_orders
GROUP BY customer_id
ORDER BY customer_Id ASC;

![image](https://github.com/Anzala189/SQL-PRACTICE-QUESTIONS/blob/09a88529b956b4ba8e681862a4171453a767dc07/customers.png)

QUESTION: FIND FIRST ORDER DATE PER CUSTOMER:

-- first Order Date Per Customer
SELECT customer_id, MIN(order_date) AS First_order_date
from customer_orders
GROUP BY Customer_id
ORDER BY Customer_id;

100	2022-01-01
200	2022-01-01
300	2022-01-01
400	2022-01-02
500	2022-01-02
600	2022-01-03

![image](https://github.com/Anzala189/SQL-PRACTICE-QUESTIONS/blob/8b5835e2d2cb14b8c28c11cf1dd53ac8f9a5969b/first_visit_date.png)

-- 3️⃣ Customers With More Than 3 Orders
SELECT customer_id, 
       COUNT(*) AS orders
FROM customer_orders
GROUP BY customer_id
HAVING COUNT(*) > 3;





