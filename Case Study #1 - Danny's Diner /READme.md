# Case Study #1: Danny's Diner

![Screenshot 2025-02-18 084904](https://github.com/user-attachments/assets/77f72972-f2c6-4388-a637-90fdd343e335)

## Table of Contents
- [Business Problem](READme.md#business-problem)
- [ER Diagram](READme.md#er-diagram)
- [Case Study Questions & Answers](READme.md#case-study-questions--answers)
---
### Business Problem

Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. He plans on using these insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL. 

See the entire Case Study #1 [here.](https://8weeksqlchallenge.com/case-study-1/)

---
### ER Diagram & Database From DB Fiddle
![image](https://github.com/user-attachments/assets/21339843-ad8e-468b-bec8-b7be922e5827)


``` SQL
CREATE SCHEMA dannys_diner;
SET search_path = dannys_diner;

CREATE TABLE sales (
  "customer_id" VARCHAR(1),
  "order_date" DATE,
  "product_id" INTEGER
);

INSERT INTO sales
  ("customer_id", "order_date", "product_id")
VALUES
  ('A', '2021-01-01', '1'),
  ('A', '2021-01-01', '2'),
  ('A', '2021-01-07', '2'),
  ('A', '2021-01-10', '3'),
  ('A', '2021-01-11', '3'),
  ('A', '2021-01-11', '3'),
  ('B', '2021-01-01', '2'),
  ('B', '2021-01-02', '2'),
  ('B', '2021-01-04', '1'),
  ('B', '2021-01-11', '1'),
  ('B', '2021-01-16', '3'),
  ('B', '2021-02-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-01', '3'),
  ('C', '2021-01-07', '3');
 

CREATE TABLE menu (
  "product_id" INTEGER,
  "product_name" VARCHAR(5),
  "price" INTEGER
);

INSERT INTO menu
  ("product_id", "product_name", "price")
VALUES
  ('1', 'sushi', '10'),
  ('2', 'curry', '15'),
  ('3', 'ramen', '12');
  

CREATE TABLE members (
  "customer_id" VARCHAR(1),
  "join_date" DATE
);

INSERT INTO members
  ("customer_id", "join_date")
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
```

The above database was pre-created on DB Fiddle, and the following solutions will be code and solutions that were run on DB Fiddle.

---
### Case Study Questions & Answers
**1. What is the total amount each customer spent at the restaurant?**

``` sql
SELECT customer_id, SUM(price) as total_spent
FROM sales s JOIN menu m
ON s.product_id = m.product_id
GROUP BY s.customer_id
ORDER BY total_spent DESC;
```
| Customer | Total Spent           |
|------------|----------------|
| A          | 76   |
| B          | 74    |
| C          | 36       |

- Step 1: Join sales and menu tables since we are wanting to find price per customer.
  
- Step 2: Group by customer_id.
  
- Step 3: Select customer_id, the sum of price, and create an alias for SUM(price).
***
**2. How many days has each customer visited the restaurant?**

``` sql
SELECT customer_id, COUNT(DISTINCT order_date) as days_visited
FROM sales
GROUP BY customer_id;
```

| Customer | Days Visited           |
|------------|----------------|
| A          | 4   |
| B          | 6    |
| C          | 2       |

- Step 1: Select customer_id and and the count of distinct order_date since one order date signifies the date a customer visited the restaurant.
- Step 2: Group by customer_id.
***
**3. What was the first item from the menu purchased by each customer?**
```sql
WITH ordered_purchase AS (
  SELECT 
    s.customer_id, 
    s.product_id,
    s.order_date,
    ROW_NUMBER() OVER(PARTITION BY s.customer_id ORDER BY s.order_date ASC) AS rn
  FROM sales s
)

SELECT 
  op.customer_id,
  m.product_name,
  op.order_date
FROM ordered_purchase op
JOIN menu m ON op.product_id = m.product_id
WHERE op.rn = 1;

```
![image](https://github.com/user-attachments/assets/1ac61fd7-790f-4511-872e-993981f338e5)

- Step 1: Create a mini table called 'ordered_purchase' containing assigned row numbers for each purchase made by customer, ordered by order_date in ascending order. Here the ROW_NUMBER() OVER(PARTITION BY...) clause is partitioning row number assignments to each customer_id, so each customer gets their own independent ranking sequence by order_date, assigning row number 1 to the first purchase.
- Step 2: Join the 'ordered_purchase' table with menu with the condition of showing only the first row for each customer_id. This is the earliest order_date by customer due to row number partitioning.
- Step 3: Select customer_id, product_name, and order_date.
***
**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**
``` sql
SELECT m.product_name AS most_purchased, COUNT(m.product_id) AS purchase_count
FROM sales s JOIN menu m
ON s.product_id = m.product_id
GROUP BY m.product_name
ORDER BY purchase_count DESC
LIMIT 1;
```
| Most Purchased | Purchase Count   |
|------------|----------------|
| Ramen          | 8   |

- Step 1: Join sales and menu tables to get the count of products sold and product name in the same query.
- Step 2: Select the count of products from the sales table, group by product name, order the count in descending order and choose the first row.
***
**5. Which item was the most popular for each customer?**
```sql
WITH most_popular AS (
  SELECT s.customer_id, COUNT(m.product_id) AS times_ordered, m.product_name as most_popular,
  	DENSE_RANK() OVER (
    	PARTITION BY s.customer_id
    	ORDER BY COUNT(m.product_id) DESC) AS RANK
  FROM menu m 
  JOIN sales s
  ON m.product_id = s.product_id
  GROUP BY s.customer_id, m.product_name)
  
 SELECT customer_id, times_ordered, most_popular
  FROM most_popular
  WHERE rank = 1;
```
***
**6. Which item was purchased first by the customer after they became a member?**

***
**7. Which item was purchased just before the customer became a member?**

***
**8. What is the total items and amount spent for each member before they became a member?**

***
**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**

***
**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**

