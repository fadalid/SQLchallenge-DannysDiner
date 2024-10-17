# SQLchallenge-DannysDiner
📍 Case study #1 - https://8weeksqlchallenge.com/case-study-1/ by Data with Danny
<!-- insert logo img -->

## Table of contents
- [Introduction & Problem Statement](#introduction)
- [Skills showcased](#skills-showcased)
- [ASK -> Business task](#ask)
- [PREPARE](https://github.com/fadalid/SQLchallenge-DannysDiner#prepare)
- [PROCESS](https://github.com/fadalid/SQLchallenge-DannysDiner#process)
- [ANALYZE](https://github.com/fadalid/SQLchallenge-DannysDiner#analyze)
- [SHARE](https://github.com/fadalid/SQLchallenge-DannysDiner#share)
- [ACT](https://github.com/fadalid/SQLchallenge-DannysDiner#act)


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
- <code class="language-plaintext">sales</code>
- <code class="language-plaintext">menu</code>
- <code class="language-plaintext">members</code>
  
You can inspect the entity relationship diagram and example data below. <br>
<!-- insert diagram img -->

### dannys_diner database schema

```SQL

CREATE SCHEMA dannys_diner;
SET search_path = dannys_diner;

```

Creating the <code>sales</code> table 

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

Creating the <code>menu</code> table

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

Creating the <code>members</code> table

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

## PROCESS

## ANALYZE

### Case study questions

1. What is the total amount each customer spent at the restaurant?

```SQL

SELECT customer_id, SUM(price) AS total_amount
FROM dannys_diner.sales
JOIN dannys_diner.menu
ON sales.product_id = menu.product_id
GROUP BY customer_id
ORDER BY total_amount;

```

2. How many days has each customer visited the restaurant?

```SQL

SELECT customer_id, COUNT(DISTINCT order_date) AS days_visited
FROM dannys_diner.sales
GROUP BY customer_id;

```

3. What was the first item from the menu purchased by each customer?

```
