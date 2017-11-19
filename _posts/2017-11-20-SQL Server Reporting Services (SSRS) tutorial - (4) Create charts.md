---
layout: post
title: SQL Server Reporting Services Tutorial 4 - Create charts
author: Andy Feng
---

## Create a chart ##
### Prepare query
>
	select 
		i.title as device,
		sum(t.quantity_purchased * transaction_price_value) as amount,
		round(sum(t.quantity_purchased * transaction_price_value) / sum(sum(t.quantity_purchased * transaction_price_value)) over(), 2) as percentage
	from ...
>

![](/images/posts/20170821-ssrs-chart-1.png)

### Create table

right click project > add report

![](/images/posts/20170815-vs-demo-2.png)

Create a new data source or use a shared one

![](/images/posts/20170815-vs-demo-3.png)

![](/images/posts/20170815-vs-demo-4.png)

![](/images/posts/20170815-vs-demo-6.png)

select fields

![](/images/posts/20170821-ssrs-chart-2.png)

Save and preview

![](/images/posts/20170821-ssrs-chart-3.png)

### Create chart ###

switch to design view, expand area

![](/images/posts/20170821-ssrs-chart-4.png)

right click > insert chart > piechart

![](/images/posts/20170821-ssrs-chart-5.png)

right click > set value

![](/images/posts/20170821-ssrs-chart-6.png)

preview the chart

![](/images/posts/20170821-ssrs-chart-7.png)

## Create drillthrough report ##

### prepare child chart ###

check tutorial 3 > Create drillthrough report > Create a child report with parameter

### Link chart with child report ###
switch to design view > right click the chart > show data labels

![](/images/posts/20170821-ssrs-chart-8.png)

right click the chart > series label properties > action > go to report > specify child report > add parameters

![](/images/posts/20170821-ssrs-chart-9.png)

swith to preview

![](/images/posts/20170821-ssrs-chart-10.png)

click the label and navigate to child report

![](/images/posts/20170817-ssrs-drillthrough-9.png)

### Customize chart ###
right click label > properties > number 

![](/images/posts/20170821-ssrs-chart-11.png)

right click legent > properties

![](/images/posts/20170821-ssrs-chart-12.png)

preview chart

![](/images/posts/20170821-ssrs-chart-13.png)

## Display multiple reports in a page ##

right click project > add item > report, add a blank report

![](/images/posts/20170821-ssrs-multireport-1.png)

add subreports via toolbox or right click > insert

![](/images/posts/20170821-ssrs-multireport-2.png)

![](/images/posts/20170821-ssrs-multireport-3.png)

right click subreport > properties > select a report

![](/images/posts/20170821-ssrs-multireport-4.png)

save and preview

![](/images/posts/20170821-ssrs-multireport-5.png)

or

![](/images/posts/20170821-ssrs-multireport-6.png)

