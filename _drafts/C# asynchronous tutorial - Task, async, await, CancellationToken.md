---
layout: post
title: C# asynchronous tutorial - Task, async, await, CancellationToken
author: Andy Feng
---

# Introduction #

# legacy BackgroundWorker before .net 4
Back in the old .NET days we used a BackgroundWorker instance to run asynchronous and long-running operation.
> BackgroundWorker通过DoWork，ProgressChanged，RunWorkerCompleted这三个EventHandler，分别控制程序的后台运行，进程的更新和结束后，我们只需要把任务分类到这三个EventHandler中，然后在UI线程中调用即可。
> 为了控制在程序在后台的运行状况和是否可被取消，需要设置它的两个属性True/False：WorkerReportsProgress， WorkerSupportsCancellation。

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
BackgroundWorker作为Asynchronous Programming的基础，可以为设计结构较为简单的程序实现后台任务的运行，和Thread相比能够更方便地和UI进程进行信息交互。然而它们都比较繁琐。

Starting with the `.NET Framework 4`, the .NET Framework introduces `Task` and use the async await pattern to simplify the code. For this post we will use the latter. [Task](https://msdn.microsoft.com/en-us/library/system.threading.tasks.task.aspx) class represent an asynchronous operation and we use [CancellationTokens](https://msdn.microsoft.com/en-us/library/system.threading.cancellationtoken.aspx) to cancel `Task` 
# Task, Async, Await since .net 5
After .Net 4.0, Microsoft introduces Task.
O'REILLY出版的《C# 5.0 IN A NUTSHELL》 中指出Task弥补了Thread的不足：

> A thread is a low-level tool for creating concurrency, and as such it has limitations. In particular:

1. While it's easy to pass data into a thread that you start, there's no easy way to get a **"return value"** back from a thread that you **Join**. You have to set up some kind of shared field. And if the operation throws an exception, catching and propagating that exception is equally painful
2. You can't tell a thread to start something else when it's finished; instead you must **Join** it (blocking your own thread in the process)
C#5.0之后推出了async和await关键词:

> These keywords let you write asynchronous code that has the same structure and simplicity as synchronous code, as well as eliminating the "plumbing" of asynchronous programming

Task与async/await关键词两者的结合使用，让Asynchronous Programming能够在Synchronous代码的基础快速改写完成，换言之，就是简单易用。

# CancellationToken
在《Entity Framework Core Cookbook》中指出：

> **All** asynchronous methods take **an optional CancellationToken parameter**. This parameter, when supplied, provides a way for the caller method to cancel the asynchronous execution.

.NET 5提供了一个类方便用来发出操作取消的信号，这个类就是CancellationToken，它的好处在于它可以在任意数量的线程之间、线程池任务之间、Task之间传递信号，并且所需的代码很简单。通常用于下载超时中断、用户取消任务等情况。
这个`CancellationToken`相当异步Task的控制器。.NET 很多库的异步方法都可以传入 Token。
> CancellationToken是.NET中用于协调取消操作的结构。它通常用于多线程操作，例如任务和线程等。当你启动一个新的任务或线程时，你可以传递一个CancellationToken给它，然后在其他线程中，你可以使用这个token来请求取消操作。
> CancellationToken 通常搭配 CancellationTokenSource 使用，后者是前者的一个管理类，使用 CancellationTokenSource 的 Token 属性，可以获取CancellationToken，并控制信号的发送。这两个类都属于命名空间 System.Threading
> 在异步编程中，只需将 Token 作为一个参数传入异步方法中。在异步方法外便能通过 CancellationTokenSource.Cancel 方法发出取消信号或者 CancelAfter 方法在一段时间后发出取消信号，这会改变 Token 的 isCancellationRequested 属性。在异步方法内，通过这个属性获取取消信号，并作出对应的处理操作。
> 除了通过 IsCancellationRequested 属性判断是否需要取消外，还可以通过 ThrowIfCancellationRequested 方法在需要取消时立即抛出异常，该异常是 OperationCanceledException

常用方法

	```csharp
	Token属性
	//取消后执行的回调委托(cancel event hander)，这里面还有一些override
	public CancellationTokenRegistration Register(Action callback);
	//取消
	Cannel()
	//延迟取消
	CancelAfter(long milliseconds)
	//判断是否已经取消 
	public bool IsCancellationRequested { get; } 
	//取消的话就抛出一个异常 
	public void ThrowIfCancellationRequested();
	```

e.g.

	```csharp
	    class Program
	    {
	        static async Task Main(string[] args)
	        {
	            CancellationTokenSource cts = new CancellationTokenSource();
	            //cts.Cancel() //立即发出取消信号
	            //3秒后发出取消信号，模拟取消行为
	            cts.CancelAfter(3000);
	            Console.WriteLine("下载开始");
	            await DownloadAsync(cts.Token);
	            Console.ReadKey();
	        }
	
	        static async Task DownloadAsync(CancellationToken ct)
	        {
	            using (HttpClient client = new HttpClient())
	            {
	                //模拟一个比较耗时的下载的过程
	                for (int i = 0; i < 30; i++)
	                {
	                    string s = await client.GetStringAsync("https://kfm.ink");
	                    Console.WriteLine(s);
	
	                    //ct.ThrowIfCancellationRequested();//直接抛出异常
	                    //判断是否需要取消，并自行处理
	                    if (ct.IsCancellationRequested)
	                    {
	                        Console.WriteLine("下载取消");
	                        break;
	                    }
	                }                
	            }
	        }
	    }
	```

e.g v1.

	```cs
	    public static void Main(string[] args)
	        {
				CancellationTokenSource cts = new CancellationTokenSource();
				cts.CancelAfter(5000);
				while (true)
				{
					Console.WriteLine("任务执行中！");
					if (cts.IsCancellationRequested)
					{
						Console.WriteLine("任务被取消！");
						break;
					}
				}
		}
	```

5秒钟后，CancelAfter方法会触发取消请求，打印"任务被取消！"，跳出循环。
![[20240314-canceltoken-1.png]]

e.g. v2
```cs
    public static void Main(string[] args)
	{
		CancellationTokenSource cts = new CancellationTokenSource();
		cts.CancelAfter(5000);
		while (true)
		{
			Console.WriteLine("任务执行中！");
			cts.Token.ThrowIfCancellationRequested();
		}
	}
```
5秒钟后，CancelAfter方法触发取消请求，于是执行到ThrowIfCancellationRequested()的时候抛出了一个异常，操作被取消！
![[20240314-canceltoken-2.png]]
## Demo 1
original long running Task:

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

## Demo 2 Web project
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

[# 在c#中使用CancellationToken取消任务](https://blog.csdn.net/weixin_65243968/article/details/132953650)

some videos on a few topics for what we need to just be aware of. I found each of them had a few nuggets of info:
async await pitfalls: https://www.youtube.com/watch?v=lQu-eBIIh-w&ab_channel=NickChapsas
cancellation token: https://www.youtube.com/watch?v=dqrUtbzpmJg&t=3s&ab_channel=gavilanch3
database (chained cancellation) : https://www.youtube.com/watch?v=zFdMjI2x0Ds&ab_channel=GuiFerreira
more cancellation: https://youtu.be/TKc5A3exKBQ?si=zSY7PQUTwmcnHA0G&t=119