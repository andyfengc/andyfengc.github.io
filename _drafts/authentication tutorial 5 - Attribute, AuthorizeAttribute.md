---
layout: post
title: .NET Attribute, AuthorizeAttribute, Authorization
author: Andy Feng
---

# Introduction
Authentication and Authorization are different steps when authorizing users.

- Authentication is knowing the identity of the user. For example, Alice logs in with her username and password, and the server uses the password to authenticate Alice.
- Authorization is deciding whether a user is allowed to perform an action. For example, Alice has permission to get a resource but not create a resource.

To implement these steps, .NET creates a pipeline to make authentication followed by authorization.

![](/images/posts/20190211-jwt-1.png)

## Authentication in .NET ##
In .NET, typically, authentication was implemented via `authentication filters`. After the server authenticates the user, it create a `principal`, which is a [IPrincipal](https://docs.microsoft.com/en-us/dotnet/api/system.security.principal.iprincipal?redirectedfrom=MSDN&view=netframework-4.7.2) object that represents the security context. The server attaches the principal object to the current thread by setting `Thread.CurrentPrincipal`, it is equavalent to `HttpContext.Current.User` object.

	private void SetPrincipal(IPrincipal principal)
	{
	    Thread.CurrentPrincipal = principal;
	    if (HttpContext.Current != null)
	    {
	        HttpContext.Current.User = principal;
	    }
	}

This principal object contains an associated `Identity` object that contains information about the user. If the user is authenticated, the `Identity.IsAuthenticated` property returns `true`. For anonymous requests, `IsAuthenticated` property returns `false`. 

## Write Custom Authorization Filters ##
create new authentication filter(interceptor) to implements `IAuthenticationFilter` interface. e.g.

	public class JwtAuthenticationAttribute : IAuthenticationFilter
	{
		public async Task AuthenticateAsync(HttpAuthenticationContext context, CancellationToken cancellationToken)
		{
			...
		}
	
		public Task ChallengeAsync(HttpAuthenticationChallengeContext context, CancellationToken cancellationToken)
		{
			...
		}
	}
	
see JWT post for more details

## Authorization in .NET ##
In .NET, authorization happens after authentication in the pipeline, closer to the controller. That lets us make more granular choices when we grant user's access to resources.

In .NET WebAPI/MVC/.NET CORE, it provides a built-in authorization filter, `AuthorizeAttribute`. This filter checks whether the user is authenticated. If not, it returns HTTP status code 401 (Unauthorized), without invoking the action.

We can apply the filter globally, at the controller level, or at the level of individual actions.

- Globally: To restrict access for every Web API controller, add the AuthorizeAttribute filter to the global filter list:

		public static void Register(HttpConfiguration config)
		{
		    config.Filters.Add(new AuthorizeAttribute());
		}

- Controller: To restrict access for a specific controller, add the filter as an attribute to the controller:

		// Require authorization for all actions on the controller.
		[Authorize]
		public class ValuesController : ApiController
		{
		    public HttpResponseMessage Get(int id) { ... }
		    public HttpResponseMessage Post() { ... }
		}

- Action: To restrict access for specific actions, add the attribute to the action method:

		public class ValuesController : ApiController
		{
		    public HttpResponseMessage Get() { ... }
		
		    // Require authorization for a specific action.
		    [Authorize]
		    public HttpResponseMessage Post() { ... }
		}

In the previous examples, the filter allows any authenticated user to access the restricted methods; only anonymous users are kept out. We can also limit access to specific users or to users in specific roles:

	// Restrict by user:
	[Authorize(Users="Alice,Bob")]
	public class ValuesController : ApiController
	{
	}
	   
	// Restrict by role:
	[Authorize(Roles="Administrators")]
	public class ValuesController : ApiController
	{
	}

> Please note the `AuthorizeAttribute` filter for Web API controllers is located in the `System.Web.Http` namespace. There is a similar filter for MVC controllers in the `System.Web.Mvc` namespace, which is not compatible with Web API controllers.

## Write Custom Authorization Filters ##
To write a custom authorization filter, derive from one of these types:

- **AuthorizeAttribute**. Extend this class to perform authorization logic based on the current user and the user's roles.
- **AuthorizationFilterAttribute**. Extend this class to perform synchronous authorization logic that is not necessarily based on the current user or role.
- **IAuthorizationFilter**. Implement this interface to perform asynchronous authorization logic; for example, if your authorization logic makes asynchronous I/O or network calls. (If your authorization logic is CPU-bound, it is simpler to derive from AuthorizationFilterAttribute, because then you don't need to write an asynchronous method.)

![](/images/posts/20190211-jwt-2.png)

## In practice ##
In some cases, you might allow a request to proceed, but change the behavior based on the principal. For example, the information that you return might change depending on the user's role. Within a webapi controller method, you can get the current principal from the `ApiController.User` property.

	public HttpResponseMessage Get()
	{
	    if (User.IsInRole("Administrators"))
	    {
	        // ...
	    }
	}

# Attribute #
## Introduction ##
`Attribute` is a piece of additional declarative information that is specified for a target. `Attribute` is a reusable element can be applied to a variety of targets i.e. classes, structs, methods, constructors, and more. 

In C#, `attributes` are classes that inherit from the `Attribute` base class. Any class that inherits from Attribute can be used as a sort of "tag" on other pieces of code. For instance, there is an attribute called `ObsoleteAttribute`. This is used to signal that code is obsolete and shouldn't be used anymore. You can place this attribute on a class by using square brackets.

	[Obsolete]
	public class MyClass
	{	
	}

Moreover, When marking a class obsolete, it's a good idea to provide some information as to why it's obsolete, and/or what to use instead. Do this by passing a string parameter to the Obsolete attribute.

	[Obsolete("ThisClass is obsolete. Use ThisClass2 instead.")]
	public class ThisClass
	{	
	}

> The string is being passed as an argument to an ObsoleteAttribute constructor, just like `var attr = new ObsoleteAttribute("some string")`

Please note Attributes in the .NET base class library like `ObsoleteAttribute` trigger certain behaviors within the compiler. Any attribute you create acts only as metadata, and doesn't result in any code within the attribute class being executed. 

Two ways to use Attribute:

- At compiling time, used by compiler
- At runtime, we need to use reflection. e.g.

	TypeInfo typeInfo = typeof(MyClass).GetTypeInfo();
	var attrs = typeInfo.GetCustomAttributes(); // get a collection of 	Attribute objects
	foreach(var attr in attrs)
	    Console.WriteLine("Attribute on MyClass: " + attr.GetType().Name);

## Create custom attribute ##

	public class GotchaAttribute : Attribute
	{
	    public GotchaAttribute(Foo myClass, string str) {       
	    }
	}

Then we can use as 

	[Gotcha]
	public class SomeOtherClass
	{	
	}

# AuthorizeAttribute #
In webapi, System.Web.Http.AuthorizeAttribute.cs

```csharp```
public class AuthorizeAttribute : AuthorizationFilterAttribute
{
    private string _roles;
    private string[] _rolesSplit = _emptyArray;
    private string _users;
    private string[] _usersSplit = _emptyArray;

    // Gets or sets the authorized roles.
    public string Roles
    {
        get { return _roles ?? String.Empty; }
        set
        {
            _roles = value;
            _rolesSplit = SplitString(value);
        }
    }

    // Gets or sets the authorized users.
    public string Users
    {
        get { return _users ?? String.Empty; }
        set
        {
            _users = value;
            _usersSplit = SplitString(value);
        }
    }

    // Determines whether access for this particular request is authorized. This method uses the user <see cref="IPrincipal"/>
    protected virtual bool IsAuthorized(HttpActionContext actionContext)
    {
        if (actionContext == null)
        {
            throw Error.ArgumentNull("actionContext");
        }

        IPrincipal user = actionContext.ControllerContext.RequestContext.Principal;
        if (user == null || user.Identity == null || !user.Identity.IsAuthenticated)
        {
            return false;
        }

        if (_usersSplit.Length > 0 && !_usersSplit.Contains(user.Identity.Name, StringComparer.OrdinalIgnoreCase))
        {
            return false;
        }

        if (_rolesSplit.Length > 0 && !_rolesSplit.Any(user.IsInRole))
        {
            return false;
        }

        return true;
    }

    // Called when an action is being authorized. This method uses the user <see cref="IPrincipal"/>
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext == null)
        {
            throw Error.ArgumentNull("actionContext");
        }

        if (SkipAuthorization(actionContext))
        {
            return;
        }

        if (!IsAuthorized(actionContext))
        {
            HandleUnauthorizedRequest(actionContext);
        }
    }

    internal static string[] SplitString(string original)
    {
        if (String.IsNullOrEmpty(original))
        {
            return _emptyArray;
        }

        var split = from piece in original.Split(',')
                    let trimmed = piece.Trim()
                    where !String.IsNullOrEmpty(trimmed)
                    select trimmed;
        return split.ToArray();
    }
}
```csharp```

# Reference #
[Attributes](https://docs.microsoft.com/en-us/dotnet/csharp/tutorials/attributes)

[Writing Custom Attributes](https://docs.microsoft.com/en-us/dotnet/standard/attributes/writing-custom-attributes)

[AuthorizeAttribute.cs](https://github.com/aspnet/AspNetWebStack/blob/master/src/System.Web.Http/AuthorizeAttribute.cs)