## Schema SQL
Database: MySQL v8.0
```sql
CREATE DATABASE dannys_diner;

USE dannys_diner;

CREATE TABLE sales (
  customer_id VARCHAR(1),
  order_date DATE,
  product_id INT
);

INSERT INTO sales
  (customer_id, order_date, product_id)
VALUES
  ('A', '2021-01-01', 1),
  ('A', '2021-01-01', 2),
  ('A', '2021-01-07', 2),
  ('A', '2021-01-10', 3),
  ('A', '2021-01-11', 3),
  ('A', '2021-01-11', 3),
  ('B', '2021-01-01', 2),
  ('B', '2021-01-02', 2),
  ('B', '2021-01-04', 1),
  ('B', '2021-01-11', 1),
  ('B', '2021-01-16', 3),
  ('B', '2021-02-01', 3),
  ('C', '2021-01-01', 3),
  ('C', '2021-01-01', 3),
  ('C', '2021-01-07', 3);

CREATE TABLE menu (
  product_id INT,
  product_name VARCHAR(5),
  price INT
);

INSERT INTO menu
  (product_id, product_name, price)
VALUES
  (1, 'sushi', 10),
  (2, 'curry', 15),
  (3, 'ramen', 12);

CREATE TABLE members (
  customer_id VARCHAR(1),
  join_date DATE
);

INSERT INTO members
  (customer_id, join_date)
VALUES
  ('A', '2021-01-07'),
  ('B', '2021-01-09');
```

***
## Question 1
### What is the total amount each customer spent at the restaurant?
#### Answer:
```sql
SELECT s.customer_id, SUM(m.price) as total_amount_spent
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m
	ON s.product_id=m.product_id
GROUP BY customer_id;
```
#### Output:
The total amount of money spent by each customer is:
| customer_id | total_amount_spent |
|-------------|---------------------|
| A           | 76                  |
| B           | 74                  |
| C           | 36                  |

#### Explanation:
`sales` table (alias `s`) is joined with `menu` (alias `m`) table via `product_id` key since we need the according product prices from the `sales` table. `customer_id`s are returned and `price`s of all order instances are summed up and aggregated by the customers.

***
## Question 2
### How many days has each customer visited the restaurant?
#### Answer:
```sql
SELECT customer_id, COUNT(DISTINCT order_date) as num_days_visited
FROM dannys_diner.sales
GROUP BY customer_id;
```
#### Output:
The number of days each customer visited the restaurant:
| customer_id | num_days_visited |
| ----------- | ---------------- |
| A           | 4                |
| B           | 6                |
| C           | 2                |

***
## Question 3
### What was the first item from the menu purchased by each customer?
#### Answer:
```sql
WITH sq AS (SELECT DISTINCT 
                customer_id, 
                order_date,
                s.product_id,
                product_name, 
                RANK() OVER (ORDER BY order_date) AS rank_date
			FROM dannys_diner.sales s 
           	INNER JOIN dannys_diner.menu m
           		ON s.product_id=m.product_id)

SELECT customer_id, product_name 
FROM sq
WHERE rank_date = 1;
```

***
## Question 4
### What is the most purchased item on the menu and how many times was it purchased by all customers?
#### Answer:
```sql
SELECT product_name, COUNT(*) as times_purchased
FROM dannys_diner.menu m
INNER JOIN dannys_diner.sales s
	ON m.product_id=s.product_id
GROUP BY product_name
ORDER BY times_purchased DESC
LIMIT 1;
```

***
## Question 5
### Which item was the most popular for each customer?
#### Answer:

***
## Question 6
### Which item was purchased first by the customer after they became a member?
#### Answer:

***
## Question 7
### Which item was purchased just before the customer became a member?
#### Answer:

***
## Question 8
### What is the total items and amount spent for each member before they became a member?
#### Answer:

***
## Question 9
### If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
#### Answer:

***
## Question 10
### In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?
#### Answer:

