---
layout: post
title: Power BI tutorial - FAQ
---

# format date
way1:
=FormatDateTime(Fields!due_date.Value, DateFormat.ShortDate)

way2:
=Format(Fields!etd.Value, "M/d/yyyy")
[https://learn.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings](https://learn.microsoft.com/en-us/dotnet/standard/base-types/custom-date-and-time-format-strings)

# show image
In Power BI, images can be stored in three different locations:
- Within the report (embedded) - not recommended
	![[images/posts/20240221-powerbi-1.jpg]]
	![[images/posts/20240221-powerbi-2.jpg]]
- On a web server (external url)
	![[images/posts/20240221-powerbi-5.jpg]]
- In a database, which can be retrieved by a dataset
	-if we use base64
	![[images/posts/20240221-powerbi-3.jpg]]
	![[images/posts/20240221-powerbi-4.jpg]]
And images can be used in a variety of scenarios in report layouts:
- Free-standing logo, or picture
- Pictures associated with rows of data
- Background for certain report items:
    - Report body
    - Textbox
    - Rectangle
    - Tablix data region (table, matrix, or list)

# The tablix 'TablixXX' has a detail member with inner members. Detail members can only contain static inner members.
This issue happens when a tablix with group was added to another tablix with group. e.g.
![[images/posts/20240222-powerbi-10.jpg]]
Please note we can only have one detail group in one tablix. The detail group will be releated once for each row returned by your dataset. It doesn't make sense that a tablix contains repeated rows again within that row for the same dataset.
2 solutions:
1. combine your two tablixes into one, don't nest one in the other.
2. only keep one group row

# How to show HTML formated content in report
We can display HTML content by selecting 'HTML-Interpret html tags as styles' present in placeholder properties.

But most of the styles present in your HTML code not supported by SSRS/Power BI. By default, SSRS/Power BI supports only few HTML tags and CSS styles. You can find them in this link.

[Importing HTML into a paginated report (Report Builder)](https://learn.microsoft.com/en-us/sql/reporting-services/report-design/importing-html-into-a-report-report-builder-and-ssrs?view=sql-server-ver16_)

# How to embed a report to web page
[Embed a report in a secure portal or website](https://learn.microsoft.com/en-us/power-bi/collaborate-share/service-embed-secure)
# Reference
[SSRS Detail members can only contain static inner members](https://stackoverflow.com/questions/7800217/ssrs-detail-members-can-only-contain-static-inner-members)