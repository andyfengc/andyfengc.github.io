---
layout: post
title: nopCommerce tutorial (2) increase file upload size
author: Andy Feng
---

## Issue ##
nopCommerce throws exception when uploading a large file for downloadable products.

![](/images/posts/20180203-nopcommerce-file-1.png)

## Solution ##

Please modify web.config because nopCommerce uses .NET MVC

Please note that the default maximum filesize in II7 is 30MB. We need to update  'maxRequestLength' of 'httpRuntime' element in web.config. 
	
	<configuration>
	    <system.web>
			<!-- default is 4096 kbs or 4MB -->
			<!-- 1024 mb = 1048576 -->
			<!-- 50MB = 51200 -->
	        <httpRuntime maxRequestLength="1048576" executionTimeout="3600" />
	    </system.web>
	</configuration>

For IIS7 and above, we also need to add the lines below:

	 <system.webServer>
	   <security>
	      <requestFiltering>
			 <!-- default is 30000000 or approx. 28.6102 Mb -->
			 <!-- 1024 MB = 1073741824 -->
			 <!-- 50MB = 52428800 -->
	         <requestLimits maxAllowedContentLength="1073741824" />
	      </requestFiltering>
	   </security>
	 </system.webServer>

Here is the sample web.config:

![](/images/posts/20180203-nopcommerce-file-2.png)

Please note both of these values must match. But keep in mind `maxAllowedContentLength` is measured in bytes while `maxRequestLength` is measured in kilobytes. In this case, the max upload(maxRequestLength) is 50mb = 51200kb, and maxAllowedContentLength has 52428800 bytes.

## .NET Core solution ##
In .NET Core, it might throw "Multipart body length limit 134217728 exceeded" exception. It is related to the ASP.NET Core specificity and not relevant to front-end uploader.

Please fix .net core web project > Startup.cs

	public IServiceProvider ConfigureServices(IServiceCollection services)
	    {
	        services.AddMvc();
	        services.Configure<FormOptions>(o => {
	            o.ValueLengthLimit = int.MaxValue;
	            o.MultipartBodyLengthLimit = int.MaxValue;
	            o.MemoryBufferThreshold = int.MaxValue;
	        });
	
	        return services.ConfigureApplicationServices(Configuration);
	    }

right cick web project > properties > build/debug tab > target x64

![](/images/posts/20180226-file-size-2.png)

![](/images/posts/20180226-file-size-1.png)

## Try ##

Restart IIS. try again and uploading succeeds:

50m

![](/images/posts/20180203-nopcommerce-file-3.png)

200m

![](/images/posts/20180226-file-size-5.png)

500m

![](/images/posts/20180226-file-size-3.png)

900m

![](/images/posts/20180226-file-size-4.png)

1gb

![](/images/posts/20180226-file-size-5.png)

## References ##

[Maximum request length exceeded.
](https://stackoverflow.com/questions/3853767/maximum-request-length-exceeded)

[How to increase the max upload file size in ASP.NET?
](https://stackoverflow.com/questions/288612/how-to-increase-the-max-upload-file-size-in-asp-net)

[http://www.cnblogs.com/maxzhang1985/p/6113695.html](http://www.cnblogs.com/maxzhang1985/p/6113695.html)