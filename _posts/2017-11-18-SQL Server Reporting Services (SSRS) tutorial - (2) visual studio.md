---
layout: post
title: SQL Server Reporting Services Tutorial 2 - Visual Studio
author: Andy Feng
---

## Install SQL Server Data Tools ##

SQL Server Data Tools (SSDT) is a development tool that we can used to build SQL Server relational databases, Azure SQL databases, Integration Services packages, Analysis Services data models, and Reporting Services reports. With SSDT, we can design and deploy any SQL Server content type with the same ease as you would develop an application in Visual Studio. 

Download it at [here](https://docs.microsoft.com/en-us/sql/ssdt/download-sql-server-data-tools-ssdt).

run the installer

![](/images/posts/20170815-ssdt-install-1.png)

![](/images/posts/20170815-ssdt-install-2.png)

## Create a report using Visual Studio ##

Open visual studio > create new project > templates > Business Intelligence > Reporting services > report server project wizard

![](/images/posts/20170815-vs-demo-1.png)

![](/images/posts/20170815-vs-demo-2.png)

![](/images/posts/20170815-vs-demo-3.png)

![](/images/posts/20170815-vs-demo-4.png)

![](/images/posts/20170815-vs-demo-5.png)

![](/images/posts/20170815-vs-demo-6.png)

![](/images/posts/20170815-vs-demo-7.png)

![](/images/posts/20170815-vs-demo-8.png)

![](/images/posts/20170815-vs-demo-9.png)

Now, we can format the report and review the report

## Deploy the report ##

project > reports folder > report > right click > deploy

![](/images/posts/20170815-vs-demo-10.png)

Reports website

`http://localhost/ReportServer`

![](/images/posts/20170815-vs-demo-22.png)

Reporting services manager

 `http://localhost/Reports`

![](/images/posts/20170815-vs-demo-23.png)

View the report: click the report, reporting services will access sql server and generate the report 

![](/images/posts/20170815-vs-demo-24.png)

### permission issue ###

if visual studio failed to deploy and we failed to access below urls:

1. reporting services manager

	![](/images/posts/20170815-vs-demo-12.png)

1. reports website	

	![](/images/posts/20170815-vs-demo-13.png)

open ie in administrator mode, open `http://localhost/Reports`

![](/images/posts/20170815-vs-demo-16.png)

![](/images/posts/20170815-vs-demo-17.png)

Folder settings > New Role Assignment > enter current username, select all > ok

![](/images/posts/20170815-vs-demo-18.png)

try open browser in normal mode, open `http://localhost/ReportServer`

![](/images/posts/20170815-vs-demo-19.png)

optional

> open Reporting Service Configuration Manager
>
> ![](/images/posts/20170815-vs-demo-14.png)
>
> ![](/images/posts/20170815-vs-demo-15.png)
> 
> switch to report manager url, can find the url of reporting manager
> 
> ![](/images/posts/20170815-vs-demo-21.png)

### compile issue 1 - compatibility ###

if we are using sql server 2012 and visual studio 2015, we might have compatible issue when deploying

![](/images/posts/20170815-vs-demo-11.png)

right click project properties > general > TargetServerVersion, select the right version

![](/images/posts/20170815-vs-demo-20.png) 

### compile issue 2 - datasource ###

if we have data source issue

![](/images/posts/20170815-vs-demo-25.png)

1. make sure we create datasource in report manager, the path should like 
	
	`<datasource folder name>/<datasource name>`

![](/images/posts/20170815-vs-demo-26.png)

right click project properties > modify TargetDatasourceFolder to the right `<datasource folder name> `