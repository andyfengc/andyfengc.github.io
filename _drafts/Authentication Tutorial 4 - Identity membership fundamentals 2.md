---
layout: post
title: ASP.NET Identity authentication (5) - identity framework fundamentals 2
author: Andy Feng
---

# Introduction #
Authentication and Authorization are two important terms about the security of web applications. Authentication is used to validate user's credentials. Authorization is used to grant access to one or more resources of the application to a user.

There are 2 major ways to implement authorization in ASP.NET Core: `role-based` authorization and `policy-based` authorization.

# Role based authorization
Role based authorization is designed to map a set of permissions for authenticated user. It is a classic authorization model. 

> Role is nothing but a string. User must be a member of role(s) to access resources.
> 
> `[Authorize]` attribute is used to restrict access to resources based on roles. If this attribute is used without any arguments, it only checks if user is authenticated. If the attribute is used with `Roles`, it checks if user is belong a the specified role(s).

1. Register role-based authorization services

	Program.cs
		
		builder.Services.AddDefaultIdentity<IdentityUser>( ... )
		    .AddRoles<IdentityRole>()
		    ...

	i.e.

		var builder = WebApplication.CreateBuilder(args);
		
		var connectionString = builder.Configuration.GetConnectionString("DefaultConnection");
		builder.Services.AddDbContext<ApplicationDbContext>(options =>
		    options.UseSqlServer(connectionString));
		builder.Services.AddDatabaseDeveloperPageExceptionFilter();
		
		builder.Services.AddDefaultIdentity<IdentityUser>(
		    options => options.SignIn.RequireConfirmedAccount = true)
		    .AddRoles<IdentityRole>()
		    .AddEntityFrameworkStores<ApplicationDbContext>();

1. Apply [attribute] attribute to controller/action in .NET MVC. Or, use <AuthorizeView> tag in blazor.

		[Authorize]
		public class UserController : Controller
		{
		  //Action methods
		}

	or

		[Authorize(Roles = "Manager,Administrator")]
		public class DocumentsController : Controller
		{
		   //Action methods
		}

	or

		[Authorize(Roles = "Manager, Administrator")]
		public class DocumentsController : Controller
		{
		    public ActionResult ViewDocument()
		    {
		        //Your code here
		    }

		    [Authorize(Roles = "Administrator")]
		    public ActionResult DeleteAllDocuments()
		    {
		        //Your code here
		    }
		}

	Remember attribute in Action method always overrides the attribute in Controller.

	or

		[Authorize(Roles = "Manager")]
		[Authorize(Roles = "Administrator")]
		public class DocumentsController  : Controller
		{
		}

	Users who belong to both Manager and Administrator roles have access to the DocumentsController and its methods

However, role-based authorization has limitations:

1. In real world, we might need variation of roles. For example, there are 3 roles in an application, i.e., User, Admin, and Manager. Now if we have multiple variations of the Admin role, like, CustomerAdmin, ReportsAdmin, and SuperAdmin, we have to consider each of variation as an individual. The coding will become very time-consuming. we need policy based authorization to handle this.

# Claim based authorization
Claim is a name-value pair which can be used to decide whether you can access a resource. Claims based authorization, basically, checks the value of a claim and allows access to a resource based on that value. Claims specifies something which the current user must possess, and optionally the value the claim must hold to access the requested resource. Claims requirements are policy based, the developer must build and register a policy expressing the claims requirements.

> The simplest type of claim policy looks for the presence of a claim and doesn't check the value.
> 
> Role is a type of claim
> 
> an identity can contain multiple claims with multiple values

Register claim based authorization:

Program.cs

	var builder = WebApplication.CreateBuilder(args);
	
	builder.Services.AddRazorPages();
	builder.Services.AddControllersWithViews();
	
	// add claim authorization
	builder.Services.AddAuthorization(options =>
	{
	   options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber"));
	});
	
	var app = builder.Build();
	
	if (!app.Environment.IsDevelopment())
	{
	    app.UseExceptionHandler("/Error");
	    app.UseHsts();
	}
	
	app.UseHttpsRedirection();
	app.UseStaticFiles();
	
	app.UseAuthentication();
	app.UseAuthorization(); // add authorization
	
	app.MapDefaultControllerRoute();
	app.MapRazorPages();

	app.Run();

In this case the EmployeeOnly policy checks for the presence of an EmployeeNumber claim on the current identity. but it doesn't check the exact value.

Next, apply the policy using the Policy property on the [Authorize] attribute to specify the policy name;

	[Authorize(Policy = "EmployeeOnly")]
	public IActionResult VacationBalance()
	{
	    return View();
	}

Typically, claims come with a value. You can specify a list of allowed values when creating the policy. for example below would only succeed for employees whose employee number was 1, 2, 3, 4 or 5.

	...
	builder.Services.AddAuthorization(options =>
	{
	    options.AddPolicy("Founders", policy =>
	                      policy.RequireClaim("EmployeeNumber", "1", "2", "3", "4", "5"));
	});
	...

If we want more complicated policies such as taking a date of birth claim, calculating an age from it then checking the age is 21 or older then you need to write custom policy handlers.

# Policy based authorization
Policy based authorization implements authorization model of role-based authorization and claim-based authorization to secure applications. 
> Build and register the policy use `AddAuthorization()` 

## Policy implement claim based authorization
Policy can be used to register claim based authentication:

	var builder = WebApplication.CreateBuilder(args);
	
	builder.Services.AddRazorPages();
	builder.Services.AddControllersWithViews();
	
	// In this case "EmployeeOnly" policy only checks the presence of an EmployeeNumber claim on current identity
	builder.Services.AddAuthorization(options =>
	{
	   options.AddPolicy("EmployeeOnly", policy => policy.RequireClaim("EmployeeNumber")); // here
	});
	
	var app = builder.Build();
	
	if (!app.Environment.IsDevelopment())
	{
	    app.UseExceptionHandler("/Error");
	    app.UseHsts();
	}
	
	app.UseHttpsRedirection();
	app.UseStaticFiles();
	
	app.UseAuthentication();
	app.UseAuthorization(); // here
	
	app.MapDefaultControllerRoute();
	app.MapRazorPages();
	
	app.Run();

Then, in controller:

	[Authorize(Policy = "EmployeeOnly")]
	public IActionResult VacationBalance()
	{
	    return View();
	}

# Policy implement role-based authorization
Policy syntax can also be used to register role-based authorization, Startup.cs/Program.cs:

way1:

	builder.Services.AddAuthorization(options =>
	{
	    options.AddPolicy("RequireAdministratorRole",
	         policy => policy.RequireRole("Administrator"));
	});

	or
	
	services.AddMvc(obj =>
	    {
	        var policy = new AuthorizationPolicyBuilder()
	            .RequireAuthenticatedUser()
	            .Build();
	        obj.Filters.Add(new AuthorizeFilter(policy));
	    });

way2:

	var policy = new AuthorizationPolicyBuilder()
	  .RequireAuthenticatedUser()
	  .RequireRole("Admin")
	  .Build();
	
    services.AddAuthorization(options =>
    {
        options.AddPolicy("RequireManagerOnly", policy => 
              policy.RequireRole("Manager","Administrator"));
    });

## Policy, Requirement and Requirement handler
Essentially, role-based authorization and claims-based authorization use a requirement, a requirement handler, and a preconfigured policy. They can be used to implement complex authorization logic.

> An authorization policy consists of one or more requirements. A requirement contains data parameters to validate the user’s identity. Lastly, a handler is used to determine if a user has access to a specific resource. 

Register policy and a requirement in the app's Program.cs:

	builder.Services.AddAuthorization(options =>
	{
	    options.AddPolicy("AtLeast21", policy =>
	        policy.Requirements.Add(new MinimumAgeRequirement(21)));
	});

An authorization requirement is a collection of data parameters that a policy can use to evaluate the current user principal. In our "AtLeast21" policy, the requirement is a single parameter—the minimum age. 
> A requirement implements IAuthorizationRequirement, which is an empty marker interface.
> A requirement can be used to implement complex authorization logic based on users' identity

	using Microsoft.AspNetCore.Authorization;
	public class MinimumAgeRequirement : IAuthorizationRequirement
	{
	    public MinimumAgeRequirement(int minimumAge) =>
	        MinimumAge = minimumAge;
	
	    public int MinimumAge { get; }
	}

An authorization handler is responsible for the evaluation of a requirement's properties. There are 2 ways to create handler. A handler may inherit AuthorizationHandler<TRequirement>, where TRequirement is the requirement to be handled. Or, a handler may implement IAuthorizationHandler 
> A requirement can have multiple handlers. 

Here is an example to use a handler for one requirement

	using System.Security.Claims;
	using AuthorizationPoliciesSample.Policies.Requirements;
	using Microsoft.AspNetCore.Authorization;
	
	public class MinimumAgeHandler : AuthorizationHandler<MinimumAgeRequirement>
	{
	    protected override Task HandleRequirementAsync(
	        AuthorizationHandlerContext context, MinimumAgeRequirement requirement)
	    {
	        var dateOfBirthClaim = context.User.FindFirst(
	            c => c.Type == ClaimTypes.DateOfBirth && c.Issuer == "http://contoso.com");
	
	        if (dateOfBirthClaim is null)
	        {
	            return Task.CompletedTask; // authorization reject
	        }
	
	        var dateOfBirth = Convert.ToDateTime(dateOfBirthClaim.Value);
	        int calculatedAge = DateTime.Today.Year - dateOfBirth.Year;
	        if (dateOfBirth > DateTime.Today.AddYears(-calculatedAge))
	        {
	            calculatedAge--;
	        }
	
	        if (calculatedAge >= requirement.MinimumAge)
	        {
	            context.Succeed(requirement); // authorization successful
	        }
	
	        return Task.CompletedTask; // authorization reject
	    }
	}

another example:

	// requirement
	public class MinimumExpRequirement : IAuthorizationRequirement
	{
	    public int MinimumExp { get; set; }
	    public MinimumExpRequirement(int experience)
	    {
	        MinimumExp = experience;
	    }    
	}

	// requirement handler
	public class MinimumExpHandler : 
	          AuthorizationHandler<MinimumExpRequirement>
	    {
	        protected override Task HandleRequirementAsync(
	               AuthorizationHandlerContext context, 
	               MinimumExpRequirement requirement)
	        {
	            var user = context.User;
	            var claim = context.User.FindFirst("MinExperience");
	            if(claim != null)
	            {
	                var expInYears = int.Parse(claim?.Value);
	                if (expInYears >= requirement.MinimumExp)
	                    context.Succeed(requirement);
	            }
	            return Task.CompletedTask;
	        }
	    }

Here is an example to use a handler for multiple requirements:

	using System.Security.Claims;
	using AuthorizationPoliciesSample.Policies.Requirements;
	using Microsoft.AspNetCore.Authorization;
	
	public class PermissionHandler : IAuthorizationHandler
	{
	    public Task HandleAsync(AuthorizationHandlerContext context)
	    {
	        var pendingRequirements = context.PendingRequirements.ToList();
	
	        foreach (var requirement in pendingRequirements)
	        {
	            if (requirement is ReadPermission)
	            {
	                if (IsOwner(context.User, context.Resource)
	                    || IsSponsor(context.User, context.Resource))
	                {
	                    context.Succeed(requirement);
	                }
	            }
	            else if (requirement is EditPermission || requirement is DeletePermission)
	            {
	                if (IsOwner(context.User, context.Resource))
	                {
	                    context.Succeed(requirement);
	                }
	            }
	        }
	
	        return Task.CompletedTask;
	    }
	
	    private static bool IsOwner(ClaimsPrincipal user, object? resource)
	    {
	        // Code omitted for brevity
	        return true;
	    }
	
	    private static bool IsSponsor(ClaimsPrincipal user, object? resource)
	    {
	        // Code omitted for brevity
	        return true;
	    }
	}

Here is an example to create multiple handlers for a single requirement:

	public class EmployeeRequirement : IAuthorizationRequirement
	{
	   //Write your code here
	}
	public class EmployeeRoleHandler : 
	         AuthorizationHandler<EmployeeRequirement>
	{
	    //Write your code here to check if the user is an employee
	}
	public class MinimumAgeHandler : 
	         AuthorizationHandler<EmployeeRequirement>
	{
	    //Write your code here to validate min age
	}

Register requirement handlers:

	builder.Services.AddSingleton<IAuthorizationHandler, MinimumAgeHandler>();

A simple way to implement claim-based authentication using policy, Func<AuthorizationHandlerContext, bool>

	builder.Services.AddAuthorization(options =>
	{
	    options.AddPolicy("BadgeEntry", policy =>
	        policy.RequireAssertion(context => context.User.HasClaim(c =>
	            (c.Type == "BadgeId" || c.Type == "TemporaryBadgeId")
	            && c.Issuer == "https://microsoftsecurity")));
	});

equivalent to:

	// requirement
	using Microsoft.AspNetCore.Authorization;
	public class BuildingEntryRequirement : IAuthorizationRequirement { }

	// requirement handler 1
	using AuthorizationPoliciesSample.Policies.Requirements;
	using Microsoft.AspNetCore.Authorization;
	
	public class BadgeEntryHandler : AuthorizationHandler<BuildingEntryRequirement>
	{
	    protected override Task HandleRequirementAsync(
	        AuthorizationHandlerContext context, BuildingEntryRequirement requirement)
	    {
	        if (context.User.HasClaim(
	            c => c.Type == "BadgeId" && c.Issuer == "https://microsoftsecurity"))
	        {
	            context.Succeed(requirement);
	        }
	
	        return Task.CompletedTask;
	    }
	}

	// requirement handler 2
	using AuthorizationPoliciesSample.Policies.Requirements;
	using Microsoft.AspNetCore.Authorization;
	
	public class TemporaryStickerHandler : AuthorizationHandler<BuildingEntryRequirement>
	{
	    protected override Task HandleRequirementAsync(
	        AuthorizationHandlerContext context, BuildingEntryRequirement requirement)
	    {
	        if (context.User.HasClaim(
	            c => c.Type == "TemporaryBadgeId" && c.Issuer == "https://microsoftsecurity"))
	        {
	            // Code to check expiration date omitted for brevity.
	            context.Succeed(requirement);
	        }
	
	        return Task.CompletedTask;
	    }
	}

## Use policy authentication
Three ways to use policy

1. apply policy to MVC controller:

		using Microsoft.AspNetCore.Authorization;
		using Microsoft.AspNetCore.Mvc;
		[Authorize(Policy = "AtLeast21")]
		public class AtLeast21Controller : Controller
		{
		    public IActionResult Index() => View();
		}

1. apply policy to razor pages

		using Microsoft.AspNetCore.Authorization;
		using Microsoft.AspNetCore.Mvc.RazorPages;
		
		[Authorize(Policy = "AtLeast21")]
		public class AtLeast21Model : PageModel { }

1. apply policy to endpoints

		app.MapGet("/helloworld", () => "Hello World!")
		    .RequireAuthorization("AtLeast21");

# FAQ

# References #
[Policy-based Authorization in ASP.NET Core – A Deep Dive](https://www.red-gate.com/simple-talk/development/dotnet-development/policy-based-authorization-in-asp-net-core-a-deep-dive/)

[Role-based authorization in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authorization/roles?view=aspnetcore-7.0)

[Claims-based authorization in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authorization/claims?view=aspnetcore-7.0)

[Policy-based authorization in ASP.NET Core](https://learn.microsoft.com/en-us/aspnet/core/security/authorization/policies?view=aspnetcore-7.0)

[Create an ASP.NET Core web app with user data protected by authorization](https://learn.microsoft.com/en-us/aspnet/core/security/authorization/secure-data?view=aspnetcore-7.0#rau)

[Using Role Claims in ASP.NET Identity Core](https://benfoster.io/blog/asp-net-identity-role-claims/)