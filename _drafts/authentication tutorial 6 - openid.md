---
layout: post
title: ASP.NET Identity authentication 8 - OpenID Connect
author: Andy Feng
---
# Outline
- OAuth
- OAuth development
- OpenID Connect
- OpenID Connect development


# OpenID Connect
`Open ID Connect` adds an additional layer on top of the OAuth 2.0 protocol. It uses the same underlying REST protocol, but adds consistency and additional security on top of the OAuth protocol.

> Please note that OpenID Connect is a very different protocol to OpenID. The later was an XML based protocol, which follows similar approaches and goals to OpenID Connect but in a less developer-friendly way.

OpenID Connect builds on top of OAuth 2.0. The workflow is the same as the OAuth 2.0 flow

![](/images/posts/20181024-identity-14.png)

# OpenID Connect development in ASP.NET core approach 1
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

# ASP.NET Core approach 2
1. install `IdentityServer4.AccessTokenValidation`

	![](/images/posts/20181031-openid-1.png)

# OpenID Connect development in Angular
1. Create a Angular project via angular-cli
1. install oidc lib: `npm install oidc-client --save`
1. 



# OpenID vs. OAuth
OAuth 2.0 is fundamentally an authorization protocol, not an authentication protocol. It's entire design is based around providing access to some protected resource (e.g. Facebook Profile, or Photos) to a third party (e.g. our application). OpenID is kind of authentication protocol.

OAuth 2.0 is very loose in it's requirements for implementation. There are many subtly different implementations across various providers. Each of those providers requires some degree of customisation aside from specifying urls and secrets. Each one returns data in a different format and must have the returned Claims parsed. OpenID Connect is far more rigid in its requirements, which allows a great deal of interoperability.

OpenID Connect is kind of a “super-set” of OAuth 2.0 and always recommended against using OAuth.

# References
[https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/?view=aspnetcore-2.0](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/?view=aspnetcore-2.0)

[https://docs.microsoft.com/en-us/aspnet/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on](https://docs.microsoft.com/en-us/aspnet/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)

[https://github.com/IdentityModel/oidc-client-js](https://github.com/IdentityModel/oidc-client-js)

[https://openid.net/specs/openid-connect-core-1_0.html](https://openid.net/specs/openid-connect-core-1_0.html)