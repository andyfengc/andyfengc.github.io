---
layout: post
title: ASP.NET Identity authentication 1 - add identity framework to mvc project
author: Andy Feng
---

## Create mvc project ##
### create new empty web application ###

![](/images/posts/20160604-create-project-1.png)

![](/images/posts/20160604-create-mvc-project-2.png)

![](/images/posts/20160604-create-mvc-project-3.png)

### Add mvc support ###
Open nuget, install mvc library
![](/images/posts/20160604-create-mvc-project-4.png)

Create App_Start folder, create RouteConfig.cs file and FilterConfig.cs file under this folder. Also, create global.asax file

![](/images/posts/20160604-create-mvc-project-5.png)

![](/images/posts/20160604-create-mvc-project-6.png)

![](/images/posts/20160604-create-mvc-project-7.png)

![](/images/posts/20160604-create-mvc-project-8.png)

### quick test ###

Add Controllers folder, create a new Controller class, HomeController.cs

![](/images/posts/20160604-create-mvc-project-9.png)

Start project, succeed

![](/images/posts/20160604-create-mvc-project-10.png)

## Add identity support ##

### add libraries ###

Open nuget, install microsoft.owin.host.systemweb library. It installs Owin lib as well

![](/images/posts/20160604-add-identity-1.png)

Install owin basic security libs: Microsoft.Owin.Security, Microsoft.Owin.Security.OAuth, Microsoft.Owin.Security.Cookies library

![](/images/posts/20160604-add-identity-2.png)

Install identity packages, include Microsoft.AspNet.Identity.Core, Microsoft.AspNet.Identity.Owin, Microsoft.AspNet.Identity.EntityFramework

![](/images/posts/20160604-add-identity-3.png)

Install owin webapi package: Microsoft.AspNet.WebApi.Owin

Now, start the project we will get errors. It means we need to configure Owin

![](/images/posts/20160604-add-identity-4.png)

### Configuration ###
Create Startup.cs file in the root. 

![](/images/posts/20160604-add-identity-5.png)

	[assembly: OwinStartupAttribute(typeof(IdentityDemo.Startup))]
	namespace IdentityDemo
	{
	    
	    public class Startup
	    {
	        public void Configuration(IAppBuilder app)
	        {
	        }
	    }
	}

Restart project, we will see the success message as previous.

![](/images/posts/20160604-create-mvc-project-10.png)

Identity component is added successfully.

## Add a basic add user feature ##

Add connection string in the Web.config, connection name is IdentityDemo
	
	<connectionStrings>
    	<add connectionString="Data Source=(local); Initial Catalog=IdentityDemo; MultipleActiveResultSets=true; Integrated Security=true;" name="IdentityContext" providerName="System.Data.SqlClient"/>
	</connectionStrings>

Modify HomeController.cs

	   	[HttpGet]
		public async Task<IHttpActionResult> User()
		{
		    var db = new IdentityDbContext<IdentityUser>("IdentityContext");
		    var store = new UserStore<IdentityUser>(db);
		    var manager = new UserManager<IdentityUser>(store);
		    var email = "andy@gmail.com";
		    var password = "Pass123";
		    var user = await manager.FindByEmailAsync(email); 
		    if (user == null)
		    {
		        user = new IdentityUser
		        {
		            UserName = email,
		            Email = email
		        };
		        await manager.CreateAsync(user, password);
		    }
		    return Json("Create user successfully");
		}

Please note that by default, password should compost of Upper case, lower case and digits.

![](/images/posts/20160604-add-identity-6.png)

Start project and access this method, we get the success message

![](/images/posts/20160604-add-identity-7.png)

Open Sql Server Management Studio, we will find the IdentityDemo database was created and a new user is inserted.

![](/images/posts/20160604-add-identity-8.png)

## Customize user ##

Create Models folder, add User class which inherits IdentityUser

	public class User : IdentityUser
    {
        public string Skype { get; set; }
        public double Salary { get; set; }
    }

Modify previous code, replace IdentityUser with User

	public async Task<ActionResult> User()
        {
            var db = new IdentityDbContext<User>("IdentityContext");
            var store = new UserStore<User>(db);
            var manager = new UserManager<User>(store);
            var email = "andy@gmail.com";
            var password = "Pass123";
            var user = await manager.FindByEmailAsync(email);
            if (user == null)
            {
                user = new User
                {
                    UserName = email,
                    Email = email,
                    Skype = "andy@skype.com",
                    Salary = 3000
                };
                await manager.CreateAsync(user, password);
            }
            return Content("Create user successfully");
        }

Drop the database, restart the application

Now the database will be recreated and the customized columns appear

![](/images/posts/20160604-add-identity-9.png)

# Reference #
[https://blog.jayway.com/2014/09/25/securing-asp-net-web-api-endpoints-using-owin-oauth-2-0-and-claims/](https://blog.jayway.com/2014/09/25/securing-asp-net-web-api-endpoints-using-owin-oauth-2-0-and-claims/)