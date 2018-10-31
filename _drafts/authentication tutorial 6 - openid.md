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
	  constructor(private oidcFacade: OidcFacade) {}
	
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

# OpenID vs. OAuth
OAuth 2.0 is fundamentally an authorization protocol, not an authentication protocol. It's entire design is based around providing access to some protected resource (e.g. Facebook Profile, or Photos) to a third party (e.g. our application). OpenID is kind of authentication protocol.

OAuth 2.0 is very loose in it's requirements for implementation. There are many subtly different implementations across various providers. Each of those providers requires some degree of customisation aside from specifying urls and secrets. Each one returns data in a different format and must have the returned Claims parsed. OpenID Connect is far more rigid in its requirements, which allows a great deal of interoperability.

OpenID Connect is kind of a “super-set” of OAuth 2.0 and always recommended against using OAuth.

# References
[https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/?view=aspnetcore-2.0](https://docs.microsoft.com/en-us/aspnet/core/security/authentication/social/?view=aspnetcore-2.0)

[https://docs.microsoft.com/en-us/aspnet/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on](https://docs.microsoft.com/en-us/aspnet/mvc/overview/security/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on)

[https://github.com/IdentityModel/oidc-client-js](https://github.com/IdentityModel/oidc-client-js)

[https://openid.net/specs/openid-connect-core-1_0.html](https://openid.net/specs/openid-connect-core-1_0.html)

[ng-oidc-client-server](https://github.com/Fileless/ng-oidc-client-server)

[ng-oidc-client](https://github.com/fileless/ng-oidc-client)