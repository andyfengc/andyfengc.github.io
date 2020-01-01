---
layout: post
title: ExpressVPN tutorial
author: Andy Feng
---

# Introduction #
Log Parser Studio is a utility designed for analyzing IIS web server logs. It can parse various flat file formats using an SQL like query language.

Some of its features include:

- Ability to parse a variety of log file formats including, W3SVC/IIS, CSV, HTTP etc.
- Query log files using an SQL like query language
- Generate reports
- Generate a PowerShell script containing your query.

# Installation #
1. Download and install the Log Parser framework from 

	[[https://technet.microsoft.com/en-au/scriptcenter/dd919274.aspx](https://technet.microsoft.com/en-au/scriptcenter/dd919274.aspx)](http://www.microsoft.com/en-us/download/details.aspx?displaylang=en&id=24659)

1. Download and run the Log Parser Studio GUI from 

	[https://gallery.technet.microsoft.com/Log-Parser-Studio-cd458765](https://gallery.technet.microsoft.com/Log-Parser-Studio-cd458765)

1. Open Log Parser studio > it shows some parsing templates

	![](/images/posts/20191009-log-1.png)

# Steps

1. toolbar > choose log file > import the log files need to be parsed

	![](/images/posts/20191009-log-2.png)
	
1. Create a NEW QUERY, or we can simple select a query template

	create a customized query

	![](/images/posts/20191009-log-3.png)

	create a query by template

	![](/images/posts/20191009-log-4.png)

1. write customized query

 	we can use some predefined variables in standard IIS log specification:

	![](/images/posts/20191009-log-7.png)

	sample queries to get counts of a url:

		select count(*) as requestcount from '[LOGFILEPATH]' where cs(Referer) like '%/some-url%' 
		select count(*) as requestcount from '[LOGFILEPATH]' where cs-uri-stem like '%/some-url/assets%' 

	> pls note `[LOGFILEPATH]` is a variable and it refers the imported log files
	
1. Specify the types of log files. e.g. IISWECLOG for *.log files >  F5 to execute the query 

	![](/images/posts/20191009-log-5.png)

	we can also generate chart

	![](/images/posts/20191009-log-6.png)

# References
[Log Parser Studio](https://gallery.technet.microsoft.com/Log-Parser-Studio-cd458765)

[How to Parse IIS Logs using Log Parser Studio](https://www.shanebart.com/parsing-iis-logs-using-log-parser-studio/)

[Introducing: Log Parser Studio](https://techcommunity.microsoft.com/t5/Exchange-Team-Blog/Introducing-Log-Parser-Studio/ba-p/601131)