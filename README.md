# SQLchallenge-DannysDiner
📍 Hi there! My name is Fer and I'm following the Case study #1 - https://8weeksqlchallenge.com/case-study-1/ by Data with Danny

![DannysDiner_logo](https://github.com/fadalid/SQLchallenge-DannysDiner/blob/main/dannysdiner-logo.png?raw=true)

## Table of contents
- [Introduction & Problem Statement](#introduction)
- [Skills showcased](#skills-showcased)
- [Dataset Overview](#dataset-overview)
- [Case Study Questions & Solutions](#case-study-questions--solutions)
- [Insights & Recommendations](#insights--recommendations)
- [Tools & Technologies Used](#tools--technologies-used)
- [Future Improvements](#future-improvements)

## Introduction 
Danny seriously loves Japanese food so in the beginning of 2021, he decides to embark upon a risky venture and opens up a cute little restaurant that sells his 3 favourite foods: sushi, curry and ramen.
### Problem Statement
Danny wants some insights to help him decide whether he should expand the existing customer loyalty program - additionally he needs help to generate some basic datasets so his team can easily inspect the data without needing to use SQL.

## Skills showcased
- **Data Wrangling and Preparation**
	- Created and managed relational database schemas.
	- Performed joins, data cleaning, and preparation to integrate the <code>sales</code>, <code>menu</code>, and <code>members</code> datasets.
- **Analytical Problem-Solving**
	- Addressed 10 business-critical questions, translating Danny’s business challenges into SQL-based solutions.
	- Delivered actionable insights to improve customer engagement and loyalty strategies.
- **Advanced SQL Techniques**
	- Utilized CTEs to break down queries into simpler, more manageable tasks.
	- Applied window functions (DENSE_RANK, RANK) to analyze customer purchasing trends and loyalty behaviors.
	- Designed conditional logic with CASE statements for customized calculations like loyalty points allocation.
- **Data Visualization and Interpretation**
	- Structured results in readable formats to support decision-making.
	- Summarized and presented key findings in a business-friendly manner for stakeholders.
- **Customer Behavior Analytics**
	- Analyzed customer purchase patterns, frequency, and spending before and after joining the loyalty program.
	- Offered insights to optimize the loyalty program, including points calculation and item popularity ranking.

## Dataset Overview
Danny has shared with you 3 key datasets for this case study:
- <code>sales</code>
- <code>menu</code>
- <code>members</code>
  
You can inspect the entity relationship diagram below. <br>
![Tables_relationship_diagram](https://github.com/fadalid/SQLchallenge-DannysDiner/blob/main/relationship-diagram.png?raw=true)

### dannys_diner database schema

<details>
	<summary>
 		Creating the schema, <code>sales</code>, <code>menu</code> and <code>members</code> tables
	</summary>

```SQL

CREATE SCHEMA dannys_diner;
SET search_path = dannys_diner;

```

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

## Case Study Questions & Solutions

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

**Output** 
| customer_id | total_amount |
| ----------- | ------------ |
| C           | 36           |
| B           | 74           |
| A           | 76           |

**Insight** From here we can spot our best customers, working on offer them benefits to furter assure fidelity. Also we can draw more data on the customers that spent the less to come up with new sales strategies.

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

**Output** 
| customer_id | days_visited |
| ----------- | ------------ |
| A           | 4            |
| B           | 6            |
| C           | 2            |

**Insight** To write down

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

**Output** 
| customer_id | product_name |
| ----------- | ------------ |
| A           | curry        |
| A           | sushi        |
| B           | curry        |
| C           | ramen        |

**Insight** Here we can see wich products have a better response on customers based on first impressions.

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

**Output**
| customer_id | product_name | item_purchases |
| ----------- | ------------ | -------------- |
| A           | ramen        | 3              |
| B           | ramen        | 2              |
| C           | ramen        | 3              |

**Insight** This query clearly shows us the best selling product that's on the menu.

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

**Output**
| customer_id | product_name | item_purchases |
| ----------- | ------------ | -------------- |
| C           | ramen        | 3              |
| A           | ramen        | 3              |
| B           | curry        | 2              |
| B           | sushi        | 2              |
| B           | ramen        | 2              |
| A           | curry        | 2              |
| A           | sushi        | 1              |

**Insight** This query shows us product's trends among customers.

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

**Output**
| customer_id | product_name | order_date               | first_member_purchase |
| ----------- | ------------ | ------------------------ | --------------------- |
| B           | sushi        | 2021-01-11T00:00:00.000Z | 1                     |
| A           | curry        | 2021-01-07T00:00:00.000Z | 1                     |

**Insights** This query allow us to see if there was any changes on customers purchases before and after becoming members.

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

**Output**
| customer_id | product_name | order_date               |
| ----------- | ------------ | ------------------------ |
| A           | curry        | 2021-01-01T00:00:00.000Z |
| A           | sushi        | 2021-01-01T00:00:00.000Z |
| B           | sushi        | 2021-01-04T00:00:00.000Z |

**Insight** Combining this query with the previous one we have a more clear view of each customer purchases' tendencies.

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

**Output**
| customer_id | total_items | total_amount |
| ----------- | ----------- | ------------ |
| B           | 3           | 40           |
| A           | 2           | 25           |

**Insight** 

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

**Output**
| customer_id | points |
| ----------- | ------ |
| C           | 360    |
| A           | 860    |
| B           | 940    |

**Insight** 

---
*10. In the first week after a customer joins the program (including their join date) they earn 2x points on all items, not just sushi - how many points do customer A and B have at the end of January?*
<details>
	<summary>
		SQL Query
	</summary>

```SQL
SELECT sales.customer_id,
	SUM(
		CASE WHEN order_date >= join_date
		AND order_date < (join_date +7) THEN price*20
		WHEN sales.product_id = 1 THEN price*20
		ELSE price*10 END) AS points
FROM dannys_diner.sales
JOIN dannys_diner.menu
	ON sales.product_id = menu.product_id
JOIN dannys_diner.members
	ON sales.customer_id = members.customer_id
WHERE order_date <= '2021-01-31'
GROUP BY sales.customer_id;

```
</details>

**Output**
| customer_id | points |
| ----------- | ------ |
| A           | 1370   |
| B           | 820    |

**Insight** 

---
*Bonus Question #1*
Recreate the following table output using the available data:

| customer_id | order_date | product_name | price | member |
| ----------- | ---------- | ------------ | ----- | ------ |
| A           | 2021-01-07 | curry        | 15    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-11 | ramen        | 12    | Y      |
| A           | 2021-01-10 | ramen        | 12    | Y      |
| A           | 2021-01-01 | sushi        | 10    | N      |
| A           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-04 | sushi        | 10    | N      |
| B           | 2021-01-11 | sushi        | 10    | Y      |
| B           | 2021-01-01 | curry        | 15    | N      |
| B           | 2021-01-02 | curry        | 15    | N      |
| B           | 2021-01-16 | ramen        | 12    | Y      |
| B           | 2021-02-01 | ramen        | 12    | Y      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-01 | ramen        | 12    | N      |
| C           | 2021-01-07 | ramen        | 12    | N      |

<details>
	<summary>
		SQL Query
	</summary>

```SQL

SELECT sales.customer_id, order_date, product_name, price,
	CASE
	WHEN members.join_date > sales.order_date THEN 'N'
	WHEN members.join_date <= sales.order_date THEN 'Y'
	ELSE 'N'
	END AS member
FROM dannys_diner.sales
JOIN dannys_diner.menu 
	ON sales.product_id = menu.product_id
LEFT JOIN members
	ON sales.customer_id = members.customer_id;

```
</details>

---
*Bonus Question #2*
Danny also requires further information about the ranking of customer products, but he purposely does not need the ranking for non-member purchases so he expects null ranking values for the records when customers are not yet part of the loyalty program.
<details>
	<summary>
		SQL Query
	</summary>

```SQL

WITH clients_table AS 
	(
	SELECT sales.customer_id, order_date, product_name, price,
		CASE
		WHEN join_date > order_date THEN 'N'
		WHEN join_date <= order_date THEN 'Y'
		ELSE 'N' END AS member
   	FROM dannys_diner.sales
	JOIN dannys_diner.menu
    		ON sales.product_id = menu.product_id
	LEFT JOIN dannys_diner.members
      		ON sales.customer_id = members.customer_id
)

SELECT *, 
	CASE
	WHEN member = 'N' then NULL
	ELSE
    	RANK () OVER(PARTITION BY customer_id, member
    	ORDER BY order_date) END AS ranking
FROM clients_table;

```
</details>

**Output**
| customer_id | order_date | product_name | price | member | ranking |
| ----------- | ---------- | ------------ | ----- | ------ | ------- |
| A           | 2021-01-01 | sushi        | 10    | N      |         |
| A           | 2021-01-01 | curry        | 15    | N      |         |
| A           | 2021-01-07 | curry        | 15    | Y      | 1       |
| A           | 2021-01-10 | ramen        | 12    | Y      | 2       |
| A           | 2021-01-11 | ramen        | 12    | Y      | 3       |
| A           | 2021-01-11 | ramen        | 12    | Y      | 3       |
| B           | 2021-01-01 | curry        | 15    | N      |         |
| B           | 2021-01-02 | curry        | 15    | N      |         |
| B           | 2021-01-04 | sushi        | 10    | N      |         |
| B           | 2021-01-11 | sushi        | 10    | Y      | 1       |
| B           | 2021-01-16 | ramen        | 12    | Y      | 2       |
| B           | 2021-02-01 | ramen        | 12    | Y      | 3       |
| C           | 2021-01-01 | ramen        | 12    | N      |         |
| C           | 2021-01-01 | ramen        | 12    | N      |         |
| C           | 2021-01-07 | ramen        | 12    | N      |         |

##Insights & Recommendations

##Tools & Technologies Used
- SQL (for querying and analysis)
- Tableau (for visualization)
- GitHub (for documentation)

##Future Improvements
- Develop a dashboard in Tableau for real-time insights.
