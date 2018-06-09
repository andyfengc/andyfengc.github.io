---
layout: post
title: ASP.NET Identity authentication 1 - OWIN and Katana
author: Andy Feng
categories: [OWIN, Katana]
---

# Introduction #
In the .NET world there hasn't been much choice in web server technology aside from IIS. IIS has been around for a long time now, longer than ASP.NET itself. For a developer, tackles IIS and tons of libraries can be quite a daunting task. Also, System.Web is a monolithic assembly that contains everything under the sun all tightly coupled into one namespace, often being coupled into IIS.

With more and more processing moving onto the client, servers have stopped processing and returning html and are instead just returning data for the client to parse and present. Modern approaches such as node.js, WebAPI, Spring Boots require minimal effort to act as a web server, containing only what is needed to build the application and nothing else.

`Open Web Interface for .NET (OWIN)` defines an abstraction  between .NET web servers and web applications. First, by decoupling the web server from the application, OWIN makes it easier to create middleware for .NET web development. Also, OWIN makes it easier to port web applications to other hosts. We can host our .NET web applications on any web server following OWIN. For example, self-hosting in a Windows service or other process. Prior to OWIN, when we are building ASP.NET application, we are inheritedly bound to IIS due to the heavy dependency on System.Web assembly.

`Katana` project is a set of open-source OWIN components developed by Microsoft. These components include both infrastructure components, such as hosts and servers, as well as functional components, such as authentication components and bindings to frameworks such as SignalR and ASP.NET Web API. 

Overall, OWIN is a specification and abstraction(interface), not an implementation. It defines how web servers and web applications should be built in order to decouple them. Katana on the other hand, is fully developed framework made to make a bridge between current ASP.NET frameworks and OWIN specification. 

In .NET 4.6, Katana has successfully adapted the Web API and SignalR frameworks to OWIN but .NET MVC and Web Forms are still running exclusively via System.Web, and in the long run there is a plan to decouple those as well.

# OWIN #
OWIN specifies the interaction among Web Servers and Web Frameworks and defines the specification decoupling the web server and web applications. It is available at [nuget](https://www.nuget.org/packages/Microsoft.Owin/)

![](/images/posts/20180524-owin-1.png)

## Environment
Environment is a dictionary storing all of the state necessary for processing a HTTP request and response:

`IDictonary<string, object>`

Some of the dictionary keys for a HTTP Request are as follows:

![](/images/posts/20180524-owin-2.png)

![](/images/posts/20180524-owin-3.png)

# Katana
Katana is the Microsoft implementation of the OWIN specification and can be self-hosted or integrated with the IIS pipeline. Also, Katana adds some additional features such as authentication framework Identity to make OWIN easier to use. Katana aleady integrated into ASP.NET framework and Katana components use the Microsoft.Owin.* namespace. 

The architecture of KATANA is divided into 4 major parts:
![](/images/posts/20180524-owin-4.png)

- Host: Manages the process in which the OWIN pipeline runs. The host is a process that hosts all of other parts. It can be anything from a simple console application to a windows service or even a traditional IIS. It is responsible for starting everything up. 
    There are the following three main primary options available for hosting the KATANA based Web Applications:

    > IIS Host: IIS hosting is possible with the same process used in ASP.NET that is by using HttpModule and HttpHandler. IIS acts as a server and as a host. If you want to host by ASP.NET, then you need to write the following command in the Package Manager Console:
        `install-package Microsoft.Owin.Host.SystemWeb`
    
    > Custom Host: Now in KATANA, the developer can host applications with their own custom process. It can be your window service, console application and so on. This is as similar as self-host.
    
    > OWIN Host: The OWIN Host is the default hosting server of any web applications. The developer can use this default server rather then using the custom host. OwinHost.exe is used in a KATANA component for hosting. 

- Server: Opens a network socket and listens for requests. The Server responds to any request from any client and sends back response. Sepcifically, the server opens a network socket, listens for requests and sends through the OWIN-pipeline, then invokes the right services and returns response. There are two main implementations of servers of a KATANA project as in the following:

    `Microsoft.Owin.Host.SystemWeb` - The SystemWeb registers both ASP.NET HttpModule and HttpHandler to ambush requests so that then the stream is sent through the HTTP pipeline and sent through the OWIN pipeline that is specified by the user.
    
    `Microsoft.Owin.Host.HttpListener` - server uses the HttpListener class of the .NET Framework to access a socket and send through the user specified OWIN pipeline. This is by default the server for KATANA self-host API and OWINHost.exe.

    In IIS, Host and Server are the same thing. However, treading them as two separate pieces gives us more flexibility when we're buidling our solutions.

- Middleware: Processes the HTTP request and response via a pipeline. When any server gets a client request, it passes through a pipeline consists of OWIN components. The OWIN components are defined in the startup code of the developer's application. The pipeline components are called Middleware.
    The OWIN application delegate is implemented by the OWIN middleware components so that it is callable as in the following:

    `Func<IDictionary<string, object>, Task>`

    The request/response flow acts as a pipeline, with all requests having to pass through the various middleware components before reaching the application. In fact the request may never reach your application, with the middleware responding for you (e.g. when using an authentication middleware component)

    ![](/images/posts/20180524-owin-5.png)

- Application: application is responsible for generating the actual response. Technically speaking, there's no difference between the application and middleware, except for the fact that the application is specially built to generate response and send it back to the client, whereas the middleware is built to pass through on the way to and from the application.  Of course, middleware can return result before the request arrives the application.

    If we are using IIS, the application is very similar to HTTP handler.

The workflow of Katana is:

1. Host starts up the application
1. client sends a http request to Server
1. Server takes the incoming HTTP request, parses it and breaks it into smaller pieces. Then, server takes pieces and puts them into dictionary of type string objects. 
1. Server then passes the environment dictionary to the first middleware in the pipeline of middleware components.
1. Middleware inspects the dictionary and makes whatever changes it is supposed to do based on the information inside the dictionary. Then, pass the dictionary on to the next middleware in the pipeline untile it reaches the application.
1. Application generates a response based on the information in the dictionary by setting response header and writing result to the response stream. Then send it back to the client if we do not specify middleware to handle respone. otherwire, application passes the dictionary including response to the last middleware in the pipeline.
1. Optional, middleware can do what it supposes to do, then pass the dictionary back to previous middleware until the beginning of the pipeline. Please note that middleware cannot make any changes to the response headers for return inteception and it can only append things to the response body stream. 
1. Once the dictionary reaches the beginning of the pipeline, the server is notified of the completion of the processing, then server sends response to the client and closes the connection.

Please note that in most cases we use web framework to wrap up the pipeline of middlewares and application.
    ![](/images/posts/20180524-owin-6.png)

# Create a OWIN based application #
1. Create a new empty web application
    ![](/images/posts/20180524-owin-7.png)

    ![](/images/posts/20180524-owin-8.png)

    ![](/images/posts/20180524-owin-9.png)

1. Install `Microsoft.Owin.Host.SystemWeb` package

    ![](/images/posts/20180524-owin-10.png)

1. Create Startup.cs in the root to start up OWIN-based application. By default, it requires a public void method Configuration()

```
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            app.Use(async (context, next) =>
            {
                await context.Response.WriteAsync("Hello !");
            });
        }
    }

```
run F5, start in DEBUG mode

We get
    ![](/images/posts/20180524-owin-13.png)

# Create a middleware #
Owin.dll contains a method
    `IAppBuilder.Use(object middleware, params object[] args)`

It takes a middleware implementation object and optional data arguments for middleware. The object middleware can be a delegate reference to a middleware implementation method or inline middleware implementation. 

1. Inline implementation

    ```
        public partial class Startup
        {
            public void Configuration(IAppBuilder app)
            {
                app.Use(async (context, next) =>
                {
                    Debug.WriteLine("Incoming request: " + context.Request.Path);
                    await next();
                    Debug.WriteLine("Output response: " + context.Request.Path);
                });
                app.Use(async (context, next) =>
                {
                    await context.Response.WriteAsync("Hello !");
                });
            }
        }

    ```
    We get
    ![](/images/posts/20180524-owin-13.png)
    ![](/images/posts/20180524-owin-14.png)

1. External implementation 1

    ```
        public class GenericMiddleware 
        {
            AppFunc _next;
            public GenericMiddleware(AppFunc next) {
                _next = next;
            }
            
            public async Task Invoke(IDictionary<string, object> environment)
            {
                Debug.WriteLine("generic middleware begins");
                await this._next(environment);
                Debug.WriteLine("generic middleware ends");
            }
        }
    ```

1. External implementation 2

    ```
        public class KatanaMiddleware : OwinMiddleware
        {
            public KatanaMiddleware(OwinMiddleware next) : base(next)
            {
            }

            public override async Task Invoke(IOwinContext context)
            {
                Debug.WriteLine("katana midware begins");
                await this.Next.Invoke(context);
                Debug.WriteLine("katana midware ends");
            }
    }
    ```

1. Update `Startup.cs`

    ```
        public void Configuration(IAppBuilder app)
        {
            app.Use(async (context, next) =>
            {
                Debug.WriteLine("Incoming request: " + context.Request.Path);
                await next();
                Debug.WriteLine("Output response: " + context.Request.Path);
            });
            app.Use<GenericMiddleware>();
            app.Use<KatanaMiddleware>();
            app.Use(async (context, next) =>
            {
                await context.Response.WriteAsync("Hello !");
            });
        }
    ```
    we will get

    ![](/images/posts/20180524-owin-15.png)

# Integrate frameworks #
## Integrate Web Api 
1. Install web api package

    `Install-Package Microsoft.AspNet.WebApi.Owin`

    package.json will be upated:

    ```
    <?xml version="1.0" encoding="utf-8"?>
    <packages>
    <package id="Microsoft.AspNet.WebApi.Client" version="5.2.6" targetFramework="net452" />
    <package id="Microsoft.AspNet.WebApi.Core" version="5.2.6" targetFramework="net452" />
    <package id="Microsoft.AspNet.WebApi.Owin" version="5.2.6" targetFramework="net452" />
    <package id="Microsoft.CodeDom.Providers.DotNetCompilerPlatform" version="1.0.0" targetFramework="net452" />
    <package id="Microsoft.Net.Compilers" version="1.0.0" targetFramework="net452" developmentDependency="true" />
    <package id="Microsoft.Owin" version="4.0.0" targetFramework="net452" />
    <package id="Microsoft.Owin.Host.SystemWeb" version="4.0.0" targetFramework="net452" />
    <package id="Newtonsoft.Json" version="6.0.4" targetFramework="net452" />
    <package id="Owin" version="1.0" targetFramework="net452" />
    </packages>

    ```

1. Add a new controller:

    ```
        [RoutePrefix("api")]
        public class WebApiController : ApiController
        {
            [Route("hello")]
            [HttpGet]
            public IHttpActionResult Hello()
            {
                return Content(HttpStatusCode.OK, "hello from webapi");
            }
        }
    ```

1. Register web api in the owin pipeline in Startup.cs

        public void Configuration(IAppBuilder app)
        {
            // register webapi
            var config = new HttpConfiguration();
            config.MapHttpAttributeRoutes();
            app.UseWebApi(config);
            // middlewares
            ...
        }

    we will get

    ![](/images/posts/20180524-owin-16.png)

## Integrate .NET MVC
1. Install web api package

    `Install-Package Microsoft.AspNet.Mvc`

    package.json was updated:

    ```
        <?xml version="1.0" encoding="utf-8"?>
        <packages>
        <package id="Microsoft.AspNet.Mvc" version="5.2.6" targetFramework="net452" />
        <package id="Microsoft.AspNet.Razor" version="3.2.6" targetFramework="net452" />
        <package id="Microsoft.AspNet.WebApi.Client" version="5.2.6" targetFramework="net452" />
        <package id="Microsoft.AspNet.WebApi.Core" version="5.2.6" targetFramework="net452" />
        <package id="Microsoft.AspNet.WebApi.Owin" version="5.2.6" targetFramework="net452" />
        <package id="Microsoft.AspNet.WebPages" version="3.2.6" targetFramework="net452" />
        <package id="Microsoft.CodeDom.Providers.DotNetCompilerPlatform" version="1.0.0" targetFramework="net452" />
        <package id="Microsoft.Net.Compilers" version="1.0.0" targetFramework="net452" developmentDependency="true" />
        <package id="Microsoft.Owin" version="4.0.0" targetFramework="net452" />
        <package id="Microsoft.Owin.Host.SystemWeb" version="4.0.0" targetFramework="net452" />
        <package id="Microsoft.Web.Infrastructure" version="1.0.0.0" targetFramework="net452" />
        <package id="Newtonsoft.Json" version="6.0.4" targetFramework="net452" />
        <package id="Owin" version="1.0" targetFramework="net452" />
        </packages>

    ```

1. Add controller and view 

    ```
    public class MvcController : Controller
    {
        // GET: Mvc
        public ActionResult Index()
        {
            return View();
        }
    }

    ```

    ```
    @inherits System.Web.Mvc.WebViewPage
    <!DOCTYPE html>
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Index</title>
    </head>
    <body>
    <div>
        <h1>Hello from mvc</h1>
    </div>
    </body>
    </html>

    ```

1. Due to ASP.NET MVC couldn't live with inside of the OWIN pipeline, we need to integrate beyond OWIN Startup.cs configuration.

    root > add global.asax

    ```
    public class Global : System.Web.HttpApplication
    {
        protected void Application_Start(object sender, EventArgs e)
        {
            RouteTable.Routes.MapRoute(name: "Default"
                , url: "{controller}/{action}"
                , defaults: new { controller = "Mvc", action = "Index" });
        }
        ...
    }

    ```

    Please note that when web application initializes, global.asax runs before OWIN startup. 

1. we need to disable OWIN middleware returns any response so that ASP.NET MVC can handle request.

    ```
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            // register webapi
            var config = new HttpConfiguration();
            config.MapHttpAttributeRoutes();
            app.UseWebApi(config);
            // external middleware
            app.Use<GenericMiddleware>();
            // external middleware
            app.Use<KatanaMiddleware>();
            // disable owin middleware returns result so that mvc can respond to request
            //app.Use(async (context, next) =>
            //{
            //    await context.Response.WriteAsync("Hello !");
            //});
        }
    }
    ```

1. run will get

    ![](/images/posts/20180524-owin-18.png)

# Add identity cookie authentication
1. Add a new secret controller, modify with [Authorize]
    ```
    [Authorize]
    public class SecretController : Controller
    {
        // GET: Secret
        public ActionResult Index()
        {
            return View();
        }
    }
    ```
    index view:
    ```
    @inherits System.Web.Mvc.WebViewPage
    <!DOCTYPE html>
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Index</title>
    </head>
    <body>
        <div>
            <h1>I am secret</h1>
        </div>
    </body>
    </html>
    ```

1. Now, start application and try to access this method

    ![](/images/20160604-add-identity-10.png)

1. Add a login controller
    ```
    public class AccountController : Controller
    {
        // GET: Account
        [HttpGet]
        public ActionResult Login()
        {
            return View();
        }
        [HttpPost]
        public ActionResult Login(LoginModel model)
        {
            if (model.Username == "admin" && model.Password == "admin")
            {
                var identity = new ClaimsIdentity("ApplicationCookie");
                identity.AddClaims(new List<Claim>()
                {
                    new Claim(ClaimTypes.NameIdentifier, model.Username),
                    new Claim(ClaimTypes.Name, model.Username)
                });
                HttpContext.GetOwinContext().Authentication.SignIn(identity);
            }
            return View();
        }
    }
    ```
    login model
    ```
    public class LoginModel
    {
        public string Username { get; set; }
        public string Password { get; set; }
    }
    ```

    login view
    ```
    @inherits System.Web.Mvc.WebViewPage<OwinDemo.Models.LoginModel>
    @using System.Web.Mvc.Html;
    <!DOCTYPE html>
    <html>
    <head>
        <meta name="viewport" content="width=device-width" />
        <title>Login</title>
    </head>
    <body>
        <div>
            @using (var form = Html.BeginForm())
            {
                <div>
                    @Html.LabelFor(x => x.Username)
                    @Html.TextBoxFor(x => x.Username)
                </div>
                <div>
                    @Html.LabelFor(x => x.Password)
                    @Html.TextBoxFor(x => x.Password)
                </div>
                <div>
                    <input type="submit" name="name" value="Login" />
                </div>
            }
        </div>
    </body>
    </html>
    ```
1. Add cookie authentication middleware
    `Install-Package microsoft.owin.security.cookies`

    two libs will be added to package.json

    ```
    <package id="Microsoft.Owin.Security" version="4.0.0" targetFramework="net452" />
    <package id="Microsoft.Owin.Security.Cookies" version="4.0.0" targetFramework="net452" />
    ```

    Update Startup.cs
    ```
    public partial class Startup
    {
        public void Configuration(IAppBuilder app)
        {
            // cookie authentication 
            app.UseCookieAuthentication(new Microsoft.Owin.Security.Cookies.CookieAuthenticationOptions()
            {
                AuthenticationType = "ApplicationCookie",
                LoginPath = new PathString("/account/login")
            });
            // register webapi
            var config = new HttpConfiguration();
            config.MapHttpAttributeRoutes();
            app.UseWebApi(config);
        }
    }
    ```

1. run the app
    navigate to /secret, it will redirect to login page
    ![](/images/posts/20180524-owin-19.png)

    enter admin/admin, will display the result
     ![](/images/posts/20180524-owin-20.png)
   
# Add facebook authentication
1. register facebook developer, get id and secret

1. Add owin facebook provider

    install package
    `Install-package microsoft.owin.security.facebook`

    ```
    app.UseFacebookAuthentication(
       appId: "",
       appSecret: "");
    ```
1. Configure middle ware with id and secret

1. Create login challenge()

# FAQ
1. Could not load file or assembly 'Microsoft.AI.Web' or one of its dependencies

    ![](/images/posts/20180524-owin-11.png)

    Solution: Install nuget: `Install-Package Microsoft.ApplicationInsights.Web`

2. Could not load file or assembly 'Owin, Version=1.0.0.0, Culture=neutral, PublicKeyToken=f0ebd12fd5e55cc5' or one of its dependencies.

    ![](/images/posts/20180524-owin-12.png)

    Solution: the project name cannot be OWIN and it conflicts with Katana naming

1. Server cannot append header after HTTP headers have been sent.

    ![](/images/posts/20180524-owin-17.png)

    reason : mvc wants to take the request over but owin middleware returns response as well. Both of them returns response and they conflicts

    solution: disable either of them

# References
[OWIN](http://owin.org)
[An Overview of Project Katana](https://docs.microsoft.com/en-us/aspnet/aspnet/overview/owin-and-katana/an-overview-of-project-katana)