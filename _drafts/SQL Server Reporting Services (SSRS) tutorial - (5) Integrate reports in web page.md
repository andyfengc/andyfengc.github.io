---
layout: post
title: SQL Server Reporting Services Tutorial 5 - Integrate reports in web page
author: Andy Feng
---

## Prepare report(s)/chart(s) ##

Report1: 

![](/images/posts/20170818-ssrs-report-1.png)

Report2: 

![](/images/posts/20170818-ssrs-report-2.png)

## Integrate reports - way1: use iframe ##

find the report file at `http://localhost/reportserver`

![](/images/posts/20170818-ssrs-reportserver.png)

open a report and get the url like 

`http://hostname/ReportServer/Pages/ReportViewer.aspx?%2fReports%2fEbay%2forder+summary&rs:Command=Render`

it will display a non-bordered report

![](/images/posts/20170818-ssrs-report-3.png)

Create a html page, add an iframe with this url

{% highlight html%}
	<!DOCTYPE html>
	<html>
	<head>
	    <title></title>
		<meta charset="utf-8" /> 
	</head>
	<body>
	<iframe src="http://hostname/ReportServer/Pages/ReportViewer.aspx?%2fReports%2fEbay%2forder+summary&rs:Command=Render" width="800" height="400" frameborder="0"></iframe>
	</body>
	</html>
{% endhighlight %}

Open this page, the browser will load the report report

![](/images/posts/20170818-ssrs-report-4.png)

## Integrate reports - way2: use ReportViewer ##

### Create webform project ###

Microsoft provides ReportView control only for webform project, therefore, we have to create a web form project

1. Install nuget lib `ReportViewerControl`

	![](/images/posts/20170818-ssrs-webform-2.png)

1. add a webform page

### configuration ###

1. edit web.config

>
	...
	<compilation debug="true" targetFramework="4.6.1">
      <buildProviders>
        <add extension=".rdlc" type="Microsoft.Reporting.RdlBuildProvider, Microsoft.ReportViewer.WebForms, Version=14.0.0.0, Culture=neutral, PublicKeyToken=89845DCD8080CC91" />
      </buildProviders>
      <assemblies>
        <add assembly="Microsoft.ReportViewer.Common, Version=14.0.0.0, Culture=neutral, PublicKeyToken=89845DCD8080CC91" />
        <add assembly="Microsoft.ReportViewer.WebForms, Version=14.0.0.0, Culture=neutral, PublicKeyToken=89845DCD8080CC91" />
      </assemblies>
    </compilation>
	 	<httpHandlers>
	      <add path="Reserved.ReportViewerWebControl.axd" verb="*" type="Microsoft.Reporting.WebForms.HttpHandler, Microsoft.ReportViewer.WebForms, Version=14.0.0.0, Culture=neutral, PublicKeyToken=89845DCD8080CC91" validate="false" />
	    </httpHandlers>
	</system.web>
	<system.webServer>
	    ...
	    <validation validateIntegratedModeConfiguration="false" />
	    <handlers>
	      <add name="ReportViewerWebControlHandler" preCondition="integratedMode" verb="*" path="Reserved.ReportViewerWebControl.axd" type="Microsoft.Reporting.WebForms.HttpHandler, Microsoft.ReportViewer.WebForms, Version=14.0.0.0, Culture=neutral, PublicKeyToken=89845dcd8080cc91" />
    	</handlers>
  	</system.webServer>
>

make sure remove previous reportviewer assemblies i.e. v12.x

### Add ReportViewer control ###

1. drop and drop ReportViewer control from toolbox, put it into `<form>...</form>`

	![](/images/posts/20170818-ssrs-webform-1.png)

1. add a ScriptManager control within `<form>...</form>`

1. Compile the project and open the web page

	![](/images/posts/20170818-ssrs-webform-3.png)

## Reference ##

[Integrating Reporting Services Using ReportViewer Controls - Get Started](https://docs.microsoft.com/en-us/sql/reporting-services/application-integration/integrating-reporting-services-using-reportviewer-controls-get-started)

[Get more done in the new Reporting Services web portal with SQL Server 2016 RC0](https://blogs.msdn.microsoft.com/sqlrsteamblog/2016/03/07/get-more-done-in-the-new-reporting-services-web-portal-with-sql-server-2016-rc0/)

[Report Viewer control update now available](https://blogs.msdn.microsoft.com/sqlrsteamblog/2016/11/30/report-viewer-2016-control-update-now-available/)

