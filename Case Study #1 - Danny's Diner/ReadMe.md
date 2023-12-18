## The Problem
The full information about the case study can be found at: [Link](https://8weeksqlchallenge.com/case-study-1/)

In summary, Danny needs help with understanding his customers better in terms of visiting patterns, spending patterns, and preferences. His plan is to use these insights to decide if expanding the existing loyalty program is the right choice or not.

***
## Entity Relationship Diagram
![image](https://private-user-images.githubusercontent.com/60505826/291169578-9d7777c3-4c23-45b2-94d4-adb0ee693dde.png?jwt=eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJpc3MiOiJnaXRodWIuY29tIiwiYXVkIjoicmF3LmdpdGh1YnVzZXJjb250ZW50LmNvbSIsImtleSI6ImtleTEiLCJleHAiOjE3MDI4ODEwMDgsIm5iZiI6MTcwMjg4MDcwOCwicGF0aCI6Ii82MDUwNTgyNi8yOTExNjk1NzgtOWQ3Nzc3YzMtNGMyMy00NWIyLTk0ZDQtYWRiMGVlNjkzZGRlLnBuZz9YLUFtei1BbGdvcml0aG09QVdTNC1ITUFDLVNIQTI1NiZYLUFtei1DcmVkZW50aWFsPUFLSUFJV05KWUFYNENTVkVINTNBJTJGMjAyMzEyMTglMkZ1cy1lYXN0LTElMkZzMyUyRmF3czRfcmVxdWVzdCZYLUFtei1EYXRlPTIwMjMxMjE4VDA2MjUwOFomWC1BbXotRXhwaXJlcz0zMDAmWC1BbXotU2lnbmF0dXJlPWZlMGNiNzRhMTgyOGUzNGU5MWEwOWE1ZWU2MGI3MzNmM2E2MmY2YmMyNzM4MWM0NDI2NjU2MjQxZTZiY2QzZGQmWC1BbXotU2lnbmVkSGVhZGVycz1ob3N0JmFjdG9yX2lkPTAma2V5X2lkPTAmcmVwb19pZD0wIn0.bEKY_jmPXOcdaunXRiciPPcbktrvClbWuuKe8r2IVJM)
***
## Schema SQL
You can execute the queries using MySQL on [DB Fiddle](https://www.db-fiddle.com/f/2rM8RAnq7h5LLDTzZiRWcd/138) by changing the Database to MySQL v8.0 and replacing the existing Schema with the following:
```sql
CREATE DATABASE IF NOT EXISTS dannys_diner;

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
SELECT s.customer_id, SUM(m.price) AS total_amount_spent
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu m
	ON s.product_id=m.product_id
GROUP BY customer_id;
```
#### Output:
| customer_id | total_amount_spent |
|-------------|---------------------|
| A           | 76                  |
| B           | 74                  |
| C           | 36                  |

A spent 76$, B spent 74$ and C spent 36$.

***
## Question 2
### How many days has each customer visited the restaurant?
#### Answer:
```sql
SELECT customer_id, COUNT(DISTINCT order_date) AS num_days_visited
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

A visited the restaurants 4 times, while B and C visited 6 and 2 times respectively.

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
#### Output:
| customer_id | product_name |
| ----------- | ------------ |
| A           | sushi        |
| A           | curry        |
| B           | curry        |
| C           | ramen        |

For customer A, their first items purchased were sushi and curry on the same day. Customer B's first dish was curry and C's was ramen.

***
## Question 4
### What is the most purchased item on the menu and how many times was it purchased by all customers?
#### Answer:
```sql
SELECT product_name, COUNT(*) AS times_purchased
FROM dannys_diner.menu m
INNER JOIN dannys_diner.sales s
	ON m.product_id=s.product_id
GROUP BY product_name
ORDER BY times_purchased DESC
LIMIT 1;
```
#### Output:
| product_name | times_purchased |
| ------------ | --------------- |
| ramen        | 8               |

The most purchased item is ramen, which was purchased 8 times by all customers.

***
## Question 5
### Which item was the most popular for each customer?
#### Answer:
```sql
WITH t AS 
        (SELECT s.customer_id, 
                m.product_name,
                COUNT(*) AS times_ordered,
                RANK() OVER (PARTITION BY s.customer_id ORDER BY COUNT(*) DESC) AS rnk
        FROM dannys_diner.sales s
        INNER JOIN dannys_diner.menu m
        		ON s.product_id=m.product_id
        GROUP BY s.customer_id, m.product_name)

SELECT customer_id, product_name, times_ordered
FROM t
WHERE rnk = 1;
```
#### Output:
| customer_id | product_name | times_ordered |
| ----------- | ------------ | ------------- |
| A           | ramen        | 3             |
| B           | curry        | 2             |
| B           | sushi        | 2             |
| B           | ramen        | 2             |
| C           | ramen        | 3             |

Based on the highest times ordered, the most popular item for both A and C was ramen. B, however, seems to like curry, sushi, and ramen equally.

***
## Question 6
### Which item was purchased first by the customer after they became a member?
#### Answer:
```sql
WITH t AS 
      (SELECT s.customer_id, 
       			s.order_date, 
       			me.product_name,
       			m.join_date,
       			RANK() OVER (PARTITION BY customer_id ORDER BY order_date) AS rnk
      FROM dannys_diner.sales s
      INNER JOIN dannys_diner.members m
              ON s.customer_id=m.customer_id
              AND s.order_date > m.join_date
      INNER JOIN dannys_diner.menu me
              ON s.product_id=me.product_id)
        
SELECT customer_id, product_name, join_date, order_date
FROM t
WHERE rnk = 1;
```
#### Output:
| customer_id | product_name | join_date  | order_date |
| ----------- | ------------ | ---------- | ---------- |
| A           | ramen        | 2021-01-07 | 2021-01-10 |
| B           | sushi        | 2021-01-09 | 2021-01-11 |

After becoming a member on 7/1/2021, the first item purchased by A was ramen, on 10/1/2021.
After becoming a member on 9/1/2021, the first item purchased by B was sushi, on 11/1/2021.
***
## Question 7
### Which item was purchased just before the customer became a member?
#### Answer:
```sql
WITH t AS 
      (SELECT s.customer_id, 
       			s.order_date, 
       			me.product_name,
       			m.join_date,
       			RANK() OVER (PARTITION BY customer_id ORDER BY order_date DESC) AS rnk
      FROM dannys_diner.sales s
      INNER JOIN dannys_diner.members m
              ON s.customer_id=m.customer_id
              AND s.order_date < m.join_date
      INNER JOIN dannys_diner.menu me
              ON s.product_id=me.product_id)
        
SELECT customer_id, product_name, join_date, order_date
FROM t
WHERE rnk = 1;
```
#### Output:
| customer_id | product_name | join_date  | order_date |
| ----------- | ------------ | ---------- | ---------- |
| A           | sushi        | 2021-01-07 | 2021-01-01 |
| A           | curry        | 2021-01-07 | 2021-01-01 |
| B           | sushi        | 2021-01-09 | 2021-01-04 |

Just before becoming members, A purchased sushi and curry on the same day (1/1/2021), while B purchased sushi on 4/1/2021.

***
## Question 8
### What are the total items and amount spent for each member before they became a member?
#### Answer:
```sql
SELECT s.customer_id, COUNT(me.product_name) AS total_items, SUM(me.price) AS amount_spent
FROM dannys_diner.sales s
INNER JOIN dannys_diner.members m
		ON s.customer_id=m.customer_id AND s.order_date < m.join_date
INNER JOIN dannys_diner.menu me
		ON s.product_id=me.product_id
GROUP BY s.customer_id
ORDER BY s.customer_id;
```
#### Output:
| customer_id | total_items | amount_spent |
| ----------- | ----------- | ------------ |
| A           | 2           | 25           |
| B           | 3           | 40           |

Before becoming members, A spent a total of 25$ on 2 items, while B spent a total of 40$ on 3 items.

***
## Question 9
### If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?
#### Answer:
```sql
SELECT s.customer_id, 
	SUM(CASE 
		WHEN me.product_name = "sushi" THEN 20*me.price 
		ELSE 10*me.price END) AS points
FROM dannys_diner.sales s
INNER JOIN dannys_diner.menu me
		ON s.product_id=me.product_id
GROUP BY s.customer_id;
```
#### Output:
| customer_id | points |
| ----------- | ------ |
| A           | 860    |
| B           | 940    |
| C           | 360    |

A would have 860 points, B would have 940 points and C would have 360 points in total.

***
## Question 10
### In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customers A and B have at the end of January?
#### Answer:
```sql
SELECT s.customer_id, 
	SUM(CASE 
		WHEN (me.product_name = "sushi" OR (s.order_date BETWEEN m.join_date AND m.join_date+6)) THEN 20*me.price
		ELSE 10*me.price END) AS points
FROM dannys_diner.sales s
INNER JOIN dannys_diner.members m
	ON s.customer_id=m.customer_id
INNER JOIN dannys_diner.menu me
	ON s.product_id=me.product_id
WHERE MONTH(s.order_date) < 2
GROUP BY s.customer_id
ORDER BY s.customer_id;
```
#### Output:
| customer_id | points |
| ----------- | ------ |
| A           | 1370   |
| B           | 820    |

Taking into account all past purchase history until the end of January 2021, A's points would be 1370, and B's points would 820.

***
## Bonus Questions
### Join All The Things
#### Recreate the following table output using the available data:
| customer_id | order_date | product_name | price | member |
| ----------- | ---------- | ------------ | ----- | ------ |
| A           | 2021-01-01 | curry        | 15    | N      |
| A           | 2021-01-01 | sushi        | 10    | N      |
| A           | 2021-01-07 | curry        | 15    | Y      |
| A           | 2021-01-10 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| B           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-02 | curry        | 15    | N      |
| B           | 2021-01-04 | sushi        | 10    | N      |
| B           | 2021-01-11 | sushi        | 10    | Y      |
| B           | 2021-01-16 | ramen        | 12    | Y      |
| B           | 2021-02-01 | ramen        | 12    | Y      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-07 | ramen        | 12    | N      |

#### Answer:
```sql
SELECT s.customer_id,
	s.order_date,
	me.product_name,
	me.price,
	CASE
		WHEN s.order_date >= m.join_date THEN "Y"
        	ELSE "N"
	END AS member
FROM dannys_diner.sales s
LEFT JOIN dannys_diner.members m
	ON s.customer_id = m.customer_id
INNER JOIN dannys_diner.menu me
	ON s.product_id = me.product_id
ORDER BY s.customer_id, s.order_date, me.product_name;
```

### Rank All The Things
#### Danny also requires further information about the ranking of customer products, but he does not need the ranking for non-member purchases.
| customer_id | order_date | product_name | price | member | ranking |
| ----------- | ---------- | ------------ | ----- | ------ | ------- |
| A           | 2021-01-01 | curry        | 15    | N      | null        |
| A           | 2021-01-01 | sushi        | 10    | N      | null        |
| A           | 2021-01-07 | curry        | 15    | Y      | 1       |
| A           | 2021-01-10 | ramen        | 12    | Y      | 2       |
| A           | 2021-01-11 | ramen        | 12    | Y      | 3       |
| A           | 2021-01-11 | ramen        | 12    | Y      | 3       |
| B           | 2021-01-01 | curry        | 15    | N      | null        |
| B           | 2021-01-02 | curry        | 15    | N      | null        |
| B           | 2021-01-04 | sushi        | 10    | N      | null        |
| B           | 2021-01-11 | sushi        | 10    | Y      | 1       |
| B           | 2021-01-16 | ramen        | 12    | Y      | 2       |
| B           | 2021-02-01 | ramen        | 12    | Y      | 3       |
| C           | 2021-01-01 | ramen        | 12    | N      | null        |
| C           | 2021-01-01 | ramen        | 12    | N      | null        |
| C           | 2021-01-07 | ramen        | 12    | N      | null        |

#### Answer:
```sql
SELECT s.customer_id,
	s.order_date,
	me.product_name,
	me.price,
	CASE
		WHEN s.order_date >= m.join_date THEN "Y"
        	ELSE "N"
	END AS member,
    	CASE 
    		WHEN s.order_date >= m.join_date
			THEN
				DENSE_RANK() OVER
					(PARTITION BY s.customer_id,
						CASE WHEN s.order_date >= m.join_date THEN 0 ELSE 1 END
					ORDER BY s.order_date)
	END AS ranking
FROM dannys_diner.sales s
LEFT JOIN dannys_diner.members m
	ON s.customer_id = m.customer_id
INNER JOIN dannys_diner.menu me
	ON s.product_id = me.product_id
ORDER BY s.customer_id, s.order_date, me.product_name;
```

