---
layout: post
title: Moq mocking tutorial
author: Andy Feng
---

# Introduction #
Mocking technique is very useful for testing purpose. Moq is a third party framework that enables us to create the dummy class and dummy implementation. This is very useful in the scenarios where we want to test the functionality of a class without implementing the other classes and their methods. 

# Installation #
1. create a service project and a unit test project

1. nuget > Moq > install

	![](/images/posts/20180312-moq-1.png)

# Steps #
1. Create a mock object `var mockObj = new Mock<MockClass>()`

1. Set up the mock object

	two major methods

	1. specific value

			// set up the mock by specifying argument values
			mockObj.Setup(x => x.method(param1, ...)).Returns(xxx)

	1. Moq's argument matching technique

			// specify the param of the mock should be a certain data type
			mockObj.Setup(x => x.method(It.IsAny<Param1DataType>(), ...)).Returns(xxx)
	
			// specify the param of the mock must meet some conditions
			mockObj.Setup(x => x.IsValid(It.Is<Param1DataType>(param1 => param1..condition...)).Returns(xxx)
	
			// specify the param of the mock must be within a set
			mockObj.Setup(x => x.IsValid(It.IsIn("xx", "yy", "zz"))).Returns(xxx) 
	
			// specify the param of the mock must be within a range
			mockObj.Setup(x => x.IsValid(It.IsInRange("xx", "yy", "zz", Range.Inclusive))).Returns(xxx) 
	
			// specify the param of the mock must meet a regex
			mockObj.Setup(x => x.IsValid(It.IsRegex("[xx|yy|zz]", RegexOptions.None))).Returns(xxx) 

1. Inject the mock object via constructor or method parameter to business logic. Then, make the test

		var result = BusinessServiceClassInstance.get(mockObj.Object);
		Assert.IsTrue(result);
		//
		mockObj.Verify(m => m.Method1(It.isAny<ParamDataType>()), Times.Once);
		mockObj.Verify(m => m.Method2(It.isAny<ParamDataType>()), Times.Exactly(2));

# Demo 1 - Make uniting testing with mock via AAA principle #

In service project 

1. Develop some service logic

		public interface IMailClient
	    {
	        string Server { get; set; }
	        string Port { get; set; }
	
	        bool SendMail(string from, string to, string subject, string body);
	    }
	
	    public interface IMailer
	    {
	        string From { get; set; }
	        string To { get; set; }
	        string Subject { get; set; }
	        string Body { get; set; }
	
	        bool SendMail(IMailClient mailClient);
	    }
		
		// logic
	    public class DefaultMailer : IMailer
	    {
	        public string From { get; set; }
	        public string To { get; set; }
	        public string Subject { get; set; }
	        public string Body { get; set; }
	
	        public bool SendMail(IMailClient mailClient)
	        {
	            return mailClient.SendMail(this.From, this.To, this.Subject, this.Body);
	        }
	    }

We defined two interfaces and interfaces is okay to mock. Moq will create mocks to make `default` implementation for us. 

In unit test project

1. Create a unit test class

1. Arrange - Create a mock object. Pass the mock to the logic

		// create mock object
		var mockMailClient = new Mock<IMailClient>();
		// setup the mock object, here we setup the SendMail() method
		mockMailClient.Setup(client =>
                client.SendMail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>())
                );

1. Act- Execute the logic with mock

		// create mock data
		var mailer = new DefaultMailer() { From = "from@mail.com", To = "to@mail.com", Subject = "Using Moq", Body = "Moq is awesome" };
		// use mock to send email
		mailer.SendMail(mockMailClient.Object);

1. Assert - Verify the system interaction

		// we expect client.SendMail() method is called at least once
		mockMailClient.Verify(client => client.SendMail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>()), Times.AtLeastOnce);

The complete unit test code

	    [TestClass]
	    public class MoqTest
	    {
	        [TestMethod]
	        public void SendEmail_ShouldSucceed()
	        {
	            // create mock
	            var mockMailClient = new Mock<IMailClient>();
	            mockMailClient
	                .SetupProperty(client => client.Server, "server.mail.com")
	                .SetupProperty(client => client.Port, "1000");
	            // set up mock
	            mockMailClient.Setup(client =>
	                client.SendMail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>())
	                )
	                .Callback(new Action<string, string, string, string>((a, b, c, d) =>
	                {
	                    System.Console.WriteLine($"from: {a} -> to: {b}, subject: {c}, body: {d}");
	                }))
	                .Returns(true);
	            // create mock data
	            var mailer = new DefaultMailer() { From = "from@mail.com", To = "to@mail.com", Subject = "Using Moq", Body = "Moq is awesome" };
	            // use mock to send email
	            var result = mailer.SendMail(mockMailClient.Object);
	            // verify result
	            mockMailClient.Verify(client => client.SendMail(It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>(), It.IsAny<string>()), Times.AtLeastOnce);
	            System.Console.WriteLine($"sent result: {result}");
	        }
	    }

# Demo 2 - make unit testing for entity framework based logic #
1. Create entity framework basic entities and dbcontext

		public class BloggingContext : DbContext 
	    { 
	        public virtual DbSet<Blog> Blogs { get; set; } // property must be virtual so that moq can create proxy
	        public virtual DbSet<Post> Posts { get; set; } // property must be virtual so that moq can create proxy
	    } 
	 
	    public class Blog 
	    { 
	        public int BlogId { get; set; } 
	        public string Name { get; set; } 
	        public string Url { get; set; } 
	 
	        public virtual List<Post> Posts { get; set; }
	    } 
	 
	    public class Post 
	    { 
	        public int PostId { get; set; } 
	        public string Title { get; set; } 
	        public string Content { get; set; } 
	 
	        public int BlogId { get; set; } 
	        public virtual Blog Blog { get; set; } 
	    } 

	Please note that the DbSet properties on the context are marked as virtual. This will allow the mocking framework to derive from our context and overriding these properties with a mocked implementation.

1. Create the service to be tested

	    public class BlogService 
	    { 
	        private BloggingContext _context; 
	 
	        public BlogService(BloggingContext context) 
	        { 
	            _context = context; 
	        } 
	 
	        public Blog AddBlog(string name, string url) 
	        { 
	            var blog = _context.Blogs.Add(new Blog { Name = name, Url = url }); 
	            _context.SaveChanges(); 
	 
	            return blog; 
	        } 
	 
	        public List<Blog> GetAllBlogs() 
	        { 
	            var query = from b in _context.Blogs 
	                        orderby b.Name 
	                        select b; 
	 
	            return query.ToList(); 
	        } 
	 
	        public async Task<List<Blog>> GetAllBlogsAsync() 
	        { 
	            var query = from b in _context.Blogs 
	                        orderby b.Name 
	                        select b; 
	 
	            return await query.ToListAsync(); 
	        } 
	    } 

1. Create mocks and test the saving logic

	    [TestClass] 
	    public class NonQueryTests 
	    { 
	        [TestMethod] 
	        public void CreateBlog_saves_a_blog_via_context() 
	        { 
				// set up mocks
	            var mockSet = new Mock<DbSet<Blog>>();	 
	            var mockContext = new Mock<BloggingContext>(); 
	            mockContext.Setup(m => m.Blogs).Returns(mockSet.Object); 

	 			// create service instance
	            var service = new BlogService(mockContext.Object); 
	            service.AddBlog("ADO.NET Blog", "http://blogs.msdn.com/adonet"); 

	 			// verify
	            mockSet.Verify(m => m.Add(It.IsAny<Blog>()), Times.Once()); 
	            mockContext.Verify(m => m.SaveChanges(), Times.Once()); 
	        } 
	    } 

	Here are steps:

	1. uses Moq to create a mock context
	2. creates a mock DbSet<Blog> and wires it up to be returned from the context’s Blogs property
	3. create a new BlogService using mock context. Then create a new blog using the AddBlog() method.
	4. verifies that the service added a new Blog and called SaveChanges on the context.

1. Create in-memory data and test the query logic

	    [TestClass] 
	    public class QueryTests 
	    { 
	        [TestMethod] 
	        public void GetAllBlogs_orders_by_name() 
	        { 
	            var data = new List<Blog> 
	            { 
	                new Blog { Name = "BBB" }, 
	                new Blog { Name = "ZZZ" }, 
	                new Blog { Name = "AAA" }, 
	            }.AsQueryable(); 
	 
	            var mockSet = new Mock<DbSet<Blog>>(); 
	            mockSet.As<IQueryable<Blog>>().Setup(m => m.Provider).Returns(data.Provider); 
	            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression); 
	            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType); 
	            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(0 => data.GetEnumerator()); 
	 
	            var mockContext = new Mock<BloggingContext>(); 
	            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object); 
	 
	            var service = new BlogService(mockContext.Object); 
	            var blogs = service.GetAllBlogs(); 
	 
	            Assert.AreEqual(3, blogs.Count); 
	            Assert.AreEqual("AAA", blogs[0].Name); 
	            Assert.AreEqual("BBB", blogs[1].Name); 
	            Assert.AreEqual("ZZZ", blogs[2].Name); 
	        } 
	    } 

	Here are steps:
	1. create some in-memory data – List<Blog>.
	2. create a mock context and mock DBSet<Blog> then wire up the IQueryable implementation for the DbSet – they’re just delegating to the LINQ to Objects provider that works with List<T>.

1. Test the async query logic

	In order to use the async methods we need to create an in-memory DbAsyncQueryProvider to process the async query. 

	    internal class TestDbAsyncQueryProvider<TEntity> : IDbAsyncQueryProvider 
	    { 
	        private readonly IQueryProvider _inner; 
	 
	        internal TestDbAsyncQueryProvider(IQueryProvider inner) 
	        { 
	            _inner = inner; 
	        } 
	 
	        public IQueryable CreateQuery(Expression expression) 
	        { 
	            return new TestDbAsyncEnumerable<TEntity>(expression); 
	        } 
	 
	        public IQueryable<TElement> CreateQuery<TElement>(Expression expression) 
	        { 
	            return new TestDbAsyncEnumerable<TElement>(expression); 
	        } 
	 
	        public object Execute(Expression expression) 
	        { 
	            return _inner.Execute(expression); 
	        } 
	 
	        public TResult Execute<TResult>(Expression expression) 
	        { 
	            return _inner.Execute<TResult>(expression); 
	        } 
	 
	        public Task<object> ExecuteAsync(Expression expression, CancellationToken cancellationToken) 
	        { 
	            return Task.FromResult(Execute(expression)); 
	        } 
	 
	        public Task<TResult> ExecuteAsync<TResult>(Expression expression, CancellationToken cancellationToken) 
	        { 
	            return Task.FromResult(Execute<TResult>(expression)); 
	        } 
	    } 
	 
	    internal class TestDbAsyncEnumerable<T> : EnumerableQuery<T>, IDbAsyncEnumerable<T>, IQueryable<T> 
	    { 
	        public TestDbAsyncEnumerable(IEnumerable<T> enumerable) 
	            : base(enumerable) 
	        { } 
	 
	        public TestDbAsyncEnumerable(Expression expression) 
	            : base(expression) 
	        { } 
	 
	        public IDbAsyncEnumerator<T> GetAsyncEnumerator() 
	        { 
	            return new TestDbAsyncEnumerator<T>(this.AsEnumerable().GetEnumerator()); 
	        } 
	 
	        IDbAsyncEnumerator IDbAsyncEnumerable.GetAsyncEnumerator() 
	        { 
	            return GetAsyncEnumerator(); 
	        } 
	 
	        IQueryProvider IQueryable.Provider 
	        { 
	            get { return new TestDbAsyncQueryProvider<T>(this); } 
	        } 
	    } 
	 
	    internal class TestDbAsyncEnumerator<T> : IDbAsyncEnumerator<T> 
	    { 
	        private readonly IEnumerator<T> _inner; 
	 
	        public TestDbAsyncEnumerator(IEnumerator<T> inner) 
	        { 
	            _inner = inner; 
	        } 
	 
	        public void Dispose() 
	        { 
	            _inner.Dispose(); 
	        } 
	 
	        public Task<bool> MoveNextAsync(CancellationToken cancellationToken) 
	        { 
	            return Task.FromResult(_inner.MoveNext()); 
	        } 
	 
	        public T Current 
	        { 
	            get { return _inner.Current; } 
	        } 
	 
	        object IDbAsyncEnumerator.Current 
	        { 
	            get { return Current; } 
	        } 
	    } 

	Then, use our Moq DbSet to test GetAllBlogsAsync() method

	    [TestClass] 
	    public class AsyncQueryTests 
	    { 
	        [TestMethod] 
	        public async Task GetAllBlogsAsync_orders_by_name() 
	        { 
	 
	            var data = new List<Blog> 
	            { 
	                new Blog { Name = "BBB" }, 
	                new Blog { Name = "ZZZ" }, 
	                new Blog { Name = "AAA" }, 
	            }.AsQueryable(); 
	 
	            var mockSet = new Mock<DbSet<Blog>>(); 
	            mockSet.As<IDbAsyncEnumerable<Blog>>() 
	                .Setup(m => m.GetAsyncEnumerator()) 
	                .Returns(new TestDbAsyncEnumerator<Blog>(data.GetEnumerator())); 
	 
	            mockSet.As<IQueryable<Blog>>() 
	                .Setup(m => m.Provider) 
	                .Returns(new TestDbAsyncQueryProvider<Blog>(data.Provider)); 
	 
	            mockSet.As<IQueryable<Blog>>().Setup(m => m.Expression).Returns(data.Expression); 
	            mockSet.As<IQueryable<Blog>>().Setup(m => m.ElementType).Returns(data.ElementType); 
	            mockSet.As<IQueryable<Blog>>().Setup(m => m.GetEnumerator()).Returns(data.GetEnumerator()); 
	 
	            var mockContext = new Mock<BloggingContext>(); 
	            mockContext.Setup(c => c.Blogs).Returns(mockSet.Object); 
	 
	            var service = new BlogService(mockContext.Object); 
	            var blogs = await service.GetAllBlogsAsync(); 
	 
	            Assert.AreEqual(3, blogs.Count); 
	            Assert.AreEqual("AAA", blogs[0].Name); 
	            Assert.AreEqual("BBB", blogs[1].Name); 
	            Assert.AreEqual("ZZZ", blogs[2].Name); 
	        } 
	    } 

# FAQ #
1. loose mock vs. strict mock

	By default, Moq use loose mock strategy for every mock object and it saves lines of code. If we specify a mock object as strict mode, each method of the mock object must has a corresponding Setup() method

		var mockObj = new Mock<IMailClient>(MockBehavior.Strict);

	Generally, we prefer loose mock as strict mock might break whenever we modidy the unit testing.

1. what can be mocked?
	
	Moq and other similar mocking frameworks can only mock interfaces, abstract methods/properties (on abstract classes) or virtual methods/properties on concrete classes.
	
	This is because Moq generates a proxy that will implement the interface or create a derived class that overrides those overrideable methods in order to intercept calls.

# References #
[Moq documentation](http://www.nudoq.org/#!/Projects/Moq)

[Entity Framework Testing with a Mocking Framework ](https://msdn.microsoft.com/en-us/library/dn314429(v=vs.113).aspx)