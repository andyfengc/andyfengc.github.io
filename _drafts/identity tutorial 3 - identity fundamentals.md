---
layout: post
title: ASP.NET Identity authentication 3 - identity framework fundamentals
author: Andy Feng
---

# Introduction #
There are basically two different ways of implementing server side authentication for apps with a frontend and an API:

The most common one, is Cookie-Based Authentication that uses server side cookies to authenticate the user on every request.

A newer approach, Token-Based Authentication, relies on a signed token that is sent to the server on each request.

# Outline #n
- Create web project skeleton
- Add identity support
- Add a basic add user feature
- Customize data models
- add cookie-based authentication
- Add token-based authentication

# Create web project skeleton #
1. Create an empty web project

1. add mvc support
 
find the tutorial at [web project tutorial]

# Add identity support #
## add libraries ##

Open nuget, install microsoft.owin.host.systemweb library. It install Owin lib as well

![](/images/20160604-add-identity-1.png)

Install owin basic security libs: Microsoft.Owin.Security, Microsoft.Owin.Security.OAuth, Microsoft.Owin.Security.Cookies library

![](/images/20160604-add-identity-2.png)

Install identity packages, include Microsoft.AspNet.Identity.Core, Microsoft.AspNet.Identity.Owin, Microsoft.AspNet.Identity.EntityFramework

![](/images/20160604-add-identity-3.png)

Now, start the project we will get errors. It means we need to configure Owin

![](/images/20160604-add-identity-4.png)

## Configuration ##
Create Startup.cs file. 

![](/images/20160604-add-identity-5.png)

	[assembly: OwinStartupAttribute(typeof(IdentityDemo.Startup))]
	namespace IdentityDemo
	{
	    
	    public partial class Startup
	    {
	        public void Configuration(IAppBuilder app)
	        {
	        }
	    }
	}

Restart project, we will see the success message as previous.

![](/images/20160604-create-mvc-project-10.png)

Identity component is added successfully.

# Add a basic add user feature #

Add connection string in the Web.config, connection name is IdentityDemo
	
	<connectionStrings>
    	<add connectionString="Data Source=(local); Initial Catalog=IdentityDemo; MultipleActiveResultSets=true; Integrated Security=true;" name="IdentityContext" providerName="System.Data.SqlClient"/>
	</connectionStrings>

Modify HomeController.cs

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
	        return Ok("Create user successfully");
	    }

Please note that by default, password should compost of Upper case, lower case and digits.

![](/images/20160604-add-identity-6.png)

Start project and access this method, we get the success message

![](/images/20160604-add-identity-7.png)

Open Sql Server Management Studio, we will find the IdentityDemo database was created and a new user is inserted.

![](/images/20160604-add-identity-8.png)

# Customize data models #

Create Models folder

add User class which inherits IdentityUser

	public class ApplicationUser : IdentityUser
    {
        public string Skype { get; set; }
        public double Salary { get; set; }
    }

add ApplicationRole:

    public class ApplicationRole : IdentityRole
    {
    }	

add ApplicationDbContext:

    public class ApplicationDbContext : IdentityDbContext<ApplicationUser>
    {
        public ApplicationDbContext()
            : base("IdentityContext", throwIfV1Schema: false)
        {
        }
        public static ApplicationDbContext Create()
        {
            return new ApplicationDbContext();
        }
    }

add ApplicationUserManager:

    public class ApplicationUserManager : UserManager<ApplicationUser>
    {
        public ApplicationUserManager(IUserStore<ApplicationUser> store) : base(store)
        {
        }
    }

Modify previous code, replace IdentityUser with User

	public async Task<IHttpActionResult> User()
        {
            var db = new ApplicationDbContext();
            var store = new UserStore<ApplicationUser>(db);
            var manager = new ApplicationUserManager(store);
            var email = "andy@gmail.com";
            var password = "Pass123";
            var user = await manager.FindByEmailAsync(email);
            if (user == null)
            {
                user = new ApplicationUser
                {
                    UserName = email,
                    Email = email
                };
                await manager.CreateAsync(user, password);
            }
            return Ok("Create user successfully");
        }

Drop the database, restart the application

Now the database will be recreated and the customized columns appear

![](/images/20160604-add-identity-9.png)

# add https support #

[https://github.com/MikeWasson/LocalAccountsApp](https://github.com/MikeWasson/LocalAccountsApp)

# References #
[https://github.com/auth0/blog/blob/master/_posts/2014-01-07-angularjs-authentication-with-cookies-vs-token.markdown](https://github.com/auth0/blog/blob/master/_posts/2014-01-07-angularjs-authentication-with-cookies-vs-token.markdown)

[https://blog.jayway.com/2014/09/25/securing-asp-net-web-api-endpoints-using-owin-oauth-2-0-and-claims/](https://blog.jayway.com/2014/09/25/securing-asp-net-web-api-endpoints-using-owin-oauth-2-0-and-claims/)