---
layout: post
title: ASP.NET Identity authentication 3 - add identity framework to web api
author: Andy Feng
---

# Introduction #
There are basically two different ways of implementing server side authentication for apps with a frontend and an API:

The most common one, is Cookie-Based Authentication that uses server side cookies to authenticate the user on every request.

A newer approach, Token-Based Authentication, relies on a signed token that is sent to the server on each request.

# Outline #
- Create web api project
- Add identity support
- Add a basic add user feature
- Add token-based authentication

# Create web api project #
## create new empty web application ##

![](/images/20160604-create-project-1.png)

![](/images/20160604-create-mvc-project-2.png)

![](/images/20160604-create-mvc-project-3.png)

## Add webapi support ##
Open nuget, install webapi.core, webapi, webapi cors library

![](/images/20160612-create-webapi-project-1.png)

Create App_Start folder, create WebApiConfig.cs file under this folder. Also, create global.asax file

![](/images/20160612-create-webapi-project-2.png)

![](/images/20160612-create-webapi-project-3.png)

## quick test ##

Add Controllers folder, create a new Controller class, HomeController.cs

![](/images/20160612-create-webapi-project-4.png)

Start project, succeed

![](/images/20160612-create-webapi-project-5.png)

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

## Customize data models ##

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

# Add token-based authentication #

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

1. Create Providers > ApplicationOAuthProvider.cs

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

1. Update root > Startup.cs 

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

# add https support #

[https://github.com/MikeWasson/LocalAccountsApp](https://github.com/MikeWasson/LocalAccountsApp)

# Reference #
[https://github.com/auth0/blog/blob/master/_posts/2014-01-07-angularjs-authentication-with-cookies-vs-token.markdown](https://github.com/auth0/blog/blob/master/_posts/2014-01-07-angularjs-authentication-with-cookies-vs-token.markdown)