---
layout: post
title: ASP.NET Identity authentication 8 - OAuth
author: Andy Feng
---

# Introduction
Traditionally, applications create a local database for the users' accounts and credentials. Then, implement local authentication internally. However, local authentication can be bad for business:

- sign up and create account for each application is tedious.
- For enterprises with many apps, maintenance of separate user databases can easily become an administrative and security nightmare. 

A better solution to these problems is to delegate user authentication and provisioning to a dedicated, purpose-built service, called an Identity Provider (IdP).

OAuth and OpenID Connect are the modern technologies for authenticating users. These techniques allow users to participate single-sign-on experience across one or more applications in the federated ecosystems. There are quite a few libs in the market. Also, Google, Facebook and Twitter, where many people on the internet are registered, offer such IdP services for their users. A consumer website can greatly streamline user onboarding by integrating login with these IdPs. Moreover, inside a enterprise, this would ideally be one internal IdP service, for employees and contractors to log into the internal applications. Centralisation has considerable benefits, such as easier administration and potentially faster development cycles for new apps. 

# Outline
- OAuth1
- OAuth2
- OAuth2 development
- OpenID Connect
- OpenID Connect development

# OAuth 1.0 #
OAuth (Open Authorization) is an open standard for token-based authentication and authorization on the Internet. It has been globally used in Internet applications. OAuth allows an end user's account information to be used by third-party services, such as we can got user information from Facebook or Google without exposing the user's password. OAuth acts as an intermediary on behalf of the end user, providing the service with an access token that authorizes specific account information to be shared. The process for obtaining the token is called a flow.

OAuth 1.0 was first released in 2007, was conceived as an authentication method for the Twitter application program interface (API). OAuth 2.0 was published in 2010. Like the original OAuth, OAuth 2.0 provides users with the ability to grant third-party access to web resources without sharing a password. Updated features available in OAuth 2.0 include new flows, simplified signatures and short-lived tokens with long-lived authorizations.

## Workflow of OAuth 1.0 (3-legged):

![](/images/posts/20180721-twitter-8.png)

# OAuth 2.0
## Terms
There are 4 major players in OAuth2:

![](/images/posts/20181223-owin-1.png)

- client: It is the entity that accesses protected resources, i.e. access the backend data via token
- resource owner(user): it is the owner(usually human being) that owns the protected resource (backend data)
- authorization server: it is the server be responsible for authorization and generates access token
- resource server: it is the server that stores protected resources that the OAuth client accesses, i.e. it is the backend that feeds data

- Access token: it is an identifier for client to access the protected resources of the resource owner. It is issued by authorization server with resource owner's consent. The OAuth client can use the access token before it expire - the client requests data from the resource server with access token. The resource server honors the access token until expiry.
- Refresh token: it is an identifier to obtain access tokens. The authorization server generates the refresh token together with the access token as configured. The OAuth client can use the refresh token to request a new access token from the authorization server when the current access token expires or to request an access token with identical or narrower scope. Unlike access tokens, refresh tokens are not sent to the resource server.

## Workflows of OAuth 2.0:
Here is a fundamental workflow of OAuth 2.0. By default, it is 3-legged but can have simplified 2-legged version. 

![](/images/posts/20181024-identity-11.png)

1. User use 3rd party application(client) and that application wanna sensitive information hosted in another server, which  consists of an authentication server and a resource server.
2. The 3rd party application requests sensitive information from the resource server. The request will be redirected to authentication server. 
3. The application tries to get a token from the authentication server via entering username/password. Authentication server verifies user credentials; If pass, it asks user(owner) to grant access permission regarding the 3rd application.
4. user(owner) approves the access request of 3rd application. Please note that the grant is only for selected part of user's  account information, not all sensitive information.
5. After user granting, authentication server returns access token to 3rd application and redirects back to the 3rd application.
6. For any later on request, 3rd application has to carry over the access token and get information from the resource server.
7. Each token was bound to certain user and cannot used by other user. Each token was restricted to certain permission(partial information) and cannot beyond the permission. Each token has expiration time. These are declared in the OAuth specification.
8. If a token expired, 3rd application can renew a new token automatically or apply a new one. step 3-5 has to be go through if apply a new token.

### Major workflows
The fundamental workflow derives 4 major workflows(grant types) :

- Authorization code flow
- Implicit flow
- Resource owner credential flow
- Client credential flow

### Authorization code flow
`grant_type=authorization_code`

It is suitable for classic desktop web applications, need the application is in a safe environment and a little complex(3-legged).

![](/images/posts/20190630-oauth-1.png)

The client need to maintain client secret, access token and refresh token.

Please note that traditionally, the client must have both a front-end and a back-end, the reason is that the client secret must be stored in a safe place, i.e. back-end. This means a pure Front-end application (Javascript, React, Angular) cannot implement this flow but can use the Implicit grant one. However, some new specification ignore client secret and it enables the front-end can use this flow. Because it has an authorization code step, this flow is safer than implicit flow.

### Implicit flow
`grant_type=implicit`

It is suitable for mobile app or browser-based apps. The OAuth client is usually a browser and don't have to maintain secret. The client must also be able to receive incoming requests through redirection from the authorization server.

![](/images/posts/20190830-oauth-1.gif)

This flow is commonly used in browser-based apps or mobile apps.

### Resource owner credential flow
`grant_type=password`

It is designed for legacy applications. It is suitable in cases where the resource owner has a trust relationship with the client as the user credentials will go through the client when reach the authorization server. In this flow, user enters their username and password in the client application. This fact implies that users must fully trust the client application, to connect the Authorization Server by storing and passing their credentials. 

![](/images/posts/20190830-oauth-2.gif)

You can only use this grant type when the OAuth client has obtained the credentials of the resource owner(user). It is also used to migrate existing clients using direct authentication schemes by exchanging the stored credentials for an access token. 

In summary, the username/password will be used to exchange for access token via this flow. It has limited usage and not recommended.

### Client credential flow
`grant_type=client_credentials`

It is suitable for classic desktop web applications or backend-only applications. It is used when the OAuth client requests an access token using only its client credentials. 

The client need to maintain client secret, access token. It is 2-legged and simplier than Authorization code flow. 

Different from resource owner credential flow, Client credential flow only authenticates the client, not the user. Only client and no user interaction are involved in this workflow.

![](/images/posts/20190830-oauth-3.gif)

To summarize, the most common workflows are `implicit flow (for front-end)`, `authorization code flow without client secret`, and `client credential flow(for back-end)`. Authorization code flow is a little complex and resource owner credential flow is a little unsafe.

These workflows can be categorized into two types: 

- 3-legged with user interaction
	- Authorization code flow
	- Implicit flow
	- Resource owner credential flow
- 2-legged without user interaction 
	- Client credential flow

### 3-legged with user involvement
3-legged with user involvement, client app + user + authorization/resource server. Here is the typical procedure:

![](/images/posts/20190830-oauth-4.gif)

For instance, here is the 3-legged OAuth workflow example of Google apps: In this diagram, Google plays as the authorization server and web application represents the client.

![](/images/posts/20181024-identity-12.png)

### 2-legged without user involvement
There is another simplified version of OAuth 2.0: two-legged OAuth without user involvement (client app + authorization/resource server):

![](/images/posts/20181024-identity-12.gif)

For instance, here is the 2-legged OAuth workflow example of Google apps:

![](/images/posts/20181024-identity-13.png)

1. An OAuth client initiates a request with an authorization server and receives an access token.
1. The OAuth client uses the access token to access protected resources on the resource server.

OAuth is very useful for many senarios. For instance, Youku application tries to access user's QQ account. Groupon tries to access PayPal, Facebook tries to access Google and so on. 

# OAuth 2.0 development
To implement OAuth 2 you have to develop:

- The client: if you want to use an Authorization Server services
- The authorization server: if you want to expose authentication mechanisms
- The resource server: the server to provide services and accept access token

## Client development - add OAuth 2.0 support to our application access google account #

1. create a mvc project, use individual authentication (form authentication)

1. apply google oauth key

	- login google developer console
	
	- select or create a new project
	![](/images/20160602-google-oauth-api-0.9.png)

	- select credentials > create a new oAuth client id
	![](/images/20160602-google-oauth-api-1.png)

	- fill authorized url, 
		- e.g. 
		- authorized url: http://localhost:8382
		- authorized redirect url: http://localhost:60184/signin-google (must be hostname/signin-google)
	![](/images/20160602-google-oauth-api-2.png)
	
	- get client id and client secret pair

	- select overview > social api > google+ api > click enable

	![](/images/20160602-google-oauth-api-3.1.png)

	![](/images/20160602-google-oauth-api-3.2.png)
	
1. update startup.auth.cs, uncomment google api code with 

	![](/images/20160602-google-oauth-api-3.png)

1. start mvc project, select signin > use another service to login

	![](/images/20160602-google-oauth-api-4.png) 

	enter google credentials, authorize its permission

**detailed steps in at**: [http://www.asp.net/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on](http://www.asp.net/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)

# OpenID Connect
`Open ID Connect` adds an additional layer on top of the OAuth 2.0 protocol. It uses the same underlying REST protocol, but adds consistency and additional security on top of the OAuth protocol.

> Please note that OpenID Connect is a very different protocol to OpenID. The later was an XML based protocol, which follows similar approaches and goals to OpenID Connect but in a less developer-friendly way.

OpenID Connect builds on top of OAuth 2.0. The workflow is the same as the OAuth 2.0 flow

![](/images/posts/20181024-identity-14.png)

# OpenID Connect client development in ASP.NET core
1. Create a ASP.NET core project
1. nuget > install `Microsoft.AspNetCore.Authentication.OpenIdConnect` lib

	![](/images/posts/20181024-identity-15.png)

1. Modify `Startup.cs` to configure  middleware

	    public class Startup
	    {
	        ...
	        // This method gets called by the runtime. Use this method to add services to the container.
	        public void ConfigureServices(IServiceCollection services)
	        {
	            ...
	            services.AddAuthentication(options =>
	                {
	                    options.DefaultScheme = CookieAuthenticationDefaults.AuthenticationScheme;
	                    options.DefaultChallengeScheme = OpenIdConnectDefaults.AuthenticationScheme;
	                    options.DefaultAuthenticateScheme = OpenIdConnectDefaults.AuthenticationScheme;
	                })
	                .AddOpenIdConnect(o =>
	                {
	                    o.ClientId = _clientId;
	                    o.ClientSecret = _secret;
	                    o.Authority = _authority;
	                    o.ResponseType = "code id_token";
	                    o.RequireHttpsMetadata = false;
	                    o.Scope.Add("openid profile roles email AS_Roles OI_Roles");
	                    o.SaveTokens = true;
	                    o.GetClaimsFromUserInfoEndpoint = true;
	                    o.CallbackPath = _callBackPath;
	                    o.Events.OnRedirectToIdentityProvider = redirectContext => RedirectToIdentityProvider(redirectContext);
	                    o.Events.OnUserInformationReceived = userInformationReceivedContext => OnUserInformationReceived(userInformationReceivedContext);
	
	                })
	                .AddCookie();
	            services.AddSession(options => {
	                options.IdleTimeout = TimeSpan.FromDays(1);
	            });
	        }
	
	        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
	        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
	        {
	            ...
	            app.UseSession();
	            app.UseAuthentication();
	            app.UseMvc();
	        }
	
	        private async Task RedirectToIdentityProvider(RedirectContext redirectContext)
	        {
	            redirectContext.ProtocolMessage.RedirectUri = _returnUri;
	            await Task.FromResult(0);
	        }
	
	        private async Task OnUserInformationReceived(UserInformationReceivedContext userInformationReceivedContext)
	        {
	            userInformationReceivedContext.HttpContext.Session.Set("EmployeeNo", Encoding.ASCII.GetBytes(userInformationReceivedContext.User["sub"].ToString()));
	            userInformationReceivedContext.HttpContext.Session.Set("EmployeeName", Encoding.ASCII.GetBytes(userInformationReceivedContext.User["name"].ToString()));
	            userInformationReceivedContext.HttpContext.Session.Set("EmployeeEmail", Encoding.ASCII.GetBytes(userInformationReceivedContext.User["email"].ToString()));
	            await Task.FromResult(0);
	        }	
	    }

# OpenID Connect development in Angular
see next posting for more details

# OpenID vs. OAuth
OAuth 2.0 is fundamentally an authorization protocol, not an authentication protocol. It's entire design is based around providing access to some protected resource (e.g. Facebook Profile, or Photos) to a third party (e.g. our application). OpenID is kind of authentication protocol.

OAuth 2.0 is very loose in it's requirements for implementation. There are many subtly different implementations across various providers. Each of those providers requires some degree of customisation aside from specifying urls and secrets. Each one returns data in a different format and must have the returned Claims parsed. OpenID Connect is far more rigid in its requirements, which allows a great deal of interoperability.

OpenID Connect is kind of a “super-set” of OAuth 2.0 and always recommended against using OAuth. 

# References
[https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/?view=aspnetcore-2.0](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/?view=aspnetcore-2.0)

[https://docs.microsoft.com/en-us/aspnet/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on](https://docs.microsoft.com/en-us/aspnet/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)

[https://oauth.net/2/](https://oauth.net/2/)

[https://www.ibm.com/support/knowledgecenter/en/SS4J57_6.2.2.6/com.ibm.tivoli.fim.doc_6226/config/concept/OAuth20Workflow.html](https://www.ibm.com/support/knowledgecenter/en/SS4J57_6.2.2.6/com.ibm.tivoli.fim.doc_6226/config/concept/OAuth20Workflow.html)

[OAuth flows](https://www.ibm.com/support/knowledgecenter/en/SS9H2Y_7.5.0/com.ibm.dp.doc/oauth_flows.html)

[OAuth2 Authorization flows explained with examples](https://www.codeproject.com/Articles/1171546/OAuth-Authorization-flows-explained-with-examples)
