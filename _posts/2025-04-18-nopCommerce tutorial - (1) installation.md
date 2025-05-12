---
layout: post
title: nopCommerce tutorial (1) - installation and debug
author: Andy Feng
---

# Prepare environment 

1. operating system windows 10+, sql server 2022 developer+

2. control panel > program and features > turn windows feature on/off > IIS > Application Development Features > select all
	
	![[20250418-nop-1.jpg]]
3. For nopCommerce 4.80: Install .NET 9 runtime [Download .NET (Linux, macOS, and Windows)](https://dotnet.microsoft.com/en-us/download)
4. For nopCommerce 4.80 or above: MS Visual Studio 2022 (version 17.12 or above). install .NET 9 SDK ([download](https://dotnet.microsoft.com/en-us/download/dotnet/thank-you/sdk-9.0.100-windows-x64-installer))**
	![[20250418-nop-2.jpg]]

5. open iis > root > modules, check aspnetcore module

	![](/images/posts/20180206-aspnetcore-1.png)

	![](/images/posts/20180109-nopcommerce-3.png)

# Install website without source code 

1. download nopCommerce - Web (no source) at [https://www.nopcommerce.com/downloads.aspx](https://www.nopcommerce.com/downloads.aspx)

	![](/images/posts/20180109-nopcommerce-4.png)

1. Enable IIS at Settings > Windows feature on/off
2. IIS > application pools > add a new application pool > CLR version - No Managed Code

	![](/images/posts/20180109-nopcommerce-5.png)

3. unzip the dist files > IIS > sites > new a website > application pool is the new added one

	![](/images/posts/20180109-nopcommerce-6.png)

4. open browser > navigate to the new website

	![](/images/posts/20180109-nopcommerce-7.png)

5. sql server manage studio > create a new empty database

	![](/images/posts/20180109-nopcommerce-8.png)

6. return to website > enter database credentials, set admin password 

	![](/images/posts/20180109-nopcommerce-9.png)

	![](/images/posts/20180109-nopcommerce-10.png)

7. Test the website, login with admin password 

	![](/images/posts/20180109-nopcommerce-11.png)

	![](/images/posts/20180109-nopcommerce-12.png)

	![](/images/posts/20180109-nopcommerce-13.png) 

# Install the website with source code 

1. Install visual studio 2022

2. download nopCommerce - source code at [https://www.nopcommerce.com/downloads.aspx](https://www.nopcommerce.com/downloads.aspx)

	![](/images/posts/20180109-nopcommerce-14.png)

3. unzip and open the solution > nop.web project > App_Data > create dataSettings.json > change the connection string. 

		{
		  "DataProvider": "sqlserver",
		  "DataConnectionString": "Data Source=(local);Initial Catalog=nopCommerce;Integrated Security=true;Persist Security Info=False;",
		  "RawDataSettings": {}
		}

	![](/images/posts/20180109-nopcommerce-15.png) 

	Here we assume the nopCommerce database was already created. If not, follow the previous installation instruction to initialize the database tables.

	If you already have a exisitng database, please specify the proper connection string. 

4. visual studio > set nop.web project as startup > run
	
	![](/images/posts/20180109-nopcommerce-11.png)

Reference
https://docs.nopcommerce.com/en/installation-and-upgrading/technology-and-system-requirements.html#supported-web-servers


