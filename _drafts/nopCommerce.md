---
layout: post
title: nopCommerce
author: Andy Feng
---

## Prepare environment ##

1. operating system windows 7+, sql server 2008+

1. control panel > program and features > turn windows feature on/off > IIS > Application Development Features > install all
	
	![](/images/posts/20180109-nopcommerce-1.png)

1. download and install .NET Core SDK 2.x, Windows Server Hosting (.exe) 2.x at [https://www.microsoft.com/net/download/windows](https://www.microsoft.com/net/download/windows)

	![](/images/posts/20180109-nopcommerce-2.png)

1. open iis > root > modules, check aspnetcore module

	![](/images/posts/20180109-nopcommerce-3.png)

## Install website without source code ##

1. download nopCommerce - Web (no source) at [https://www.nopcommerce.com/downloads.aspx](https://www.nopcommerce.com/downloads.aspx)

	![](/images/posts/20180109-nopcommerce-4.png)

1. IIS > application pools > add a new application pool > CLR version - No Managed Code

	![](/images/posts/20180109-nopcommerce-5.png)

1. unzip the dist files > IIS > sites > new a website > application pool is the new added one

	![](/images/posts/20180109-nopcommerce-6.png)

1. open browser > navigate to the new website

	![](/images/posts/20180109-nopcommerce-7.png)

1. sql server manage studio > create a new empty database

	![](/images/posts/20180109-nopcommerce-8.png)

1. return to website > enter database credentials, set admin password 

	![](/images/posts/20180109-nopcommerce-9.png)

	![](/images/posts/20180109-nopcommerce-10.png)

1. Test the website, login with admin password 

	![](/images/posts/20180109-nopcommerce-11.png)

	![](/images/posts/20180109-nopcommerce-12.png)

	![](/images/posts/20180109-nopcommerce-13.png) 

## Install the website with source code ##

1. Install visual studio 2017

1. download nopCommerce - source code at [https://www.nopcommerce.com/downloads.aspx](https://www.nopcommerce.com/downloads.aspx)

	![](/images/posts/20180109-nopcommerce-14.png)

1. zip and open the solution > nop.web project > App_Data > dataSettings.json > change the connection string. Here we assume the nopCommerce tables were already created. If not, follow the installation instruction as previous to initialize the database.

	![](/images/posts/20180109-nopcommerce-15.png) 

1. visual studio > set nop.web project as startup > run
	
	![](/images/posts/20180109-nopcommerce-11.png)
