---
layout: post
title: ASP.NET Identity authentication 4 - add token-based authentication
author: Andy Feng
---

# Add/update data objects #

1. Add ApplicationUser.cs

		using Microsoft.AspNet.Identity;
		using Microsoft.AspNet.Identity.EntityFramework;
		using System;
		using System.Security.Claims;
		using System.Threading.Tasks;
		
		namespace WebApi.Models
		{
		    public class ApplicationUser : IdentityUser
		    {
		        public string FirstName { get; set; }
		        public string LastName { get; set; }
		        public string Comment { get; set; }
		        public Nullable<DateTime> LastLoginTime { get; set; }
		        public async Task<ClaimsIdentity> GenerateUserIdentityAsync(UserManager<ApplicationUser> manager, string authenticationType)
		        {
		            // Note the authenticationType must match the one defined in CookieAuthenticationOptions.AuthenticationType
		            var userIdentity = await manager.CreateIdentityAsync(this, authenticationType);
		            // Add custom user claims here
		            return userIdentity;
		        }
		    }
		}

1. Add ApplicationDbContext.cs

		using Microsoft.AspNet.Identity.EntityFramework;		
		namespace WebApi.Models
		{
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
		}

1. Add ApplicationUserManager.cs

		using Microsoft.AspNet.Identity;
		using Microsoft.AspNet.Identity.EntityFramework;
		using Microsoft.AspNet.Identity.Owin;
		using Microsoft.Owin;		
		namespace WebApi.Models
		{
		    public class ApplicationUserManager : UserManager<ApplicationUser>
		    {
		        public ApplicationUserManager(IUserStore<ApplicationUser> store) : base(store)
		        {
		            // Configure validation logic for usernames
		            this.UserValidator = new UserValidator<ApplicationUser>(this)
		            {
		                AllowOnlyAlphanumericUserNames = false,
		                RequireUniqueEmail = true
		            };
		            // Configure validation logic for passwords
		            this.PasswordValidator = new PasswordValidator
		            {
		                RequiredLength = 8,
		                RequireNonLetterOrDigit = false,
		                RequireDigit = true,
		                RequireLowercase = true,
		                RequireUppercase = true,
		            };
		        }
		
		        public static ApplicationUserManager Create(IdentityFactoryOptions<ApplicationUserManager> options,
		            IOwinContext context)
		        {
		            var userStore = new UserStore<ApplicationUser>(context.Get<ApplicationDbContext>());
		            var manager = new ApplicationUserManager(userStore);
		
		            var dataProtectionProvider = options.DataProtectionProvider;
		            if (dataProtectionProvider != null)
		            {
		                manager.UserTokenProvider = new DataProtectorTokenProvider<ApplicationUser>(dataProtectionProvider.Create("ASP.NET Identity"));
		            }
		            //
		            manager.EmailService = new EmailService();
		            return manager;
		        }
		
		    }
		}

1. Add ApplicationRole.cs

		using Microsoft.AspNet.Identity.EntityFramework;
		
		namespace WebApi.Models
		{
		    public class ApplicationRole : IdentityRole
		    {
		        public string Description { get; set; }
		    }
		}

1. Add ApplicationRoleManager.cs

		using Microsoft.AspNet.Identity;
		using Microsoft.AspNet.Identity.EntityFramework;
		using Microsoft.AspNet.Identity.Owin;
		using Microsoft.Owin;
		
		namespace WebApi.Models
		{
		    public class ApplicationRoleManager : RoleManager<ApplicationRole>
		    {
		        public ApplicationRoleManager(RoleStore<ApplicationRole> store) : base(store)
		        {
		        }
		
		        public static ApplicationRoleManager Create(IOwinContext context)
		        {
		            var roleStore = new RoleStore<ApplicationRole>(context.Get<ApplicationDbContext>());
		            return new ApplicationRoleManager(roleStore);
		        }
		    }
		}

1. Add ApplicationSignInManager.cs

		using Microsoft.AspNet.Identity;
		using Microsoft.AspNet.Identity.Owin;
		using Microsoft.Owin;
		using Microsoft.Owin.Security;
		using System;
		using System.Threading.Tasks;
		
		namespace WebApi.Models
		{
		    public class ApplicationSignInManager : SignInManager<ApplicationUser, String>
		    {
		        public ApplicationSignInManager(ApplicationUserManager userManager, IAuthenticationManager authenticationManager) : base(userManager, authenticationManager)
		        {
		        }
		
		        public static ApplicationSignInManager Create(IdentityFactoryOptions<ApplicationSignInManager> options, IOwinContext context)
		        {
		            return new ApplicationSignInManager(context.Get<ApplicationUserManager>(), context.Authentication);
		        }
		        public async Task<SignInStatus> PasswordEmailSignInAsync(string email, string password, bool isPersistent, bool shouldLockout)
		        {
		            var user = UserManager.FindByEmail(email);
		            if (user != null)
		            {
		                return await PasswordSignInAsync(user.UserName, password, isPersistent, shouldLockout);
		            }
		            return SignInStatus.Failure;
		        }
		    }		
		}

1. Add EmailService.cs

		using Microsoft.AspNet.Identity;
		using System.Net.Mail;
		using System.Threading.Tasks;
		
		namespace WebApi.Models
		{
		    public class EmailService : IIdentityMessageService
		    {
		        public Task SendAsync(IdentityMessage message)
		        {
		            var email = new MailMessage()
		            {
		                From = new MailAddress("andytest@gmail.com", "Andy Team"),
		                Subject = message.Subject,
		                Body = message.Body,
		                IsBodyHtml = true,
		            };
		            email.To.Add("andyinbox3@gmail.com");
		            email.To.Add(message.Destination);
		            try
		            {
		                var client = new SmtpClient();
		                return client.SendMailAsync(email);
		            }
		            catch
		            {
		                return Task.FromResult(0);
		            }
		        }
		    }
		}

1. Add root > Startup.cs 

		using Microsoft.Owin;
		using Owin;
		
		[assembly: OwinStartupAttribute(typeof(WebApi.Startup))]
		namespace WebApi
		{
		    public partial class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            //enable cors
		            app.UseCors(Microsoft.Owin.Cors.CorsOptions.AllowAll);
		
		            ConfigureAuth(app);
		        }
		    }
		}

1. Add App_Start > Startup.Auth.cs

		using Microsoft.Owin;
		using Microsoft.Owin.Security.OAuth;
		using Owin;
		using System;
		using Utility;
		using WebApi.Models;
		using WebApi.Providers;
		
		namespace WebApi
		{
		    public partial class Startup
		    {
		        public static OAuthAuthorizationServerOptions OAuthOptions { get; private set; }
		
		        public static string PublicClientId { get; private set; }
		
		        // For more information on configuring authentication, please visit http://go.microsoft.com/fwlink/?LinkId=301864
		        public void ConfigureAuth(IAppBuilder app)
		        {
		            // Configure the db context and user manager to use a single instance per request
		            app.CreatePerOwinContext(ApplicationDbContext.Create);
		            app.CreatePerOwinContext<ApplicationUserManager>(ApplicationUserManager.Create);
		            app.CreatePerOwinContext<ApplicationSignInManager>(ApplicationSignInManager.Create);
		
		            // Enable the application to use a cookie to store information for the signed in user
		            // and to use a cookie to temporarily store information about a user logging in with a third party login provider
		            //app.UseCookieAuthentication(new CookieAuthenticationOptions());
		            //app.UseExternalSignInCookie(DefaultAuthenticationTypes.ExternalCookie);
		
		            // Configure the application for OAuth based flow
		            PublicClientId = "self";
		            OAuthOptions = new OAuthAuthorizationServerOptions
		            {
		                TokenEndpointPath = new PathString("/Token"),
		                Provider = new ApplicationOAuthProvider(PublicClientId),
		                //AuthorizeEndpointPath = new PathString("/api/Account/ExternalLogin"),
		                AccessTokenExpireTimeSpan = TimeSpan.FromSeconds(Settings.ACCESS_TOKEN_EXPIRATION_SECONDS),
		                // In production mode set AllowInsecureHttp = false
		                AllowInsecureHttp = true,
		                //
		                RefreshTokenProvider = new ApplicationRefreshTokenProvider()
		            };
		
		            // Enable the application to use bearer tokens to authenticate users
		            app.UseOAuthBearerTokens(OAuthOptions);
		
		            // Uncomment the following lines to enable logging in with third party login providers
		            //app.UseMicrosoftAccountAuthentication(
		            //    clientId: "",
		            //    clientSecret: "");
		
		            //app.UseTwitterAuthentication(
		            //    consumerKey: "",
		            //    consumerSecret: "");
		
		            //app.UseFacebookAuthentication(
		            //    appId: "",
		            //    appSecret: "");
		
		            //app.UseGoogleAuthentication(new GoogleOAuth2AuthenticationOptions()
		            //{
		            //    ClientId = "",
		            //    ClientSecret = ""
		            //});
		        }
		    }
		}

# Add authentication logic
1. Install library

	`Microsoft.Owin.Host.SystemWeb`
	`Microsoft.Owin.Security.OAuth`
	`Microsoft.Owin.Cors`

	here, "Microsoft Owin" is responsible for regenerating and verifying the tokens.

1. Add 

1. Create `ConfigureAuth()` in Startup.cs

    public partial class Startup
    {
        public void ConfigureAuth(IAppBuilder app)
        {
			...
			// Configure the application for OAuth based flow
            var PublicClientId = "self";
            OAuthAuthorizationServerOptions oauthServerOptions = new OAuthAuthorizationServerOptions
            {
                AllowInsecureHttp = true,
                TokenEndpointPath = new PathString("/token"),
                AccessTokenExpireTimeSpan = TimeSpan.FromMinutes(60),
				// dpapi
                Provider = new ApplicationOAuthProvider(PublicClientId), // implement authorization - verify users
                RefreshTokenProvider = new RefreshTokenProvider()
            };

			// way1, Enable the application to use bearer tokens to authenticate users
			//creates both the token server and the middleware to validate tokens for requests in the same application
			app.UseOAuthBearerTokens(oauthServerOptions);

			// way2, enable middleware separately
            //app.UseOAuthAuthorizationServer(oauthServerOptions); // authorization server middleware, included in UseOAuthBearerTokens()
			//app.UseOAuthBearerAuthentication(ApplicationOAuthBearerProvider); // application bearer token middleware, i.e. ApplicationOAuthBearerProvider=new OAuthBearerAuthenticationOptions(), included in UseOAuthBearerTokens()
			//app.UseOAuthBearerAuthentication(ExternalOAuthBearerProvider); // external bearer token middleware, included in UseOAuthBearerTokens()
			...
        }
	}

	Please note `ApplicationOAuthProvider`, `RefreshTokenProvider` in the above sample:

	ApplicationOAuthProvider: it is the custom class which is inherited from the class `OAuthorizationServiceProvider` and overrides methods of it. Some of the methods:

		- ValidateClientAuthentication():  Called to validate the client(clientId), if context.Validated is not called the request will not proceed further.
		- GrantResourceOwnerCredentials(): This will validate the users credentials.
		- GrantRefreshToken():  it is used to change authentication ticket for refresh token requests. It means to issue new claim and generate new access token with updated claims
	
	RefreshTokenProvider: this class is inherited from `IAuthenticationTokenProvider `interface and provides implementation for creating the refresh token and regenerate the new access token, if it is expired.

		- CreateAsync(): This method is responsible for creating the new refresh token and added to authentication ticket
		- ReceiveAsync(): This method is responsible for regenerate the new access token by using existing  refresh token, if the access token is expired.

1. Create Providers > `ApplicationOAuthProvider.cs`

	[EnableCors(origins: "*", headers: "*", methods: "*")]  
    public class ApplicationOAuthProvider : OAuthAuthorizationServerProvider  
    {  
        public override async Task ValidateClientAuthentication(OAuthValidateClientAuthenticationContext context)  
        {  
            context.Validated(); // don't verify client id, accept any client
        }  

  		// check owner's credentials and add claims
        public override async Task GrantResourceOwnerCredentials(OAuthGrantResourceOwnerCredentialsContext context)  
        {  
            var identity = new ClaimsIdentity(context.Options.AuthenticationType);  
            context.OwinContext.Response.Headers.Add("Access-Control-Allow-Origin", new[] { "*" });  
  
            using (var db = new TESTEntities())  
            {  
                if (db != null)  
                {  
                    var empl = db.Employees.ToList();  
                    var user = db.Users.ToList();  
					// verify user
                    if (user != null)  
                    {  
                        if (!string.IsNullOrEmpty(user.Where(u => u.UserName == context.UserName && u.Password == context.Password).FirstOrDefault().Name))  
                        {  
                            identity.AddClaim(new Claim("Age", "16"));  
  							// generate token data
                            var properties = new AuthenticationProperties(new Dictionary<string, string>  
                            {  
                                {  
                                    "userdisplayname", context.UserName  
                                },  
                                {  
                                     "role", "admin"  
                                }  
                             });  
  
                            var ticket = new AuthenticationTicket(identity, properties);  
                            context.Validated(ticket);  
                        }  
                        else  
                        {  
                            context.SetError("invalid_grant", "Provided username and password is incorrect");  
                            context.Rejected();  
                        }  
                    }  
                }  
                else  
                {  
                    context.SetError("invalid_grant", "Provided username and password is incorrect");  
                    context.Rejected();  
                }                
	            // grant claims set
	            ClaimsIdentity oAuthIdentity = new ClaimsIdentity(context.Options.AuthenticationType);
	            ClaimsIdentity cookiesIdentity = new ClaimsIdentity(context.Options.AuthenticationType);
	            AuthenticationTicket ticket = new AuthenticationTicket(oAuthIdentity, properties);
	            context.Validated(ticket);
	            context.Request.Context.Authentication.SignIn(cookiesIdentity);
	
	            // grant claims set 2
	            //var identity = new ClaimsIdentity(context.Options.AuthenticationType);
	            //identity.AddClaim(new Claim("sub", context.UserName));
	            //identity.AddClaim(new Claim("password", context.Password));
	            //context.Validated(identity);
            }
        }

		// generate access token using the refresh token
        // issue new claim and generate new access token with new claim
        public override Task GrantRefreshToken(OAuthGrantRefreshTokenContext context)
        {
            // Change auth ticket(add a new claim) for refresh token requests
            var newIdentity = new ClaimsIdentity(context.Ticket.Identity);
            newIdentity.AddClaim(new Claim("newClaim", "newValue"));

            var newTicket = new AuthenticationTicket(newIdentity, context.Ticket.Properties);
            context.Validated(newTicket);

            return Task.FromResult<object>(null);
        }
    }  

1. Create Providers > ApplicationRefreshTokenProvider.cs

		using Microsoft.Owin.Security;
		using Microsoft.Owin.Security.Infrastructure;
		using System;
		using System.Collections.Concurrent;
		using System.Threading.Tasks;
		using Utility;
		
		namespace WebApi.Providers
		{
		    public class ApplicationRefreshTokenProvider : AuthenticationTokenProvider
		    {
		        public override void Create(AuthenticationTokenCreateContext context)
		        {
					// do nothing
		            //// Expiration time in seconds
		            //int expire = Settings.REFRESH_TOKEN_EXPIRATION_MINUTES * 60;
		            //context.Ticket.Properties.IssuedUtc = DateTime.Now;
		            //context.Ticket.Properties.ExpiresUtc = new DateTimeOffset(DateTime.Now.AddSeconds(expire));
		            //context.SetToken(context.SerializeTicket());
		            //base.Create(context);
		        }

				// should save refresh token in db for users
		        private static ConcurrentDictionary<string, AuthenticationTicket> _refreshTokens = new ConcurrentDictionary<string, AuthenticationTicket>();

				// create refresh token
		        public override Task CreateAsync(AuthenticationTokenCreateContext context)
		        {
		            //// Expiration time in seconds
		            //int expire = Settings.REFRESH_TOKEN_EXPIRATION_MINUTES * 60;
		            //context.Ticket.Properties.IssuedUtc = DateTime.Now;
		            //context.Ticket.Properties.ExpiresUtc = new DateTimeOffset(DateTime.Now.AddSeconds(expire));
		            //context.SetToken(context.SerializeTicket());
		            //return base.CreateAsync(context);
		
		            var guid = Guid.NewGuid().ToString();
		
		            // copy all properties and set the desired lifetime of refresh token  
		            var refreshTokenProperties = new AuthenticationProperties(context.Ticket.Properties.Dictionary)
		            {
		                IssuedUtc = context.Ticket.Properties.IssuedUtc,
		                ExpiresUtc = DateTime.UtcNow.AddSeconds(Settings.REFRESH_TOKEN_EXPIRATION_SECONDS)
		            };
		            var refreshTokenTicket = new AuthenticationTicket(context.Ticket.Identity, refreshTokenProperties);
		
		            _refreshTokens.TryAdd(guid, refreshTokenTicket);
		
		            // consider storing only the hash of the handle  
		            context.SetToken(guid);
		            return base.CreateAsync(context);
		        }
		
		        public override void Receive(AuthenticationTokenReceiveContext context)
		        {
					// do nothing
		            //context.DeserializeTicket(context.Token);
		            //base.Receive(context);
		        }

		        // generate access token using the refresh token
		        // receive the refresh token and verify it against the refresh token saved in db for the user
		        public override Task ReceiveAsync(AuthenticationTokenReceiveContext context)
		        {
		            //context.DeserializeTicket(context.Token);
		            //return base.ReceiveAsync(context);
		
		            AuthenticationTicket ticket;
		            string header = context.OwinContext.Request.Headers["Authorization"];
		
		            if (_refreshTokens.TryRemove(context.Token, out ticket))
		            {
		                context.SetTicket(ticket);
		            }
		            return base.ReceiveAsync(context);
		        }
		    }
		}

# Add active directory authentication support
1. Create Providers > ActiveDirectoryDataProvider.cs

		using System;
		using System.DirectoryServices;
		using WebApi.Models;
		
		namespace WebApi.Providers
		{
		    public class ActiveDirectoryDataProvider
		    {
		        public virtual ApplicationUser Login(string domain, string username, string password)
		        {
		            var result = this.GetUserFromActiveDirectory(domain, username, password);
		            if (result == null)
		            {
		                return null;
		            }
		            // create user if not exist
		            var user = ToIdentityUser(result);
		            return user;
		        }
		
		        protected virtual SearchResult GetUserFromActiveDirectory(string domain, string username, string password)
		        {
		            var domainAndUsername = string.Format("{0}\\{1}", domain, username);
		            var ldapPath = "";
		            var entry = new DirectoryEntry(ldapPath, domainAndUsername, password);
		            try
		            {
		                // Bind to the native AdsObject to force authentication.
		                //var obj = entry.NativeObject;
		                var search = new DirectorySearcher(entry) { Filter = "(SAMAccountName=" + username + ")" };
		                search.PropertiesToLoad.Add("cn");
		                return search.FindOne();
		            }
		            catch (Exception ex)
		            {
		                //throw new AuthenticationException($"cannot authorize user {ex.Message}");
		                return null;
		            }
		        }
		
		        public ApplicationUser ToIdentityUser(SearchResult result)
		        {
		            var user = new ApplicationUser();
		            user.UserName = result.GetDirectoryEntry().Properties["SAMAccountName"].Value.ToString();
		            user.EmployeeNumber = result.GetDirectoryEntry().Properties["employeeID"].Value.ToString();
		            user.FirstName = result.GetDirectoryEntry().Properties["givenName"].Value.ToString();
		            user.LastName = result.GetDirectoryEntry().Properties["sn"].Value.ToString();
		            user.Email = result.GetDirectoryEntry().Properties["mail"].Value.ToString();
		            user.Id = result.GetDirectoryEntry().Properties["employeeID"].Value.ToString();
		            return user;
		        }
		    }
		}

1. Update Providers > ApplicationOAuthProvider.cs with ActiveDirectory data provider

		using Microsoft.AspNet.Identity.Owin;
		using Microsoft.Owin.Security;
		using Microsoft.Owin.Security.Cookies;
		using Microsoft.Owin.Security.OAuth;
		using System;
		using System.Collections.Generic;
		using System.Security.Claims;
		using System.Threading.Tasks;
		using Utility;
		using WebApi.Models;
		
		namespace WebApi.Providers
		{
		    public class ApplicationOAuthProvider : OAuthAuthorizationServerProvider
		    {
		        private readonly string _publicClientId;
		
		        public ApplicationOAuthProvider(string publicClientId)
		        {
		            if (publicClientId == null)
		            {
		                throw new ArgumentNullException("publicClientId");
		            }
		            _publicClientId = publicClientId;
		        }
		
		        public override async Task GrantResourceOwnerCredentials(OAuthGrantResourceOwnerCredentialsContext context)
		        {
		            var userManager = context.OwinContext.GetUserManager<ApplicationUserManager>();
		            var signInManager = context.OwinContext.Get<ApplicationSignInManager>();
		
		            //await LoginByUserName(context, userManager);
		
		            // login by active directory
		            var adProvider = new ActiveDirectoryDataProvider();
		            var user = adProvider.Login(Settings.DOMAIN_NAME, context.UserName, context.Password);
		            if (user == null)
		            {
		                context.SetError("invalid_grant", "The user name or password is incorrect.");
		                return;
		            }
		            // create user if not exist
		            if (await userManager.FindByIdAsync(user.Id) == null)
		            {
		                await userManager.CreateAsync(user, context.Password);
		            }
		            ClaimsIdentity oAuthIdentity = await user.GenerateUserIdentityAsync(userManager,
		                OAuthDefaults.AuthenticationType);
		            ClaimsIdentity cookiesIdentity = await user.GenerateUserIdentityAsync(userManager,
		                CookieAuthenticationDefaults.AuthenticationType);
		
		            AuthenticationProperties properties = CreateProperties(user.UserName);
		            AuthenticationTicket ticket = new AuthenticationTicket(oAuthIdentity, properties);
		            context.Validated(ticket);
		            context.Request.Context.Authentication.SignIn(cookiesIdentity);
		        }
		
		        private static async Task LoginByUserName(OAuthGrantResourceOwnerCredentialsContext context,
		            ApplicationUserManager userManager)
		        {
		            ApplicationUser user = await userManager.FindAsync(context.UserName, context.Password);
		
		            if (user == null)
		            {
		                context.SetError("invalid_grant", "The user name or password is incorrect.");
		                return;
		            }
		
		            ClaimsIdentity oAuthIdentity = await user.GenerateUserIdentityAsync(userManager,
		                OAuthDefaults.AuthenticationType);
		            ClaimsIdentity cookiesIdentity = await user.GenerateUserIdentityAsync(userManager,
		                CookieAuthenticationDefaults.AuthenticationType);
		
		            AuthenticationProperties properties = CreateProperties(user.UserName);
		            AuthenticationTicket ticket = new AuthenticationTicket(oAuthIdentity, properties);
		            context.Validated(ticket);
		            context.Request.Context.Authentication.SignIn(cookiesIdentity);
		        }
		
		        public override Task TokenEndpoint(OAuthTokenEndpointContext context)
		        {
		            foreach (KeyValuePair<string, string> property in context.Properties.Dictionary)
		            {
		                context.AdditionalResponseParameters.Add(property.Key, property.Value);
		            }
		            return Task.FromResult<object>(null);
		        }
		
		        public override Task ValidateClientAuthentication(OAuthValidateClientAuthenticationContext context)
		        {
		            // Resource owner password credentials does not provide a client ID.
		            if (context.ClientId == null)
		            {
		                context.Validated();
		            }
		            return Task.FromResult<object>(null);
		        }
		
		        public override Task ValidateClientRedirectUri(OAuthValidateClientRedirectUriContext context)
		        {
		            if (context.ClientId == _publicClientId)
		            {
		                Uri expectedRootUri = new Uri(context.Request.Uri, "/");
		
		                if (expectedRootUri.AbsoluteUri == context.RedirectUri)
		                {
		                    context.Validated();
		                }
		            }
		            return Task.FromResult<object>(null);
		        }
		
		        public static AuthenticationProperties CreateProperties(string userName)
		        {
		            IDictionary<string, string> data = new Dictionary<string, string>
		            {
		                { "userName", userName }
		            };
		            return new AuthenticationProperties(data);
		        }
		    }
		}

	Here, `ApplicationOAuthProvider` inherits from class `OAuthAuthorizationServerProvider`, we’ve overridden two methods `ValidateClientAuthentication` and `GrantResourceOwnerCredentials`.

	- `ValidateClientAuthentication` method is responsible for validating the `Client`, in our case we have only one client so we’ll always return that its validated successfully.
	- `GrantResourceOwnerCredentials` is responsible to validate the username and password sent to the authorization server’s token endpoint. We can use dbcontext to find user and check if the username and password are valid.

		 context.OwinContext.Response.Headers.Add("Access-Control-Allow-Origin", new[] { "*" });

            using (AuthRepository _repo = new AuthRepository())
            {
                IdentityUser user = await _repo.FindUser(context.UserName, context.Password);

                if (user == null)
                {
                    context.SetError("invalid_grant", "The user name or password is incorrect.");
                    return;
                }
            }

	- In GrantResourceOwnerCredentials, if the credentials are valid we’ll create `ClaimsIdentity` class and pass the authentication type to it, e.g. bearer token, then we’ll add claims (e.g. “sub”,”role”) and those will be included in the signed token. We can add different claims here but the token size will increase for sure.

			var identity = new ClaimsIdentity(context.Options.AuthenticationType);
            identity.AddClaim(new Claim("sub", context.UserName));
            identity.AddClaim(new Claim("role", "user"));
	
	- Finally, in GrantResourceOwnerCredentials, we can generate the token happens behind the scenes when we call “context.Validated(identity)”.

            context.Validated(identity);

# webapi + owin
web api can integrated with OWIN pipeline

1. Install library

	`Microsoft.AspNet.WebApi.Owin`

1. Add webapi component as a middleware

		[assembly: OwinStartupAttribute(typeof(WebApi.Startup))]
		namespace WebApi
		{
		    public partial class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            // enable cors
		            app.UseCors(Microsoft.Owin.Cors.CorsOptions.AllowAll);
		            // add authentication component
		            ConfigureAuth(app);
		
		            // add webapi
		            HttpConfiguration config = new HttpConfiguration();
		            config.MapHttpAttributeRoutes();
		            config.Routes.MapHttpRoute(
		                name: "DefaultApi",
		                routeTemplate: "api/{controller}/{id}",
		                defaults: new { id = RouteParameter.Optional }
		            );
		            // add webapi middleware
		            app.UseWebApi(config); 
		        }
		    }
		}

1. do not include global.asax or App_Start > WebApiConfig 

1. Add a webapi controller, test

# autofac + webapi + owin

1. Add libs

	- Autofac
	- Autofac.Owin
	- Autofac.WebApi2.Owin

1. Add autofac registration: App_Start > AutofacWebapiConfig.cs

	    public class AutofacWebapiConfig
	    {
	        public static ContainerBuilder RegisterServices(ContainerBuilder builder)
	        {
	
	            // register components
	            builder.RegisterType<ServiceBase>()
	                .As<IService>()
	                .AsSelf()
	                .InstancePerLifetimeScope();
	            builder.RegisterType<EmployeeService>()
	                .As<IEmployeeService>()
	                .AsSelf()
	                .InstancePerLifetimeScope();
	            builder.RegisterType<TaskService>()
	                .As<ITaskService>()
	                .AsSelf()
	                .InstancePerLifetimeScope();
	            return builder;
	        }
	    }

1. If autofac need to inject instances to controller, we have to add autofac to webapi

		[assembly: OwinStartupAttribute(typeof(WebApi.Startup))]
		namespace WebApi
		{
		    public partial class Startup
		    {
		        public void Configuration(IAppBuilder app)
		        {
		            // enable cors
		            app.UseCors(Microsoft.Owin.Cors.CorsOptions.AllowAll);
		            // add authentication component
		            ConfigureAuth(app);
		
		            // add webapi
		            HttpConfiguration config = new HttpConfiguration();
		            config.MapHttpAttributeRoutes();
		            config.Routes.MapHttpRoute(
		                name: "DefaultApi",
		                routeTemplate: "api/{controller}/{id}",
		                defaults: new { id = RouteParameter.Optional }
		            );
		
		            // add autofac
		            var builder = new ContainerBuilder();
		            // register Web API controller  
		            builder.RegisterApiControllers(Assembly.GetExecutingAssembly());
		            // setup dependency
		            var container = AutofacWebapiConfig.RegisterServices(builder).Build();
		            config.DependencyResolver = new AutofacWebApiDependencyResolver(container);
		            // add autofac middleware with webapi
		            app.UseAutofacMiddleware(container);
		            app.UseAutofacWebApi(config);
		            // add webapi middleware
		            app.UseWebApi(config);
		        }
		    }
		}

# Access token
## DPAPI ##
Access token can be generated and encrypted or signed via multiple algorithms. 

By default, if in IIS mode (SystemWeb), the encryption and signing is done via the "decryptionKey" and "validationKey" key values in machineKey node of app.config. e.g.

	<?xml version="1.0" encoding="utf-8" ?>
	<configuration>
	  <startup>
	    <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.6.1" />
	  </startup>
	  <system.web>
	    <compilation debug="true" targetFramework="4.6.1" />
	    <machineKey decryptionKey="B7EFF1C5839A624E3F97D0268917EDE82F408D2ECBFAC817" validation="SHA1" validationKey="C2B8DF31AB9624D69428066DFDA1A479542825F3B48865C4E47AF6A026F22D853DEC2B3248DF268599BF89EF78B9E86CA05AC73577E0D5A14C45E0267588850B" />
	  </system.web>
	</configuration>

Be default, if running as a self-host OWIN application, the encryption uses the `DPAPI` to protect it and that actually uses the 3DES algorithm. To decrypt it we need to invoke this code in our controller action method (not necessary but if we want to see what inside this encrypted token). Then, We can easily decrypt the bear token using: 

	Microsoft.Owin.Security.AuthenticationTicket ticket= Startup.OAuthBearerOptions.AccessTokenFormat.Unprotect(token);

e.g. 
![](/images/posts/20181120-jwt-2.png)
![](/images/posts/20181120-jwt-1.png)

# JWT #
We can configure authentication server to issue JWT signed tokens so we can decode them using public online tools such as [https://jwt.io/](https://jwt.io/) or decrypt by coding to get the information.

## decrypt JWT token ##
One approach to decrype JWT token in C#:

1. Install nuget library: `System.IdentityModel.Tokens`, `System.IdentityModel.Tokens.Jwt`, `Microsoft.IdentityModel.Tokens`

	![](/images/posts/20181120-jwt-3.png)

1. Parse 
	
        string accessToken =
            "eyJhbGciOiJSUzI1NiIsImtpZCI6IjUzNTk2M0M0RjE0N0VERjk4NUU0MjlDQTRFMjQ4NkEwQkY0NzdGQjUiLCJ0eXAiOiJKV1QiLCJ4NXQiOiJVMWxqeFBGSDdmbUY1Q25LVGlTR29MOUhmN1UifQ.eyJuYmYiOjE1NDI0MDM5ODksImV4cCI6MTU0MjQwNzU4OSwiaXNzIjoiaHR0cDovL3dmbWxlZ2FjeS5pbnQuYmVsbC5jYS9jZW50cmUiLCJhdWQiOlsiaHR0cDovL3dmbWxlZ2FjeS5pbnQuYmVsbC5jYS9jZW50cmUvcmVzb3VyY2VzIiwiVW5pZmllZEVtcGxveWVlSGllcmFyY2h5QXBpIl0sImNsaWVudF9pZCI6IjBENDk3ODVFRDRDQTRGREU5QjQ2NEY4Qzc0MjcwNUY0Iiwic3ViIjoiNjA5NDY2MSIsImF1dGhfdGltZSI6MTU0MjM4NjAwOSwiaWRwIjoibG9jYWwiLCJzY29wZSI6WyJvcGVuaWQiLCJwcm9maWxlIiwiZW1haWwiLCJyb2xlcyIsIkFTX1JvbGVzIiwiVW5pZmllZEVtcGxveWVlSGllcmFyY2h5QXBpIl0sImFtciI6WyJwd2QiXX0.p90Ei7YZhUSOs59v5TwqpnT_E3iq5jeqRjc0d_WLxpn3FaJaOgEysgB9hYqOoW300ECdcLQCbyDqtrkgzGxnLus1mBxplBngRrIfDTnZ82Iv4EZbePaEiyqm1baTkklTTddSYm8JPX_E3o2j55rtZteiTPKb7_SproAS5K9tBMrXxD76n4eiTEHOXYyiCMrKK3EwgaxBFr2hmELHt9WqFBq6VYKLMG4eJ1va-f6biK36Ur6Z9vfvToO2ZOb0Ncsxf0sc5m_CYo0eZEbpN5tmba_8TSnQakmCDXkCmsiwmc5ah_NSIijpUh2_tve6Q-ooezpbrTKCOMVsFZO9wcWzAA";
        var jwt = new JwtSecurityToken(accessToken);
        var sub = jwt.Claims.First(c => c.Type == "sub").Value;

or

		string issuer = "https://tweebaa.com";
		var audience = "tweebaa";
		var secretBase64 = "xxxxxxxx";
		var secretBase64Bytes = Encoding.UTF8.GetBytes(secretBase64);
		var signingKey = new Microsoft.IdentityModel.Tokens.SymmetricSecurityKey(secretBase64Bytes);
		var validationParams = new TokenValidationParameters
		{
			IssuerSigningKey = signingKey,
			ValidIssuer = issuer,
			ValidAudience = audience,
			//	ValidateLifetime = true,
			//	ValidateIssuerSigningKey = true,
			//	ValidateIssuer = false,
			//	ValidateAudience = false,
		};
		var handler = new JwtSecurityTokenHandler();
		// return all claims like issuer, audience, expiration, subject
		Microsoft.IdentityModel.Tokens.SecurityToken validatedToken;
		var principal = handler.ValidateToken(accessToken, validationParams, out validatedToken);
		validatedToken.Dump();
		principal.Dump();

## Add JWT support ##
There is no direct support for issuing JWT in ASP.NET Web API,  so in order to start issuing JWTs we need to implement this manually by implementing the interface “ISecureDataFormat” and implement the method “Protect”.

1. Install nuget library: 

	`Microsoft.IdentityModel.Tokens` - Includes types that provide support for SecurityTokens, Cryptographic operations: Signing, Verifying Signatures, Encryption.
	`Microsoft.Owin.Security.Jwt` - Middleware that enables an application to protect and validate JSON Web Tokens.
	`System.IdentityModel.Tokens.Jwt` - Includes types that provide support for creating, serializing and validating JSON Web Tokens. used forgenerating and validating jwt tokens

	We can use `System.IdentityModel.Tokens.Jwt`(or even another package) to generate the token. Typically, we use HMACSHA256 with SymmetricKey:

		private const string SecretBase64 = "db3OIsj+BXE9NZDy0t8W3TcNekrF+2d/1sFnWG4HnV8TZY30iTOdtVWJG8abWvB1GlOgJuQZdcF2Luqm/hccMw==";
		
		    public static string GenerateToken(string username, int expireMinutes = 20)
		    {
		        var symmetricKey = Convert.FromBase64String(SecretBase64);
		        var tokenHandler = new JwtSecurityTokenHandler();
		
		        var now = DateTime.UtcNow;
		        var tokenDescriptor = new SecurityTokenDescriptor
		        {
		            Subject = new ClaimsIdentity(new[]
		                    {
		                        new Claim(ClaimTypes.Name, username)
		                    }),	
		            Expires = now.AddMinutes(Convert.ToInt32(expireMinutes)),	
		            SigningCredentials = new SigningCredentials(new SymmetricSecurityKey(symmetricKey), SecurityAlgorithms.HmacSha256Signature)
		        };
		
		        var stoken = tokenHandler.CreateToken(tokenDescriptor);
		        var token = tokenHandler.WriteToken(stoken);
		
		        return token;
		    }

	or

		using System;
		using System.IdentityModel.Tokens;
		using System.IdentityModel.Tokens.Jwt;
		using System.Text;
		
		public void GenerateToken() {
		    const string sec = "401b09eab3c013d4ca54922bb802bec8fd5318192b0a75f201d8b3727429090fb337591abd3e44453b954555b7a0812e1081c39b740293f765eae731f5a65ed1";
		    var now = DateTime.UtcNow;
		    var securityKey = new Microsoft.IdentityModel.Tokens.SymmetricSecurityKey(Encoding.Default.GetBytes(sec));
		    var signingCredentials = new Microsoft.IdentityModel.Tokens.SigningCredentials(
		        securityKey,
		        SecurityAlgorithms.HmacSha256Signature);
		
		    var header = new JwtHeader(signingCredentials);
		
		    var payload = new JwtPayload
		    {
		            {"iss", "a5fgde64-e84d-485a-be51-56e293d09a69"},
		            {"scope", "https://example.com/ws"},
		            {"aud", "https://example.com/oauth2/v1"},
		            {"iat", now},
		        };
		
		    var secToken = new JwtSecurityToken(header, payload);
		
		    var handler = new JwtSecurityTokenHandler();
		    var tokenString = handler.WriteToken(secToken);
		    Console.WriteLine(tokenString);
		}

1. Create authorization/resource server

    public partial class Startup
    {
        public void ConfigureAuth(IAppBuilder app)
        {
			...
			// Configure the application for OAuth based flow
            var PublicClientId = "self";
            OAuthAuthorizationServerOptions oauthServerOptions = new OAuthAuthorizationServerOptions
            {
                AllowInsecureHttp = true,
                TokenEndpointPath = new PathString("/token"),
                AccessTokenExpireTimeSpan = TimeSpan.FromMinutes(60),
                // jwt
                Provider = new JwtApplicationOAuthProvider(PublicClientId),
                AccessTokenFormat = new JwtFormat("https://tweebaa.com"),
                RefreshTokenProvider = new RefreshTokenProvider()
            };

			// use jwt for authorization server, only used for authorization server
            app.UseOAuthAuthorizationServer(oauthServerOptions); 
			
			// user jwt for resource server, used for consume jwt token
			var issuer = "https://tweebaa.com";
            var audience = "tweebaa"; // audience id
            var secretBase64 = "secret-key-base64"; // audience secret
            var secretBase64Bytes = Encoding.UTF8.GetBytes(secretBase64);
            var signingKey = new Microsoft.IdentityModel.Tokens.SymmetricSecurityKey(secretBase64Bytes);
			// Api controllers with an [Authorize] attribute will be validated with JWT
            app.UseJwtBearerAuthentication(new JwtBearerAuthenticationOptions
            {
                AuthenticationMode = AuthenticationMode.Active,
                AllowedAudiences = new[] { audience },
                //IssuerSecurityKeyProviders = new IIssuerSecurityKeyProvider[]
                //{
                //    new SymmetricKeyIssuerSecurityKeyProvider(issuer, secretBase64Bytes),
                //},
                TokenValidationParameters = new TokenValidationParameters
                {
                    IssuerSigningKey = signingKey,
                    //IssuerSigningToken =  new sign
                    ValidIssuer = issuer,
                    ValidAudience = audience,
                    //ValidateLifetime = true,
                    //ValidateIssuerSigningKey = true,
                    //ClockSkew = TimeSpan.FromMinutes(0)
                }
            });
        }
	}

1. Create `JwtApplicationOAuthProvider` to verify user

	    public class JwtApplicationOAuthProvider : ApplicationOAuthProvider
	    {
	        public JwtApplicationOAuthProvider(string publicClientId): base(publicClientId)
	        {
	        }
	        // validate audience by client_id
	        public async override Task ValidateClientAuthentication(OAuthValidateClientAuthenticationContext context)
	        {
	            string clientId = string.Empty;
	            string clientSecret = string.Empty;
	            string symmetricKeyAsBase64 = string.Empty;
	
	            if (!context.TryGetBasicCredentials(out clientId, out clientSecret))
	            {
	                context.TryGetFormCredentials(out clientId, out clientSecret);
	            }
	
	            if (context.ClientId == null)
	            {
	                context.SetError("invalid_clientId", "client_Id is not set");
	            }
	            // validate audience
	            //var audience = AudiencesStore.FindAudience(context.ClientId);
	
	            //if (audience == null)
	            //{
	            //    context.SetError("invalid_clientId", string.Format("Invalid client_id '{0}'", context.ClientId));
	            //    return Task.FromResult<object>(null);
	            //}
	
	            context.Validated();
	        }
	        // validate resource owner's credentials, create claims
	        public async override Task GrantResourceOwnerCredentials(OAuthGrantResourceOwnerCredentialsContext context)
	        {
	            context.OwinContext.Response.Headers.Add("Access-Control-Allow-Origin", new[] { "*" });
	            //Dummy check here, you need to do your DB checks against memebrship system
	            if (context.UserName != context.Password)
	            {
	                context.SetError("invalid_grant", "The user name or password is incorrect");
	                return;
	            }
	
	            var identity = new ClaimsIdentity("JWT");
	            //identity.AddClaim(new Claim(ClaimTypes.Name, context.UserName));
	            //identity.AddClaim(new Claim("sub", context.UserName));
	            //identity.AddClaim(new Claim(ClaimTypes.Role, "Manager"));
	            //identity.AddClaim(new Claim(ClaimTypes.Role, "Supervisor"));
	
	            var props = new AuthenticationProperties(new Dictionary<string, string>
	            {
	                {
	                    "audience", (context.ClientId == null) ? string.Empty : context.ClientId
	                }
	            });
	
	            var ticket = new AuthenticationTicket(identity, props);
	            context.Validated(ticket);
	        }
	    }

1. create a JwtFormat class to format jwt token

    public class JwtFormat : ISecureDataFormat<AuthenticationTicket>
    {
        private const string AudiencePropertyKey = "tweebaa";
        //private static readonly byte[] _secret = TextEncodings.Base64Url.Decode("IxrAjDoa2FqElO7IhrSrUJELhUckePEPVpaePlS_Xaw");
        //private static readonly byte[] _secret = TextEncodings.Base64Url.Decode("secret-to-string");
        //private static readonly byte[] _secret = Encoding.UTF8.GetBytes("#asf09%dfG9ns");
        private static readonly byte[] _secret = Encoding.UTF8.GetBytes("I2FzZjA5JWRmRzlucw==");
        private readonly string _issuer;

        public JwtFormat(string issuer)
        {
            _issuer = issuer;
        }
        // generate jwt
        public string Protect(AuthenticationTicket data)
        {
            if (data == null)
            {
                throw new ArgumentNullException(nameof(data));
            }

            // read the audience (client id)
            //string audienceId = data.Properties.Dictionary.ContainsKey(AudiencePropertyKey) ? data.Properties.Dictionary[AudiencePropertyKey] : null;
            //if (string.IsNullOrWhiteSpace(audienceId)) throw new InvalidOperationException("AuthenticationTicket.Properties does not include audience");
            //Audience audience = AudiencesStore.FindAudience(audienceId);

            // create signing key
            string symmetricKeyAsBase64 = Convert.ToBase64String(_secret);
            var keyByteArray = TextEncodings.Base64Url.Decode(symmetricKeyAsBase64);
            //var keyByteArray = _secret;
            //var signingKey = new HmacSigningCredentials(keyByteArray);

            var signingKey = new Microsoft.IdentityModel.Tokens.SymmetricSecurityKey(_secret);//symmetric key
            var signingCredentials = new Microsoft.IdentityModel.Tokens.SigningCredentials(
                signingKey, Microsoft.IdentityModel.Tokens.SecurityAlgorithms.HmacSha256);
            //var signingCredentials = new Microsoft.IdentityModel.Tokens.SigningCredentials(
            //    signingKey, 
            //    System.IdentityModel.Tokens.SecurityAlgorithms.HmacSha256Signature,
            //    System.IdentityModel.Tokens.SecurityAlgorithms.Sha256Digest);

            var issued = data.Properties.IssuedUtc;
            var expires = data.Properties.ExpiresUtc;

            // way1, create jwt via issuer, audience, user claims, issue date, expiry date, signing key
            var token = new JwtSecurityToken(issuer: _issuer, audience: "tweebaa", claims: data.Identity.Claims, notBefore: null, expires: expires.Value.UtcDateTime, signingCredentials: signingCredentials);

            // way2, create jwt via header, payload
            //var header = new JwtHeader(signingCredentials: signingCredentials);
            //var payload = new JwtPayload(issuer: _issuer, audience: "tweebaa", claims: data.Identity.Claims, notBefore: null, expires: expires.Value.UtcDateTime);
            //var token = new JwtSecurityToken(header, payload);

            var tokenHandler = new JwtSecurityTokenHandler();
            //var token = tokenHandler.CreateToken(securityTokenDescriptor);
            var jwt = tokenHandler.WriteToken(token);

            return jwt;
        }

        public AuthenticationTicket Unprotect(string protectedText)
        {
            return null;
        }
    }

1. create a refresh token provider

    public class RefreshTokenProvider : IAuthenticationTokenProvider
    {
        private static ICollection<RefreshToken> refreshTokens = new List<RefreshToken>();
        public void Create(AuthenticationTokenCreateContext context)
        {
            throw new NotImplementedException();
        }
        // create refresh token
        public async Task CreateAsync(AuthenticationTokenCreateContext context)
        {
            var refreshTokenId = Guid.NewGuid().ToString("n");

            var refreshTokenLifeTime = context.OwinContext.Get<string>("as:clientRefreshTokenLifeTime");

            var token = new RefreshToken()
            {
                Id = Helper.GetHash(refreshTokenId),
                Subject = context.Ticket.Identity.Name,
                IssuedUtc = DateTime.UtcNow,
                ExpiresUtc = DateTime.UtcNow.AddYears(1)
            };

            context.Ticket.Properties.IssuedUtc = token.IssuedUtc;
            context.Ticket.Properties.ExpiresUtc = token.ExpiresUtc;

            token.ProtectedTicket = context.SerializeTicket();

            context.SetToken(refreshTokenId);

            // save refresh tokens
            refreshTokens.Add(token);
        }
        public void Receive(AuthenticationTokenReceiveContext context)
        {
            throw new NotImplementedException();
        }
        // generate access token using the refresh token
        // 1. receive the refresh token
        public async Task ReceiveAsync(AuthenticationTokenReceiveContext context)
        {
            //context.OwinContext.Response.Headers.Add("Access-Control-Allow-Origin", new[] { "" });
            string hashedTokenId = Helper.GetHash(context.Token);
            // query saved refresh tokens and verify
            var refreshToken = refreshTokens.FirstOrDefault(t => t.Id == hashedTokenId);
            if (refreshToken != null)
            {
                //Get protectedTicket from refreshToken class
                context.DeserializeTicket(refreshToken.ProtectedTicket);
            }
        }
    }

    public class RefreshToken
    {
        public string Id { get; set; }
        public string Subject { get; set; }
        public DateTime IssuedUtc { get; set; }
        public DateTime ExpiresUtc { get; set; }
        public string ProtectedTicket { get; set; }
    }

    public class Helper
    {
        public static string GetHash(string input)
        {
            HashAlgorithm hashAlgorithm = new SHA256CryptoServiceProvider();

            byte[] byteValue = System.Text.Encoding.UTF8.GetBytes(input);

            byte[] byteHash = hashAlgorithm.ComputeHash(byteValue);

            return Convert.ToBase64String(byteHash);
        }
    }

# References
[http://autofac.readthedocs.io/en/latest/integration/aspnet.html](http://autofac.readthedocs.io/en/latest/integration/aspnet.html)

http://autofac.readthedocs.io/en/latest/integration/webapi.html](http://autofac.readthedocs.io/en/latest/integration/webapi.html)

[http://autofac.readthedocs.io/en/latest/integration/owin.html](http://autofac.readthedocs.io/en/latest/integration/owin.html)

[http://bitoftech.net/2014/10/27/json-web-token-asp-net-web-api-2-jwt-owin-authorization-server/](http://bitoftech.net/2014/10/27/json-web-token-asp-net-web-api-2-jwt-owin-authorization-server/)

[http://bitoftech.net/2014/06/01/token-based-authentication-asp-net-web-api-2-owin-asp-net-identity/](http://bitoftech.net/2014/06/01/token-based-authentication-asp-net-web-api-2-owin-asp-net-identity/)

[Enable OAuth Refresh Tokens in AngularJS App using ASP .NET Web API 2, and Owin](http://bitoftech.net/2014/07/16/enable-oauth-refresh-tokens-angularjs-app-using-asp-net-web-api-2-owin/)

[https://blogs.ibs.com/2017/11/22/token-based-authentication-in-asp-net-using-jwts-part-1/](https://blogs.ibs.com/2017/11/22/token-based-authentication-in-asp-net-using-jwts-part-1/)