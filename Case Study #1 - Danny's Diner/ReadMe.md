## Question 1
### What is the total amount each customer spent at the restaurant?
#### Answer:
```sql
SELECT s.customer_id, sum(m.price) as total_amount_spent
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
                rank() over (ORDER BY order_date) AS rank_date
			FROM dannys_diner.sales s 
           	INNER JOIN dannys_diner.menu m
           	ON s.product_id=m.product_id)

SELECT customer_id, product_name 
FROM sq
WHERE rank_date = 1;
```

***
## Question 4
### What was the first item from the menu purchased by each customer?
#### Answer:
