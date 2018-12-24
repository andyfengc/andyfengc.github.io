---
layout: post
title: Hangfire Tutorial (1) Introduction
author: Andy Feng
---

# Overview #

Scheduling jobs in Web applications is a challenge, and we can choose from many job scheduling frameworks. 

- [Hangfire](https://www.hangfire.io): an open-source framework that helps us to create, process and manage background jobs

- [Quartz.NET](https://www.quartz-scheduler.net): a full-featured, open source job scheduling system that can be used from smallest apps to large scale enterprise systems.

- [FluentScheduler](https://github.com/fluentscheduler/FluentScheduler): a .NET library to create automated job scheduler with fluent interface

- [Telerik .NET Scheduler](https://www.telerik.com/products/aspnet-ajax/scheduler.aspx): a ASP.NET library to schedule jobs of data source component, Web service, WCF service, and OData.

- and more

# Hangfire Introduction #
Hangfire is  an open source job scheduling framework to schedule fire-and-forget, recurring tasks in Web applications sans the need of a Windows Service. It takes advantage of the request processing pipeline of ASP.Net for processing and executing jobs. 

Hangfire is not limited to Web applications; we can also use it in your Console applications. The documentation for Hangfire is very detailed and well structured, and the best feature is its built-in dashboard. The Hangfire dashboard shows detailed information on jobs, queues, status of jobs, and so on.

![](/images/posts/20180206-hangfire-1.png)

# Hangfire architecture #

There are three major components in Hangfire: client, storage and server.

- Client create jobs and drop to storage
- Storage saves jobs and execution results. By default, Hangfire job storage supports SQL Server Storage and MemoryStorage. Please note that all waiting jobs will disappear if our server restart using MemoryStorage. Database storage is preferred.
- Server pick up jobs from storage and execute in background

![](/images/posts/20180206-hangfire-2.png)

# .net 4.5 Installation #
1. Create a web project: Scheduler

	![](/images/posts/20180206-hangfire-3.png)

1. Install Hangfire package
	Hangfire is published as NuGet packages. Please right-click on your project > choose Add Library Package Reference> search Hangfire > Install Hangfire	

	![](/images/posts/20180206-hangfire-4.png)

	below packages will be installed:
	- Hangfire
	- Hangfire.Core
	- Hangfire.SqlServer
	- Microsoft.Owin
	- Microsoft.Owin.Host.SystemWeb
	- Newtonsoft.Json
	- Owin

	Please note that Hangfire depends on Microsoft.Owin.Host.SystemWeb therefore OWIN is installed as well.

1. Add Owin Startup class

	![](/images/posts/20180206-hangfire-5.png)

	![](/images/posts/20180206-hangfire-6.png)

	Add Hangfire configuration in Startup.cs

		using Microsoft.Owin;
		using Owin;
		using Hangfire;
		
		[assembly: OwinStartup(typeof(Scheduler.Startup))]
		namespace Scheduler
		{
		    public class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            // For more information on how to configure your application, visit http://go.microsoft.com/fwlink/?LinkID=316888
		            // way1, use database as storage
		            GlobalConfiguration.Configuration
		                .UseSqlServerStorage("SchedulerContext");
		            // way2, use MemoryStorage as storage
		            //GlobalConfiguration.Configuration.UseMemoryStorage();
		            app.UseHangfireDashboard();
		            app.UseHangfireServer();
		        }
		    }
		}

	Please note that here we use database as storage and therefore we have to install entity framework and add connection string in web.config.

	![](/images/posts/20180206-hangfire-7.png)

1. Start the project. Please note that when Hangfire starts, it will automatically create and populate relevant tables/stored procedures for us.

	![](/images/posts/20180206-hangfire-9.png)

	navigate to http://hostname:port/hangfire to view dashboard

	![](/images/posts/20180206-hangfire-8.png)

	Please note that we have one server running and ready for running jobs.

# .net core Installation #
1. Create a web project: Scheduler

	![](/images/posts/20181218-hangfire-1.png)

1. Install `Hangfire` nuget package

1. modify `startup.cs`

		namespace Scheduler
		{
		    public class Startup
		    {
		        public void ConfigureServices(IServiceCollection services)
		        {
		            services.AddHangfire(x =>
		                x.UseSqlServerStorage(
		                    "Data Source=servername; Initial Catalog=Scheduler; MultipleActiveResultSets=True; User Id=username; Password=password"));
		        }
		
		        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
		        {
		            if (env.IsDevelopment())
		            {
		                app.UseDeveloperExceptionPage();
		            }
		            app.UseHangfireServer();
		            app.UseHangfireDashboard();
		            app.Run(async (context) =>
		            {
		                await context.Response.WriteAsync("Hello World!");
		            });
		        }
		    }
		}

1. Start the project. Please note that when Hangfire starts, it will automatically create and populate relevant tables/stored procedures for us.

	![](/images/posts/20180206-hangfire-9.png)

	navigate to http://hostname:port/hangfire to view dashboard

	![](/images/posts/20180206-hangfire-8.png)

# Create a simple job #
Now we are ready to create long running tasks and drop them to Hangfire as jobs. These tasks should be independent without interacting any external resources. 

Typically, they are void methods and encapsulate contextual details such as database access, 3rd api calls. Also, it should only accept primitive data types or serializable objects as inputs. Here is an example.

	public class TaskRunner {
		public void Run(int requestId, string employeeNo){
			// new db context, access database, make processing, save data...
		}
	}

	In this case, we can drop Run() method of an instance as a job to Hangfire.

Here, let's create our first job.

1. Convert this web project to a mvc/webapi project, add a HomeController. Add a Test() method and a TaskRunner class

	    [RoutePrefix("")]
	    public class HomeController : Controller
	    {
	        [Route("test")]
	        [HttpPost]
	        public ActionResult TestHangfire()
	        {
	            new TaskRunner().Run();
	            return Content("ok");
	        }
	    }
	
	    public class TaskRunner
	    {
	        public void Run()
	        {
	            // new db context, access database, make processing, save data...
	            Thread.Sleep(20000); // simulate some long running work
	            File.WriteAllBytes("c:/delete/hangfire/test.txt", Encoding.UTF8.GetBytes("I am some result"));
	        }
	    }

	Please note:

	1. we can call the endpoint via: `POST http://hostname:port/test`
	2. we sleep the thread on purpose to simulate a long running work

1. Next, we use BankgroundJob of Hangfire to create a simple fire and forget background job. It only run once.

		using Hangfire;
		[RoutePrefix("")]
	    public class HomeController : Controller
	    {
	        [Route("test")]
	        [HttpPost]
	        public ActionResult TestHangfire()
	        {
	            // turn the call into a fire and forget background job.
	            BackgroundJob.Enqueue(() => new Scheduler.Controllers.TaskRunner().Run());
	            return Content("ok");
	        }
	    }

1. POST a request to `http://hostname:port/test`, then we immediately open the Hangfire dashboard at `http://hostname:port/hangfire`

	we will see a job is in processing

	![](/images/posts/20180206-hangfire-10.png)

	After 20 seconds, the job is done

	![](/images/posts/20180206-hangfire-11.png)

	Also, we will get the result after the job is done:

	![](/images/posts/20180206-hangfire-12.png)

# Next #
- more job types
- dashboard
- performance optimization

## References ##
[Hangfire best practices](http://docs.hangfire.io/en/latest/best-practices.html)
