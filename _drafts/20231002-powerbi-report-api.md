---
layout: post
title: Power BI tutorial - Report Builder
---

# Setup environment
## Setup Power BI workspace with a report
Power BI keeps your reports, dashboards, and tiles in a workspace. To embed these items, you'll need to create them and upload them into a workspace.

Create a Power BI workspace. Then, create a report and publish it to your workspace. 

![](/images/posts/20231002-powerbi-1.jpg)

## Setup Azure Active Directory tenant
After you sign in to the Azure portal, you can create a new tenant for your organization. Your new tenant represents your organization and helps you to manage Microsoft cloud services

![](/images/posts/20231002-powerbi-2.jpg)

## Register an Azure Active Directory (Azure AD) application in Azure
[Set up your Power BI embedding environment](https://app.powerbi.com/embedsetup)

![](/images/posts/20231002-powerbi-3.png)

![](/images/posts/20231002-powerbi-4.jpg)

## Get application ID
Log into Microsoft Azure > App registrations

![](/images/posts/20231002-powerbi-5.jpg)

## Get Tenant ID
Sign in to the Azure portal > Select Azure Active Directory(Microsoft Entra ID)
![](/images/posts/20231002-powerbi-6.jpg)

or 

Log into Microsoft Azure > App registrations > overview

![](/images/posts/20231002-powerbi-7.jpg)

## Get Workspace ID
Sign in to Power BI service.

Open the report you want to embed.

Copy the GUID from the URL. The GUID is the number between /groups/ and /reports/.

![](/images/posts/20231002-powerbi-6.png)

## Get Report ID
Sign in to Power BI service.

Open the report you want to embed.

Copy the GUID from the URL. The GUID is the number between /reports/ and /ReportSection.

![](/images/posts/20231002-powerbi-7.jpg)

## Get Client ID and Client Secret
Log into Microsoft Azure > App registrations > Certificates & secrets

![](/images/posts/20231002-powerbi-8.jpg)

# C# program
Add `Microsoft.Identity.Web`, and `Microsoft.PowerBI.Api` NuGet packages to your app.

Add the NuGet packages listed below to your app:

1. In VS Code, open a terminal and type in the code below.

1. In Visual studio, navigate to Tools > NuGet Package Manager > Package Manager Console and type in the code below.

		dotnet add package Microsoft.Identity.Web
		dotnet add package Microsoft.Identity.Web.UI
		dotnet add package Microsoft.PowerBI.Api

## Restful API
[https://learn.microsoft.com/en-us/power-bi/developer/embedded/embed-organization-app](https://learn.microsoft.com/en-us/power-bi/developer/embedded/embed-organization-app#configure-your-startup-file-to-support-microsoftidentityweb)

# Reference
[Create an Azure Active Directory tenant to use with Power BI](https://learn.microsoft.com/en-us/power-bi/developer/embedded/create-an-azure-active-directory-tenant)

[Quickstart: Create a new tenant in Microsoft Entra ID](https://learn.microsoft.com/en-us/azure/active-directory/fundamentals/create-new-tenant)

[Tutorial: Embed a Power BI report in an application for your organization](https://learn.microsoft.com/en-us/power-bi/developer/embedded/embed-organization-app)

[Using the Power BI REST APIs](https://learn.microsoft.com/en-us/rest/api/power-bi/)

[Tutorial: Embed Power BI content using a sample embed for your organization application](https://learn.microsoft.com/en-us/power-bi/developer/embedded/embed-sample-for-your-organization?tabs=net-core#configure-your-azure-ad-app)

[Getting Started With Power BI API (Set Up, Configuration, Examples)](https://www.youtube.com/watch?v=APj3MFt2w5I)

[https://github.com/microsoft/PowerBI-CSharp](https://github.com/microsoft/PowerBI-CSharp)

[Export paginated report to file](https://learn.microsoft.com/en-us/power-bi/developer/embedded/export-paginated-report)

[Reports - Export To File](https://learn.microsoft.com/en-us/rest/api/power-bi/reports/export-to-file)

[All About Multi Value Parameters in Power BI Paginated Report |](https://www.youtube.com/watch?v=cldsI8b4UIs)