---
layout: post
title: SQL Server Reporting Services Tutorial 1 - Introduction
author: Andy Feng
---

## Introduction ##

SQL Server Reporting Services (SSRS) is a server-based report generating software by Microsoft. It is part of suite of Microsoft SQL Server services. Basically, it can be used to prepare and deliver a variety of interactive and printed reports with a Web interface. It provides an interface into <code>Microsoft Visual Studio</code> so that developers can connect to SQL databases and use SSRS tools to format SQL reports in many complex ways.

The complete solution includes SQL Server Reporting Services (server), SQL Server Report Builder (report creation) and Visual Studio (report customize and development). 

## Install SQL Server Reporting Services ##

SQL Server Reporting Services (SSRS) is installed together with SQL Server enterprise. [Here](https://docs.microsoft.com/en-us/sql/reporting-services/install-windows/install-reporting-services-native-mode-report-server) is the detailed steps to install and configure. 

![](/images/posts/20170814-install-ssrs.png)

After installing, the url of web interface will be `http://ServerUrl/Reports`

## Install Visual Studio ##

Please check [here](https://docs.microsoft.com/en-us/visualstudio/install/install-visual-studio) for details.

## Install SQL Server Report Builder ##

Download SQL Server Report Builder at [here](https://www.microsoft.com/en-us/download/details.aspx?id=53613)

run installer file

![](/images/posts/20170814-install-ssrb-1.png)

Next, enter target server url if already have

![](/images/posts/20170814-install-ssrb-2.png)

Next to start install

## Manage SQL Server Reporting Services ##

1. Open SSRS web portal, `http://ServerUrl/Reports`

![](/images/posts/20170814-ssrs-1.png)

1. enter Data Sources folder, upload new rds file of datasource or create a new datasource

![](/images/posts/20170814-ssrs-create-new-datasource.png)

1. Up to the root, create a new Reports folder to save reports. Then, upload rdl files or create new reports.

## Create new reports ##

New > Paginated Report, it will prompt us to open Report Builder tool

![](/images/posts/20170814-ssrs-create-new-report-1.png)

Enter SSRS server url, connect

![](/images/posts/20170814-ssrs-create-new-report-2.png)

Connected successfully

![](/images/posts/20170814-ssrs-create-new-report-3.png)

New > Table or Matrix Wizard > create a dataset

![](/images/posts/20170814-ssrs-create-new-report-4.png)

![](/images/posts/20170814-ssrs-create-new-report-5.png)

click build to create a new connection string

![](/images/posts/20170814-ssrs-create-new-report-6.png)

Next > select a table,

![](/images/posts/20170814-ssrs-create-new-report-7.png)

select some fields

![](/images/posts/20170814-ssrs-create-new-report-8.png)

Next to preview and save the report

![](/images/posts/20170814-ssrs-create-new-report-9.png)

Click Run button to get the report

![](/images/posts/20170814-ssrs-create-new-report-10.png)

![](/images/posts/20170814-ssrs-create-new-report-11.png)