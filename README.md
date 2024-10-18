# SQLchallenge-DannysDiner
📍 Case study #1 - https://8weeksqlchallenge.com/case-study-1/ by Data with Danny
<!-- insert logo img -->

## Table of contents
- [Introduction & Problem Statement](#introduction)
- [Skills showcased](#skills-showcased)
- [ASK -> Business task](#ask)
- [PREPARE](#prepare)
- [PROCESS](#process)
- [ANALYZE](#analyze)
- [SHARE](#share)
- [ACT](#act)


## Introduction 
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.
### Problem Statement
Danny wants to use the data to answer a few simple questions about his customers, especially about their visiting patterns, how much money they’ve spent and also which menu items are their favourite. Having this deeper connection with his customers will help him deliver a better and more personalised experience for his loyal customers.

## Skills showcased
- to add
- to add
- to add

## ASK 
### Business Task
Danny wants some insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

## PREPARE 
Danny has shared with you 3 key datasets for this case study:
- <code>sales</code>
- <code>menu</code>
- <code>members</code>
  
You can inspect the entity relationship diagram and example data below. <br>
<!-- insert diagram img -->

### dannys_diner database schema

```SQL

CREATE SCHEMA dannys_diner;
SET search_path = dannys_diner;

```

<details>
	<summary>
Creating the <code>sales</code> table 
  </summary>

```SQL

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

```
</details>

<details>
  <summary>
Creating the <code>menu</code> table
  </summary>

```SQL

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

```
</details>

<details>
  <summary>
Creating the <code>members</code> table
  </summary>

```SQL

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
</details>

## PROCESS

## ANALYZE

### Case study questions

---
*1. What is the total amount each customer spent at the restaurant?*
<details>
  <summary>
		SQL Query
  </summary>

```SQL
SELECT customer_id, SUM(price) AS total_amount
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY customer_id
ORDER BY total_amount;
```
</details>

| customer_id | total_amount |
| ----------- | ------------ |
| C           | 36           |
| B           | 74           |
| A           | 76           |

**Answer** The total amount each customer spent at the restaurant is: <br>
- Customer C		$36
- Customer B		$74
- Customer A		$76

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*2. How many days has each customer visited the restaurant?*
<details>
  <summary>
		SQL Query
  </summary>

```SQL
SELECT customer_id, COUNT(DISTINCT order_date) AS days_visited
FROM dannys_diner.sales
GROUP BY customer_id;
```
</details>

| customer_id | days_visited |
| ----------- | ------------ |
| A           | 4            |
| B           | 6            |
| C           | 2            |

**Answer** Each customer has visited the restaurant for: <br>
- Customer A		4 days
- Customer B		6 days
- Customer C		2 days

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*3. What was the first item from the menu purchased by each customer?*
<details>
  <summary>
		SQL Query
  </summary>

```SQL
WITH sales_ordered AS
(
   SELECT customer_id, order_date, product_name,
      DENSE_RANK() OVER(PARTITION BY sales.customer_id
      ORDER BY sales.order_date) AS rank
   FROM dannys_diner.sales
   JOIN dannys_diner.menu
      ON sales.product_id = menu.product_id
)

SELECT customer_id, product_name
FROM sales_ordered
WHERE rank = 1
GROUP BY customer_id, product_name;
```
</details>

| customer_id | product_name |
| ----------- | ------------ |
| A           | curry        |
| A           | sushi        |
| B           | curry        |
| C           | ramen        |

**Answer** The first item purchased by each customer was: <br>
- Customer A		curry & sushi
- Customer B		curry
- Customer C		ramen

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*4. What is the most purchased item on the menu and how many times was it purchased by all customers?*
<details>
  <summary>
  	SQL Query
  </summary>

```SQL
WITH most_purchased_item AS 
	(
	SELECT menu.product_id, menu.product_name, COUNT(sales.product_id) AS item_sales
  	FROM dannys_diner.sales
      JOIN dannys_diner.menu
      ON sales.product_id = menu.product_id
    GROUP BY menu.product_id, menu.product_name
  	ORDER BY item_sales DESC
    LIMIT 1
  	)
 
 SELECT customer_id, product_name, COUNT(sales.product_id) AS item_purchases
 FROM dannys_diner.sales
 JOIN most_purchased_item
 ON sales.product_id = most_purchased_item.product_id
 GROUP BY customer_id, most_purchased_item.product_name;
```
</details>

| customer_id | product_name | item_purchases |
| ----------- | ------------ | -------------- |
| A           | ramen        | 3              |
| B           | ramen        | 2              |
| C           | ramen        | 3              |

**Answer** The most purchased item on the menu is *Ramen* and it was purchased by each customer: <br>
- Customer A		3 times
- Customer B		2 times
- Customer C		3 times

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*5. Which item was the most popular for each customer?*
<details>
	<summary>
		SQL Query
  </summary>

```SQL
SELECT customer_id, product_name, COUNT(sales.product_id) AS item_purchases
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY customer_id, product_name
ORDER BY item_purchases DESC
```
</details>

| customer_id | product_name | item_purchases |
| ----------- | ------------ | -------------- |
| C           | ramen        | 3              |
| A           | ramen        | 3              |
| B           | curry        | 2              |
| B           | sushi        | 2              |
| B           | ramen        | 2              |
| A           | curry        | 2              |
| A           | sushi        | 1              |

**Answer** The most popular item for each customer was: <br>
- Customer C		Ramen
- Customer A		Ramen
- Customer B		Curry

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*6. Which item was purchased first by the customer after they became a member?*
<details>
	<summary>
		SQL Query
	</summary>

```SQL
 WITH purchase_date AS
 	(
 	SELECT sales.customer_id, join_date, order_date, product_id,
    	DENSE_RANK() OVER(PARTITION BY sales.customer_id
      	ORDER BY sales.order_date) AS first_member_purchase
   	FROM dannys_diner.sales
   	JOIN dannys_diner.members
    	  ON sales.customer_id = members.customer_id
   	WHERE sales.order_date >= members.join_date
    )
    
SELECT customer_id, product_name, order_date, first_member_purchase
FROM dannys_diner.menu
JOIN purchase_date
ON menu.product_id = purchase_date.product_id
WHERE first_member_purchase = 1;
```
</details>

| customer_id | product_name | order_date               | first_member_purchase |
| ----------- | ------------ | ------------------------ | --------------------- |
| B           | sushi        | 2021-01-11T00:00:00.000Z | 1                     |
| A           | curry        | 2021-01-07T00:00:00.000Z | 1                     |

**Answer** The first item each customer bought after becaming a member was: <br>
- Customer B		Sushi
- Customer A		Curry

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*7. Which item was purchased just before the customer became a member?*
<details>
	<summary>
		SQL Query
	</summary>

```SQL
WITH last_purchase_cte AS
	(
	SELECT sales.customer_id, product_id, order_date,
		DENSE_RANK() OVER(PARTITION BY sales.customer_id 
		ORDER BY order_date DESC) AS last_purchase
	FROM dannys_diner.sales
	JOIN dannys_diner.members
	 	ON sales.customer_id = members.customer_id
	WHERE order_date < join_date
	GROUP BY sales.customer_id, product_id, order_date
	ORDER BY last_purchase
    )
    
SELECT customer_id, product_name, order_date
FROM last_purchase_cte
JOIN dannys_diner.menu
	ON last_purchase_cte.product_id = menu.product_id
WHERE last_purchase = 1
GROUP BY customer_id, product_name, order_date;
```
</details>

| customer_id | product_name | order_date               |
| ----------- | ------------ | ------------------------ |
| A           | curry        | 2021-01-01T00:00:00.000Z |
| A           | sushi        | 2021-01-01T00:00:00.000Z |
| B           | sushi        | 2021-01-04T00:00:00.000Z |

**Answer** The item each customer bought just before becaming a member was: <br>
- Customer A		Curry & Sushi
- Customer B		Sushi

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*8. What is the total items and amount spent for each member before they became a member?*
<details>
	<summary>
		SQL Query
	</summary>

```SQL
SELECT sales.customer_id,
	COUNT(sales.product_id) AS total_items,
    SUM(price) AS total_amount
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
JOIN dannys_diner.members
ON sales.customer_id = members.customer_id
WHERE order_date < join_date
GROUP BY sales.customer_id;

```
</details>

| customer_id | total_items | total_amount |
| ----------- | ----------- | ------------ |
| B           | 3           | 40           |
| A           | 2           | 25           |

**Answer** The total number of items and amount spent of each customer before de became a member are:
- Customer B 3 items, $40
- Customer A 2 items, $25

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*9. If each $1 spent equates to 10 points and sushi has a 2x points multiplier - how many points would each customer have?*
<details>
	<summary>
		SQL Query
	</summary>

```SQL
SELECT customer_id, 
	SUM(
    CASE WHEN sales.product_id = 1 THEN price*20
    ELSE price*10 END
    ) AS points
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY customer_id 
ORDER BY points;

```
</details>

| customer_id | points |
| ----------- | ------ |
| C           | 360    |
| A           | 860    |
| B           | 940    |

**Answer** Te total amount of points each customer has is:
- Customer C 360
- Customer A 860
- Customer B 940

<details>
	<summary>
		Resolution
	</summary>
</details>

---
*10. *
<details>
	<summary>
		SQL Query
	</summary>

```SQL



```

**Answer**

<details>
	<summary>
		Resolution
	</summary>
</details>
