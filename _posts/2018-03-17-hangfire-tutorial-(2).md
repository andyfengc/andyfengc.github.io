---
layout: post
title: Hangfire Tutorial (2) Job Types
author: Andy Feng
---

# Introduction

There are different types of background tasks. In previous posting, we create a simple one-time-running task (namely fire-and-forget job). Here is a list of types of job.

## Fire-and-forget job
Once you create a fire-and-forget job, it is saved to Hangfire queue. The queue is listened by a couple of dedicated workers that fetch a job and perform it. We simply call the Enqueue with the action we want to invoke on the server. i.e.

	```csharp
	BackgroundJob.Enqueue(() => Console.WriteLine("I am a fire-and-forget job"));
	```

Please note that the Enqueue method does not call the target method immediately. Here is the steps how it runs:

![](/images/posts/20180206-hangfire-2.png)

1. Caller enqueue a background job to Hangfire job storage
	* Caller create a fire-and-forget job via passing a lambda expression
	* Hangfire serialize a method information and all its arguments.
	* Hangfire create a new background job based on the serialized information.
	* Hangfire save background job to a persistent storage and enqueue it to queue.
	* Hangfire return to the caller

1. Hangfire server executes jobs
	* Hangfire server picks up job and processes 
	* Hangfire server update job status
	* Hangfire server return the result to the caller

## Delayed job
Delayed job is used in the senario when we want to postpone the method invocation. After the given delay, the job will be put to its queue and invoked as a regular fire-and-forget job. For instance, we can send an email to a user a couple days after they have signed up for a trial of your software.

	```csharp
	BackgroundJob.Schedule(() => Console.WriteLine("I am a delayed job"), TimeSpan.FromDays(1));
	```

## Recurring job
Recurring job is used to call a method on a recurrent basis (hourly, daily, etc) using the RecurringJob class. i.e.

	```csharp
	RecurringJob.AddOrUpdate(() => Console.WriteLine("I am a daily recurring job"), Cron.Daily);
	```

Here, we use Cron types to define the recurring pattern. Also, we can use [CRON expression](https://en.wikipedia.org/wiki/Cron#CRON_expression) expressions to specify more complex scenarios.

We can specify a unique job identifier to each job, later on we can update/remove this job based on the identifier 

```csharp
RecurringJob.AddOrUpdate("job#1", () => Console.WriteLine("I am a daily recurring job"), Cron.Daily);
```

We can remove recurring jobs via `RecurringJob.RemoveIfExists("job#1",() => Console.WriteLine("I am a daily recurring job, updated!"), Cron.Hourly)`

We can enqueue recurring jobs immediately via `RecurringJob.Trigger("job#1")`

## Continuations job
Continuations job allow us to define complex workflows by chaining multiple background jobs together.

	```csharp
	var id = BackgroundJob.Enqueue(() => Console.WriteLine("first job"));
	BackgroundJob.ContinueWith(id, () => Console.WriteLine("second job"));
	```

## Batch job
BatchJob.StartNew()

only pro version support

# Create a recurring job
Based on previous webapi project with Hangfire enabled via OWIN.

1.  Modify the webapi controller:

		```csharp
		using Hangfire;
		[RoutePrefix("")]
		public class HomeController : Controller
		{
			[Route("test")]
			[HttpPost]
			public ActionResult TestHangfire()
			{
				// turn the call into a recurring job.
				RecurringJob.AddOrUpdate(() => new TaskRunner().Run(), Cron.Minutely);
				return Content("ok");
			}
		}
	
		public class TaskRunner
		{
			static int count = 0;
			public void Run()
			{
	
				// new db context, access database, make processing, save data...
				Thread.Sleep(20000); // simulate some long running work
				File.WriteAllBytes("c:/delete/hangfire/test_" + count++ + ".txt", Encoding.UTF8.GetBytes("I am some result " + count));
			}
		}
		```

Here, we create a simple task runner and wanna schedule it to run every minute and create a new file for each run.

1. We call the endpoint via: `POST http://hostname:port/test`

1. After a while, we will got:

	![/images/posts/20180206-hangfire-13.png](/images/posts/20180206-hangfire-13.png)

1. We can observe the job at `http://hostname:port/hangfire` 

	![/images/posts/20180206-hangfire-14.png](/images/posts/20180206-hangfire-14.png)

# References
[Workers patterns with hangfire](https://zdjohn.wordpress.com/2016/06/20/workers-patterns-with-hangfire/)