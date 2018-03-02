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
	
# Swagger in .NET Core Web Api #

1. install swagger package

	- visual studio: nuget > `Install-Package Swashbuckle.AspNetCore`
	- visual studio code (cli): command line > `dotnet add TodoApi.csproj package Swashbuckle.AspNetCore`

	![](/images/posts/20180301-swagger-1.png)

1. in Startup.cs, add Swagger generator

		public void ConfigureServices(IServiceCollection services)
		{
		    ...
		    services.AddMvc();		
		    // Register the Swagger generator, defining one or more Swagger documents
		    services.AddSwaggerGen(c =>
		    {
		        c.SwaggerDoc("v1", new Info 
					{ Title = "My API", Version = "v1" }
				);
		    });
		}

	Here, we can add information such as the author, license, and description 

		services.AddSwaggerGen(c =>
		{
		    c.SwaggerDoc("v1", new Info
		    {
		        Version = "v1",
		        Title = "ToDo API",
		        Description = "A simple example ASP.NET Core Web API",
		        TermsOfService = "None",
		        Contact = new Contact { Name = "Andy Feng", Email = "", Url = "https://twitter.com/andyfengc" },
		        License = new License { Name = "Use under LICX", Url = "https://andyfeng.ga/license" }
		    });
		});

1. in Startup.cs, enable the middleware for serving the generated JSON document and the SwaggerUI

		public void Configure(IApplicationBuilder app)
		{
		    // Enable middleware to serve generated Swagger as a JSON endpoint.
		    app.UseSwagger();
		
		    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.), specifying the Swagger JSON endpoint.
		    app.UseSwaggerUI(c =>
		    {
		        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
		    });
		
		    app.UseMvc();
		}

1. run the app either via visual studio or command line `dotnet run`. Navigate to `http://localhost:<random_port>/swagger` to view the swagger UI

	![](/images/posts/20180301-swagger-2.png)	

	It was driven by json document at `http://localhost:<random_port>/swagger/v1/swagger.json`. The generated document describing the endpoints appears.

		{
		   "swagger": "2.0",
		   "info": {
		       "version": "v1",
		       "title": "API V1"
		   },
		   "basePath": "/",
		   "paths": {
		       "/api/Todo": {
		           "get": {
		               ...
		           },
		           "post": {
		               ...
		           }
		       },
		       ...
		   },
		   "definitions": {
		       "TodoItem": {
		           "type": "object",
		            "properties": {
		                "id": {
		                    "format": "int64",
		                    "type": "integer"
		                },
		                "name": {
		                    "type": "string"
		                },
		                "isComplete": {
		                    "default": false,
		                    "type": "boolean"
		                }
		            }
		       }
		   },
		   "securityDefinitions": {}
		}

1. Add xml documentation

	project > properties > build tab > enable xml documentation file

	![](/images/posts/20180301-swagger-3.png)
	
1. Customize Swagger UI.
	
	Swagger provides options for documenting the object model and customizing the UI to match our theme. 

	

	add new package Microsoft.AspNetCore.StaticFiles

		<PackageReference Include="Microsoft.AspNetCore.StaticFiles" Version="2.0.0" />

	Enable the static files middleware:

		public void Configure(IApplicationBuilder app)
		{
		    app.UseStaticFiles();		
		    // Enable middleware to serve generated Swagger as a JSON endpoint.
		    app.UseSwagger();		
		    // Enable middleware to serve swagger-ui (HTML, JS, CSS, etc.), specifying the Swagger JSON endpoint.
		    app.UseSwaggerUI(c =>
		    {
		        c.SwaggerEndpoint("/swagger/v1/swagger.json", "My API V1");
		    });		
		    app.UseMvc();
		}

	then, we can add `wwwroot/swagger/ui/css/custom.css` to customize the scheme. check tutorial for more details

1. Add comment

	example 1, 

		/// <summary>
		/// Deletes a specific TodoItem.
		/// </summary>
		/// <param name="id"></param>        
		[HttpDelete("{id}")]
		public IActionResult Delete(long id)
		{...}

	![](/images/posts/20180301-swagger-4.png)	

	example 2, <remarks> tag can add additional information, it can consist of text, JSON, or XML.

		/// <summary>
		/// Creates a TodoItem.
		/// </summary>
		/// <remarks>
		/// Sample request:
		///
		///     POST /Todo
		///     {
		///        "id": 1,
		///        "name": "Item1",
		///        "isComplete": true
		///     }
		///
		/// </remarks>
		/// <param name="item"></param>
		/// <returns>A newly-created TodoItem</returns>
		/// <response code="201">Returns the newly-created item</response>
		/// <response code="400">If the item is null</response>            
		[HttpPost]
		[ProducesResponseType(typeof(TodoItem), 201)]
		[ProducesResponseType(typeof(TodoItem), 400)]
		public IActionResult Create([FromBody] TodoItem item)
		{
		    if (item == null)
		    {
		        return BadRequest();
		    }
		
		    _context.TodoItems.Add(item);
		    _context.SaveChanges();
		
		    return CreatedAtRoute("GetTodo", new { id = item.Id }, item);
		}

	![](/images/posts/20180301-swagger-5.png)	


## References ##

[https://www.codeproject.com/Articles/1078249/RESTful-Web-API-Help-Documentation-using-Swagger-U](https://www.codeproject.com/Articles/1078249/RESTful-Web-API-Help-Documentation-using-Swagger-U)

[http://stevemichelotti.com/customize-authentication-header-in-swaggerui-using-swashbuckle/](http://stevemichelotti.com/customize-authentication-header-in-swaggerui-using-swashbuckle/)

[https://docs.microsoft.com/en-us/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio](https://docs.microsoft.com/en-us/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio)