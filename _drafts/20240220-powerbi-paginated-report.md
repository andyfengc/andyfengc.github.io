---
layout: post
title: Power BI tutorial - FAQ
---
# Introduction
Power BI Paginated report is for printing, or PDF generation. It produces highly formatted, print-ready layouts and ideal for operational reports, sales invoices, financial statement, inventory reports and more. They're called "paginated" because they're formatted to fit well on multiple pages. Paginated reports are based on the RDL report technology in SQL Server Reporting Services.
It is used in below scenarios:
- the report must be printed, or output as a PDF document.
- Data grid layouts could expand and must be printed in multiple pages. 
# Cascading parameters
[Use cascading parameters in paginated reports](https://learn.microsoft.com/en-us/power-bi/guidance/paginated-report-cascading-parameter)
[Add cascading parameters to a Power BI paginated report (Power BI Report Builder)](https://learn.microsoft.com/en-us/power-bi/paginated-reports/parameters/add-cascading-parameters-report-builder)
# Layout
## Page setup
Report page setup determines the page orientation, dimensions, and margins.
way1: Right-click the dark gray area outside the report canvas > Report Properties_.
![[20240221-powerbi-6.jpg]]
![[20240221-powerbi-8.png]]

way2: Click the dark gray area outside the report canvas  > open **Properties** pane
![[20240221-powerbi-7.jpg]]
## Fix blank page when printing
1. check if report body width exceeds the available page space
	click anywhere in empty area of report canvas
	![[20240221-powerbi-9.jpg]]
	![[20240221-powerbi-10.png]]
	body size determines the available space for report objects
	`body size + margin = page size`
	`body width + Left margin + Right margin <= page width`
1. check if there's excess space in the report body, after the last object. always reduce the height of the body to remove any trailing space
	![[20240221-powerbi-11.png]]
1. disable page break option of the property of last item just before blank page
	Each data region and data visualization has page break options. You can access these options in its property page, or in the **Properties** pane
	![[20240221-powerbi-12.png]]	
	Ensure the **Add a page break after** property is disabled..
	1. disable Consume Container Whitespace of report property
		right click outside gray area > properties pane
		![[20240221-powerbi-6.jpg]]
		![[20240221-powerbi-13.png]]	
		By default, it's enabled. It directs whether minimum whitespace in containers, such as the report body or a rectangle, should be consumed. Only whitespace to the right of, and below, the contents is affected.
## Summary
set page size 8.5in x 11in
set 0 for all 4 of margins
set size of body is 8in x 10.5in
keep all items in one page if possible
set row group/tablix properties > uncheck page break option
set `ConsumeContainerWhitespace` to `True`
set `Keep Together` to false
# migrate .rdl reports to Power BI
[Plan to migrate .rdl reports to Power BI](https://learn.microsoft.com/en-us/power-bi/guidance/migrate-ssrs-reports-to-power-bi)
[Publish .rdl files to Power BI from Power BI Report Server and Reporting Services](https://learn.microsoft.com/en-us/power-bi/guidance/publish-reporting-services-power-bi-service?tabs=reporting-services)
[Find and retire unused .rdl reports](https://learn.microsoft.com/en-us/power-bi/guidance/retire-unused-reports-ssrs)
# Reference
[When to use paginated reports in Power BI](https://learn.microsoft.com/en-us/power-bi/guidance/report-paginated-or-power-bi)
[Avoid blank pages when printing paginated reports](https://learn.microsoft.com/en-us/power-bi/guidance/report-paginated-blank-page)
[SSRS 2008 R2 creating random blank pages on report? (2 Solutions!!)](https://www.youtube.com/watch?v=10SqJ_73_Aw)

[Add a page break to a paginated report (Report Builder)](https://learn.microsoft.com/en-us/sql/reporting-services/report-design/add-a-page-break-report-builder-and-ssrs?view=sql-server-ver16)

[Nested data regions in a paginated report (Report Builder)](https://learn.microsoft.com/en-us/sql/reporting-services/report-design/nested-data-regions-report-builder-and-ssrs?view=sql-server-ver16)