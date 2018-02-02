---
layout: post
title: Swagger tutorial
author: Andy Feng
---

## Introduction ##

[Swagger](https://swagger.io) is a powerful framework of API developer tools for the [OpenAPI Specification(OAS)](https://swagger.io/specification/). OpenAPI specification (formerly known as the Swagger Specification) is a definition to describe RESTful APIs. By following OpenAPI specification, we can easily develope and consume an API by effectively mapping all the resources and operations associated with it.

Basically, Swagger is a simple and powerful representation of RESTful API. It gives a powerful interface to our API.

## Swagger in .NET Web Api ##

[Swashbuckle](https://github.com/domaindrivendev/Swashbuckle) is an implementation of Swagger in .NET. It can add Swagger OpenApi specification to our WebApi projects. It combines ApiExplorer and Swagger/swagger-ui to provide a rich discovery, documentation and playground experience to our API consumers. 

Here is how we use Swashbuckle 

1. Create a Web Api project, project name `WebApi`

	![](/images/posts/20180202-swagger-7.png)

1. Adding Swashbuckle to our Web API

	nuget > Swashbuckle > install

	![](/images/posts/20180202-swagger-1.png)

1. run the project, navigate to `http://localhost:port/swagger`

	![](/images/posts/20180202-swagger-2.png)

1. we can try the endpoint easily via this web portal

	![](/images/posts/20180202-swagger-3.png)

1. Next, we add documentation to endpoints

	project properties > build tab > select the checkbox “XML documentation file:” > get the XML path from Bin folder

	![](/images/posts/20180202-swagger-4.png)	

	Now open the Swagger Config file `SwaggerConfig.cs`

	uncomment `c.IncludeXmlComments(GetXmlCommentsPath())`, we get:

		public static void Register()
		{
		    var thisAssembly = typeof(SwaggerConfig).Assembly;
		
		    GlobalConfiguration.Configuration
		        .EnableSwagger(c =>
		        {
		            c.SingleApiVersion("v1", "SwaggerUi");
					...
		            c.IncludeXmlComments(GetXmlCommentsPath());
		        })
		        .EnableSwaggerUi(c =>
		        {
		        });
		}

	add c.IncludeXmlComments(GetXmlCommentsPath()) function that returns the path to XML file from bin folder.

		private static string GetXmlCommentsPath()
		{
		    return String.Format(@"{0}\bin\WebApi.XML", System.AppDomain.CurrentDomain.BaseDirectory);
		}

1. Now we can add XML comments to each Web Api controller methods. 
	![](/images/posts/20180202-swagger-5.png)

	Run the application and navigate to Swagger Help page at `http://localhost:port/swagger`. We will find xml comments were added to the endpoint.
	
	![](/images/posts/20180202-swagger-6.png)

1. Customize Swagger UI

	We can customize swagger help pages with our own css styles. Specifally, we use the predefined method “InjectStylesheet” to inject our own .css files as an embedded resources. We need to use “Logical Name” of the file as the second parameter and “media=screen” as third optional parameter along with current assembly as a first parameter.

	First, create a new css file e.g. “swagger-help.css” in the Content folder and add the following style to change the headers default background color from Green to Blue:

		.swagger-section #header {
		    background-color: #2365B0;
		    padding: 14px;
		}

	Next, css file > right click > properties > set its Build Action to “Embedded Resource”
	
	![](/images/posts/20180202-swagger-8.png)

	Now we inject the css style in SwaggerConfig settings to enable UI:

		public static void Register()
		{
		    var thisAssembly = typeof(SwaggerConfig).Assembly;
		
		    GlobalConfiguration.Configuration
		        .EnableSwagger(c =>
		        {
		            c.SingleApiVersion("v1", "SwaggerUi");
					...
		            c.IncludeXmlComments(GetXmlCommentsPath());
					...
					c.InjectStylesheet(thisAssembly, "WebApi.Content.swagger-help.css");
		        })
		        ...
		}
    
	Now run the application to see the change.

	![](/images/posts/20180202-swagger-9.png)
	

## References ##

https://www.codeproject.com/Articles/1078249/RESTful-Web-API-Help-Documentation-using-Swagger-U

http://stevemichelotti.com/customize-authentication-header-in-swaggerui-using-swashbuckle/