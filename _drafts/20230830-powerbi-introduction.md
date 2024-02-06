---
layout: post
title: Power BI tutorial
---

# Introduction
`Power BI` is a collection of software services, apps, and connectors that work together to turn your unrelated sources of data into interactive insights. Your data might be an Excel spreadsheet, or a collection of cloud-based and hybrid data warehouses. Power BI lets you easily connect to your data sources, visualize and discover the data.

# PowerBI structure
Power BI consists of 5 basic elements:

- A Windows desktop application called Power BI Desktop. always free.
	> Power BI Desktop is a free application you install on your local computer that lets you connect to, transform, and visualize your data. 
	> Most users who work on business intelligence projects use Power BI Desktop to create reports, and then use the Power BI service to share their reports with others.
	
- An online software as a service (SaaS) service called the Power BI service. It is Internet cloud service.

- Power BI Mobile apps for Windows, iOS, and Android devices.

- Power BI Report Builder, for creating paginated reports to share in the Power BI service.

- Power BI Report Server, an on-premises report server where you can publish your Power BI reports, after creating them in Power BI Desktop. It is local server and behind firewall.

![](/images/posts/20230831-powerbi-11.png)

In a typical Power BI workflow, you begin by building a report in Power BI Desktop, then publishing it to the Power BI service.

For different roles, they might use different PowerBI elements, for example:

> Manager might primarily use the Power BI service to view reports and dashboards
> Analyst or report creator might might make use Power BI Desktop or Power BI Report Builder to create reports, then publish those reports to the Power BI service
> Sales might mainly use the Power BI Mobile app to monitor progress on sales quotas, and to drill into new sales lead details.
> Software developer might use Power BI APIs to push data into datasets or to embed dashboards and reports into your own custom applications. 

![](/images/posts/20230831-powerbi-12.jpg)

## Power BI Desktop
Desktop is a complete data analysis and report creation tool that is used to connect to, transform, visualize, and analyze your data. 

The most common uses for Power BI Desktop are as follows:

- Connect to data.
- Transform and clean data to create a data model.
- Create visuals, such as charts or graphs that provide visual representations of the data.
- Create reports that are collections of visuals on one or more report pages.
- Share reports with others by using the Power BI service. Please note sharing reports requires a Power BI Pro license. 

Data analysts, business intelligence professionals, and report creators use Power BI Desktop to create compelling reports, or to pull data from various sources. They can build data models, and then share the reports with their coworkers and organizations.

There are 3 views in Power BI Desktop:

- Report: You create reports and visuals, where most of your creation time is spent.
- Data: You see the tables, measures, and other data used in the data model associated with your report, and transform the data for best use in the report's model.
- Model: You see and manage the relationships among tables in your data model.

![](/images/posts/20230831-powerbi-13.png)

1. Connect to data

	![](/images/posts/20230831-powerbi-14.png)

1. Create model

	Transform and clean data, create a model
	
	In Power BI Desktop, you can clean and transform data using the built-in `Power Query Editor`. With Power Query Editor, you make changes to your data, such as building queries, renaming columns or tables, removing rows or columns, changing data types, or combining data from multiple sources. . 
	
	![](/images/posts/20230831-powerbi-15.png)
	![](/images/posts/20230831-powerbi-18.png)

1. Create report

	After you have a data model, you can drag fields onto the report canvas to create visuals. A visual is a graphic representation of the data in your model.

	![](/images/posts/20230831-powerbi-16.png)

1. Share reports

	After a report is ready to share with others, you can publish the report to the Power BI service, and make it available to anyone in your organization who has a Power BI license.

	![](/images/posts/20230831-powerbi-17.png)

More details at [Get started with Power BI Desktop](https://learn.microsoft.com/en-us/power-bi/fundamentals/desktop-getting-started)

## Power BI Service
The Power BI service is a cloud-based service, or software as a service (SaaS). It supports report editing and collaboration for teams and organizations. 
> Power BI service is a collection of software services, apps, and connectors that work together to help you create, share, and consume business insights in the way that serves you and your business most effectively.

Power BI service requires different licenses - [Power BI licenses and subscriptions](https://learn.microsoft.com/en-us/power-bi/fundamentals/service-features-license-type)
# Install

download and install [Power BI report builder](https://www.microsoft.com/en-US/download/details.aspx?id=58158)

![](/images/posts/20230831-powerbi-1.jpg)

download and install [Power BI desktop](https://www.microsoft.com/en-US/download/details.aspx?id=58494)

![](/images/posts/20230831-powerbi-10.jpg)

create a powerbi account at [here](https://signup.microsoft.com/get-started/signup?sku=a403ebcc-fae0-4ca2-8c8c-7a907fd6c235&ru=https%3a%2f%2fapp.powerbi.com%3fpbi_source%3ddesktop%26redirectedFromSignup%3d1%26noSignUpCheck%3d1&products=a403ebcc-fae0-4ca2-8c8c-7a907fd6c235&brandingId=28b276fb-d2a0-4379-a7c0-57dce33da0f9&ali=1)

![](/images/posts/20230831-powerbi-11.jpg)

# Create a report
1. report builder > create a table

	![](/images/posts/20230831-powerbi-2.jpg)

	![](/images/posts/20230831-powerbi-5.jpg)
	
	![](/images/posts/20230831-powerbi-6.jpg)

1. publish

	![](/images/posts/20230831-powerbi-7.jpg)

1. open powerbi web > workspace > select the report

	![](/images/posts/20230831-powerbi-8.jpg)

	![](/images/posts/20230831-powerbi-9.jpg)
	
More details at [Get started creating in the Power BI service](https://learn.microsoft.com/en-us/power-bi/fundamentals/service-get-started)

# Reference
[Power BI get started documentation](https://learn.microsoft.com/en-us/power-bi/fundamentals/)

[Power BI Tutorial](https://www.youtube.com/playlist?list=PL6Omre3duO-OGTAMuFuDOS8wMuuxmyaiX)

[SSRS Report Builder Tutorial: Creating Your First Report](https://www.youtube.com/watch?v=b9bMB65oCes)

[How Power BI Desktop works](https://learn.microsoft.com/en-us/power-bi/fundamentals/desktop-getting-started)

[Microsoft Power BI](https://www.youtube.com/@MicrosoftPowerBI)

[12 days of Paginated Reports](https://www.youtube.com/playlist?list=PLclDw3xU_tI5bypr74FnLuLGTyuTfKpV1)