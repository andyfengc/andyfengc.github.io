---
layout: post
title: ASP.NET Core tutorial - fundamentals
author: Andy Feng
categories: [asp.net, core, fundamentals]
---

# Host
On startup, an ASP.NET Core app builds a host. The host encapsulates all of the app's resources, such as:

- An HTTP server implementation
- Middleware components
- Logging
- Dependency injection (DI) services
- Configuration

There are two different hosts:

- .NET Generic Host recommended
- ASP.NET Core Web Host available only for backwards compatibility.

e.g. creates a .NET Generic Host:
	
	public class Program
	{
	    public static void Main(string[] args)
	    {
	        CreateHostBuilder(args).Build().Run();
	    }
	
	    public static IHostBuilder CreateHostBuilder(string[] args) =>
	        Host.CreateDefaultBuilder(args)
	            .ConfigureWebHostDefaults(webBuilder =>
	            {
	                webBuilder.UseStartup<Startup>();
	            });
	}

CreateDefaultBuilder() and ConfigureWebHostDefaults() methods configure a host with a set of default options, such as:

- web server, use Kestrel or IIS as web server.
- Load configuration from appsettings.json, appsettings.{Environment Name}.json, environment variables, command line arguments, and other configuration sources.
- Send logging output to the console and debug providers.
- specify Startup class

![](/images/posts/20201011-net-core-1.png)

# Startup class
The Startup class is where:

- Services required by the app are configured.
- The app's request handling pipeline is defined, as a series of middleware components.

e.g.

	public class Startup
	{
	    public void ConfigureServices(IServiceCollection services)
	    {
			// dependency injection (DI)
	        services.AddDbContext<RazorPagesMovieContext>(options =>
	            options.UseSqlServer(Configuration.GetConnectionString("RazorPagesMovieContext")));
	
	        services.AddControllersWithViews();
	        services.AddRazorPages();
	    }
	
		// configure a pipeline of middleware components
	    public void Configure(IApplicationBuilder app)
	    {			
	        app.UseHttpsRedirection();
	        app.UseStaticFiles();
	
	        app.UseRouting();
	
	        app.UseEndpoints(endpoints =>
	        {
	            endpoints.MapDefaultControllerRoute();
	            endpoints.MapRazorPages();
	        });
	    }
	}

Startup class includes 2 methods. ConfigureServices() and Configure() are called by the ASP.NET Core runtime when the app starts:

- ConfigureServices() - optional, configure the app's services. A service is a reusable component that provides app functionality. Services are registered in ConfigureServices and consumed across the app via dependency injection (DI) or ApplicationServices. It add services via Add{Service} extension methods on `IServiceCollection`
- Configure():  create the app's request processing pipeline. It specify how the app responds to HTTP requests. The request pipeline is configured by adding middleware components to an `IApplicationBuilder` instance.

Below service types can be injected into the Startup constructor when using the Generic Host (IHostBuilder):

- IWebHostEnvironment
- IHostEnvironment
- IConfiguration

e.g.

public class Startup
{
    private readonly IWebHostEnvironment _env;
    public IConfiguration Configuration { get; }

    public Startup(IConfiguration configuration, IWebHostEnvironment env)
    {
        Configuration = configuration;
        _env = env;
    }

	public void ConfigureServices(IServiceCollection services)
    {
		// add services
    }

	public void Configure(IApplicationBuilder app)
    {			
        // config pipeline
    }
}

## IServiceCollection

IServiceCollection is a collection of ServiceDescriptor objects. The following example shows how to register a service by creating and adding a ServiceDescriptor:

	public void ConfigureServices(IServiceCollection services)
    {
		var descriptor = new ServiceDescriptor(
		    typeof(IMyDependency),
		    sp => new MyDependency5(),
		    ServiceLifetime.Transient);	
		services.Add(descriptor);
    }
	
Essentially, Add{LIFETIME} methods use the same approach. e.g. AddScoped() [source code](https://github.com/dotnet/extensions/blob/v3.1.6/src/DependencyInjection/DI.Abstractions/src/ServiceCollectionServiceExtensions.cs#L216-L237)

e.g.

	public void Configure(IApplicationBuilder app, IHostingEnvironment env)
	{
		...
		var serviceProvider = app.ApplicationServices;
		var classObj = serviceProvider.GetService<ClassName>();
		// or
		var classObj = serviceProvider.GetService(tyoeof(ClassName)) as ClassName;
	}
## IServiceProvider
The IServiceCollection interface is used for building a dependency injection container. After it's fully built, it gets composed to an IServiceProvider instance which you can use to resolve services. 

You can inject an IServiceProvider into any class. The IApplicationBuilder and HttpContext classes can provide the service provider as well, via their ApplicationServices or RequestServices properties respectively.

## Service lifetime
Services can be registered with one of the following lifetimes:

- Transient: Transient lifetime services are created each time they're requested from the service container. This lifetime works best for lightweight, stateless services. Register transient services with AddTransient().
- Scoped: Scoped lifetime services are created once per client request (connection). Register scoped services with AddScoped(). scoped services are disposed at the end of the request.
- Singleton. Singleton services must be thread safe and are often used in stateless services. Singleton lifetime services are created when the first time they're requested.

> When using Entity Framework Core, the AddDbContext extension method registers DbContext types with a scoped lifetime by default.
> resolving a service from another service with a longer lifetime throws an exception

e.g.

	public interface IMyDependency
	{
	    void WriteMessage(string message);
	}
	public class MyDependency : IMyDependency
	{
	    public void WriteMessage(string message)
	    {
	        Console.WriteLine($"MyDependency.WriteMessage Message: {message}");
	    }
	}
	public void ConfigureServices(IServiceCollection services)
	{
	    services.AddTransient<IMyDependency, MyDependency>();	
	    services.AddScoped<IMyDependency, MyDependency>();
	    services.AddSingleton<IMyDependency, MyDependency>();

	    services.AddRazorPages();
	}

> there are TryAdd{LIFETIME} extension methods, which register the service only if there isn't already an implementation registered.> 
> - TryAdd
> - TryAddTransient
> - TryAddScoped
> - TryAddSingleton

## Middleware
The request handling pipeline is composed as a series of middleware components. Each component performs operations on an HttpContext and either invokes the next middleware in the pipeline or terminates the request.

ASP.NET Core includes a rich set of built-in middleware. Custom middleware components can also be written.

# IStartupFilter
We can extend Startup with startup filters by implementing `IStartupFilter`

IStartupFilter has Configure(), which receives and returns an Action<IApplicationBuilder>. An IApplicationBuilder defines a class to configure an app's request pipeline. Each IStartupFilter can add one or more middlewares in the request pipeline. The filters are invoked in the order they were added to the service container. Filters may add middleware before or after passing control to the next filter, thus they append to the beginning or end of the app pipeline.

e.g. create a middleware, then register to pipeline with `IStartupFilter`

middleware:

	public class RequestSetOptionsMiddleware
	{
	    private readonly RequestDelegate _next;
	
	    public RequestSetOptionsMiddleware( RequestDelegate next )
	    {
	        _next = next;
	    }
	
	    // Test with https://localhost:5001/Privacy/?option=Hello
	    public async Task Invoke(HttpContext httpContext)
	    {
	        var option = httpContext.Request.Query["option"];
	
	        if (!string.IsNullOrWhiteSpace(option))
	        {
	            httpContext.Items["option"] = WebUtility.HtmlEncode(option);
	        }
	
	        await _next(httpContext);
	    }
	}

implement IStartupFilter

	public class RequestSetOptionsStartupFilter : IStartupFilter
	{
	    public Action<IApplicationBuilder> Configure(Action<IApplicationBuilder> next)
	    {
	        return builder =>
	        {
	            builder.UseMiddleware<RequestSetOptionsMiddleware>();
	            next(builder);
	        };
	    }
	}

add to pipeline

	public class Startup
	{
		public void ConfigureServices(IServiceCollection services)
	    {
			// add services
	 		services.AddTransient<IStartupFilter, RequestSetOptionsStartupFilter>();
	    }
	
		public void Configure(IApplicationBuilder app)
	    {			
	        // config pipeline
	    }
	}

# References
[ASP.NET Core fundamentals](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/?view=aspnetcore-3.1&tabs=windows)

[Middleware in ASP .NET Core 3.1](https://wakeupandcode.com/middleware-in-asp-net-core-3-1/)