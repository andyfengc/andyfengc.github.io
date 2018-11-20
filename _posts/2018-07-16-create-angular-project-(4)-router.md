---
layout: post
title: Create Angular v2+ project (3) - router
author: Andy Feng
---

# Introduction #
The Angular `Router` enables navigation from one view to another view when user perform some tasks. Also, it can pass optional parameters along to the target view component.

There are below typical navigation senarios:

- Enter a URL in the address bar and the browser navigates to a corresponding page.
- User click links on the page and the browser navigates to a new page.
- User click the browser's back and forward buttons and the browser navigates backward and forward through the history of pages we've seen.

Angular `Router` uses the browser's [history.pushState](https://developer.mozilla.org/en-US/docs/Web/API/History_API#Adding_and_modifying_history_entries) for navigation. Thanks to `pushState`, we can make in-app URL paths look the way we want them to look, e.g. `localhost:3000/crisis-center`. The in-app URLs is different from server URLs and handled by browsers.

# Outline
- steps to create simple routing
- refactor routing into modules
- route guards
- route resolvers

# Steps #
1. the routed Angular application has a configured routes
2. The hosting component has a `RouterOutlet` where it can display views produced by the router. 
3. the hosting component also has `RouterLinks` that users can click to navigate via the router.
4. we can handle routing data or events in components corresponding to specific routes.

Here is Routing related terms in Angular:

![](/images/posts/20180829-router-3.png)

## Prepare root component ##
Due to Angular routing system uses HTML5 `pushState`, we must add a `<base href>` element to the app's `index.html`. The browser uses the <base href> value to prefix relative URLs when referencing CSS files, scripts, and images.

	<!doctype html>
	<html lang="en">
	
	<head>
	  <meta charset="utf-8">
	  <title>Web</title>
	  <base href="/">
	
	  <meta name="viewport" content="width=device-width, initial-scale=1">
	  <link rel="icon" type="image/x-icon" href="favicon.ico">
	</head>
	
	<body>
	  <app-root></app-root>
	</body>
	
	</html>

## Set router configuration
creates route definitions, configures the router via the `RouterModule.forRoot` method, and adds the result to the AppModule's imports array.

app/app.module.ts

	import { NgModule } from '@angular/core';
	import { NgModule }             from '@angular/core';
	import { BrowserModule }        from '@angular/platform-browser';
	import { FormsModule }          from '@angular/forms';
	import { RouterModule, Routes } from '@angular/router';	

	import { AppComponent }          from './app.component';
	import { CrisisListComponent }   from './crisis-list.component';
	import { HeroListComponent }     from './hero-list.component';
	...
	const appRoutes: Routes = [
	  { path: 'crisis-center', component: CrisisListComponent },
	  { path: 'hero/:id',      component: HeroDetailComponent },
	  {
	    path: 'heroes',
	    component: HeroListComponent,
	    data: { title: 'Heroes List' }
	  },
	  { path: '',
	    redirectTo: '/heroes',
	    pathMatch: 'full'
	  },
	  { path: '**', component: PageNotFoundComponent }
	];
	
	@NgModule({
	  imports: [
		BrowserModule,
	    FormsModule,
	    RouterModule.forRoot(
	      appRoutes,
	      { enableTracing: true } // <-- debugging purposes only
	    )
	    // other imports here
	  ],
	 declarations: [
	    AppComponent,
	    HeroListComponent,
	    CrisisListComponent,
	  ],
	  bootstrap: [ AppComponent ]
	  ...
	})
	export class AppModule { }

Please note:

1. The `appRoutes` array of routes describes how to navigate. Pass it to the `RouterModule.forRoot()` method in the module imports to configure the router.
2. Each `Route` maps a URL path to a component.
3. `:id` in the second route is a token for a route parameter. such as /hero/42, "42" is the value of the id parameter
4. `data` property in the third route is a place to store arbitrary data associated with this specific route. The data property is accessible within each activated route. Use it to store items such as page titles, breadcrumb text, and other read-only, static data. We use it as the resolve guard to retrieve dynamic data 
5. The empty path in the fourth route represents the default path for the application, the place to go when the path in the URL is empty
6. `**` path in the last route will be used if the requested URL doesn't match any paths for routes defined earlier in the configuration. This is useful for displaying a `404 - Not Found` page or redirecting to another route.
7. `Router` uses `first-match` wins strategy when matching routes, so more specific routes should be placed above less specific routes. In the configuration above, routes with specific path are listed first, followed by an empty path route, that matches the default route. The wildcard route comes last because it matches every URL and should be selected only if no other routes are matched first.
8. `enableTracing` option is only for debugging purpose and it outputs each router event that took place during each navigation lifecycle to the browser console. We set the `enableTracing: true` option in the object passed as the second argument to the RouterModule.forRoot() method.

## Set routing endpoints
In the application host view(shell), we set up `router-outlet` and it is the navigation root. Typically, the root `AppComponent` is the application shell.

app.component.html

	<div class="container">
	    <router-outlet></router-outlet>
	</div>

## Set routing urls
Now we have routes configured and ready to render navigated views. Here, we use `routerLink`

app.component.html

	<h1>Angular Router</h1>
	<nav>
		<a routerLink="/crisis-center" routerLinkActive="active">Crisis Center</a>
		<a routerLink="/heroes" routerLinkActive="active">Heroes</a>
	</nav>
	<router-outlet></router-outlet>

the host view looks like

![](/images/posts/20180829-router-4.png)

The `RouterLink` directives on the anchor tags give the router control over those elements. Because navigation paths are fixed, so we just assign a string to the routerLink.

Had the navigation path been more dynamic, you could have bound to a template expression that returned an array of route link parameters (the link parameters array). The router resolves that array into a complete URL.

The RouterLinkActive directive on each anchor tag helps visually distinguish the anchor for the currently selected "active" route. The router adds the active CSS class to the element when the associated RouterLink becomes active. You can add this directive to the anchor or to its parent element.

hero-list.component.ts

	<h2>HEROES</h2>
	<ul class="items">
	<li *ngFor="let hero of heroes$ | async"
	  [class.selected]="hero.id === selectedId">
	  <a [routerLink]="['/hero', hero.id]">
	    <span class="badge">{{ hero.id }}</span>{{ hero.name }}
	  </a>
	</li>
	</ul>
	
	<button routerLink="/sidekicks">Go to sidekicks</button>

## Process routing data in component
A routed Angular application has one singleton instance of the Router service. 

After the end of each successful navigation lifecycle, the router builds a tree of `ActivatedRoute` objects that make up the current state of the router. We can access the data in the application

	import { ActivatedRoute, Router } from "@angular/router";
	...
	@Component({
		...
	})
	
	export class HeroComponent{
		private id : number;
	    constructor(protected route: ActivatedRoute
	        , protected router: Router) {
			this.id = this.route.snapshot.paramMap.get('id'); // get id parameter via /hero/42
			// or
			//this.route.paramMap.pipe(
			//    switchMap((params: ParamMap) =>
			//      this.id = params.get('id');
			//  );
	    }
		...
	}

Activated route object includes the route path, parameters and other information:

	![](/images/posts/20180829-router-1.png)

	Important properties:

	**paramMap** - an Observable that contains the required and optional parameters specific to the route. old name is `params`.
	
	**queryParamMap** — an Observable that contains the query parameters available to all routes. old name is `queryParams`

## Process routing events in component
During each navigation, `Router` service emits multiple navigation events through the `Router.events` property.

	![](/images/posts/20180829-router-2.png)

These events are logged to the console when the `enableTracing` option is enabled. Because the events are Observable, we can filter() for events of interest and subscribe() to them.

	import { ActivatedRoute, Router, NavigationEnd, NavigationStart } from '@angular/router';
	...
	@Component({
		...
	})
	
	export class HeroComponent{
	    constructor(protected route: ActivatedRoute
	        , protected router: Router) {
			 this.router.events.subscribe((event) => {
		      if (event instanceof NavigationStart) {
		        console.log('current url: ', this.router.url);
		        console.log('to url: ', event.url);
		      }
		      if (event instanceof NavigationEnd) {
		        console.log('current url: ', this.router.url);
		        console.log('to url: ', event.url);
		      }
		    })
		    }
		...
	}

## Navigate routing
hero-detail.component.ts

	gotoHeroes() {
	  	this.router.navigate(['/heroes']);
		//this.router.navigate([['/hero', hero.id]]);
	}

# Refactor routing module
Create a file app/app-routing.module.ts to contain the routing module.

	import { NgModule }              from '@angular/core';
	import { RouterModule, Routes }  from '@angular/router';
	 
	import { CrisisListComponent }   from './crisis-list.component';
	import { HeroListComponent }     from './hero-list.component';
	import { PageNotFoundComponent } from './not-found.component';
	 
	const appRoutes: Routes = [
	  { path: 'crisis-center', component: CrisisListComponent },
	  { path: 'heroes',        component: HeroListComponent },
	  { path: '',   redirectTo: '/heroes', pathMatch: 'full' },
	  { path: '**', component: PageNotFoundComponent }
	];
	 
	@NgModule({
	  imports: [
	    RouterModule.forRoot(
	      appRoutes,
	      { enableTracing: true } // <-- debugging purposes only
	    )
	  ],
	  exports: [
	    RouterModule
	  ]
	})
	export class AppRoutingModule {}

update the app.module.ts file, first importing the newly created `AppRoutingModule` from app-routing.module.ts, then replacing `RouterModule.forRoot` in the imports array with the AppRoutingModule.

	import { NgModule }       from '@angular/core';
	import { BrowserModule }  from '@angular/platform-browser';
	import { FormsModule }    from '@angular/forms';
	 
	import { AppComponent }     from './app.component';
	import { AppRoutingModule } from './app-routing.module';
	 
	import { CrisisListComponent }   from './crisis-list.component';
	import { HeroListComponent }     from './hero-list.component';
	import { PageNotFoundComponent } from './not-found.component';
	 
	@NgModule({
	  imports: [
	    BrowserModule,
	    FormsModule,
	    AppRoutingModule
	  ],
	  declarations: [
	    AppComponent,
	    HeroListComponent,
	    CrisisListComponent,
	    PageNotFoundComponent
	  ],
	  bootstrap: [ AppComponent ]
	})
	export class AppModule { }

# child routing #
create a sub routing config `crisis-center-routing.module.ts`:

	import { NgModule }             from '@angular/core';
	import { RouterModule, Routes } from '@angular/router';
	// some new components
	import { CrisisCenterHomeComponent } from './crisis-center-home.component';
	import { CrisisListComponent }       from './crisis-list.component';
	import { CrisisCenterComponent }     from './crisis-center.component';
	import { CrisisDetailComponent }     from './crisis-detail.component';
	
	const crisisCenterRoutes: Routes = [
	  {
	    path: 'crisis-center',
	    component: CrisisCenterComponent,
	    children: [
	      {
	        path: '',
	        component: CrisisListComponent,
	        children: [
	          {
	            path: ':id',
	            component: CrisisDetailComponent
	          },
	          {
	            path: '',
	            component: CrisisCenterHomeComponent
	          }
	        ]
	      }
	    ]
	  }
	];
	
	@NgModule({
	  imports: [
	    RouterModule.forChild(crisisCenterRoutes)
	  ],
	  exports: [
	    RouterModule
	  ]
	})
	export class CrisisCenterRoutingModule { }

then, import this new routing module to app.module.ts

	import { NgModule }       from '@angular/core';
	import { CommonModule }   from '@angular/common';
	import { FormsModule }    from '@angular/forms';
	
	import { AppComponent }            from './app.component';
	import { PageNotFoundComponent }   from './not-found.component';
	
	import { AppRoutingModule }        from './app-routing.module';
	import { HeroesModule }            from './heroes/heroes.module';
	import { CrisisCenterRoutingModule }      from './crisis-center/crisis-center-routing.module';
	
	import { DialogService }           from './dialog.service';
	
	@NgModule({
	  imports: [
	    CommonModule,
	    FormsModule,
	    HeroesModule,
	    CrisisCenterRoutingModule,
	    AppRoutingModule
	  ],
	  declarations: [
	    AppComponent,
	    PageNotFoundComponent
	  ],
	  providers: [
	    DialogService
	  ],
	  bootstrap: [ AppComponent ]
	})
	export class AppModule { }

navigation, *.component.ts:

	this.router.navigate(['../', { id: crisisId, foo: 'foo' }], { relativeTo: this.route });

# Route guards #
At the moment, any user can navigate anywhere in the application anytime. But for below senarios, we have to add guards to the route configuration to implement them:

- Perhaps the user is not authorized to navigate to the target component. - CanActivate
- Maybe the user must login (authenticate) first. - CanActivate
- Maybe you should fetch some data before you display the target component. - CanActivate
- We might want to save pending changes before leaving a component. - CanDeactivate
- We might ask the user if it's OK to discard pending changes rather than save them. - CanDeactivate

Typically, a routing guard's return value controls the router's behavior. It can return an Observable<boolean> or a Promise<boolean>

- If it returns true, the navigation process continues.
- If it returns false, the navigation process stops and the user stays there.

Angular router supports multiple guard interfaces:

- **CanActivate** to mediate navigation to a route. determines whether the component of a router is accessible
- CanActivateChild to mediate navigation to a child route.
- **CanDeactivate** to mediate navigation away from the current route. determines whether the component of a router is okay to leave away
- Resolve to perform route data retrieval before route activation.
- CanLoad to mediate navigation to a feature module loaded asynchronously.

## CanActivate: requiring authentication
This interface can be used to restrict access to a router on the user identity - whether user can navigate into the router

1. add a admin folder, add admin-specific components, routing config, module config

	structure:

		- src/app/admin
			|- admin.module.ts
			|- admin-routing.module.ts
			|- admin.component.ts
			|- admin-dahboard.component.ts
			|- admin-feature1.component.ts
			|- admin-feature2.component.ts

1. some admin feature components:

	src/app/admin/admin.component.ts

		import { Component } from '@angular/core';
				 
		@Component({
		  template:  `
		    <h3>ADMIN</h3>
		    <nav>
		      <a routerLink="./" routerLinkActive="active"
		        [routerLinkActiveOptions]="{ exact: true }">Dashboard</a> | 
		      <a routerLink="./feature1" routerLinkActive="active">Feature1</a> | 
		      <a routerLink="./feature2" routerLinkActive="active">Feature2</a>
		    </nav>
		    <router-outlet></router-outlet>
		  `
		})
		export class AdminComponent {
		}

	src/app/admin/admin-dashboard.component.ts

		import { Component } from '@angular/core';
		
		@Component({
		  template:  `
		    <p>Dashboard</p>
		  `
		})
		export class AdminDashboardComponent { }

	src/app/admin/admin-feature1.component.ts

		import { Component } from '@angular/core';
		
		@Component({
		  template:  `
		    <p>Admin feature 1 </p>
		  `
		})
		export class AdminFeature1Component { }

1. create admin routing config

	src/app/admin/admin-routing.module.ts

		import { AdminComponent } from "./admin.component";
		import { Routes, RouterModule } from "@angular/router";
		import { AdminFeature1Component } from "./admin-feature1.component";
		import { AdminFeature2Component } from "./admin-feature2.component";
		import { AdminDashboardComponent } from "./admin-dashboard.component";
		import { NgModule } from "@angular/core";
		
		const adminRoutes: Routes = [
		    {
		        path: 'admin',
		        component: AdminComponent,
		        children: [
		            {
		                path: '',
		                children: [
		                    { path: 'feature1', component: AdminFeature1Component },
		                    { path: 'feature2', component: AdminFeature2Component },
		                    { path: '', component: AdminDashboardComponent }
		                ]
		            }
		        ]
		    }
		];
		
		@NgModule({
		    imports: [
		        RouterModule.forChild(adminRoutes)
		    ],
		    exports: [
		        RouterModule
		    ]
		})
		export class AdminRoutingModule { }

1. create admin module config, importing admin routing config

	src/app/admin/admin.module.ts

		import { NgModule }       from '@angular/core';
		import { CommonModule }   from '@angular/common';
		 
		import { AdminDashboardComponent }  from './admin-dashboard.component';
		...
		import { AdminRoutingModule }       from './admin-routing.module';
		 
		@NgModule({
		  imports: [
		    CommonModule,
		    AdminRoutingModule
		  ],
		  declarations: [
		    AdminComponent,
		    AdminDashboardComponent,
		    AdminFeature1Component,
		    AdminFeature2Component
		  ]
		})
		export class AdminModule {}

	import the AdminModule into app.module.ts and add it to the imports array to register the admin routes.

		import { NgModule }       from '@angular/core';
		import { CommonModule }   from '@angular/common';
		import { FormsModule }    from '@angular/forms';
		
		import { AppComponent }            from './app.component';
		import { PageNotFoundComponent }   from './not-found.component';
		
		import { AppRoutingModule }        from './app-routing.module';
		import { AdminModule }             from './admin/admin.module';
		
		@NgModule({
		  imports: [
		    CommonModule,
		    FormsModule,
		    AdminModule,
		    AppRoutingModule
		  ],
		  declarations: [
		    AppComponent,
		    PageNotFoundComponent
		  ],
		  providers: [
		  ],
		  bootstrap: [ AppComponent ]
		})
		export class AppModule { }

1. Now we get

	![](/images/posts/20180829-router-5.png)

	![](/images/posts/20180829-router-6.png)

1. create a guard service to restrict navigation access

	src/app/services/auth-guard.service.ts

		import { CanActivate, ActivatedRouteSnapshot, RouterStateSnapshot } from "@angular/router";
		import { Injectable } from "@angular/core";
		
		@Injectable()
		export class AuthGuardService implements CanActivate{
		    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot){
		        console.log('you trying to access the router: ', state.url);
		        return true;
		    }
		}

1. add the guard service to admin.module.ts as providers

		import { NgModule } from '@angular/core';
		import { CommonModule } from '@angular/common';
		
		import { AdminDashboardComponent } from './admin-dashboard.component';
		
		import { AdminRoutingModule } from './admin-routing.module';
		import { AdminFeature1Component } from './admin-feature1.component';
		import { AdminComponent } from './admin.component';
		import { AdminFeature2Component } from './admin-feature2.component';
		import { AuthGuardService } from '../services/auth-guard.service';
		
		@NgModule({
		    imports: [
		        CommonModule,
		        AdminRoutingModule
		    ],
		    declarations: [
		        AdminComponent,
		        AdminDashboardComponent,
		        AdminFeature1Component,
		        AdminFeature2Component
		    ],
		    providers:[AuthGuardService] // here
		})
		export class AdminModule { }

1. add the guard service to admin-routing.module.ts

	src/app/admin/admin-routing.module.ts

		import { AdminComponent } from "./admin.component";
		import { Routes, RouterModule } from "@angular/router";
		import { AdminFeature1Component } from "./admin-feature1.component";
		import { AdminFeature2Component } from "./admin-feature2.component";
		import { AdminDashboardComponent } from "./admin-dashboard.component";
		import { NgModule } from "@angular/core";
		import { AuthGuardService } from "../services/auth-guard.service";
		
		const adminRoutes: Routes = [
		    {
		        path: 'admin',
		        component: AdminComponent,
		        canActivate: [AuthGuardService], // here
		        children: [
		            {
		                path: '',
		                children: [
		                    { path: 'feature1', component: AdminFeature1Component },
		                    { path: 'feature2', component: AdminFeature2Component },
		                    { path: '', component: AdminDashboardComponent }
		                ]
		            }
		        ]
		    }
		];
		
		@NgModule({
		    imports: [
		        RouterModule.forChild(adminRoutes)
		    ],
		    exports: [
		        RouterModule
		    ]
		})
		export class AdminRoutingModule { }

1. In real world, we can write a `auth.service.ts` to authenticate user. Then, auth-guard.service.ts call this service to guard the navigation

	e.g. src/app/auth.service.ts

		import { Injectable } from '@angular/core';		
		import { Observable, of } from 'rxjs';
		import { tap, delay } from 'rxjs/operators';
		
		@Injectable()
		export class AuthService {
		  isLoggedIn = false;
		
		  // store the URL so we can redirect after logging in
		  redirectUrl: string;
		
		  login(): Observable<boolean> {
		    return of(true).pipe(
		      delay(1000),
		      tap(val => this.isLoggedIn = true)
		    );
		  }
		
		  logout(): void {
		    this.isLoggedIn = false;
		  }
		}

	e.g. auth-guard.service.ts

		import { Injectable }       from '@angular/core';
		import {
		  CanActivate, Router,
		  ActivatedRouteSnapshot,
		  RouterStateSnapshot
		}                           from '@angular/router';
		import { AuthService }      from './auth.service';
		
		@Injectable()
		export class AuthGuardService implements CanActivate {
		  constructor(private authService: AuthService, private router: Router) {}
		
		  canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): boolean {
		    let url: string = state.url;
		
		    return this.checkLogin(url);
		  }
		
		  checkLogin(url: string): boolean {
		    if (this.authService.isLoggedIn) { return true; }
		
		    // Store the attempted URL for redirecting
		    this.authService.redirectUrl = url;
		
		    // Navigate to the login page with extras
		    this.router.navigate(['/login']);
		    return false;
		  }
		}

## CanDeactivate: handling unsaved changes
this interface can be used to determine whether user can navigate away the current route

Here, we wanna add a deactivate guard to `feature1` router

- way1, declare one guard per component:

	src\app\services\can-feature1-deactivate-guard.service.ts
	
		import { Injectable } from "@angular/core";
		import { AdminFeature1Component } from "../admin/admin-feature1.component";
		import { CanDeactivate, ActivatedRouteSnapshot, RouterStateSnapshot } from "@angular/router";
		import { Observable } from "rxjs";
		
		@Injectable()
		export class CanFeature1DeactivateGuard implements CanDeactivate<AdminFeature1Component> {
		
		  canDeactivate(
		    component: AdminFeature1Component,
		    route: ActivatedRouteSnapshot,
		    state: RouterStateSnapshot
		  ): Observable<boolean> | boolean {    
		    console.log('you are leaving router - ', state.url);
		    return true;
		  }
		}

	update admin.module.ts, add this guard as provider

		import { NgModule } from '@angular/core';
		import { CommonModule } from '@angular/common';
		...
		import { CanFeature1DeactivateGuard } from '../services/can-feature1-deactivate-guard.service';
		
		@NgModule({
		    imports: [
		        CommonModule,
		        AdminRoutingModule
		    ],
		    declarations: [
		        AdminComponent,
		        AdminDashboardComponent,
		        AdminFeature1Component,
		        AdminFeature2Component
		    ],
		    providers:[AuthGuardService, CanFeature1DeactivateGuard] // here
		})

		export class AdminModule { }

	update admin-routing.module.ts, add this guard to monitor the router

		import { NgModule } from "@angular/core";
		...
		import { CanFeature1DeactivateGuard } from "../services/can-feature1-deactivate-guard.service";
		
		const adminRoutes: Routes = [
		    {
		        path: 'admin',
		        component: AdminComponent,
		        canActivate: [AuthGuardService],
		        children: [
		            {
		                path: '',
		                children: [
		                    { path: 'feature1', component: AdminFeature1Component, canDeactivate: [CanFeature1DeactivateGuard] }, //here
		                    { path: 'feature2', component: AdminFeature2Component },
		                    { path: '', component: AdminDashboardComponent }
		                ]
		            }
		        ]
		    }
		];

		@NgModule({
		    imports: [
		        RouterModule.forChild(adminRoutes)
		    ],
		    exports: [
		        RouterModule
		    ]
		})
		export class AdminRoutingModule { }

	using this method, we have to define guard for each component.

- way2, create reusable guard. Using approach, we can just define a interface for all components and a guard based on this interface. Any router needs guard service can implement this interface with specific logic, then add a routing config.  

	1. declare a generic component deactivate guard interface, src\components\can-component-deactivate.component.ts

			import { Observable } from "rxjs";
			
			export interface CanComponentDeactivate {
			    canDeactivate: () => Observable<boolean> | Promise<boolean> | boolean;
			}

		or

			import { Observable } from "rxjs";
			import { ActivatedRouteSnapshot, RouterStateSnapshot } from "@angular/router";
			
			export interface CanComponentDeactivate {
			    canDeactivate: (currentRoute: ActivatedRouteSnapshot, currentState: RouterStateSnapshot) => Observable<boolean> | Promise<boolean> | boolean;
			}

	1. create a reusable deactivate guard, `src\services\can-deactivate-guard.service.ts`

			import { Injectable } from "@angular/core";
			import { CanDeactivate } from "@angular/router";
			import { CanComponentDeactivate } from "../components/can-component-deactivate.component";
			
			@Injectable()
			export class CanDeactivateGuard implements CanDeactivate<CanComponentDeactivate> {
			  canDeactivate(component: CanComponentDeactivate) {
			    return component.canDeactivate ? component.canDeactivate() : true;
			  }
			}

		or

			import { Injectable } from "@angular/core";
			import { CanDeactivate, ActivatedRouteSnapshot, RouterStateSnapshot } from "@angular/router";
			import { CanComponentDeactivate } from "../components/can-component-deactivate.component";
			
			@Injectable()
			export class CanDeactivateGuard implements CanDeactivate<CanComponentDeactivate> {
			  canDeactivate(component: CanComponentDeactivate, currentRoute: ActivatedRouteSnapshot, currentState: RouterStateSnapshot) {
			    return component.canDeactivate ? component.canDeactivate(currentRoute, currentState) : true;
			  }
			}

	Next, component which needs deactivate guard implements the interface
	
		import { Component } from '@angular/core';
		import { CanComponentDeactivate } from '../components/can-component-deactivate.component';
		import { ActivatedRouteSnapshot, RouterStateSnapshot } from '@angular/router';
		
		@Component({
		  template:  `
		    <p>Admin feature 2 </p>
		  `
		})
		export class AdminFeature2Component implements CanComponentDeactivate {
		    canDeactivate(currentRoute: ActivatedRouteSnapshot, currentState: RouterStateSnapshot) {
		        console.log('you are leaving router - ', currentState.url);
		        return true;
		    };
		}
	
	Finally, add the reusable deactivate guard to routing config, module config

	admin-routing.module.ts

		import { AdminFeature1Component } from "./admin-feature1.component";
		import { AdminFeature2Component } from "./admin-feature2.component";
		...
		const adminRoutes: Routes = [
		    {
		        path: 'admin',
		        component: AdminComponent,
		        canActivate: [AuthGuardService],
		        children: [
		            {
		                path: '',
		                children: [
		                    { path: 'feature1', component: AdminFeature1Component},
		                    { path: 'feature2', component: AdminFeature2Component, canDeactivate:[CanDeactivateGuard] }, //here
		                    { path: '', component: AdminDashboardComponent }
		                ]
		            }
		        ]
		    }
		];

	admin.module.ts

		import { NgModule } from '@angular/core';
		import { CommonModule } from '@angular/common';
		...
		@NgModule({
		    imports: [
		        CommonModule,
		        AdminRoutingModule
		    ],
		    declarations: [
		        AdminComponent,
		        AdminDashboardComponent,
		        AdminFeature1Component,
		        AdminFeature2Component
		    ],
		    providers:[AuthGuardService, CanDeactivateGuard]
		})
		export class AdminModule { }
		
		@NgModule({
		    imports: [
		        RouterModule.forChild(adminRoutes)
		    ],
		    exports: [
		        RouterModule
		    ]
		})
		export class AdminRoutingModule { }

# Route resolvers #
Resolves can be used to fetch data before navigating. This is an advanced Angular feature. 

For instance, we have a component to display a list of items and we can click an item to view details. In the list view, we can create a resolver to determine whether an item exists or not. 

	import { Injectable }             from '@angular/core';
	import { Router, Resolve, RouterStateSnapshot,
	         ActivatedRouteSnapshot } from '@angular/router';
	import { Observable }             from 'rxjs';
	import { map, take }              from 'rxjs/operators';
	
	import { Item }  from './item.model'; 
	import { ItemService }  from './item.service';
	 
	@Injectable()
	export class ItemDetailResolver implements Resolve<Item> {
	  constructor(private cs: ItemService, private router: Router) {}
	 
	  resolve(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<Item> {
	    let id = route.paramMap.get('id');
	 
	    return this.cs.getItemDetails(id).pipe(
	      take(1),
	      map(item => {
	        if (item) {
	          return item;
	        } else { // id not found
	          this.router.navigate(['/dashboard']);
	          return null;
	        }
	      })
	    );
	  }
	}

add the resolver to module config

	import { ItemDetailResolver }   from './item-detail-resolver.service';
	
	@NgModule({
	  imports: [
	    RouterModule.forChild(ItemRoutes)
	  ],
	  exports: [
	    RouterModule
	  ],
	  providers: [
	    ItemDetailResolver //here
	  ]
	})
	export class ItemModule { }

# References #
[Routing & Navigation](https://angular.io/guide/router)

[https://alligator.io/angular/route-resolvers/](https://alligator.io/angular/route-resolvers/)