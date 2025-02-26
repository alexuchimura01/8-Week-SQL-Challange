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
  


**2. How many days has each customer visited the restaurant?**

``` sql
SELECT customer_id, COUNT(DISTINCT order_date) as days_visited
FROM sales
GROUP BY customer_id;
```

**3. What was the first item from the menu purchased by each customer?**


**4. What is the most purchased item on the menu and how many times was it purchased by all customers?**


**5. Which item was the most popular for each customer?**


**6. Which item was purchased first by the customer after they became a member?**


**7. Which item was purchased just before the customer became a member?**


**8. What is the total items and amount spent for each member before they became a member?**


**9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?**


**10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?**

