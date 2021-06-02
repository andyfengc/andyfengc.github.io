---
layout: post
title: Entity Framework working with stored procedure
author: Andy Feng
---

# Preparation database #
create a Blog table

	CREATE TABLE Blogs(
		BlogId int IDENTITY(1,1) PRIMARY KEY,
		Name nvarchar(50),
		Url nvarchar(50)
	)

Populate some data

	INSERT INTO dbo.Blogs
	        ( Name, Url )
	VALUES
		  ( 'Andy', 'https://andyfeng.ga'),
		  ( 'Microsoft', 'https://msn.com'),
		  ( 'Google', 'https://google.ca'),
		  ( 'Bell', 'https://bell.ca')

Create some stored procedures

	-- search by name
	-- EXEC [Blog_Search] 'andy'
	CREATE PROCEDURE [dbo].[Blog_Search]  
	  @Name nvarchar(max)
	AS  
	BEGIN 
	  SELECT [BlogId]
	      ,[Name]
	      ,[Url]
	  FROM [Test].[dbo].[Blogs]
	  WHERE Name = @Name
	END

	-- insert 
	-- EXEC [Blog_Insert] 'ibm', 'https://ibm.com'
	CREATE PROCEDURE [dbo].[Blog_Insert]  
	  @Name nvarchar(max),  
	  @Url nvarchar(max)  
	AS  
	BEGIN 
	  INSERT INTO [dbo].[Blogs] ([Name], [Url]) 
	  VALUES (@Name, @Url) 
	 
	  SELECT SCOPE_IDENTITY() AS BlogId 
	END
	
	-- update 
	CREATE PROCEDURE [dbo].[Blog_Update]  
	  @BlogId int,  
	  @Name nvarchar(max),  
	  @Url nvarchar(max)  
	AS  
	  UPDATE [dbo].[Blogs] 
	  SET [Name] = @Name, [Url] = @Url     
	  WHERE BlogId = @BlogId;
	
	-- delete
	CREATE PROCEDURE [dbo].[Blog_Delete]  
	  @BlogId int  
	AS  
	  DELETE FROM [dbo].[Blogs] 
	  WHERE BlogId = @BlogId

# Create entities
Create entity Blog.cs

    [Table("Blogs")]
    public class Blog
    {
        public int BlogId { get; set; }
        public string Name { get; set; }
        public string Url { get; set; }
        public override string ToString()
        {
            return $"Blog {BlogId}: {Name} - {Url}";
        }
    }

Create db context: LocalContext

	```csharp
    public class LocalContext : DbContext
    {
        public LocalContext()
        {
            Database.SetInitializer<LocalContext>(null);
        }
        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {

            base.OnModelCreating(modelBuilder);
        }
    }

	```

# using .net framework
Add connenction string: app.config

	<connectionStrings>
    ...
		<add name="LocalContext" providerName="System.Data.SqlClient" connectionString="Server=(local);Database=Test;Integrated Security=True; MultipleActiveResultSets=True" />
	</connectionStrings>

## way1, call the stored procedure directly  ##
1. use SqlQuery()

	```csharp
	using (var context = new LocalContext())
    {
        SqlParameter param1 = new SqlParameter("@Name", "Andy");
        var blog = context.Database.SqlQuery<Blog>("Blog_Search @Name", param1).FirstOrDefault(); // or "exec Blog_Search @Name"
        //var blog = context.Database.SqlQuery<Blog>("select * from dbo.Blogs where name = 'andy'").FirstOrDefault();
        System.Console.WriteLine(blog);
    }
	```

1. we get
	
	![](/images/posts/20180208-stored-procedure-1.png)

`Please note` 
1. the order of parameters must be the exact same order of stored procedure.
2. the data type of return value from stored procedure must be consistent with the model `Blog`. double(c#) <-> float (sql). If not, do cast in stored procedure.

1. use ExecuteSqlCommand() to update a blog

	```csharp
    using (var context = new LocalContext())
    {
        SqlParameter param1 = new SqlParameter("@Name", "Andy");
        var blog = context.Database.SqlQuery<Blog>("exec Blog_Search @Name", param1).FirstOrDefault();
        //var blog = context.Database.SqlQuery<Blog>("select * from dbo.Blogs where name = 'andy'").FirstOrDefault();
        System.Console.WriteLine(blog);
        // update 
        context.Database.ExecuteSqlCommand("exec Blog_Update @BlogId, @Name, @Url"
            , new SqlParameter("@BlogId", 1)
            , new SqlParameter("@Name", "Andy")
            , new SqlParameter("@Url", "http://www.andyfeng.ga"));
        blog = context.Database.SqlQuery<Blog>("exec Blog_Search @Name", new SqlParameter("@Name", "Andy")).FirstOrDefault();
        System.Console.WriteLine("updated" + blog);
    }

	```

1. we get
	
	![](/images/posts/20180208-stored-procedure-2.png)

## way2, use stored procedure mapping  ##

1. Update LocalContext to specify stored procedure mapping

	```csharp
    public class LocalContext : DbContext
    {
        public DbSet<Blog> Blogs { get; set; }
        public LocalContext()
        {
            Database.SetInitializer<LocalContext>(null);
        }
        protected override void OnModelCreating(DbModelBuilder modelBuilder)
        {
            modelBuilder
              .Entity<Blog>()
              .MapToStoredProcedures(s =>
              {
                  s.Update(u => u.HasName("Blog_Update").Parameter(sp => sp.BlogId, "BlogId"));
                  s.Delete(d => d.HasName("Blog_Delete"));
                  s.Insert(i => i.HasName("Blog_Insert"));
              }
            );
            base.OnModelCreating(modelBuilder);
        }
    }

	```

1. Since mapping is ready, now we can use regular Entity Framework methods to add a blog


	```csharp

	    using (var context = new LocalContext())
	    {
	        SqlParameter param1 = new SqlParameter("@Name", "Andy");
	        var blog = context.Database.SqlQuery<Blog>("exec Blog_Search @Name", param1).FirstOrDefault();
	        //var blog = context.Database.SqlQuery<Blog>("select * from dbo.Blogs where name = 'andy'").FirstOrDefault();
	        System.Console.WriteLine(blog);
	        // add a new blog
	        var newBlog = new Blog() {Name = "John", Url = "http://www.john.com"};
	        context.Blogs.Add(newBlog);
	        context.SaveChanges();
	        blog = context.Database.SqlQuery<Blog>("exec Blog_Search @Name", new SqlParameter("@Name", "john")).FirstOrDefault();
	        System.Console.WriteLine(blog);
	    }

	```

1. we get
	
	![](/images/posts/20180208-stored-procedure-3.png)

	![](/images/posts/20180208-stored-procedure-4.png)

# .net core 2.1
Add connenction string: appsettings.config

	{
	  ...
	  "ConnectionStrings": {
		"LocalContext" : "Server=(local);Database=Test;Integrated Security=True; MultipleActiveResultSets=True"
	  }
	}

Entity Framework Core (previously known as Entity Framework 7) is a new version of EF designed for use with the new ASP.NET Core framework, which is intended for cross-platform development. Instead of SqlQuery(), stored procedures will be called by a new DbSet method - FromSql

EF Core provides the following methods to execute a stored procedure:

	DbSet<TEntity>.FromSql()
	DbContext.Database.ExecuteSqlCommand()

e.g.

	using (var context = new LocalContext())
	{
	    var blogs = await context.Blogs.FromSql("spGetBlogs").ToListAsync();
	}

or 
	using (var context = new LocalContext())
	{
	    await context.Database.ExecuteSqlCommandAsync("exec usp");
	}

or 

	using (var context = new LocalContext())
	{
	    var blogs = await context.Database.ExecuteSqlCommandAsync("exec usp"
			, new SqlParameter("@param_Name", "param value")
		);
	}

# .net core 3.x
EF Core 3.x+ provides two raw SQL sets of methods - FromSqlRow(replacing FromSql) and ExecuteSql, both with Raw / Interpolated and Async versions.

need install below libs via nuget

- Microsoft.EntityFrameworkCore
- Microsoft.EntityFrameworkCore.Proxies
- Microsoft.EntityFrameworkCore.Relational
- Microsoft.Data.SqlClient (replacing System.Data.SqlClient)
- Microsoft.EntityFrameworkCore. sometimes for instance WebApi project

The former are used for querying. They return IQueryable<T>, allow query composition and as any LINQ query are not executed until the result is enumerated.

	using (var context = new LocalContext())
	{
		var blogs = context.Database.ExecuteSqlRaw("ClearIterations");
	}

# References #

[https://msdn.microsoft.com/en-us/library/dn468673(v=vs.113).aspx](https://msdn.microsoft.com/en-us/library/dn468673(v=vs.113).aspx)

[http://www.entityframeworktutorial.net/entityframework6/code-first-insert-update-delete-stored-procedure-mapping.aspx](http://www.entityframeworktutorial.net/entityframework6/code-first-insert-update-delete-stored-procedure-mapping.aspx)

[http://www.c-sharpcorner.com/UploadFile/ff2f08/code-first-stored-procedure-entity-framework-6-0/](http://www.c-sharpcorner.com/UploadFile/ff2f08/code-first-stored-procedure-entity-framework-6-0/)

[https://www.mikesdotnetting.com/article/299/entity-framework-code-first-and-stored-procedures](https://www.mikesdotnetting.com/article/299/entity-framework-code-first-and-stored-procedures)