---
layout: post
title: ASP.NET Identity authentication 8 - OpenID Connect
author: Andy Feng
---

# OpenID Connect
`Open ID Connect` adds an additional layer on top of the OAuth 2.0 protocol. It uses the same underlying REST protocol, but adds consistency and additional security on top of the OAuth protocol.
o
> Please note that OpenID Connect is a very different protocol to OpenID. The later was an XML based protocol, which follows similar approaches and goals to OpenID Connect but in a less developer-friendly way.

OpenID Connect builds on top of OAuth 2.0. The workflow is very similar to the OAuth 2.0 2-legs flow:

![](/images/posts/20181024-identity-14.png)

## Benefits of OpenID Connect ##

- Decoupling:
- Single sign-on: multiple client applications can share the same token
- Centralized security management:
- 
## Terminologies ##
> **Authorization server (identity server)**: there are a few choices to select an identity server
>  
> - Social media identity servers: Google, Facebook, Twitter and so on.
> - commercial cloud identity servers: Azure Active Directory(AAD), Auth0, okta, Ping identity 
> -  opensource identity server frameworks: IdentityServer4
> 
> **Resource server**: the server which provides data/logic services
> 
> **Client**: the actor who consumes services
> 
> **Scope**: `scope` is a part of configuration of identity server(authorization server) and represents the individual resources that identity provider protects. When a client connects to the identity provider to authenticate, it tells the identity provider what scope it is requesting. Then, the identity provider checks the configuration for that client to see if it's intended to access the associated resource. And an end user will experience scopes when using external identity providers like Google or Faceboook in the form of consent screens that confirm what the user that they want to allow the application access to the resources requested. 
> 
> **JWT**: `JSON Web Tokens(JWT)` are the format used to encode information about the authenticated user. It includes identity token(ID token), access token and claims what they access in the form of scopes in access tokens.
> 
> **Identity Token**: `ID token` represents the identity of the end user or client application
> 
> **Access Token**: access token is based on OAuth2 protocol and it specifies what the holder of that token can do with respect to the resources what it is requesting access to (the scope).
> 
> A sample of JWT result

	{
	  "id_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjUzNTk2M0M0RjE0N0VERjk4NUU0MjlDQTRFMjQ4NkEwQkY0NzdGQjUiLCJ0eXAiOiJKV1QiLCJ4NXQiOiJVMWxqeFBGSDdmbUY1Q25LVGlTR29MOUhmN1UifQ.eyJuYmYiOjE1NDIzMTE5ODAsImV4cCI6MTU0MjMxMjI4MCwiaXNzIjoiaHR0cDovL3dmbWxlZ2FjeS5pbnQuYmVsbC5jYS9jZW50cmUiLCJhdWQiOiIwRDQ5Nzg1RUQ0Q0E0RkRFOUI0NjRGOEM3NDI3MDVGNCIsIm5vbmNlIjoiYWY0YjBhYjRlYTgyNDZhM2E2YzY3ODA4OWVkZmRhMDgiLCJpYXQiOjE1NDIzMTE5ODAsImF0X2hhc2giOiJXa3hKVGd4WXRJb2V6WnZ0QkE4M1VBIiwic2lkIjoiZjRhZGU3ZmU3MWQyNTc4OWU0MDlhMzBlMTg3ZTJjNTIiLCJzdWIiOiI2MDk0NjYxIiwiYXV0aF90aW1lIjoxNTQyMzExOTgwLCJpZHAiOiJsb2NhbCIsImFtciI6WyJwd2QiXX0.estpF-qSUvXzsLq0AK7KFSUkVylUktAk9qidHs9cLPh1fx_pSEDW6vaFUsWMPbSIaGdz6b-rsKKRHTtS1Xro3rYu4QogE4dnzZbJcem21eQ6q1TOYG67Ucllc56HuA-PxAjwPRXFwFp4p1UfGcES4Ff5xSNl0MunGRAq7aW7Lwb0gbZAFod4bXbZ4fZHaMQWcXsFujqtG8HgSplV4bevCBCsHrlMtO4OaMUNxoijB40lwQAWaDNn3tUIT9sX7n-mIG1wkMG06tUzy7fO54yJqvArRy5yKqIbDFAX0CvM9SqqzWsRKUiHfouRSjCvQiOd3u-HpxAmvj4RnKzKBQcMiA",
	  "session_state": "lMQ7teKDw3CseeAU7bVSebS730zIGHuxliXEoIEwINc.1897255fefc9dc9ba4eab08276ba4dda",
	  "access_token": "eyJhbGciOiJSUzI1NiIsImtpZCI6IjUzNTk2M0M0RjE0N0VERjk4NUU0MjlDQTRFMjQ4NkEwQkY0NzdGQjUiLCJ0eXAiOiJKV1QiLCJ4NXQiOiJVMWxqeFBGSDdmbUY1Q25LVGlTR29MOUhmN1UifQ.eyJuYmYiOjE1NDIzMTE5ODAsImV4cCI6MTU0MjMxNTU4MCwiaXNzIjoiaHR0cDovL3dmbWxlZ2FjeS5pbnQuYmVsbC5jYS9jZW50cmUiLCJhdWQiOlsiaHR0cDovL3dmbWxlZ2FjeS5pbnQuYmVsbC5jYS9jZW50cmUvcmVzb3VyY2VzIiwiVW5pZmllZEVtcGxveWVlSGllcmFyY2h5QXBpIl0sImNsaWVudF9pZCI6IjBENDk3ODVFRDRDQTRGREU5QjQ2NEY4Qzc0MjcwNUY0Iiwic3ViIjoiNjA5NDY2MSIsImF1dGhfdGltZSI6MTU0MjMxMTk4MCwiaWRwIjoibG9jYWwiLCJzY29wZSI6WyJvcGVuaWQiLCJwcm9maWxlIiwiZW1haWwiLCJyb2xlcyIsIkFTX1JvbGVzIiwiVW5pZmllZEVtcGxveWVlSGllcmFyY2h5QXBpIl0sImFtciI6WyJwd2QiXX0.AFORorcDFy_IVQxm_KEHTpEgYGGLL1vWzK0_hiBYPND6jHMDZwziLs3rfkxqGn0qWriQ9BGuHkL8E6i6-FSpCcMLRrNNtAQqDDwqFdMKNcbBMByUmLWqIgvpqtoPvAhR0Ff5-Th6AgY0hY2HRTHOUeTyDxGbWyPQu90DdgzP1438qmZVHKE6ZyaU2EJZuDlGk4VB5AeEub8aPf5RN6HFl1TtDJavlxRwWLejs3lWLsDJwgs0ir8pzt0aKXfMXjD4Usi03rUdnZGCjzZ2mDfvseAvuM1BDn2Dh8HoKx69c1ggCgYqd8lp_NAsymHgXXFK_1vsPg9RfLncRX3HHqhtzA",
	  "token_type": "Bearer",
	  "scope": "openid profile email roles AS_Roles UnifiedEmployeeHierarchyApi",
	  "profile": {
	    "sid": "f4ade7fe71d25789e409a30e187e2c52",
	    "sub": "6094661",
	    "auth_time": 1542311980,
	    "idp": "local",
	    "amr": [
	      "pwd"
	    ],
	    "name": "Feng, Andy",
	    "locale": "en",
	    "email": "andy.feng@email.com",
	    "role": [
	      "CP2",
	      "AS_PowerUser",
	      "AS_PM",
	      "AS_Admin",
	      "AS_QualityCoach",
	      "AS_Analyst",
	      "AS_Survey",
	      "AS_Incentive",
	      "AS_Observation",
	      "AS_Trainer",
	      "AS_Communications"
	    ]
	  },
	  "expires_at": 1542315582
	}

## OpenID Connect flows ##
OpenID Connect presents three major flows for authentication. These flows dictate how authentication is handled by the OpenID Connect Provider, including what can be sent to client application and how.

- **Authorization Code Flow**: It is designed to traditional native/server-side web applications such as desktop applications. This flow returns an authorization code that can then be exchanged for an identity token and/or access token. It requires client authentication using a client id and secret to retrieve the tokens from the identity server. This flow has the benefit of not exposing tokens to the user agent (i.e. a web browser). 
	- This flow allows for long lived access (through the use of refresh tokens). Clients using this flow must be able to maintain a secret. Typically, client application can save client_id, secret. 
	- It includes two round trips to the OpenID Connect Provider. The first round trip get authenrization code via client_id and secret. The second round trip get ID token, access token or refresh token.
	- This workflow doesn't work for browser-based applications such as Angular, React, Vue because anyone can easily find secret via development tools in browser.
	
- **Implicit Flow**: It is designed for browse-based apps. It requests tokens without explicit client authentication, instead using the redirect URI to verify the client identity. Because of this, refresh tokens are not allowed, nor is this flow suitable for long lived access tokens. From the client application's point of view, this is the simplest to implement, as there is only one round trip to the OpenID Connect Provider.
	- This flow obtains all tokens from the identity server.
	- Tokens have specific short expiration time and cannot renew via refresh token. 
	- It is not safe to save all tokens in client side.
	- It is suitable for browser-based applications such as Angular, React, Vue
	
- **Hybrid Flow**: It is a combination of aspects from the previous two and rarely used. This flow allows the client to make immediate use of an identity token and retrieve an authorization code via one round trip to the authentication server. This can be used for long lived access (again, through the use of refresh tokens). Clients using this flow must be able to maintain a secret.
	- This flow can obtain an authorization code and tokens from the authorization endpoint, and can also obtain refresh tokens from the token endpoint.

**Authorization Code Flow vs. Implicit Flow vs. Hybrid Flow**

1. The characteristics of the three flows are summarized in the following non-normative table. The table is intended to provide some guidance on which flow to choose in particular contexts.

	![](/images/posts/20190630-openid-1.png)

1. The flow used is determined by the response_type value contained in the Authorization Request. These response_type values are different for three flows:

	![](/images/posts/20190630-openid-2.png)	
	
# Architecture of OpenID Connect integration

![](/images/posts/20181031-openid-3.png)

# OpenID Connect resource server development in ASP.NET core approach 1
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

# OpenID Connect resource development in ASP.NET core approach 2
develop resource server
 
1. install `IdentityServer4.AccessTokenValidation` 2.x (for .net core 2.x), 3.x for (.net core 3.x)

	![](/images/posts/20181031-openid-1.png)

1. setup identity server authentication

	Startup.cs

		public void ConfigureServices(IServiceCollection services)
        {
            ...
            services.AddAuthentication(
                    IdentityServerAuthenticationDefaults.AuthenticationScheme)
                .AddIdentityServerAuthentication(options =>
                {
                    options.Authority = "http://www.auth-server-url.com"; // Auth Server
                    options.RequireHttpsMetadata = false; // only for development
                    options.ApiName = "UnifiedEmployeeHierarchyApi"; // API Resource Id(audience) in authentication server
                });
			...
		}

	or

		public void Configure(IApplicationBuilder app, IHostingEnvironment env, ILoggerFactory loggerFactory)
        {
            //more code....

            //add this configuration for the middleware needed to validate the tokens
            app.UseIdentityServerAuthentication(new IdentityServerAuthenticationOptions
            {
                Authority = "http://localhost:50151",
                RequireHttpsMetadata = false,
                ApiName = "scope.readaccess"
            });

            //more code...
        }   

1. In API, add [Attribute] and check the scope permission

        [HttpGet]
        public IEnumerable<string> Get()
        {
            //this is a basic code snippet to validate the scope inside the API
            bool userHasRightScope = User.HasClaim("scope", "scope.name.in.client.of.auth.server");
            if (userHasRightScope == false)
            {
                throw new Exception("Invalid scope");
            }
            return new string[] { "value1", "value2" };
        }

# OpenID Connect client development in .NET
1. create a console project

1. add `System.IdentityModel.Tokens.Jwt` lib

1. request jwt token from server

		static void Main(string[] args)
		{
		    //authorization server parameters owned from the client
		    //this values are issued from the authorization server to the client through a separate process (registration, etc...)
		    Uri authorizationServerTokenIssuerUri = new Uri("http://localhost:50151/connect/token");
		    string clientId = "ClientIdThatCanOnlyRead";    
		    string clientSecret = "secret1";
		    string scope = "scope.readaccess";
		
		    //access token request
		    string rawJwtToken = RequestTokenToAuthorizationServer(
		         authorizationServerTokenIssuerUri,
		         clientId, 
		         scope, 
		         clientSecret)
		        .GetAwaiter()
		        .GetResult();
		
		    //...some more code
		}

        private static async Task<string> RequestTokenToAuthorizationServer(Uri uriAuthorizationServer, string clientId, string scope, string clientSecret)
        {
            HttpResponseMessage responseMessage;
            using (HttpClient client = new HttpClient())
            {
                HttpRequestMessage tokenRequest = new HttpRequestMessage(HttpMethod.Post, uriAuthorizationServer);
                HttpContent httpContent = new FormUrlEncodedContent(
                    new[]
                    {
                    new KeyValuePair<string, string>("grant_type", "client_credentials"),
                    new KeyValuePair<string, string>("client_id", clientId),
                    new KeyValuePair<string, string>("scope", scope),
                    new KeyValuePair<string, string>("client_secret", clientSecret)
                    });
                tokenRequest.Content = httpContent;
                responseMessage = await client.SendAsync(tokenRequest);
            }
            return await responseMessage.Content.ReadAsStringAsync();
        }

1. Call the secured WebAPI providing the JWT token
	Now we have the token, and we can use it to perform a request to the WebAPI.

		private static async Task<string> RequestValuesToSecuredWebApi(AuthorizationServerAnswer authorizationServerToken)
		{
		    HttpResponseMessage responseMessage;
		    using (HttpClient httpClient = new HttpClient())
		    {
		        httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", authorizationSauthorizationServerTokenerverToken.access_token);
		        HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, "http://localhost:56087/api/values");
		        responseMessage = await httpClient.SendAsync(request);
		    }		
		    return await responseMessage.Content.ReadAsStringAsync();
		}

# OpenID Connect client development in Angular, use ng-oidc-client lib
1. Create a Angular project via angular-cli
1. install oidc lib: 

	npm install ng-oidc-client --save

	npm install -s @ngrx/store

	npm install -s @ngrx/router-store

	npm install -s @ngrx/effects

	npm install -s oidc-client

	![](/images/posts/20181031-openid-2.png)

1. modify `app.module.ts` to add the NgOidcClientModule to your AppModule

		import { ActionReducerMap } from '@ngrx/store';
		import { routerReducer, RouterReducerState } from '@ngrx/router-store';
		export function getWebStorageStateStore() {
		  return new WebStorageStateStore({ store: window.localStorage });
		}
		
		export interface State {
		  router: RouterReducerState;
		}
		export const rootStore: ActionReducerMap<State> = {
		  router: routerReducer
		};
		...
		  imports: [
		    ...
			StoreModule.forRoot(rootStore),
		    EffectsModule.forRoot([]),
		    NgOidcClientModule.forRoot({
			  oidc_config: {
			    authority: 'https://localhost:5001',
			    client_id: 'ng-oidc-client-identity',
			    redirect_uri: 'http://localhost:4200/callback.html',
			    response_type: 'id_token token',
			    scope: 'openid profile offline_access api1',
			    post_logout_redirect_uri: 'http://localhost:4200/signout-callback.html',
			    silent_redirect_uri: 'http://localhost:4200/renew-callback.html',
			    accessTokenExpiringNotificationTime: 10,
			    automaticSilentRenew: true,
			    userStore: getWebStorageStateStore
			  }
			})
		  ],
		...
		export function getWebStorageStateStore() {
		  return new WebStorageStateStore({ store: window.localStorage });
		}
		
		export interface State {
		  router: RouterReducerState;
		}
		export const rootStore: ActionReducerMap<State> = {
		  router: routerReducer
		};

1. Inject the OidcFacade in our Component

		...
		export class HomeComponent {
		  constructor(protected oidcFacade: OidcFacade) {}
		
		  loginPopup() {
		    this.oidcFacade.signinPopup();
		  }
		
		  logoutPopup() {
		    this.oidcFacade.signoutPopup();
		  }
		}

1. Create a new directory called static below the src folder, to serve the static callback HTML sites.

		src/static
		├── callback.html
		├── renew-callback.html
		└── signout-callback.html

	callback.html

		<!DOCTYPE html>
		<html>
		
		<head>
		  <meta charset="utf-8" />
		  <title>Callback</title>
		  <link rel="icon"
		        type="image/x-icon"
		        href="favicon.png">
		  <script src="oidc-client.min.js"
		          type="application/javascript"></script>
		</head>
		
		<body>
		  <script>
		    var Oidc = window.Oidc;
		    var config = {
		      userStore: new Oidc.WebStorageStateStore({ store: window.localStorage })
		    }
		    if ((Oidc && Oidc.Log && Oidc.Log.logger)) {
		      Oidc.Log.logger = console;
		    }
		    var isPopupCallback = JSON.parse(window.localStorage.getItem('ngoidc:isPopupCallback'));
		    if (isPopupCallback) {
		      new Oidc.UserManager(config).signinPopupCallback();
		    } else {
		      new Oidc.UserManager(config).signinRedirectCallback().then(t => {
		        window.location.href = '/';
		      });
		    }
		  </script>
		</body>
		
		</html>

	renew-callback.html

		<!DOCTYPE html>
		<html>
		
		<head>
		    <meta charset="utf-8" />
		    <title>Renew Callback</title>
		    <link rel="icon"
		          type="image/x-icon"
		          href="favicon.png">
		</head>
		
		<body>
		    <script src="oidc-client.min.js"></script>
		    <script>
		        var config = {
		            userStore: new Oidc.WebStorageStateStore({ store: window.localStorage })
		        }
		        new Oidc.UserManager(config).signinSilentCallback().catch(function (e) {
		            console.error(e);
		        });
		    </script>
		</body>
		
		</html>

	signout-callback.html

		<!DOCTYPE html>
		<html>
		
		<head>
		  <meta charset="utf-8" />
		  <title>Signout Callback</title>
		  <link rel="icon"
		        type="image/x-icon"
		        href="favicon.png">
		  <script src="oidc-client.min.js"
		          type="application/javascript"></script>
		</head>
		
		<body>
		  <script>
		    var Oidc = window.Oidc;
		    var config = {
		      userStore: new Oidc.WebStorageStateStore({ store: window.localStorage })
		    }
		    if ((Oidc && Oidc.Log && Oidc.Log.logger)) {
		      Oidc.Log.logger = console;
		    }
		    var isPopupCallback = JSON.parse(window.localStorage.getItem('ngoidc:isPopupCallback'));
		    if (isPopupCallback) {
		      new Oidc.UserManager(config).signoutPopupCallback();
		    } else {
		      new Oidc.UserManager(config).signoutRedirectCallback().then(test => {
		        window.location.href = '/';
		      });
		    }
		  </script>
		</body>
		
		</html>

1. Modify `angular.json` to include the static assets and oidc-client

		...
		"assets": [
		    "src/favicon.ico",
		    "src/assets",
		   {
		     "glob": "**/*",
		     "input": "src/static",
		     "output": "/"
		   },
		   {
		     "glob": "oidc-client.min.js",
		     "input": "node_modules/oidc-client/dist",
		     "output": "/"
		   }
		  ],
		...

	Next, we are ready to implement authentication via developing identity providers. We have two things to do

1. create a new Service and implement the AuthGuard interface. We will protect Routes using an AuthGuard. 

	`providers/oidc-guard-service.ts`

		import { Injectable } from '@angular/core';
		import { CanActivate, Router, ActivatedRouteSnapshot, RouterStateSnapshot } from "@angular/router";
		import { OidcFacade } from "ng-oidc-client";
		import { Observable, of } from "rxjs";
		import { take, switchMap } from "rxjs/operators";
		
		@Injectable({
		    providedIn: 'root'
		  })
		export class OidcGuardService implements CanActivate {
		    constructor(private router: Router, private oidcFacade: OidcFacade) {}
		  
		    public canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<boolean> | boolean {
		      return this.oidcFacade.identity$.pipe(
		        take(1),
		        switchMap(user => {
		          console.log('Auth Guard - Checking if user exists', user);
		          console.log('Auth Guard - Checking if user is expired:', user && user.expired);
		          if (user && !user.expired) {
		            return of(true);
		          } else {
		            this.router.navigate(['/login']);
		            return of(false);
		          }
		        })
		      );
		    }
		  }

	The guard needs to inject the OidcFacade to check if the user exists and is not expired. It is up to the application flow if the unauthenticated user is redirected to a login route or not.
	
	Add the Guard to the list of providers in your AppModule

		...
		providers: [
		   OidcGuardService,
		  ],
		...

1. We also need to use HTTP Interceptor to add the Bearer token to API calls. Because an API requires a valid access token to interact with remote server, we can use a HTTP Interceptor to add the required Bearer token to each call.

	Create a new Service and implement the HttpInterceptor interface

	`providers/oidc-interceptor-service.ts`

		import { Injectable } from '@angular/core';
		import { HttpRequest, HttpHandler, HttpEvent, HttpInterceptor } from '@angular/common/http';
		import { Observable } from 'rxjs';
		import { switchMap } from 'rxjs/operators';
		import { OidcFacade } from 'ng-oidc-client';
		
		@Injectable({
		  providedIn: 'root'
		})
		export class OidcInterceptorService implements HttpInterceptor {
		  static OidcInterceptorService: any;
		  constructor(private oidcFacade: OidcFacade) {}
		
		  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
		    console.log('intercept');
		    return this.oidcFacade.identity$.pipe(
		      switchMap(user => {
		        if (user && !user.expired && user.access_token) {
		          req = req.clone({
		            setHeaders: {
		              Authorization: `Bearer ${user.access_token}`
		            }
		          });
		        }
		        return next.handle(req);
		      })
		    );
		  }
		}			
	
	The interceptor needs to inject the `OidcFacade` to check if the user exists, is not expired and has an access token. The outgoing request will be cloned and an Authorization Header will be added to the request. Additionally, the requests should be filtered to only add the Bearer token where it is needed.
	
	Add the Interceptor to the list of providers in your AppModule

		...
		providers: [
		   {
		     provide: HTTP_INTERCEPTORS,
		     useClass: OidcInterceptorService,
		     multi: true
		   }
		  ],
		...

1. Next, we are ready to go. 
	Note the OidcFacade class works like an auth service and we can got user identity information from it. We inject this object to any component who need get user identity information.

	e.g. 

	- loading$ : Observable<boolean> - used to indicate if the user is currently loading
	- expiring$ : Observable<boolean> - indicates that a token is going to expire very soon
	- expired$ : Observable<boolean> - indicates that the token is expired, a new token should be obtained
	- identity$ : Observable<OidcUser> - returns an Observable to the obtained identity and is identical to User from oidc-client
	- errors$ : Observable<ErrorState> - returns an Observable to Errors caught from oidc-client

# OpenID Connect client development in Angular, use angular-oauth2-oidc lib
1. prepare an identity server. e.g. `okra`
	
	register a developer account
	
	![](/images/posts/20190412-okra-1.png)

	Create an OIDC App in Okta > Log in to Okta Developer account > Applications > Add Application > Web > Give the app a name you’ll remember > set Login redirect URI e.g. http://localhost:4200 > Click Done. Edit your app after creating it and specify Logout redirect URI. 
	
	![](/images/posts/20190412-okra-2.png)

	![](/images/posts/20190412-okra-3.png)

	![](/images/posts/20190412-okra-4.png)

1. Create a Angular project via angular-cli
1. Install lib:

	`npm install --save angular-oauth2-oidc`

1. Open `src/app/app.module.ts` and import `OAuthModule`, `HttpClientModule`.

		import { HttpClientModule } from '@angular/common/http';
		import { OAuthModule } from 'angular-oauth2-oidc';
		
		@NgModule({
		  declarations: [
		    AppComponent
		  ],
		  imports: [
		    BrowserModule,
		    AppRoutingModule,
		    HttpClientModule,
		    OAuthModule.forRoot()
		  ],
		  providers: [],
		  bootstrap: [AppComponent]
		})
		export class AppModule { }

1. Modify `src/app/app.component.ts` to import `OAuthService` and configure it to use your Okta application settings. Also, add `login()` and `logout()` methods

		import { Component } from '@angular/core';
		import { AuthConfig, OAuthService, JwksValidationHandler } from 'angular-oauth2-oidc';
		
		export const authConfig: AuthConfig = {
		  issuer: 'https://dev-622559.okta.com/oauth2/default',
		  redirectUri: window.location.origin,
		  clientId: '{yourClientId}'
		};
		
		@Component({
		  selector: 'app-root',
		  templateUrl: './app.component.html',
		  styleUrls: ['./app.component.scss']
		})
		export class AppComponent {
		  title = 'ng-secure';
		  constructor(private oauthService: OAuthService){
		    this.oauthService.configure(authConfig);
		    this.oauthService.tokenValidationHandler = new JwksValidationHandler();
		    this.oauthService.loadDiscoveryDocumentAndTryLogin();
		  }
		  login() {
		    this.oauthService.initImplicitFlow();
		  }
		
		  logout() {
		    this.oauthService.logOut();
		  }
		  
		  get givenName() {
		    const claims = this.oauthService.getIdentityClaims();
		    if (!claims) {
		      return null;
		    }
		    return claims['name'];
		  }
		}

1. update `app.component.html`

		<h1>Welcome to {{ title }}!</h1>
		
		<div *ngIf="givenName">
		  <h2>Hi, {{givenName}}!</h2>
		  <button (click)="logout()">Logout</button>
		</div>
		
		<div *ngIf="!givenName">
		  <button (click)="login()">Login</button>
		</div>
		
		<router-outlet></router-outlet>

1. Restart your app and you should see a login button. Click the login button, sign in to your Okta account, and you should see your name with a logout button.

	![](/images/posts/20190412-okra-5.png)

	![](/images/posts/20190412-okra-6.png)

# OpenID vs. OAuth
1. OAuth2 is not designed for authentication but OpenID does

	OAuth 2.0 is fundamentally an authorization protocol, not an authentication protocol. It's entire design is based around providing access to some protected resource (e.g. Facebook Profile, or Photos) to a third party (e.g. our application). OAuth2 is designed for authorizing resources by endpoints. It is endpoint-oriented and anyone who got access token via OAuth2 can access that specific resource later on. 

	OAuth2 essentially is a way we request access token from Authorization server, then we use access token to talk to backend server. It is focus on Authorization process.
	
	However, OpenID is kind of authentication protocol. It is just used for authenticating user without touching any resources. Although anyone got access token via OpenID can be authenticated but not authorized - user cannot access any resources at all. The user is just authenticated (logged in). The user has to ask additional access token in order to access any resources later on. 

1. OAuth2 is very general but OpenID is specific in authenticating

	OAuth 2.0 is very loose in it's requirements for implementation. There are many subtly different implementations across various providers. Each of those providers requires some degree of customisation aside from specifying urls and secrets. Each one returns data in a different format and must have the returned Claims parsed.

	OpenID Connect is far more rigid in its requirements, which allows a great deal of interoperability. Any implementions using OpenID Connect is exactly the same logic and can be replaced without code modification; it doesn't have the drawback of OAuth2 implementations - subtle differences between implementations.




	OpenID Connect use two major flows of OAuth2
	- Authorization code flow for server based applications
	- Implicit flow for client-based applications

	OpenID Connect add some new concepts over OAuth2. such as		
	- ID token - for client to make validation before sending request with access token
	- User info endpoint
	- additional protocols. e.g. discovery& dynamic registration, session management(login/logout)

# FAQ
1. scope vs. claim vs. audience?

	Claims are specific attributes about a user. Claims are name/value pairs. It is prepared by the authorization server; it is just for list user's info and not aim for control access.
	
	Scope is used to control client's access to resource. your server may allow or reject this. Typically, scopes are space-separated lists of identifiers used to specify what access privileges are being requested. e.g. OIDC has some built-in scopes: openid, profile, email, address, phone
	
	Audience identifies the recipients that the JWT token intended for. It represents the OAuth 2.0 resource server that consumes the access tokens.

# References
[https://github.com/IdentityModel/oidc-client-js](https://github.com/IdentityModel/oidc-client-js)

[https://openid.net/specs/openid-connect-core-1_0.html](https://openid.net/specs/openid-connect-core-1_0.html)

[ng-oidc-client-server](https://github.com/Fileless/ng-oidc-client-server)

[ng-oidc-client](https://github.com/fileless/ng-oidc-client)

[https://developer.okta.com/blog/2018/12/04/angular-7-oidc-oauth2-pkce#create-an-oidc-app-in-okta](https://developer.okta.com/blog/2018/12/04/angular-7-oidc-oauth2-pkce#create-an-oidc-app-in-okta)

[https://openid.net/specs/openid-connect-core-1_0.html#Authentication](https://openid.net/specs/openid-connect-core-1_0.html#Authentication)

[OpenID Connect FAQ and Q&As](https://openid.net/connect/faq/)

[OpenID Connect Playground](https://openidconnect.net/#)