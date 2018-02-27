---
layout: post
title: SQL Tips - over, partition by, row_number and rank
author: Andy Feng
---

Here are some tips of SQL functions and  all scripts passed under SQL Server 2012.

## Prerequisite ##

Let's assume we have a [Order] table:

	create table [dbo].[Order](
		Id int identity(1,1)
		, purchase_order_no nvarchar(50)
		, buyer_user_id nvarchar(50)
		, amount_paid float
		, order_status nvarchar(50)
		, primary key (Id)
	)

Then populate with some data.

## OVER() ##

OVER allows us to get aggregate information without using GROUP BY. It can by used to retrieve details rows and get aggregate data alongside it.

e.g.

	SELECT sum(amount_paid) over()
	, purchase_order_no
	, buyer_user_id
	, order_status
	FROM [dbo].[Order]

![](/images/posts/20171017-sql-1.png)

## OVER(PARTITION BY) ##

OVER() is used exposes the entire resultset to the aggregation. However, we can break up the resultset into logical partitions and aggregate data within each partition via PARTITION BY

e.g.

	SELECT sum(amount_paid) over(partition by purchase_order_no)
	, purchase_order_no
	, buyer_user_id
	, order_status
	FROM [dbo].[Order]

![](/images/posts/20171017-sql-2.png)

It is equivalent to

	SELECT sum(amount_paid)
	, purchase_order_no
	, buyer_user_id
	, order_status
	FROM [dbo].[Order]
	group by purchase_order_no, buyer_user_id, order_status

## ROW_NUMBER() ##

ROW_NUMBER is a ranking function and it generates a run-time column in the resultset which generates sequential number to each row. We often use the ROW_NUMBER function to produce the specific reports.

Here is the syntax:

	ROW_NUMBER() 
	OVER (PARTITION BY col1, col2, ....n ORDER BY col1, col2, ....n)

Please note:

1. ORDER BY is mandatory. ROW_NUMBER is generated in the resultset based on the column provided in ORDER BY clause
1. PARTITION BY is optional which groups the resultset based on the column provided in PARTITION BY clause, the sequence starts with 1.
1. The sequence number starts with 1 within each partition

e.g.

	SELECT ROW_NUMBER() OVER(partition by order_status order by order_status)
	, purchase_order_no
	, buyer_user_id
	, order_status
	FROM [dbo].[Order]

![](/images/posts/20171017-sql-3.png)

## RANK() ##

RANK() behaves like ROW_NUMBER(), except that “equal” rows are ranked the same.

e.g.

**ROW_NUMBER()**

	SELECT ROW_NUMBER() OVER(order by order_status)
	, order_status
	FROM [dbo].[Order]

![](/images/posts/20171017-sql-4.png)

Please note that here we do not specify PARTICION BY inside OVER(). Therefore, it is regarded as a single partition and the sequence number is increased all over the resultset.


**RANK()**

	SELECT RANK() OVER(order by order_status)
	, order_status
	FROM [dbo].[Order]

![](/images/posts/20171017-sql-5.png)

Please note that each unique value of row get the same rank. For duplicate values, the same ranking is assigned and a gap appears in the sequence for each duplicate ranking.

**DENSE_RANK()**

We can avoid those gaps by using DENSE_RANK()

	SELECT DENSE_RANK() OVER(order by order_status)
	, order_status
	FROM [dbo].[Order]

![](/images/posts/20171017-sql-6.png)

## PARTITION BY VS. GROUP BY ##

**GROUP BY**

GROUP BY is used for aggregation and works on the entire query. When a group by clause is used all the columns in the select list should either be in  group by or should be in an aggregate function. 

e.g.

	SELECT order_status, count(*) as order_count
	FROM [dbo].[Order]
	GROUP BY order_status

![](/images/posts/20171017-sql-7.png)

**PARTITION_BY**

PARTITION BY works like a window function. With it, aggregation or row number functions don't have the restriction of GROUP BY and they can be calculated alongwith other columns in the select list. Specifically,  PARTITION BY can be used to aggregate together with RANK(), or get sequential numbers together with ROW_NUMBER()/RANK(). 

	SELECT ROW_NUMBER() OVER(partition by order_status order by order_status)
	, order_status
	FROM [dbo].[Order]

![](/images/posts/20171017-sql-8.png)