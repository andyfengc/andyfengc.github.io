---
layout: post
title: ASP.NET Identity authentication 13 - build Authorization Server using IdentityServer4
author: Andy Feng
---

# Introduction
`IdentityServer4` is a .NET implementation of OpenID Connect and OAuth 2.0. In contrast to IdentityServer3, it supports ASP.NET Core 2.

# Outline
1. Implementation of client credential workflow in OAuth 2.0
	- Identity server: add API resource, add client(client id, secret, grant type=ClientCredentials, allowed scope of API)
	- API: add controller endpoints, add authentication middleware to pipeline
	- Client: use HttpClient to call API with client id, secret, scope of API

1. Implement resource owner password workflow in OAuth 2.0
	- Identity server: add TestUser, update client(grant type=ResourceOwnerPassword)
	- Client: add username, password
	
1. Add interactive user authentication with the implicit workflow of OpenID Connect protocol to the authorization server
	- MVC client: Add OpenIdConnect lib; config workflow middleware to pipeline
	- Identity Server: add identity resource, add users, add MVC client with redirect urls & allowed scope
	
1. Add external authentication support to the authorization server
	- add more external authentication services	

# Fundamentals
`IdentityServer` is a combination of middleware and services. 

# Tutorial 1: Implementation of client credential workflow in OAuth 2.0
## Create Authorization server
1. list all templates:

	`dotnet new -i IdentityServer4.Templates`

	![](/images/posts/20190629-identity-server-1.png)

1. create a new project from template

	`dotnet new is4empty -n IdentityServer`
	
	This will create the following files:
	
	- IdentityServer.csproj - the project file and a Properties\launchSettings.json file
	- Program.cs and Startup.cs - the main application entry point
	- Config.cs - IdentityServer resources and clients configuration file

	> You can now use your favorite text editor to edit or view the files. If you want to have Visual Studio support, you can add a solution file like this:
	> `dotnet new sln -n Quickstart`
	> 
	> open the solution using vs, add the new created project or using the command
	> `dotnet sln add .\IdentityServer\IdentityServer.csproj`
	> 
	> ![](/images/posts/20190629-identity-server-2.png) 

1. define API resources

	Config.cs >

	    public static IEnumerable<ApiResource> GetApis()
        {
            //return new ApiResource[] { };
            return new List<ApiResource>
            {
                new ApiResource("empApi", "employee"),
                new ApiResource("custApi", "customer"),
                new ApiResource("prdApi", "product"),
            };
        }

1. define clients

	 	public static IEnumerable<Client> GetClients()
        {
            //return new Client[] { };
            return new List<Client>
            {
                new Client
                {
                    ClientId = "client1",

                    // no interactive user, use the clientid/secret for authentication
                    AllowedGrantTypes = GrantTypes.ClientCredentials,

                    // secret for authentication
                    ClientSecrets =
                    {
                        new Secret("secret1".Sha256())
                    },

                    // scopes that client has access to
                    AllowedScopes = { "empApi" }
                },
                ...
            };
        }

	![](/images/posts/20190629-identity-server-3.png)

1. configure IdentityServer

	Startup.cs

		public void ConfigureServices(IServiceCollection services)
		{
		    var builder = services.AddIdentityServer()
		        .AddInMemoryIdentityResources(Config.GetIdentityResources())
		        .AddInMemoryApiResources(Config.GetApis())
		        .AddInMemoryClients(Config.GetClients());		
		    ...
		}


	![](/images/posts/20190629-identity-server-4.png)

1. start the project > `dotnet run` > browser > `http://localhost:5000/.well-known/openid-configuration`

	![](/images/posts/20190629-identity-server-5.png)

Please note that at first startup, IdentityServer will create a developer signing key for you, it’s a file called tempkey.rsa. You don’t have to check that file into your source control, it will be re-created if it is not present.

## Create API resource server
1. create an empty API project

	`dotnet new web -n Api`
	
	add to the solution

	`dotnet sln add Api\Api.csproj`

1. Configure the API application to run on `http://localhost:5002` only. You can do this by editing the `launchSettings.json` file inside the Properties folder. Change the application URL setting to be:

	"applicationUrl": "http://localhost:5002"

	![](/images/posts/20190629-identity-server-6.png)

1. Create a new controller resource with [Authorize] protected

	![](/images/posts/20190629-identity-server-7.png)

1. Add authentication middleware to the pipeline

	The last step is to add the authentication services to DI (dependency injection) and the authentication middleware to the pipeline. These will:

	- validate the incoming token to make sure it is coming from a trusted issuer
	- validate that the token is valid to be used with this api (aka audience)

	modify Startup.cs

	    public class Startup
	    {
	        // This method gets called by the runtime. Use this method to add services to the container.
	        // For more information on how to configure your application, visit https://go.microsoft.com/fwlink/?LinkID=398940
	        public void ConfigureServices(IServiceCollection services)
	        {
	            services.AddMvcCore()
	                .AddAuthorization()
	                .AddJsonFormatters();
	            services.AddAuthentication("Bearer")
	                .AddJwtBearer("Bearer", options =>
	                {
	                    options.Authority = "http://localhost:5000";
	                    options.RequireHttpsMetadata = false;
	
	                    options.Audience = "empApi";
	                });
	        }
	
	        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
	        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
	        {
	            if (env.IsDevelopment())
	            {
	                app.UseDeveloperExceptionPage();
	            }	
	
	            app.UseAuthentication();
	
	            app.UseMvc();
		
	            app.Run(async (context) =>
	            {
	                await context.Response.WriteAsync("Hello World!");
	            });
	        }
	    }

	- `AddAuthentication()` adds the authentication services to DI and configures "Bearer" as the default scheme. 
	- `UseAuthentication()` adds the authentication middleware to the pipeline so authentication will be performed automatically on every call into the host.
	- `UseAuthorization()` adds the authorization middleware to make sure, our API endpoint cannot be accessed by anonymous clients.

1. Navigating to the controller `http://localhost:5002/identity` on a browser should return a 401 status code. This means your API requires a credential and is now protected by IdentityServer.

## Create a OAuth 2.0 client
Now we are ready to write a client that requests an access token, and then uses this token to access the API. 

1. Add a console project to your solution:

	`dotnet new console -n Client`

1. The token endpoint at IdentityServer implements the OAuth 2.0 protocol, and you could use raw HTTP to access it. But here, we introduce a client library called `IdentityModel`, that encapsulates the protocol interaction in an easy to use API. It has extension methods for `HttpClient`.

	Add the IdentityModel NuGet package to your client. 

	`Install-Package IdentityModel`

	or by using the CLI:
	
	`dotnet add package IdentityModel`

	![](/images/posts/20190629-identity-server-8.png)

	Please note the client program invokes the Main method asynchronously in order to run asynchronous http calls. This feature is possible since C# 7.1 and will be available once you edit Client.csproj to add the following line as a `<PropertyGroup>`:

	`<LangVersion>latest</LangVersion>`

	![](/images/posts/20190629-identity-server-9.png)

1. Next we modify Program.cs to implement below logic in sequence:

	1. `IdentityModel` includes a client library to use with the discovery endpoint. This way you only need to know the base-address of IdentityServer - the actual endpoint addresses can be read from the metadata:

			// discover endpoints from metadata
			var client = new HttpClient();
			var disco = await client.GetDiscoveryDocumentAsync("http://localhost:5000");
			if (disco.IsError)
			{
			    Console.WriteLine(disco.Error);
			    return;
			}

	1. Use the information from the discovery document to request a token to IdentityServer to access api resource e.g. `empApi`:

			// request token
			var tokenResponse = await client.RequestClientCredentialsTokenAsync(new ClientCredentialsTokenRequest
			{
			    Address = disco.TokenEndpoint,
			
			    ClientId = "client",
			    ClientSecret = "secret",
			    Scope = "api1"
			});
			
			if (tokenResponse.IsError)
			{
			    Console.WriteLine(tokenResponse.Error);
			    return;
			}
	
			Console.WriteLine(tokenResponse.Json);

	1. Finally, we add the access token to the API we typically use in HTTP Authorization header. This is done using the SetBearerToken():

			// call api
			var client = new HttpClient();
			client.SetBearerToken(tokenResponse.AccessToken);
			
			var response = await client.GetAsync("http://localhost:5001/identity");
			if (!response.IsSuccessStatusCode)
			{
			    Console.WriteLine(response.StatusCode);
			}
			else
			{
			    var content = await response.Content.ReadAsStringAsync();
			    Console.WriteLine(JArray.Parse(content));
			}

1. Set multiple startup projects, then `ctrl+f5` to start

	![](/images/posts/20190629-identity-server-11.png)

	![](/images/posts/20190629-identity-server-10.png)

	here is a sample token and we can test in jwt.io 	
		`eyJhbGciOiJSUzI1NiIsImtpZCI6ImQ3ODUyNzdmNGI5OGI5NmViNzExOWIyZDVlYmE0ZDhlIiwidHlwIjoiSldUIn0.eyJuYmYiOjE1NjcxNzQ0MDQsImV4cCI6MTU2NzE3ODAwNCwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo1MDAwIiwiYXVkIjpbImh0dHA6Ly9sb2NhbGhvc3Q6NTAwMC9yZXNvdXJjZXMiLCJlbXBBcGkiXSwiY2xpZW50X2lkIjoiY2xpZW50MSIsInNjb3BlIjpbImVtcEFwaSJdfQ.OGuI37Cf1sGYYU0SC_2daLfoCwnzRu8ySnKeu5fN1sYt_dVJuRQ00yvL3mdvqEWXXm5Fr4za9VkVwVQR0WbtidaqMD2iUci8g-YIx8B_g7kyNrF8U3w-XwGrW86Y1i19td5x_9W4PGWSuxuARxM4RWrGNLzLjAxSzBHff8KdEPzaDEDmm60irKinvrmFhxhy40FVXjfkeogGaqi08fW5ZQyfRlYe48LrWGU3TyE-mAJLoJtYZoUvUWOKwJWrVNTgqynmdfD7rzi8640iRM5h5KU6fW7Ni0eqiFMY0ov-1myDyv1MqqGtqcRT8zl7k-5RJ4CFVnoff4DwQkCnJaPaSg`

# Tutorial 2: Implement resource owner password workflow in OAuth 2.0
Based on the client credential implementation, below changes should be made:

1. update authorization server project

	add users

		public static class Config
	    {
			...
	        public static List<TestUser> GetUsers()
	        {
	            return new List<TestUser>
	            {
	                new TestUser
	                {
	                    SubjectId = "1",
	                    Username = "alice",
	                    Password = "password"
	                },
	                new TestUser
	                {
	                    SubjectId = "2",
	                    Username = "bob",
	                    Password = "password"
	                }
	            };
	        }
	    }

	Startup.cs

		public void ConfigureServices(IServiceCollection services)
		{
		    // configure identity server with in-memory stores, keys, clients and scopes
		    services.AddIdentityServer()
		        .AddInMemoryApiResources(Config.GetApiResources())
		        .AddInMemoryClients(Config.GetClients())
		        .AddTestUsers(Config.GetUsers());
		}

	add support for `resource credential` grant type

	    public static IEnumerable<Client> GetClients()
        {
            return new List<Client>
            {
                new Client
                {
                    ClientId = "client1",
                    AllowedGrantTypes = GrantTypes.ResourceOwnerPassword,
                    // secret for authentication
                    ClientSecrets =
                    {
                        new Secret("secret1".Sha256())
                    },
                    // scopes that client has access to
                    AllowedScopes = { "empApi" }
                },
                ...
            };
        }

1. update client project

	the client should collect the user’s password and send to the token service during the token request. Update the code of using IdentityModel

        // get token
        Console.WriteLine("get access token");
        var tokenResponse = await client.RequestPasswordTokenAsync(new PasswordTokenRequest()
        {
            Address = metadata.TokenEndpoint,
            ClientId = "client1",
            ClientSecret = "secret1",
            Scope = "empApi",

            UserName = "alice",
            Password = "password",
        });

1. startup multiple project, we will see
	
	![](/images/posts/20190629-identity-server-12.png)

	Please note there is one small but important difference of resource owner credential grant compared to the client credentials grant. The access token will now contain a `sub` claim which uniquely identifies the user. This “sub” claim can be seen by examining the content variable after the call to the API and also will be displayed on the screen by the console application.

	The presence (or absence) of the sub claim lets the API distinguish between calls on behalf of clients and calls on behalf of users.

# Tutorial 3: Add interactive user authentication with the OpenID Connect protocol to the authorization server

First, we should already started with an empty web application, added identityserver and configured the resources, clients and users.

1. cmd > switch to authorization server folder `cd IdentityServer` > create UI project from template

	`dotnet new is4ui`

1. Since we just added the MVC UI, we will also need to enable MVC, both in the DI system and in the pipeline. 

	Startup.cs > uncomment comments in the ConfigureServices and Configure method to enable MVC.

		public class Startup
		{
		    public void ConfigureServices(IServiceCollection services)
		    {
		        services.AddMvc();
				...
		    }		
		    public void Configure(IApplicationBuilder app)
		    {
		        app.UseStaticFiles();		
		        app.UseIdentityServer();		
		        app.UseMvcWithDefaultRoute();
		    }
		}

1. Run the IdentityServer application, you should now see a home page.

	![](/images/posts/20190629-identity-server-13.png)

	click here to manage your stored grants > we can use the user password in previous tutorial to login

	![](/images/posts/20190629-identity-server-14.png)

	![](/images/posts/20190629-identity-server-15.png)

1. Create a MVC client > add to solution, this will be the OpenID connect client

	![](/images/posts/20190629-identity-server-16.png)

	![](/images/posts/20190629-identity-server-17.png)

	Once you’ve created the project, configure the application to run on port 5003.

	![](/images/posts/20190629-identity-server-18.png)

1. client > modify configuration > add support for OpenID Connect authentication to the MVC application

	Startup.cs

		// setup configuration
		public void ConfigureServices(IServiceCollection services)
		{
		    services.AddMvc();
		
		    JwtSecurityTokenHandler.DefaultInboundClaimTypeMap.Clear();
		
		    services.AddAuthentication(options =>
		        {
		            options.DefaultScheme = "Cookies";
		            options.DefaultChallengeScheme = "oidc";
		        })
		        .AddCookie("Cookies")
		        .AddOpenIdConnect("oidc", options =>
		        {
		            options.Authority = "http://localhost:5000";
		            options.RequireHttpsMetadata = false;
		
		            options.ClientId = "client1";
                    options.ClientSecret = "secret1";
		            options.SaveTokens = true;
		        });
		}
		// setup middleware of pipeline
		public void Configure(IApplicationBuilder app, IHostingEnvironment env)
		{
		    if (env.IsDevelopment())
		    {
		        app.UseDeveloperExceptionPage();
		    }
		    else
		    {
		        app.UseExceptionHandler("/Home/Error");
		    }
		
		    app.UseAuthentication();
		
		    app.UseStaticFiles();
		    app.UseMvcWithDefaultRoute();
		}

	- AddAuthentication() adds the authentication services to DI.
	- UseAuthentication() ensure the authentication services execute on each request - add it into pipeline, please note the authentication middleware should be added before the MVC in the pipeline.

1. implement the authentication handshake. We want when user click the "About" menu, it connects the identity server to grab token and redirects back. That is interactive authentication. 

	Home controller > add [Authorize] 

		public class HomeController : Controller
	    {
	        public IActionResult Index()
	        {
	            return View();
	        }
	
	        [Authorize]
	        public IActionResult About()
	        {
	            ViewData["Message"] = "Your application description page.";
	
	            return View();
	        }
			...

	then, modify the view - abount.cshtml to display the claims of the user as well as the cookie properties:

		@{
	    ViewData["Title"] = "About";
		}
		<h2>@ViewData["Title"]</h2>
		<h3>@ViewData["Message"]</h3>
		
		<h1>About page</h1>
		
		@using Microsoft.AspNetCore.Authentication
		
		<h2>Claims</h2>
		
		<dl>
		    @foreach (var claim in User.Claims)
		    {
		        <dt>@claim.Type</dt>
		        <dd>@claim.Value</dd>
		    }
		</dl>
		
		<h2>Properties</h2>
		
		<dl>
		    @foreach (var prop in (await Context.AuthenticateAsync()).Properties.Items)
		    {
		        <dt>@prop.Key</dt>
		        <dd>@prop.Value</dd>
		    }
		</dl>

	Start projects > home page > click "About"

	![](/images/posts/20190629-identity-server-18.png)

	a redirect attempt will be made to IdentityServer - this will result in an error because the MVC client is not registered yet.

	![](/images/posts/20190629-identity-server-19.png)

1. authorization server > add support for OpenID Connect Identity Scopes

	Similar to OAuth 2.0, OpenID Connect also uses the scopes concept and scopes represent something you want to protect and that clients want to access. In OAuth, scopes represent APIs; but in OIDC, scopes represent identity data like user id, name or email address.

	config.cs

		public static IEnumerable<IdentityResource> GetIdentityResources()
		{
		    return new List<IdentityResource>
		    {
		        new IdentityResources.OpenId(),
		        new IdentityResources.Profile(),
		    };
		}

1. authorization server > Add a client for OpenID Connect implicit flow

	Similar to OAuth 2.0 clients, we simply add a new OpenID Connect-based client in authorization server. Please note the flows in OIDC are always interactive, we need to add some redirect URLs to our configuration.

	config.cs

		public static IEnumerable<Client> GetClients()
		{
		    return new List<Client>
		    {
		        // other clients omitted...
		
		        // OpenID Connect implicit flow client (MVC)
		        new Client
		        {
		            ClientId = "mvc",
		            ClientName = "MVC Client",
		            AllowedGrantTypes = GrantTypes.Implicit,
		
		            // where to redirect to after login
		            RedirectUris = { "http://localhost:5003/signin-oidc" },
		
		            // where to redirect to after logout
		            PostLogoutRedirectUris = { "http://localhost:5003/signout-callback-oidc" },
		
		            AllowedScopes = new List<string>
		            {
		                IdentityServerConstants.StandardScopes.OpenId,
		                IdentityServerConstants.StandardScopes.Profile
		            }
		        }
		    };
		}

1. Configure the MvcClient to connect to this new client in authorization server

	![](/images/posts/20190629-identity-server-20.png)

1. Start the authorization server(IdentityServer project) and OpenID connect client(MvcClient project)

	![](/images/posts/20190629-identity-server-18.png)

	click "About"

	![](/images/posts/20190629-identity-server-21.png)

	enter alice/password > it redirects back > Yes, allow

	![](/images/posts/20190629-identity-server-22.png)

	![](/images/posts/20190629-identity-server-23.png)

	As you can see, the cookie has two parts, the claims of the user, and some metadata. This metadata also contains the original token that was issued by IdentityServer. Feel free to copy this token to jwt.io to inspect its content. e.g. 

	`eyJhbGciOiJSUzI1NiIsImtpZCI6ImQ3ODUyNzdmNGI5OGI5NmViNzExOWIyZDVlYmE0ZDhlIiwidHlwIjoiSldUIn0.eyJuYmYiOjE1NjcyMDM5MTEsImV4cCI6MTU2NzIwNDIxMSwiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo1MDAwIiwiYXVkIjoibXZjIiwibm9uY2UiOiI2MzcwMjgwMDY2ODExMjk5MzEuT1dNNU5HSXlPVGN0WkdVek5pMDBNamM1TFRsak1EQXRNREk0T0RGaU9UYzRNVEk1TlRVelptSmhNVGt0TlRNNE15MDBOR1l3TFdFek1tTXRZVFV4TWpVMk5USTBNbU15IiwiaWF0IjoxNTY3MjAzOTExLCJzaWQiOiJlZjIwMjI2YWQxM2YwMmM2ZDIxMjQxYjU2NzU4MDBmZSIsInN1YiI6IjEiLCJhdXRoX3RpbWUiOjE1NjcyMDM4NzcsImlkcCI6ImxvY2FsIiwiYW1yIjpbInB3ZCJdfQ.LKZ14Sv5iaL3_9JKt4JgviN1nItkzWTZDMlFKStiqkIx18yGFZhHWTpRWm5o_IGMockxqT8haj8y341JWJIFKSsd3R-efVYouLkbjn1GBd56e6EI3FKie6ZeFSN9dB7iAQiJbFPPXND5loj_xGC87XMyfedZierKyaIBC9srI-psLFGxODDcT-V0PduQ3BRNaQrzMrQ2wU_16dSEcPQ9ZmsUzB_QjwSV0-hG9VNEJNxxTUMMqmWdJLOY_rGqjk3prOEq4tJiGadVIPVTR8M9pTXoWyN5ikVQ3caXxsZ8F79dWRuWasRvww0Z9e8PDj2iS4tseqcLZQl9h-aBR16A6A`

1. add sign-out to the OpenID connect client(MvcClient).

	With an authentication service like IdentityServer, it is not enough to clear the local application cookies. In addition you also need to make a roundtrip to IdentityServer to clear the central single sign-on session.

	The exact protocol steps are implemented inside the OpenID Connect handler, simply add the following code to some controller to trigger the sign-out:

		public IActionResult Logout()
		{
		    return SignOut("Cookies", "oidc");
		}

	This will clear the local cookie and then redirect to IdentityServer. Then, IdentityServer will clear its cookies and then give the user a link to return back to the MVC application.

	brower > http://localhost:5003/Home/Logout

	![](/images/posts/20190629-identity-server-25.png)

1. Add more claims from authorization server to the client

	By default, the OpenID Connect handler asks for the profile scope. This scope also includes claims like name or website.

	authorization server > users > add more claims to let IdentityServer put them into the identity token:

		public static List<TestUser> GetUsers()
		{
		    return new List<TestUser>
		    {
		        new TestUser
		        {
		            SubjectId = "1",
		            Username = "alice",
		            Password = "password",
		
		            Claims = new []
		            {
		                new Claim("name", "Alice"),
		                new Claim("website", "https://alice.com")
		            }
		        },
		        new TestUser
		        {
		            SubjectId = "2",
		            Username = "bob",
		            Password = "password",
		
		            Claims = new []
		            {
		                new Claim("name", "Bob"),
		                new Claim("website", "https://bob.com")
		            }
		        }
		    };
		}

	authorization server > add more scopes

        public static IEnumerable<IdentityResource> GetIdentityResources()
        {
            return new IdentityResource[]
            {
                new IdentityResources.OpenId(),
                new IdentityResources.Profile(),
                new IdentityResources.Email(), 
                new IdentityResources.Phone(), 
            };
        }

	authorization server > allow client to accept more scopes

		// OpenID Connect implicit flow client (MVC)
        new Client
        {
            ClientId = "mvc",
            ClientName = "MVC Client",
            AllowedGrantTypes = GrantTypes.Implicit,

            // where to redirect to after login
            RedirectUris = { "http://localhost:5003/signin-oidc" },

            // where to redirect to after logout
            PostLogoutRedirectUris = { "http://localhost:5003/signout-callback-oidc" },

            AllowedScopes = new List<string>
            {
                IdentityServerConstants.StandardScopes.OpenId,
                IdentityServerConstants.StandardScopes.Profile,
                IdentityServerConstants.StandardScopes.Email,
                IdentityServerConstants.StandardScopes.Phone,
            }
        }

	We can add more claims - and also more scopes. Next time you authenticate, your claims page will now show the additional claims. e.g. a new token:

	`eyJhbGciOiJSUzI1NiIsImtpZCI6ImQ3ODUyNzdmNGI5OGI5NmViNzExOWIyZDVlYmE0ZDhlIiwidHlwIjoiSldUIn0.eyJuYmYiOjE1NjcyMDc0NDMsImV4cCI6MTU2NzIwNzc0MywiaXNzIjoiaHR0cDovL2xvY2FsaG9zdDo1MDAwIiwiYXVkIjoibXZjIiwibm9uY2UiOiI2MzcwMjgwNDE4Nzc5Mzk5MzEuT0dWaVkyUmxOVFF0WkdFNU55MDBPR1l6TFdFelpUY3Raakl6T0RBM09USmpZek0zTnpBMFpqQTFZVFl0TmpkbE9DMDBNR1pqTFRoaE56VXROV1F6TTJZelpEZGpOVEl5IiwiaWF0IjoxNTY3MjA3NDQzLCJzaWQiOiI1OWUyZDEzZmU1YTIxN2NlOWM3NmE5OGY1MjNmNjViOCIsInN1YiI6IjEiLCJhdXRoX3RpbWUiOjE1NjcyMDcxNDUsImlkcCI6ImxvY2FsIiwibmFtZSI6IkFsaWNlIiwiZW1haWwiOiJhbGljZUB3b3JsZC5jb20iLCJ3ZWJzaXRlIjoiaHR0cHM6Ly9hbGljZS5jb20iLCJhbXIiOlsicHdkIl19.C185S2BGrCw1h1pJxlXkqPi3jTpHdGtM62Qq5_pSS32M3kAqE_VDnBOouP65ViswZ4JFa_ZwG8bRxmdC3rQcSZp1m0q_tisuEoHgRdv5OWy7EW-ev2WBXGyuHQAnPpPLrmHCEe6llr1C0v34yrNvvBgYiiozEJxmRwUghTcRYIQrmv6RvEtCoOfhmV8dlRTrUi5rW8wdGefkUPgxssBftuilQifFfQhmT3q67wnS6M9L4lR7wcs4EMa5oTiZ1lY_5rFsvDNyE8w6sONi7LEC123PluvVZZDiqkt4NSMdmc4SvE-SpTYdroJS4J2pEkEC6gsDpQOH2NPIyig8-H-ZIw`

	In client, the Scope property on the OpenID Connect middleware is where you configure which scopes will be sent to IdentityServer during authentication.

	client > Startup.cs

		public void ConfigureServices(IServiceCollection services)
        {
            ...
            services.AddAuthentication(options =>
                {
                    options.DefaultScheme = "Cookies";
                    options.DefaultChallengeScheme = "oidc";
                })
                .AddCookie("Cookies")
                .AddOpenIdConnect("oidc", options =>
                {
                    options.Authority = "http://localhost:5000";
                    options.RequireHttpsMetadata = false;

                    //options.ClientId = "client1";
                    //options.ClientSecret = "secret1";
                    options.ClientId = "mvc";
                    options.Scope.Add("openid profile email phone"); // request more scopes
                    options.SaveTokens = true;
                });
        }

	Start the projects, client website > we can find the client is asking more scopes

	![](/images/posts/20190629-identity-server-24.png)

# Tutorial 4: Add external authentication support to the authorization server
ASP.NET Core itself ships with support for Google, Facebook, Twitter, Microsoft Account and OpenID Connect. In addition you can find implementations for many other authentication providers [here](https://github.com/aspnet-contrib/AspNet.Security.OAuth.Providers).

## Add Google support
1. google developer console [https://console.developers.google.com/](https://console.developers.google.com/) > Create a new project

	![](/images/posts/20191203-identity-server-1.png)

1. enable a new api > randomly pick a api service > enable
	
	![](/images/posts/20191203-identity-server-2.png)

	![](/images/posts/20191203-identity-server-3.png)

	![](/images/posts/20191203-identity-server-4.png)

	![](/images/posts/20191203-identity-server-5.png)

1. create a new credential > oauth client id > specify `http://localhost:5000/signin-google` as the redirect url

	![](/images/posts/20191203-identity-server-6.png)

	![](/images/posts/20191203-identity-server-7.png)

3. The developer console generate a client ID and secret

	![](/images/posts/20191203-identity-server-8.png)

1. vs > open IdentityServer project > startup.cs

	    public class Startup
	    {
	        public IHostingEnvironment Environment { get; }
	
	        public Startup(IHostingEnvironment environment)
	        {
	            Environment = environment;
	        }
	
	        public void ConfigureServices(IServiceCollection services)
	        {
	            ...
	            services.AddAuthentication()
	                .AddGoogle("Google Inc.", options =>
	                {
	                    options.SignInScheme = IdentityServerConstants.ExternalCookieAuthenticationScheme;
	                    options.ClientId = "xxxxxx";
	                    options.ClientSecret = "xxxxxx";
	                });
				...
	        }
			...
		}

1. restart the identity server project, mvc client project > open mvc client at http://localhost:5003/ > redirect to identity server > click login > identity server shows login page > login using any google account > authenticate successfully and return mvc client > we can see that the claims are now being sourced from Google data.

	![](/images/posts/20191203-identity-server-9.png)

	![](/images/posts/20191203-identity-server-10.png)

	![](/images/posts/20191203-identity-server-11.png)

	![](/images/posts/20191203-identity-server-12.png)

## Add IdentityServer4 online authentication demo services
IdentityServer4 team provides some online demos at [https://demo.identityserver.io/](https://demo.identityserver.io/), which we can integrate using OpenID Connect. More clients are available at [https://github.com/IdentityServer/IdentityServer4.Demo/blob/master/src/IdentityServer4Demo/Config.cs](https://github.com/IdentityServer/IdentityServer4.Demo/blob/master/src/IdentityServer4Demo/Config.cs)

Here, we integrate some demo services into identity server. It can enable us to have more authenticate channels. Of course, we can integrate them into our clients as well.

1. vs > open IdentityServer project > startup.cs

	    public class Startup
	    {
	        public IHostingEnvironment Environment { get; }
	
	        public Startup(IHostingEnvironment environment)
	        {
	            Environment = environment;
	        }
	
	        public void ConfigureServices(IServiceCollection services)
	        {
	            ...
	            services.AddAuthentication()
	                .AddOpenIdConnect("oidc", "Demo IdentityServer 1", options =>
	                {
	                    options.SignInScheme = IdentityServerConstants.ExternalCookieAuthenticationScheme;
	                    options.SignOutScheme = IdentityServerConstants.SignoutScheme;
	                    options.SaveTokens = true;
	
	                    options.Authority = "https://demo.identityserver.io/";
	                    options.ClientId = "server.hybrid";
	                    options.ClientSecret = "secret";
	                    options.ResponseType = "code id_token";
	                    options.Scope.Add("openid profile email api");
	
	                })
	                .AddOpenIdConnect("oidc 2", "Demo IdentityServer 2", options =>
	                {
	                    options.SignInScheme = IdentityServerConstants.ExternalCookieAuthenticationScheme;
	                    options.SignOutScheme = IdentityServerConstants.SignoutScheme;
	                    options.SaveTokens = true;
	
	                    options.Authority = "https://demo.identityserver.io/";
	                    options.ClientId = "server.code";
	                    options.ClientSecret = "secret";
	                    options.ResponseType = "code";
	                    options.Scope.Add("api");
	                })
	
	                .AddOpenIdConnect("oidc 3", "Demo IdentityServer 3", options =>
	                {
	                    options.SignInScheme = IdentityServerConstants.ExternalCookieAuthenticationScheme;
	                    options.SignOutScheme = IdentityServerConstants.SignoutScheme;
	                    options.SaveTokens = true;
	
	                    options.Authority = "https://demo.identityserver.io/";
	                    options.ClientId = "implicit";
	                    options.TokenValidationParameters = new TokenValidationParameters
	                    {
	                        NameClaimType = "name",
	                        RoleClaimType = "role"
	                    };
	                })
				...
	        }
			...
		}

1. restart the identity server and client > open client > click login > redirect to identity server > display login page

	![](/images/posts/20191203-identity-server-13.png)

	click `Demo IdentityServer x` button to login

	![](/images/posts/20191203-identity-server-14.png)

# Tutorial 5: Add refresh token to the authorization server
1. modify the client configuration in Identity server



# References
[http://docs.identityserver.io/en/latest/quickstarts/0_overview.html](http://docs.identityserver.io/en/latest/quickstarts/0_overview.html)

[IdentityServer Samples](https://github.com/IdentityServer/IdentityServer4/tree/master/samples/Quickstarts)

[Simple OAuth2 Authorization Server with Identity Server and .NET Core 1.1](https://www.codeproject.com/Articles/1183421/Simple-OAuth-Authorization-Server-with-Identity-Se)

[ASP.NET Core WebAPI secured using OAuth2 Client Credentials](https://www.codeproject.com/Articles/1185880/ASP-NET-Core-WebAPI-secured-using-OAuth-Client-Cre)


[http://docs.identityserver.io/en/latest/quickstarts/3_aspnetcore_and_apis.html](http://docs.identityserver.io/en/latest/quickstarts/3_aspnetcore_and_apis.html)

[https://auth0.com/docs/api-auth/tutorials/client-credentials](https://auth0.com/docs/api-auth/tutorials/client-credentials)

[https://www.codeproject.com/Articles/1183421/Simple-OAuth-Authorization-Server-with-Identity-Se](https://www.codeproject.com/Articles/1183421/Simple-OAuth-Authorization-Server-with-Identity-Se)



[https://www.codeproject.com/Articles/1185880/ASP-NET-Core-WebAPI-secured-using-OAuth-Client-Cre](https://www.codeproject.com/Articles/1185880/ASP-NET-Core-WebAPI-secured-using-OAuth-Client-Cre)

