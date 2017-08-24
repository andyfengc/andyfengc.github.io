---
layout: post
title: SQL Server Reporting Services Tutorial 3 - Create more reports
author: Andy Feng
---

## Create a dropdown report ##

Let's create a dropdown report including date, order_no, amount, quantity. We can drill down the date via year/quarter/month/day and view special orders

### Prepare sql query ###

1. create query

>
	select 
		year(o.created_time) as year,
		datepart(quarter, o.created_time) as quarter,
		month(o.created_time) as month,
		day(o.created_time) as day,
		o.purchase_no, 
		o.amount_paid_value as amount, 
		t.quantity_purchased as quantity
		from ...
>

1. try it

![](/images/posts/20170817-ssrs-dropdown-1.png)

## Create dropdown chart ##

open visual studio > create a brand new report project or add a new report

![](/images/posts/20170815-vs-demo-2.png)

Create a new data source or use a shared one

![](/images/posts/20170815-vs-demo-3.png)

![](/images/posts/20170815-vs-demo-4.png)

![](/images/posts/20170815-vs-demo-6.png)

Here, we want the report has year as the header, then drill down through quarter/month/day, finally has purchase_no, amount, quantity as the details.

![](/images/posts/20170817-ssrs-dropdown-2.png)

Enable subtotals and drilldown

![](/images/posts/20170817-ssrs-dropdown-3.png)

Next and create the report, here is the design view

![](/images/posts/20170817-ssrs-dropdown-4.png)

Switch to preview mode and preview

![](/images/posts/20170817-ssrs-dropdown-5.png)

### deploy the chart ###

solution exploer > right click the report > deploy

![](/images/posts/20170815-vs-demo-10.png)

Open browser > navigate to `http://localhost/Reports` > click the report

![](/images/posts/20170817-ssrs-dropdown-6.png)

## Create a matrix report ##

Let's create a new report which has year as the header, then drill down through quarter/month/day, finally has purchase_no, amount, quantity as the details.

1. prepare query

> 
	select 
	i.title as device,
	a.country_code,
	year(o.created_time) as year, 
	sum(t.quantity_purchased) as  quantity, 
	sum(t.transaction_price_value) as amount
	from ...
>

1. create query and please select metrix option in the wizard

	![](/images/posts/20170815-vs-demo-6.png)

1. Please select the column and row separately, here we leave country_code as a parameter

	![](/images/posts/20170817-ssrs-matrix-1.png)

1. save the report, here is the design view

	![](/images/posts/20170817-ssrs-matrix-2.png)

1. swith to preview mode

	![](/images/posts/20170817-ssrs-matrix-3.png)

1. right click the report to deploy and display it in the report website

## Customize matrix report ##

### Format ###

1. switch to design view, select the row we want to format, use the upper toolbar to adjust the font/alignment/...

  ![](/images/posts/20170817-ssrs-matrix-4.png)

1. right click the cell we want to format > textbox properties > number > change to currency

  ![](/images/posts/20170817-ssrs-matrix-5.png)

  ![](/images/posts/20170817-ssrs-matrix-6.png)  

### Add parameter ###

1. add a new parameter

report data pane > parameters > right click to add a new parameter

  ![](/images/posts/20170817-ssrs-matrix-7.png)

  ![](/images/posts/20170817-ssrs-matrix-8.png)

1. add new dataset as the source of parameter dropdown selector

report data pane > dataset > right click to add

  ![](/images/posts/20170817-ssrs-matrix-9.png)

  ![](/images/posts/20170817-ssrs-matrix-10.png)

1. double click the new paramter to edit properties

  ![](/images/posts/20170817-ssrs-matrix-11.png)

  ![](/images/posts/20170817-ssrs-matrix-12.png)

1. double click the previous dataset to use the new added parameter as the filter

  ![](/images/posts/20170817-ssrs-matrix-13.png)

1. switch to preview mode, now the interactive selector works! select the country and click the View Report button to get different reports

  ![](/images/posts/20170817-ssrs-matrix-14.png)

## Create drillthrough report ##

### Create a child report with parameter ###

1. Prepare query

>
	select
	o.kobo_purchase_order_no as purchase_no
	, i.title as device
	, v.sku 
	, t.quantity_purchased as quantity
	, t.transaction_price_value as cost
	, o.created_time
	, a.country_code as country
	, a.city_name as city
	
	from ...
>

1. create a new report by wizard > tabular > leave device, country as parameters > finish to save the report

![](/images/posts/20170817-ssrs-drillthrough-1.png)

### Create parameters ###

1. create two parameters, hide these parameters

	![](/images/posts/20170817-ssrs-drillthrough-2.png)
	
	![](/images/posts/20170817-ssrs-drillthrough-5.png)

1. open the dataset properties > modify query 

	![](/images/posts/20170817-ssrs-drillthrough-3.png)

	> 
		...
		where 
			i.title = @device 
			and a.country_code = @country
	>

### Link parent report with child report ###

1. switch to parent report > right click device > textbox properties

	![](/images/posts/20170817-ssrs-drillthrough-4.png)

1. switch to action tab > goto report > specify the child report > add two parameters

	![](/images/posts/20170817-ssrs-drillthrough-6.png)

1. parent report > device cell > textbox properties > change font

	![](/images/posts/20170817-ssrs-drillthrough-4.png)

	![](/images/posts/20170817-ssrs-drillthrough-7.png)

1. switch to preview mode

	select a device in the parent chart

	![](/images/posts/20170817-ssrs-drillthrough-8.png)

	navigate to child report

	![](/images/posts/20170817-ssrs-drillthrough-9.png)