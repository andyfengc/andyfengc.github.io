---
layout: post
title: ASP.NET Identity authentication 2 - identity framework fundamentals
author: Andy Feng
---

# Introduction #
ASP.NET Core Identity is a membership system that adds login functionality to ASP.NET Core apps. We can create an account with the login information stored in Identity or they can use an external login provider. Supported external login providers include Facebook, Google, Microsoft Account, and Twitter.

Identity can be configured using a SQL Server database to store user names, passwords, and profile data. Alternatively, another persistent store can be used, for example, Azure Table Storage.

All of the Microsoft.Owin.Security.* packages shipping with the new Identity System in Visual Studio (for example: Cookies, Microsoft Account, Google, Facebook, Twitter, Bearer Token, OAuth, Authorization server, JWT, Azure Active directory, and Active directory federation services) are authored as OMCs, and can be used in both self-hosted and IIS-hosted scenarios.

There are two major techniques of implementing server side authentication for apps with a frontend and an API:

- The most common one, is Cookie-Based Authentication that uses server side cookies to authenticate the user on every request.

- A newer approach, Token-Based Authentication, relies on a signed token that is sent to the server on each request.

There are also other techniques to implement authentication. For instance, filters. this is beyond identity framework.

# Outline #n
- Create web project skeleton (ASP.NET, .NET CORE)
- Add identity support
- Add a basic add user feature
- Customize data models
- Add cookie-based authentication
- Add token-based authentication
- Add filter authentication

# Identity in ASP.NET 
## Create web project skeleton
1. Create an empty web project

1. add mvc support
 
find the tutorial at [web project tutorial]

## Add identity support
### add libraries

Open nuget, install microsoft.owin.host.systemweb library. It install Owin lib as well

![](/images/20160604-add-identity-1.png)

Install owin basic security libs: Microsoft.Owin.Security, Microsoft.Owin.Security.OAuth, Microsoft.Owin.Security.Cookies library

![](/images/20160604-add-identity-2.png)

Install identity packages, include Microsoft.AspNet.Identity.Core, Microsoft.AspNet.Identity.Owin, Microsoft.AspNet.Identity.EntityFramework

![](/images/20160604-add-identity-3.png)

Now, start the project we will get errors. It means we need to configure Owin

![](/images/20160604-add-identity-4.png)

### Configuration
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

## Add a basic add user feature 

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

## Customize data models 

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

# Identity in .NET CORE
All the Identity dependent NuGet packages are included in the `Microsoft.AspNetCore.App` metapackage.

The primary package for Identity is `Microsoft.AspNetCore.Identity`. This package contains the core set of interfaces for ASP.NET Core Identity, and is included and implemented by `Microsoft.AspNetCore.Identity.EntityFrameworkCore`.

## Create a Web app with authentication
Let's create a .NET core web project use scaffolding. File > New > Project > Select ASP.NET Core Web Application > Name the project Security > Click OK.

![](/images/posts/20181024-identity-1.png)

Select ASP.NET Core 2.1 > Change Authentication > Select Individual User Accounts > click OK.

![](/images/posts/20181024-identity-2.png)

![](/images/posts/20181024-identity-3.png)

Please note that Identity is enabled by calling UseAuthentication in `Startup.cs`

    public class Startup
    {
        ...

        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
            ...
            services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
        }

        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
        {
            ...
            app.UseAuthentication();
            app.UseMvc();
        }
    }

## Test Register and Login

Start the project > register a new account

![](/images/posts/20181024-identity-4.png)

![](/images/posts/20181024-identity-5.png)

Verify the identity database > connect (localdb)\mssqllocaldb 

![](/images/posts/20181024-identity-7.png)

## Scaffold identity into a Razor project
Please note that ASP.NET Core 2.1 introduced new feature called `Razor class libraries` that lets us build views and pages as part of reusable library. ASP.NET Core Identity UI was shipped as a library(RCL) and it it avaiable at [Microsoft.AspNetCore.Identity.UI](https://www.nuget.org/packages/Microsoft.AspNetCore.Identity.UI). We can override it in our project:

1. From Solution Explorer, right-click on the project > Add > New Scaffolded Item.
1. From the left pane of the Add Scaffold dialog, select Identity > ADD.
	![](/images/posts/20181024-identity-8.png)

1. In the ADD Identity dialog, choose files you wish to override.
	![](/images/posts/20181024-identity-9.png)

1. Select layout if necessary
1. Select or create Data context class.
1. Hit ADD.

For instance, after select override Account\Register, we got

	![](/images/posts/20181024-identity-10.png)

We can override Register associated files. Please note Identity options are configured in Areas/Identity/IdentityHostingStartup.cs

Next, remove below redundant code in

	public class Startup
    {
		...
        // This method gets called by the runtime. Use this method to add services to the container.
        public void ConfigureServices(IServiceCollection services)
        {
			...
			// remove, already configured in IdentityHostingStartup.cs
            services.AddDefaultIdentity<IdentityUser>()
                .AddEntityFrameworkStores<ApplicationDbContext>();
        }
		...

# FAQ
1. Cannot create identity database on the fly.

	![](/images/posts/20181024-identity-6.png)
	
	solution: click `Apply Migrations` > refresh page


# References #
[https://github.com/auth0/blog/blob/master/_posts/2014-01-07-angularjs-authentication-with-cookies-vs-token.markdown](https://github.com/auth0/blog/blob/master/_posts/2014-01-07-angularjs-authentication-with-cookies-vs-token.markdown)

[https://blog.jayway.com/2014/09/25/securing-asp-net-web-api-endpoints-using-owin-oauth-2-0-and-claims/](https://blog.jayway.com/2014/09/25/securing-asp-net-web-api-endpoints-using-owin-oauth-2-0-and-claims/)

[https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/owin-middleware-in-the-iis-integrated-pipeline](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/owin-middleware-in-the-iis-integrated-pipeline)

[Introduction to Identity on ASP.NET Core](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/identity?view=aspnetcore-2.1&tabs=visual-studio)