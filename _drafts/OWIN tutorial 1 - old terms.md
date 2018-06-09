---
layout: post
title: OWIN tutorial - .NET Framework terms
author: Andy Feng
categories: [OWIN, Katana]
---

# Outline
- IIS services
- Pipeline / Application life cycle of ASP.NET web form application
    - Lifecycle objects
    - Request workflow
    - HTTP Modules
    - HTTP handlers
- Pipeline / life cycle of ASP.NET Web API 2 application
- Pipeline / life cycle of ASP.NET MVC application
- IPrincipal vs IIdentity

# IIS services
In IIS 7 and later, functionality that was previously handled by the `World Wide Web Publishing Service (WWW Service)` alone is now split between two services: `WWW Service` and a new service, Windows Process Activation Service (WAS). These two services run as LocalSystem in the same Svchost.exe process, and share the same binaries.

In IIS 6.0, `WWW Service` manages the following main areas in IIS:
- HTTP administration and configuration
- Process management
- Performance monitoring

`Windows Process Activation Service (WAS)` manages application pool configuration and worker processes instead of the WWW Service. This enables you to use the same configuration and process model for HTTP and non-HTTP sites.

# Pipeline / Application life cycle of ASP.NET web form application
IIS 7.0 and later integrated pipeline is a unified request processing pipeline that supports both native-code and managed-code modules. 

- native-code is built-in and need to be installed inside IIS
- Managed-code modules that implement the `IHttpModule` interface have access to all events in the request pipeline. For example, a managed-code module can be used for ASP.NET forms authentication for both ASP.NET Web pages (.aspx files) and HTML pages (.htm or .html files). We can create customized managed-code module in application.

## Lifecycle objects

![/images/posts/20180601-owin-5.png](/images/posts/20180601-owin-11.gif)

`HttpContext` class contains objects that are specific to the current application request, such as the `HttpRequest` and `HttpResponse` objects. A new HttpContext object will be created at the beginning of a request and destroyed when the request is completed. It contains:

- Request
- Response
- Application
- Server
- Session
- Cache
- User
- HttpModule
- and etc

`HttpRequest` object contains information about the current request, including cookies and browser information. 

`HttpResponse` object contains the response that is sent to the client, including all rendered output and cookies.

`HttpApplication` object represents the application and it is created at the first time when an ASP.NET page or process is requested in an application. Subsequent requests use the same application object. It is created after all core application objects have been initialized and when the application is started. If the application has a Global.asax file, ASP.NET instead creates an instance of the Global.asax class that is derived from the HttpApplication class.

`Modules` are created when an instance of HttpApplication is created. After all configured modules are created, the HttpApplication class's Init method is called.

## Request workflow

Here is the request-processing flow (pipeline):

1. When a client browser initiates an HTTP request for a resource on the Web server, HTTP.sys intercepts the request.
1. HTTP.sys contacts WAS to obtain information from the configuration store.
WAS requests configuration information from the configuration store, applicationHost.config.
1. The WWW Service receives configuration information, such as application pool and site configuration.
1. The WWW Service uses the configuration information to configure HTTP.sys.
WAS starts a worker process for the application pool to which the request was made.
1. The worker process processes the request and returns a response to HTTP.sys.
1. The client receives a response.

![/images/posts/20180601-owin-5.png](/images/posts/20180601-owin-8.png)

Within a worker process, an HTTP request passes through several ordered steps, called events, in the Web Server Core. If a request requires a managed module, the native ManagedEngine module creates an AppDomain, where the managed module can perform the necessary processing, such as authenticating a user with Forms authentication. At each event, a native module processes part of the request, such as authenticating the user or adding information to the event log. When the request passes through all of the events in the Web Server Core, the response is returned to HTTP.sys. Here is the wrokflow of a HTTP request inside the Worker Process.

![/images/posts/20180601-owin-5.png](/images/posts/20180601-owin-9.png)

## HTTP Modules (in IIS)
Modules are individual features that the server uses to process requests. IIS 7 and later include a Web server engine in which you can add or remove components depending on our needs. These components are modules. For example, IIS uses authentication modules to authenticate client credentials, and cache modules to manage cache activity.

Modules provide below benefits:
- control which modules you want on the server.
- customize a server to a specific role in your environment.
- use custom modules to replace existing modules or to introduce new features.

### Native Modules
Below built-in modules are available with a full installation of IIS 7 and later. We can remove them or replace them with custom modules.

- `HTTP Modules` respond to information and inquiries sent in client headers, to return HTTP errors, to redirect requests, and more.

    ![/images/posts/20180601-owin-1.png](/images/posts/20180601-owin-1.png)

- `Security Modules` perform tasks related to security in the request-processing pipeline, perform URL authorization and filter requests.

    ![/images/posts/20180601-owin-2.png](/images/posts/20180601-owin-2.png)

- `Content Modules` process requests for static files, to return a default page when a client doesn't specify a resource in a request, to list the contents of a directory, and more.

    ![/images/posts/20180601-owin-3.png](/images/posts/20180601-owin-3.png)

- `Compression Modules` perform compression in the request-processing pipeline.

    ![/images/posts/20180601-owin-4.png](/images/posts/20180601-owin-4.png)

- `Caching Modules` perform tasks related to caching in the request-processing pipeline. Caching improves the performance of our Web sites and Web applications by storing processed information, such as Web pages, in memory on the server, and then reusing that information in subsequent requests for the same resource.

    ![/images/posts/20180601-owin-5.png](/images/posts/20180601-owin-5.png)

- `Logging and Diagnostics Modules` implement logging and diagnostics in the request-processing pipeline. The logging modules support loading of custom modules and passing information to HTTP.sys. The diagnostics modules follow and report events during request processing.

    ![/images/posts/20180601-owin-6.png](/images/posts/20180601-owin-6.png)

`Managed Support Modules` support managed integration in the IIS request-processing pipeline.

    ![/images/posts/20180601-owin-7.png](/images/posts/20180601-owin-7.png)

### Managed Modules
In addition to native modules, IIS enables us to use managed code modules to extend IIS functionality. Please note that managed modules depend on the `ManagedEngine` module.

![/images/posts/20180601-owin-10.png](/images/posts/20180601-owin-10.png)

### Register request event handler 
We can provide application event handlers in the `Global.asax` file to add code that executes for all requests that are handled by ASP.NET, such as requests for .aspx and .axd pages. 

Please note handler code in the Global.asax file is not called for requests for non-ASP.NET resources, such as static files. To run managed code that runs for all resources, create a custom module that implements the IHttpModule interface. The custom module will run for all requests to resources in the application, even if the resource handler is not an ASP.NET handler.

### Create managed-code module
ASP.NET application life cycle can be extended with modules that implement the `IHttpModule` interface. Modules that implement the `IHttpModule` interface are managed-code modules.
An HTTP module is called on every request in response to the BeginRequest and EndRequest events. As a result, the module runs before and after a request is processed.

```
using System;
using System.Data;
using System.Web;
using System.Web.Security;
using System.Web.UI;

// Module that demonstrates one event handler for several events. 
namespace Samples
{
    public class ModuleExample : IHttpModule
    {
        public ModuleExample()
        {
            // Constructor
        }
        public void Init(HttpApplication app)
        {
            app.LogRequest += new EventHandler(App_Handler);
            app.PostLogRequest += new EventHandler(App_Handler);
        }
        public void Dispose()
        {
        }
        // One handler for both the LogRequest and the PostLogRequest events. 
        public void App_Handler(object source, EventArgs e)
        {
            HttpApplication app = (HttpApplication)source;
            HttpContext context = app.Context;

            if (context.CurrentNotification == RequestNotification.LogRequest)
            {
                if (!context.IsPostNotification)
                {
                    // Put code here that is invoked when the LogRequest event is raised.
                }
                else
                {
                    // PostLogRequest 
                    // Put code here that runs after the LogRequest event completes.
                }
            }

        }
    }
}
``` 

Register module in web.config
```
<system.webServer>
  <modules>
    <add name="ModuleExample" type="Samples.ModuleExample"/>
  </modules>
</system.webServer>
```

## HTTP Handlers
### HTTP module vs. HTTP handler
HTTP Module is plugged into the life cycle of a request. It is actually the node of pipeline. When a request is processed it is passed through all the modules in the pipeline of the request. So generally http modules are used for:

- Security: For authenticating a request before the request is handled.
- Statistics and Logging: Since modules are called for every request they can be used for gathering statistics and for logging information.
- Custom header:  Since response can be modified, one can add custom header information to the response.

HTTP Handler is the process which responds to a HTTP request. It is actually the application. e.g. webforms is a HTTP handler and it implements IHttpHandler
`public class Page : TemplateControl, IHttpHandler{..}`

![/images/posts/20180605-owin-11.png](/images/posts/20180605-owin-11.png)

Please note:
- In .NET, modules and handlers are set at web.config
- In .NET Core, modules and handlers have been taken over by middleware. Middleware are configured using code rather than in Web.config
- modules in .NET is equivalent to middlewares in .NET Core. Handlers in .NET is equivalent to application in .NET Core. 
- Execution order of middleware is based on the order in which they're inserted into the request pipeline, while order of modules is mainly based on application life cycle events
- 




### Create a HTTP handler
Http handler has to implement IHttpHandler interface
```
public class MyHandler :IHttpHandler
{
    public bool IsReusable
    {
        get { return false; }
    }

    public void ProcessRequest(HttpContext context)
    {

    }
}
```

# Pipeline / life cycle of ASP.NET Web API 2 application
Here is the Web API Pipeline.
![/images/posts/20180605-owin-1.png](/images/posts/20180605-owin-1.png)

##Hosting
Web API can be hosted either on ASP.NET or you could write a Console App or a Windows Service yourself to self-host it in a container of yours. 

a. ASP.NET Hosting: When hosted on ASP.NET, the lifecycle starts with the HttpControllerHandler which is an implementation of IHttpAsyncHandler and is responsible for passing requests into the HttpServer pipeline.

b. Self Hosting: When you are Self Hosting, the HttpServer pipeline starts at the HttpSelfHostServer which is an implementation of HttpServer and directly listens to HTTP requests.

## Message Handler
Once a request leaves your Service host, it travels as an HttpRequestMessage object in the pipeline. The next stage in the pipeline are the Message Handlers.

`Delegating Handler` allows us to massage the Request before passing it on to the rest of the pipeline. The response message on its way back has to pass through the Delegating Handler as well, so any response can also be monitored/filtered/updated at this point.

`Routing Dispatcher` checks if the Route Handler is null. If it is null, it proceeds to the next step in the pipeline. However if it’s not null, it implies there are one or more per-route message handlers in place and the request is passed on to the Handlers. Here it loops through the available handlers and picks the one matching the request, the request is then handled.

## Authorization filter
If the routing handlers pass the request on to the next stage, the request enters the Controllers.

### Authorization Filter
`Authorization Filter` is the first step in the Controllers section of the pipeline. It checks authentication for HTTP request when arriving controllers or actions. Like other filters, authentication filters can be applied per-controller, per-action, or globally to all Web API controllers.

1. Set up an Authentication Filter

    - To apply an authentication filter to a controller, decorate the controller class with the filter attribute. Here, [IdentityBasicAuthentication] filter on a controller class  enables Basic Authentication for all of the controller's actions.

        ```
        [IdentityBasicAuthentication] // Enable Basic authentication for this controller.
        [Authorize] // Require authenticated requests.
        public class HomeController : ApiController
        {
            public IHttpActionResult Get() { . . . }
            public IHttpActionResult Post() { . . . }
        }

        ```

    - To apply the filter to one action, decorate the action with the filter. The following code sets the [IdentityBasicAuthentication] filter on the controller's Post method.

        ```
        [Authorize] // Require authenticated requests.
        public class HomeController : ApiController
        {
            public IHttpActionResult Get() { . . . }

            [IdentityBasicAuthentication] // Enable Basic authentication for this action.
            public IHttpActionResult Post() { . . . }
        }

        ```
    - To apply the filter to all Web API controllers, add it to GlobalConfiguration.Filters.
        ```
        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                config.Filters.Add(new IdentityBasicAuthenticationAttribute());

                // Other configuration code not shown...
            }
        }
        ```

1. Implement a Web API Authentication Filter

    In Web API, authentication filters implement the `System.Web.Http.Filters.IAuthenticationFilter` interface. They should also inherit from `System.Attribute`, in order to be applied as attributes.

    ```
    public interface IAuthenticationFilter : IFilter
    {
        /// <summary>Authenticates the request.</summary>
        /// <returns>A Task that will perform authentication.</returns>
        /// <param name="context">The authentication context.</param>
        /// <param name="cancellationToken">The token to monitor for cancellation requests.</param>
        Task AuthenticateAsync(HttpAuthenticationContext context, CancellationToken cancellationToken);
        Task ChallengeAsync(HttpAuthenticationChallengeContext context, CancellationToken cancellationToken);
    }
    ```
    
    Two required methods:
    - `AuthenticateAsync` authenticates the request by validating credentials in the request. We can implement AuthenticateAsync(), get the request's authorization header, check the scheme, and validate the parameter, and if all is valid, set the principal.

        The AuthenticateAsync method must do one of the following:

        * Nothing (no-op).
        * Create an IPrincipal and set it on the request.
        * Set an error result.

        A sample to implement Basic Authentication 

        ```
        public async Task AuthenticateAsync(HttpAuthenticationContext context, CancellationToken cancellationToken)
        {
            // 1. Look for credentials in the request.
            HttpRequestMessage request = context.Request;
            AuthenticationHeaderValue authorization = request.Headers.Authorization;

            // 2. If there are no credentials, do nothing.
            if (authorization == null)
            {
                return;
            }

            // 3. If there are credentials but the filter does not recognize the 
            //    authentication scheme, do nothing.
            if (authorization.Scheme != "Basic")
            {
                return;
            }

            // 4. If there are credentials that the filter understands, try to validate them.
            // 5. If the credentials are bad, set the error result.
            if (String.IsNullOrEmpty(authorization.Parameter))
            {
                context.ErrorResult = new AuthenticationFailureResult("Missing credentials", request);
                return;
            }

            Tuple<string, string> userNameAndPasword = ExtractUserNameAndPassword(authorization.Parameter);
            if (userNameAndPasword == null)
            {
                context.ErrorResult = new AuthenticationFailureResult("Invalid credentials", request);
            }

            string userName = userNameAndPasword.Item1;
            string password = userNameAndPasword.Item2;

            IPrincipal principal = await AuthenticateAsync(userName, password, cancellationToken);
            if (principal == null)
            {
                context.ErrorResult = new AuthenticationFailureResult("Invalid username or password", request);
            }

            // 6. If the credentials are valid, set principal.
            else
            {
                context.Principal = principal;
            }

        }
        ```

    - `ChallengeAsync` adds authentication challenges to the HTTP response. Please note that ChallengeAsync() is called before the HTTP response is created, and possibly even before the controller action runs. 
    Challenge is generally used in cases where the current visitor is not logged in, but is trying to access an action that requires an authenticated user. It will prompt a challenge for credentials. It could also be used for an authenticated user, who is not authorised for the action, and where you want to prompt for higher privileged credentials.  Typically it is used in case of 401 Unauthorized to provide information about authentication required by server.
    
        When ChallengeAsync is called, context.Result contains an IHttpActionResult, which is used later to create the HTTP response. So when ChallengeAsync is called, we don't know anything about the HTTP response yet. The ChallengeAsync() method should replace the original value of context.Result with a new IHttpActionResult. This IHttpActionResult must wrap the original context.Result.

        The new IHttpActionResult result must do the following:

        * Invoke the inner result to create the HTTP response.
        * Examine the response.
        * Add an authentication challenge to the response, if needed

        A sample to implement Basic Authentication
    ```
    public class AddChallengeOnUnauthorizedResult : IHttpActionResult
    {
        public AddChallengeOnUnauthorizedResult(AuthenticationHeaderValue challenge, IHttpActionResult innerResult)
        {
            Challenge = challenge;
            InnerResult = innerResult;
        }

        public AuthenticationHeaderValue Challenge { get; private set; }

        public IHttpActionResult InnerResult { get; private set; }

        public async Task<HttpResponseMessage> ExecuteAsync(CancellationToken cancellationToken)
        {
            HttpResponseMessage response = await InnerResult.ExecuteAsync(cancellationToken);

            if (response.StatusCode == HttpStatusCode.Unauthorized)
            {
                // Only add one challenge per authentication scheme.
                if (!response.Headers.WwwAuthenticate.Any((h) => h.Scheme == Challenge.Scheme))
                {
                    response.Headers.WwwAuthenticate.Add(Challenge);
                }
            }

            return response;
        }
    }
    ```

    ```
    public Task ChallengeAsync(HttpAuthenticationChallengeContext context, CancellationToken cancellationToken)
    {
        var challenge = new AuthenticationHeaderValue("Basic");
        context.Result = new AddChallengeOnUnauthorizedResult(challenge, context.Result);
        return Task.FromResult(0);
    }
    ```

### Action Filter
`Action Filter` contains logic that is executed before and after a controller action executes. You can use an action filter, for instance, to modify the view data that a controller action returns.

```
public class LogActionFilter : ActionFilterAttribute
{
    public override void OnActionExecuting(ActionExecutingContext filterContext)
    {
        Log("OnActionExecuting", filterContext.RouteData); 
    }
    public override void OnActionExecuted(ActionExecutedContext filterContext)
    {
        Log("OnActionExecuted", filterContext.RouteData);  
    }
    public override void OnResultExecuting(ResultExecutingContext filterContext)
    {
        Log("OnResultExecuting", filterContext.RouteData); 
    }
    public override void OnResultExecuted(ResultExecutedContext filterContext)
    {
        Log("OnResultExecuted", filterContext.RouteData);
    }
    private void Log(string methodName, RouteData routeData)
    {
        var controllerName = routeData.Values["controller"];
        var actionName = routeData.Values["action"];
        var message = String.Format("{0} controller:{1} action:{2}", methodName, controllerName, actionName);
        Debug.WriteLine(message, "Action Filter Log");
    }
}
```

### Exception Filter
`Exception Filter` is executed when a controller method throws any unhandled exception. Exception filters implement the `System.Web.Http.Filters.IExceptionFilter` interface. The simplest way to write an exception filter is to derive from the `System.Web.Http.Filters.ExceptionFilterAttribute` class and override the OnException method.

1. Create an exception filter

    ```
    using System;
        using System.Net;
        using System.Net.Http;
        using System.Web.Http.Filters;

        public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
        {
            public override void OnException(HttpActionExecutedContext context)
            {
                if (context.Exception is NotImplementedException)
                {
                    context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
                }
            }
        }
    ```
1. Register exceptoin filter
    There are several ways to register a Web API exception filter:

    * By action
        ```
        public class ProductsController : ApiController
        {
            [NotImplExceptionFilter]
            public Contact GetContact(int id)
            {
                throw new NotImplementedException("This method is not implemented");
            }
        }
        ```

    * By controller
        ```
        [NotImplExceptionFilter]
        public class ProductsController : ApiController
        {
            // ...
        }
        ```

    * Globally - To apply the filter globally to all Web API controllers, add an instance of the filter to the `GlobalConfiguration.Configuration.Filters` collection. Exeption filters in this collection apply to any Web API controller action.
        ```
        GlobalConfiguration.Configuration.Filters.Add(
            new ProductStore.NotImplExceptionFilterAttribute());
        ```
    
        or

        ```
        public static class WebApiConfig
        {
            public static void Register(HttpConfiguration config)
            {
                config.Filters.Add(new ProductStore.NotImplExceptionFilterAttribute());

                // Other configuration code...
            }
        }
        ```

# Pipeline / life cycle of ASP.NET MVC application
ASP.NET MVC pipleline is similar to WebAPI. 

# IPrincipal vs IIdentity
`IIdentity` object encapsulates information about the user or entity being validated, such as Kerberos V5 or NTLM. At their most basic level, identity objects contain a name and an authentication type.

`IPrincipal` object represents the security context under which code is running. IPrincipal object contains a reference to an IIdentity object. 

HttpRequestContext.Principal has a short-hand property called `User` available in a MVC/WebAPI controller class. 
```
HttpRequestContext httpRequestContext = Request.GetRequestContext();
var authenticated = httpRequestContext.Principal.Identity.IsAuthenticated;
```
equaivalent to 
```
var authenticated = User.Identity.IsAuthenticated;
```
# References
[https://docs.microsoft.com/en-us/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#HTTP](https://docs.microsoft.com/en-us/iis/get-started/introduction-to-iis/introduction-to-iis-architecture#HTTP)

[https://docs.microsoft.com/en-us/iis/get-started/introduction-to-iis/iis-modules-overview](https://docs.microsoft.com/en-us/iis/get-started/introduction-to-iis/iis-modules-overview)

[https://msdn.microsoft.com/en-us/library/ms227673.aspx?f=255&MSPPError=-2147217396](https://msdn.microsoft.com/en-us/library/ms227673.aspx?f=255&MSPPError=-2147217396)

[ASP.NET Application Life Cycle Overview for IIS 5.0 and 6.0
](https://msdn.microsoft.com/en-us/library/ms178473.aspx?f=255&MSPPError=-2147217396)

[https://msdn.microsoft.com/library/ms178468%28v=vs.85%29.aspx?f=255&MSPPError=-2147217396](https://msdn.microsoft.com/library/ms178468%28v=vs.85%29.aspx?f=255&MSPPError=-2147217396)

https://exceptionnotfound.net/the-asp-net-web-api-2-http-message-lifecycle-in-43-easy-steps-2/](https://exceptionnotfound.net/the-asp-net-web-api-2-http-message-lifecycle-in-43-easy-steps-2/)
[https://docs.microsoft.com/en-us/aspnet/mvc/overview/older-versions-1/controllers-and-routing/understanding-action-filters-cs](https://docs.microsoft.com/en-us/aspnet/mvc/overview/older-versions-1/controllers-and-routing/understanding-action-filters-cs)

[https://msdn.microsoft.com/en-ca/library/ee748503.aspx?f=255&MSPPError=-2147217396](https://msdn.microsoft.com/en-ca/library/ee748503.aspx?f=255&MSPPError=-2147217396)

[https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/authentication-filters](https://docs.microsoft.com/en-us/aspnet/web-api/overview/security/authentication-filters)

[https://dotnetcodr.com/2015/07/23/web-api-2-security-extensibility-points-part-2-custom-authentication-filter/](https://dotnetcodr.com/2015/07/23/web-api-2-security-extensibility-points-part-2-custom-authentication-filter/)

[https://msdn.microsoft.com/library/ms227673.aspx?f=255&MSPPError=-2147217396](https://msdn.microsoft.com/library/ms227673.aspx?f=255&MSPPError=-2147217396)

[http://onlydifferencefaqs.blogspot.com/2012/08/difference-between-httphandler-and.html](http://onlydifferencefaqs.blogspot.com/2012/08/difference-between-httphandler-and.html)

[Migrate HTTP handlers and modules to ASP.NET Core middleware](https://docs.microsoft.com/en-us/aspnet/core/migration/http-modules?view=aspnetcore-2.1)