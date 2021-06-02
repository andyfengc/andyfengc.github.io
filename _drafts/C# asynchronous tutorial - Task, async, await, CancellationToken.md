---
layout: post
title: C# asynchronous tutorial - Task, async, await, CancellationToken
author: Andy Feng
---

# Introduction #

# CancellationToken
Back in the old .NET days we used a BackgroundWorker instance to run asynchronous and long-running operation.

We had the ability to cancel these operations by calling the CancelAsync which sets the CancellationPending flag to true.

	private void BackgroundLongRunningTask(object sender, DoWorkEventArgs e)
	{
	    BackgroundWorker worker = (BackgroundWorker)sender;
	
	    for (int i = 1; i <= 10000; i++)
	    {
	        if (worker.CancellationPending == true)
	        {
	            e.Cancel = true;
	            break;
	        }
	        
	        // Do something
	    }
	}

Starting with the .NET Framework 4, the .NET Framework introduces `Task` and use the async await pattern to simplify the code. For this post we will use the latter. [Task](https://msdn.microsoft.com/en-us/library/system.threading.tasks.task.aspx) class represent an asynchronous operation and we use [CancellationTokens](https://msdn.microsoft.com/en-us/library/system.threading.cancellationtoken.aspx) to cancel `Task` 

## Example 1
long running Task:

	private static Task<decimal> LongRunningOperation(int loop)
	{
	    // Start a task and return it
	    return Task.Run(() =>
	    {
	        decimal result = 0;
	
	        // Loop for a defined number of iterations
	        for (int i = 0; i < loop; i++)
	        {
	            // Do something that takes times like a Thread.Sleep in .NET Core 2.
	            Thread.Sleep(10);
	            result += i;
	        }
	
	        return result;
	    });
	}

typically, here is invoking code we call the task like: 

	public static async Task ExecuteTaskAsync()
	{
	    Console.WriteLine("Result {0}", await LongRunningOperation(100));
	    Console.WriteLine("Press enter to continue");
	    Console.ReadLine();
	}

Modify long running task and make it cancellable:

	private static Task<decimal> LongRunningCancellableOperation(int loop, CancellationToken cancellationToken)
	{
	    Task<decimal> task = null;
	
	    // Start a task and return it
	    task = Task.Run(() =>
	    {
	        decimal result = 0;
	
	        // Loop for a defined number of iterations
	        for (int i = 0; i < loop; i++)
	        {
	            // Check if a cancellation is requested, if yes,
	            // throw a TaskCanceledException.
	
	            if (cancellationToken.IsCancellationRequested)
	                throw new TaskCanceledException(task);
	
	            // Do something that takes times like a Thread.Sleep in .NET Core 2.
	            Thread.Sleep(10);
	            result += i;
	        }
	
	        return result;
	    });
	
	    return task;
	}

Modify the invoking code to cancel task with a timeout. please note that we use [CancellationTokenSource](https://msdn.microsoft.com/en-us/library/system.threading.cancellationtokensource.aspx) to generate CancellationToken and cancel the task

	public static async Task ExecuteTaskWithTimeoutAsync(TimeSpan timeSpan)
	{
	    using (var cancellationTokenSource = new CancellationTokenSource(timeSpan))
	    {
	        try
	        {
	            var result = await LongRunningCancellableOperation(500, cancellationTokenSource.Token);
	            Console.WriteLine("Result {0}", result);
	        }
	        catch (TaskCanceledException)
	        {
	            Console.WriteLine("Task was cancelled");
	        }
	    }
	    Console.WriteLine("Press enter to continue");
	    Console.ReadLine();
	}

Usually asynchronous worker must be modified to support cancellation. 

If a Task not support CancellationToken and we cannot modify it, we can create a wrapper to make it cancellable. see [Cancel asynchronous operations in C#](https://johnthiriet.com/cancel-asynchronous-operation-in-csharp/)

## Example 2 Web project
Sometimes we build a web app and some tasks might takes too long to complete. Very likely users click the "Stop" button, or maybe hammer F5 to reload the page. Traditionally, the poor backend server has not idea and responds to every user request. If the action method the user is hitting takes a long time to run, then refreshing five times will fire off 5 requests. Now you're doing 5 times the work. That's the default behaviour in Web project.

A better solution is, our server detect when a request is cancelled, and stop execution. If a user click stop button or refresh 5 times, while the long running tasks running,  the server can monitor and cancel all previous requests and only respond the last request. 

e.g.
	
	public class SlowRequestController : Controller
	{
	    private readonly LongRunningOperation operation;
	
	    public SlowRequestController()
	    {
	        this.operation = new LongRunningOperation();
	    }
	
	    [HttpGet("/slowtest")]
	    public async Task<string> Get()
	    {
	        // slow async action, e.g. call external api and delayed 10s
	        await this.operation.Run();
	
	        var message = "Finished slow delay of 10 seconds.";
	        return message;
	    }
	}

If we hit the URL /slowtest then the request will run for 10s, and eventually will return the message:

![](images/posts/20200913-task-1.png)

ASP.NET Core provides `CancellationToken` for server to signal when a request has been cancelled. This is exposed as HttpContext.RequestAborted, and we can inject it automatically into your actions using model binding. The following code shows how we can hook into the central CancellationTokenSource for the request, by injecting a CancellationToken into the action method, and passing the parameter to the LongRunningOperation.Run() method:
	
	public class SlowRequestController : Controller
	{
	    private readonly LongRunningOperation operation;
	
	    public SlowRequestController()
	    {
	        this.operation = new LongRunningOperation();
	    }
	
	    [HttpGet("/slowtest")]
	    public async Task<string> Get(CancellationToken cancellationToken)
	    {
	        // slow async action, e.g. call external api
	        await this.operation.Run(cancellationToken);
	
	        var message = "Finished slow delay of 10 seconds.";
	        return message;
	    }
	}

Please note
1. MVC will automatically bind any CancellationToken parameters in an action method to the HttpContext.RequestAborted token, using the CancellationTokenModelBinder. This model binder is registered automatically when you call services.AddMvc() (or services.AddMvcCore()) in Startup.ConfigureServices().
1. we have to modify LongRunningOperation.Run() to add CancellationToken parameter and let it support cancellation; 

e.g.

	public class LongRunningOperation{
		public void Run(CancellationToken cancellationToken){
			for(var i=0; i<10; i++)
			{
			    cancellationToken.ThrowIfCancellationRequested(); 
			    // slow non-cancellable work
			    Thread.Sleep(1000);
			}
		}
	}

we can also use IsCancellationRequested, e.g.

	public class LongRunningOperation{
		public void Run(CancellationToken cancellationToken){
			for(var i=0; i<10; i++)
			{
			    if (cancellationToken.IsCancellationRequested) throw new TaskCanceledException(task);
			    // slow non-cancellable work
			    Thread.Sleep(1000);
			}
		}
	}


# References #
[Using Asynchronous Methods in ASP.NET MVC 4](https://docs.microsoft.com/en-us/aspnet/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4)

[Create an Asynchronous Method [C#]](https://www.csharp-examples.net/create-asynchronous-method/)

[Cancel an Asynchronous Method [C#]](https://www.csharp-examples.net/cancel-asynchronous-method/)

[Cancel asynchronous operations in C#](https://johnthiriet.com/cancel-asynchronous-operation-in-csharp/)

[Task cancellation in C# and things you should know about it](https://binary-studio.com/2015/10/23/task-cancellation-in-c-and-things-you-should-know-about-it/)

[Cancellation in Managed Threads](https://docs.microsoft.com/en-us/dotnet/standard/threading/cancellation-in-managed-threads)

[How to: Cancel a Task and Its Children](https://docs.microsoft.com/en-us/dotnet/standard/parallel-programming/how-to-cancel-a-task-and-its-children)

[Using CancellationTokens in ASP.NET Core MVC controllers](https://andrewlock.net/using-cancellationtokens-in-asp-net-core-mvc-controllers/)

[Cancelling Long Running Queries in ASP.NET MVC and Web API](https://www.davepaquette.com/archive/2015/07/19/cancelling-long-running-queries-in-asp-net-mvc-and-web-api.aspx)

[Task CancellationToken .net C# example, Solution for - how to kill long running task in C#?](https://www.asptricks.net/2019/10/task-cancellationtoken-net-c-example.html)

[Canceling Long Running Task Using Cancellationtokensource In .NET](https://www.c-sharpcorner.com/article/cancelling-long-running-task-using-cancellationtokensource-in-net/)

[ASP.NET MVC5 - Asynchronous Controllers And Cancellation Token](https://www.c-sharpcorner.com/article/asp-net-mvc5-asynchronous-controllers-cancellation-token/)

[Recommended patterns for CancellationToken](https://devblogs.microsoft.com/premier-developer/recommended-patterns-for-cancellationtoken/)