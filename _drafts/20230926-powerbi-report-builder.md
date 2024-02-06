---
layout: post
title: Power BI tutorial - Report Builder
---

# Install Report builder
download and install [Power BI report builder](https://www.microsoft.com/en-US/download/details.aspx?id=58158)

![](/images/posts/20230831-powerbi-1.jpg)

# Workflow

1. create datasource

	datasource is just database connection 

![](/images/posts/20230926-powerbi-1.jpg)

1. create dataset

	dataset is collection of tables/views on top of datasource

1. create parameters

	1. odbc single parameter
	
		create a dataset > select odbc data source > enter script > use ? to represent paramter > click validate query > click ok
		
		![](/images/posts/20231002-powerbi-9.jpg)
	
		a new parameter was automatically created > open to rename
		
		![](/images/posts/20231002-powerbi-10.jpg)
		
		![](/images/posts/20231002-powerbi-11.jpg)
	
		open the dataset > parameter tab > set parameter value with the new parameter
	
		![](/images/posts/20231002-powerbi-12.jpg)

	1. odbc multi parameters 

		create a dataset with multi-value param support

		![](/images/posts/20231002-powerbi-13.jpg)
			
			postgresql:
			select * from reporting.flight_legs
			where trip_id = ? 
			and leg_id in ( select cast(value as integer) from unnest(string_to_array(?, ',')) as value )
			
			sqlserver:
			select * from reporting.flight_legs
			where trip_id = ? 
			and leg_id in ( select value from string_split(?, ',') )
		
		Query designer > test > convert integer to string

		![](/images/posts/20231002-powerbi-14.jpg)

	1. odbc multi value parameter

		dataset > properties > parameters > fx > join them
		
		![](/images/posts/20231002-powerbi-15.jpg)

		parameter > set it as multi value

		![](/images/posts/20231002-powerbi-16.jpg)

		paramter > set available value from another dataset, UI only

		![](/images/posts/20231002-powerbi-19.jpg)
		
		Query designer > test

		![](/images/posts/20231002-powerbi-17.jpg)

		![](/images/posts/20231002-powerbi-18.jpg)

		![](/images/posts/20231002-powerbi-20.jpg)
			
1. build report
	

1. publish report


# Reference

[Paginated Reports in Power BI - Beginner Tutorials](https://www.youtube.com/playlist?list=PLx7LcKtN_gq-JVzM6L8xNNxX7kts-KflJ)

[Get started with Power BI Paginated Reports](https://www.youtube.com/playlist?list=PLv2BtOtLblH1DC4XPMeuCFzQp_-EBu1iG)

[12 Days of Paginated Reports](https://www.youtube.com/playlist?list=PLclDw3xU_tI5bypr74FnLuLGTyuTfKpV1)

[Power BI Paginated Reports in a Day course](https://learn.microsoft.com/en-us/power-bi/learning-catalog/paginated-reports-online-course)

[Multi-value Parameter in ODBC Power BI Paginated Reports 1/3](https://www.youtube.com/watch?v=BZb3G9ID5Nk)

[Multi-value Parameter in ODBC Power BI Paginated Reports 2/3](https://www.youtube.com/watch?v=a4Frj8vG4as)

[Multi-value Parameter in ODBC Power BI Paginated Reports 3/3](https://www.youtube.com/watch?v=N-FmK3ZeiNk)

[Multi-value Parameter in ODBC Power BI Paginated Reports](https://www.youtube.com/watch?v=cldsI8b4UIs&t=1s)


