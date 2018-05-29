---
layout: post
title: ASP.NET Identity authentication 4 - add token-based authentication
author: Andy Feng
---

## Add/update data objects ##

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

1. Create Providers > ApplicationOAuthProvider.cs

	...


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
		            //// Expiration time in seconds
		            //int expire = Settings.REFRESH_TOKEN_EXPIRATION_MINUTES * 60;
		            //context.Ticket.Properties.IssuedUtc = DateTime.Now;
		            //context.Ticket.Properties.ExpiresUtc = new DateTimeOffset(DateTime.Now.AddSeconds(expire));
		            //context.SetToken(context.SerializeTicket());
		            //base.Create(context);
		        }
		        private static ConcurrentDictionary<string, AuthenticationTicket> _refreshTokens = new ConcurrentDictionary<string, AuthenticationTicket>();
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
		            //context.DeserializeTicket(context.Token);
		            //base.Receive(context);
		        }
		
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

# webapi + owin
web api can integrated with OWIN pipeline

1. Install Microsoft.AspNet.WebApi.Owin library

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

# References
[http://autofac.readthedocs.io/en/latest/integration/aspnet.html](http://autofac.readthedocs.io/en/latest/integration/aspnet.html)

http://autofac.readthedocs.io/en/latest/integration/webapi.html](http://autofac.readthedocs.io/en/latest/integration/webapi.html)

[http://autofac.readthedocs.io/en/latest/integration/owin.html](http://autofac.readthedocs.io/en/latest/integration/owin.html)