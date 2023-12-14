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
`sales` table is joined with `menu` table via `product_id` key since we need the price of each product
***
## Question 2
### How many days has each customer visited the restaurant?
#### Answer:
