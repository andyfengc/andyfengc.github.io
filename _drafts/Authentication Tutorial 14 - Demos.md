---
layout: post
title: ASP.NET Identity authentication 14 - demos
author: Andy Feng
---

# Okta angular demo
1. register an account in [Okta](https://developer.okta.com)

1. Log into the Okta Developer Dashboard > click Applications > Add Application

	![](/images/posts/20190909-okta-1.png)
	![](/images/posts/20190909-okta-2.png)

	We will get a new project in Okta and a client id

	Then, find the authorization server(issuer) under API
	![](/images/posts/20190909-okta-3.png)

	Find and manage users
	![](/images/posts/20190909-okta-4.png)

1. create a new Angular project

	ng new okta-angular-demo
	npm install angular-oauth2-oidc --save

1. Modify src/app/app.module.ts, app.component.ts to import OAuthService and configure your app to use your Okta application’s settings.

	auth-config.ts

		import { AuthConfig } from 'angular-oauth2-oidc';
		
		export const authConfig: AuthConfig = {
		
		  // Url of the Identity Provider
		  issuer: 'https://dev-957657.okta.com/oauth2/default',
		
		  // URL of the SPA to redirect the user to after login
		  redirectUri: window.location.origin,
		
		  // The SPA's id. The SPA is registered with this id at the auth-server
		  clientId: 'xxxxxxxxxxxxxxx',
		
		  // set the scope for the permissions the client should request
		  // The first three are defined by OIDC. The 4th is a usecase-specific one
		  scope: 'openid profile email',
		}

	app.module.ts

		...
		import { HttpClientModule } from '@angular/common/http';
		import { OAuthModule } from 'angular-oauth2-oidc';
		
		@NgModule({
		  declarations: [
		    AppComponent
		  ],
		  imports: [
		    ...
		    HttpClientModule,
		    OAuthModule.forRoot(),
		  ],
		  providers: [],
		  bootstrap: [AppComponent]
		})
		export class AppModule { }

	app.component.ts

		import { Component } from '@angular/core';
		import { OAuthService, JwksValidationHandler } from 'angular-oauth2-oidc';
		import { authConfig } from './auth-config';
		
		@Component({
		  selector: 'app-root',
		  templateUrl: './app.component.html',
		  styleUrls: ['./app.component.css']
		})
		export class AppComponent {
		  title = 'okta-angular-demo';
		  constructor(private oauthService: OAuthService) {
		    this.configure();
		  }
		  private configure() {
		    this.oauthService.configure(authConfig);
		    this.oauthService.tokenValidationHandler = new JwksValidationHandler();
		    this.oauthService.loadDiscoveryDocumentAndTryLogin();
		  }
		
		  public login() {
		    this.oauthService.initLoginFlow();
		  }
		
		  public logoff() {
		    this.oauthService.logOut();
		  }
		
		  public get name() {
		    let claims = this.oauthService.getIdentityClaims();
		    if (!claims) return null;
		    return claims;
		  }
		}

	app.component.html

		<h1 *ngIf="!name">
		  Hallo
		</h1>
		<h1 *ngIf="name">
		  Hallo, {{name | json}}
		</h1>
		
		<button class="btn btn-default" (click)="login()">
		  Login
		</button>
		<button class="btn btn-default" (click)="logoff()">
		  Logout
		</button>
		
		<router-outlet></router-outlet>

1. start the angular project > click login > enter a valid okta user > sign in

	![](/images/posts/20190909-okta-5.png)
	![](/images/posts/20190909-okta-6.png)

Please note that we can use Okta's official `@okta/okta-angular` lib instead of `angular-oauth2-oidc`

# Okta .NET demo
1. Create a new API project

	![](/images/posts/20180228-.netcore-webapi-2.png)

1. Create some apis, i.e.

	    [Route("api/messages")]
	    [ApiController]
	    public class MessageController : ControllerBase
	    {
	        [HttpGet]
	        public ActionResult<IEnumerable<string>> Get()
	        {
	            return new string[] { "message 1", "message 2" };
	        }
	    }

1. install lib `IdentityServer4.AccessTokenValidation`

	![](/images/posts/20181031-openid-1.png)

1. Modify startup.cs

		public class Startup
	    {
	        public Startup(IConfiguration configuration)
	        {
	            Configuration = configuration;
	        }
	
	        public IConfiguration Configuration { get; }
	
	        // This method gets called by the runtime. Use this method to add services to the container.
	        public void ConfigureServices(IServiceCollection services)
	        {
	            // way1, only verify token and not verify scope
	            services.AddAuthentication("Bearer")
	                .AddJwtBearer(
	                    "Bearer", options =>
	                    {
	                        options.Authority = "https://dev-957657.okta.com/oauth2/default";
	                        options.RequireHttpsMetadata = false;
	
	                        options.Audience = "api://default";
	                    });
	            // way2, only verify token and not verify scope
	            //services.AddAuthentication("Bearer")
	            //    .AddIdentityServerAuthentication(options =>
	            //    {
	            //        options.Authority = "https://dev-957657.okta.com/oauth2/default";
	            //        options.RequireHttpsMetadata = false;
	            //        options.ApiName = "api://default";
	            //    });
	
	            services.AddMvc().SetCompatibilityVersion(CompatibilityVersion.Version_2_1);
	        }
	
	        // This method gets called by the runtime. Use this method to configure the HTTP request pipeline.
	        public void Configure(IApplicationBuilder app, IHostingEnvironment env)
	        {
	            if (env.IsDevelopment())
	            {
	                app.UseDeveloperExceptionPage();
	            }
	            else
	            {
	                app.UseHsts();
	            }
	            app.UseAuthentication();
	            app.UseHttpsRedirection();
	
	            app.UseMvc();
	        }
	    }

	make sure the audience/apiname matches the audience in Okta authorization server config

	![](/images/posts/20190909-okta-3.png)


Please note that we can use Okta's official `Okta.AspNetCore` lib instead of IdentityServer4 lib

# References
[Okta Authentication Quickstart Guides - Angular](https://developer.okta.com/quickstart/#/angular/nodejs/express)
