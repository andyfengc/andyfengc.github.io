---
layout: post
title: ASP.NET Core WebApi Tutorial
author: Andy Feng
---

download source code [here](/images/)
# Create project via Cli #
1. Install .net core sdk, .net  core runtime at [https://www.microsoft.com/net/learn/get-started/windows](https://www.microsoft.com/net/learn/get-started/windows) or [https://www.microsoft.com/net/download/windows](https://www.microsoft.com/net/download/windows)

	in Feb, 2018, .net core sdk is v2.1.4, .net core runtime is v 2.0.5

	![](/images/posts/20180228-.netcore-webapi-6.png)
	
	![](/images/posts/20180228-.netcore-webapi-7.png)

1. open console, `dotnet new --help` to check templates

	![](/images/posts/20180228-.netcore-webapi-8.png)

1. create webapi project, `dotnet new webapi -o myApi`

	![](/images/posts/20180228-.netcore-webapi-9.png)

	![](/images/posts/20180228-.netcore-webapi-11.png)

1. run the app, `dotnet run`

	![](/images/posts/20180228-.netcore-webapi-10.png)

	![](/images/posts/20180228-.netcore-webapi-12.png)

1. install entity framework core package. `dotnet add Microsoft.EntityFrameworkCore.SqlServer`

	![](/images/posts/20180228-.netcore-webapi-13.png)

	other commands:

	> `dotnet add package`: Adds a package reference to the project file, then runs `dotnet restore` to install the package.
	> `dotnet remove` package: Removes a package reference from the project file
	> `dotnet restore`: Restores the dependencies and tools of a project. 
	> `dotnet nuget locals`: Clears or lists local NuGet resources such as the http-request cache, the temporary cache, and the machine-wide global packages folder.

# Create project via Visual Studio #
![](/images/posts/20180228-.netcore-webapi-1.png)

![](/images/posts/20180228-.netcore-webapi-2.png)

we got

![](/images/posts/20180228-.netcore-webapi-3.png)

If this webapi project references other libaries, make sure to choose the compatible .net framework version

ctrl + f5 to run, 

![](/images/posts/20180228-.netcore-webapi-4.png)

nuget > install Microsoft.EntityFrameworkCore.SqlServer package

![](/images/posts/20180228-.netcore-webapi-5.png)

# Create a simple demo #

1. add data models

	TodoItem model:

		namespace TodoApi.Models
		{
		    public class TodoItem
		    {
		        public long Id { get; set; }
		        public string Name { get; set; }
		        public bool IsComplete { get; set; }
		    }
		}
		
	Todo2Context db context:

		using Microsoft.EntityFrameworkCore;
		
		namespace TodoApi.Models
		{
		    public class Todo2Context : DbContext
		    {
		        public Todo2Context(DbContextOptions<Todo2Context> options)
		            : base(options)
		        {
		        }
		
		        public DbSet<TodoItem> TodoItems { get; set; }
		
		    }
		}

	![](/images/posts/20180228-.netcore-webapi-14.png)

1. register db context in [dependency injection](https://docs.microsoft.com/en-us/aspnet/core/fundamentals/dependency-injection) container. Here we specify an in-memory database is injected into the service container.

	update Startup.cs

		public class Startup
	    {       
	        public void ConfigureServices(IServiceCollection services)
	        {
	            services.AddDbContext<TodoContext>(opt => opt.UseInMemoryDatabase("TodoList"));
	            services.AddMvc();
	        }
			...
		}

1. Create a controller: TodoController.cs

		using System.Collections.Generic;
		using Microsoft.AspNetCore.Mvc;
		using TodoApi.Models;
		using System.Linq;
		
		namespace TodoApi.Controllers
		{
		    [Route("api/[controller]")]
		    public class TodoController : Controller
		    {
		        private readonly Todo2Context _context;
		
		        public TodoController(Todo2Context context)
		        {
		            _context = context;
		
		            if (_context.TodoItems.Count() == 0)
		            {
		                _context.TodoItems.Add(new TodoItem { Name = "Item1" });
		                _context.SaveChanges();
		            }
		        }       
		    }
		}

1. Add get endpoints

		[HttpGet]
		public IEnumerable<TodoItem> GetAll()
		{
		    return _context.TodoItems.ToList();
		}
		
		[HttpGet("{id}", Name = "GetTodo")]
		public IActionResult GetById(long id)
		{
		    var item = _context.TodoItems.FirstOrDefault(t => t.Id == id);
		    if (item == null)
		    {
		        return NotFound();
		    }
		    return new ObjectResult(item);
		}

1. Add create endpoint

		[HttpPost]
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

1. Add update endpoint

		[HttpPut("{id}")]
		public IActionResult Update(long id, [FromBody] TodoItem item)
		{
		    if (item == null || item.Id != id)
		    {
		        return BadRequest();
		    }
		
		    var todo = _context.TodoItems.FirstOrDefault(t => t.Id == id);
		    if (todo == null)
		    {
		        return NotFound();
		    }
		
		    todo.IsComplete = item.IsComplete;
		    todo.Name = item.Name;
		
		    _context.TodoItems.Update(todo);
		    _context.SaveChanges();
		    return new NoContentResult();
		}

1. add delete endpoint

		[HttpDelete("{id}")]
		public IActionResult Delete(long id)
		{
		    var todo = _context.TodoItems.FirstOrDefault(t => t.Id == id);
		    if (todo == null)
		    {
		        return NotFound();
		    }
		
		    _context.TodoItems.Remove(todo);
		    _context.SaveChanges();
		    return new NoContentResult();
		}

# Test #

1. run via `dotnet run`

	![](/images/posts/20180228-.netcore-webapi-10.png)

1. navigate to `http://localhost:5000/api/todo`

	![](/images/posts/20180228-.netcore-webapi-15.png)

1. post a json data


    {
        "id": 2,
        "name": "Item2",
        "isComplete": true
    }


# References #
[https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api](https://docs.microsoft.com/en-us/aspnet/core/tutorials/first-web-api)

[https://blogs.msdn.microsoft.com/webdev/2017/04/06/jwt-validation-and-authorization-in-asp-net-core/](https://blogs.msdn.microsoft.com/webdev/2017/04/06/jwt-validation-and-authorization-in-asp-net-core/)

[https://docs.microsoft.com/en-us/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio](https://docs.microsoft.com/en-us/aspnet/core/tutorials/web-api-help-pages-using-swagger?tabs=visual-studio)