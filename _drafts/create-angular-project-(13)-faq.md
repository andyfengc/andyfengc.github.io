---
layout: post
title: Create Angular v2+ project (8) - FAQ
author: Andy Feng
---

# Prevent Angular escape HTML code #

1. use innerHTML property

	`<div innerHTML="{{propertyName}}"></div>`
	
	or 
	
	`<div [innerHTML]="propertyName"></div>`

1. use DomSanitizer

		...
		import { DomSanitizer } from '@angular/platform-browser';
		export class ComponentA {
		    htmlContent: string = "<p>Content goes here</p>";
		    constructor(private dom: DomSanitizer){}
		
		    openDialog() {
		        htmlContent = this.dom.bypassSecurityTrustHtml(this.htmlContent);
		    }
		}

1. use Pipe

	create a pipe `safehtml.pipe.ts`
		
		import { DomSanitizer } from '@angular/platform-browser';
		import { Pipe, PipeTransform } from '@angular/core';
		
		@Pipe({ name: 'safeHtml'})
		export class SafeHtmlPipe implements PipeTransform {
		    constructor(private dom: DomSanitizer) {}
		    transform(value) {
		        return this.dom.bypassSecurityTrustHtml(value);
		    }
		}	

	declare the pipe in your module file:
		
		@NgModule({
		
		    declarations: [
		        // Other declarations here
		        SafeHtmlPipe
		    ]
		})
		export class AppModule {}

	apply the pipe to component

		...
		@Component({
			template: `<div [innerHTML]="htmlContent | safeHtml"></div>`
		})
		export class ComponentA {
		    htmlContent: string = "<p>Content goes here</p>";
		    constructor(private dom: DomSanitizer){}
		
		    openDialog() {
		    }
		}

Please note that we can use the `DomSanitizer` service to escape some HTML/url/etc. [According to the docs](https://angular.io/docs/ts/latest/guide/security.html#!#bypass-security-apis), we can mark a value as trusted by injecting DomSanitizer and calling one of the following methods:

- bypassSecurityTrustHtml
- bypassSecurityTrustScript
- bypassSecurityTrustStyle
- bypassSecurityTrustUrl
- bypassSecurityTrustResourceUrl

so we can escape our html like this :

constructor(private sanitizer: DomSanitizer) {
  let dangerousHtml = '<script>alert("hello")</script>';
  this.trustedHtml = sanitizer.bypassSecurityTrustHtml(dangerousHtml);
and in you template :

<section class="content" [innerHtml]="trustedHtml"></section>

# Handle navigation change event #
1. In component, inject router instance

		import { Router} from "@angular/router";

		export class ComponentA {
		    constructor(private router: Router) {    }
		}

1. subscribe to the change events either in constructor or in init hooker

		import { Router, NavigationStart, RoutesRecognized, RouteConfigLoadStart, RouteConfigLoadEnd, NavigationCancel, NavigationError, NavigationEnd } from "@angular/router";

		export class ComponentA implements OnInit{
		    constructor(private router: Router) {    }
		
			ngOnInit() {
		        this.router.events.subscribe(event => {
		            if (event instanceof NavigationStart) {
		                //An event triggered when navigation starts.
		            } else if (event instanceof RoutesRecognized) {
		                //An event triggered when the Router parses the URL and the routes are recognized.
		            } else if (event instanceof RouteConfigLoadStart) {
		                //An event triggered before the Router lazy loads a route configuration.
		            } else if (event instanceof RouteConfigLoadEnd) {
		                //An event triggered after a route has been lazy loaded.
		            } else if (event instanceof NavigationEnd) {
		                if (event.url.indexOf('/current-route-path') < 0) {
		                    // navigate to another router		                    
							this.cleanUp();
		                }
		                //An event triggered when navigation ends successfully.
		            } else if (event instanceof NavigationCancel) {
		                //An event triggered when navigation is canceled. This is due to a Route Guard returning false during navigation.
		            } else if (event instanceof NavigationError) {
		                //An event triggered when navigation fails due to an unexpected error.
		            }
		        });
		    }
			// some clean up...
			cleanUp() : void {...}
		}

# Get hostname #
In the component/service, inject DOCUMENT class instance

	import {DOCUMENT} from '@angular/platform-browser';
	...
	export class SomeComponent{
	
		constructor(@Inject(DOCUMENT) private document) {
		}
		
		someMethod() {
			var url = document.location.protocol + "//" + document.location.host + "/api/orders";
		}
	}

# Resolve ExpressionChangedAfterItHasBeenCheckedError
It happens when parent component specify property vlue of child component. Also, child component update its property value asynchorously. At that moment, parent component has not aware of property changes of child component. 

Force Change Detection cycle Inside your parent component

	import { Component, Input, ChangeDetectorRef } from '@angular/core';
	export class ParentComponent{
		constructor(private cd: ChangeDetectorRef) {
		}
		ngAfterViewInit() {
		    this.cd.detectChanges();	
		}
	}

# Add HTTP Interceptor
1. write a HTTP interceptor

		import { Injectable } from '@angular/core';
		import {
		  HttpRequest,
		  HttpHandler,
		  HttpEvent,
		  HttpInterceptor,
		  HttpResponse,
		  HttpErrorResponse,
		} from '@angular/common/http';
		 
		import { Observable } from 'rxjs/Observable';
		import 'rxjs/add/operator/do';
		 
		@Injectable()
		export class RequestInterceptor implements HttpInterceptor {
		 
		  constructor() {}
		 
		  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
		
		    return next.handle(request).do((event: HttpEvent<any>) => {}, (err: any) => {
		      if (err instanceof HttpErrorResponse) {
		        // do error handling here
		      }
		    });
		  }
		}

	or

		import { Injectable } from '@angular/core';
		import { HttpRequest, HttpHandler, HttpEvent,
		    HttpResponse, HttpErrorResponse } from '@angular/common/http';
		import { Observable } from 'rxjs';
		import { tap } from 'rxjs/operators';
		
		@Injectable()
		export class HttpInterceptor implements HttpInterceptor {
		  intercept(request: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
		    return next.handle(request).pipe(
		      tap(
		        event => event instanceof HttpResponse ? 'succeeded' : '',
		        error => {
		          if (error instanceof HttpErrorResponse) {
		            console.log('http error intercepted')
		          }
		        })
		    );
		  }
		}

1. modify app.module.ts

		import { RequestInterceptor } from './services/request-interceptor.service';
		
		@NgModule({
		  ...
		  providers: [
		    ...
		    {
		      provide: HTTP_INTERCEPTORS,
		      useClass: RequestInterceptor,
		      multi: true
		    }
		  ],
		  ...
		})
		export class AppModule { }

# How to Import json into TypeScript
## way1: make a simple http get request ##
1. make sure import HttpModule in app.module.ts

		import { NgModule }      from '@angular/core';
		import { BrowserModule } from '@angular/platform-browser';
		import { HttpModule }      from '@angular/http';
		import { AppComponent }   from './app.component';
		 
		@NgModule({
		  imports:      [ BrowserModule, HttpModule ],
		  declarations: [ AppComponent],
		  bootstrap:    [ AppComponent ]
		})
		export class AppModule { }

1. put your JSON file in a location accessible via Http, for example, `/data` folder. Then, use `http.get()`

		import {Http} from '@angular/http';
		import { Component } from '@angular/core';
		 
		@Component({
		  selector: 'my-app',
		  template: ``,
		})
		export class AppComponent { 	 
		    data;	 
		    constructor(private http:Http) {
		        this.http.get('data/data.json')
		                .subscribe(res => this.data = res.json());
		    }
		}

## way2 ##
1. add "your-json-dir" into angular.json file (optional)

		"assets": [
		  "src/assets",
		  "src/your-json-dir"
		]

1. create or edit typings.d.ts file (at your project root)

	![](/images/posts/20181122-angular-1.png)

		declare module "*.json" {
		    const value: any;
		    export default value;
		}

1. Then, in our code, simply import the file by using this relative path:

		import * as data from './example.json'; 
		const value = (<any>data).key;
		console.log(value); 

# Use cookie #
1. install `npm install ngx-cookie-service --save`

1. Add to our module, `app.module.ts`: 

		import { BrowserModule } from '@angular/platform-browser';
		import { NgModule } from '@angular/core';
		import { FormsModule } from '@angular/forms';
		import { HttpModule } from '@angular/http';	 
		import { AppComponent } from './app.component';
		...
		import { CookieService } from 'ngx-cookie-service';
		 
		@NgModule({
		  declarations: [ AppComponent ],
		  imports: [ BrowserModule, FormsModule, HttpModule ],
		  providers: [ CookieService ],
		  bootstrap: [ AppComponent ]
		})
		export class AppModule { }

1. import and inject it into a component:

		import { Component, OnInit } from '@angular/core';
		import { CookieService } from 'ngx-cookie-service';
		 
		@Component({
		  selector: 'demo-root',
		  templateUrl: './app.component.html',
		  styleUrls: [ './app.component.scss' ]
		})
		export class AppComponent implements OnInit {
		  cookieValue = 'UNKNOWN';
		 
		  constructor( private cookieService: CookieService ) { }
		 
		  ngOnInit(): void {
		    this.cookieService.set( 'Test', 'Hello World' );
		    this.cookieValue = this.cookieService.get('Test');
		  }
		}

# How to inject service into model class #
1. declare class as injectable

	add `@Injectable()` to MyClass and provide MyClass like `providers: [MyClass]` in a component or NgModule.
	
	When you then inject MyClass somewhere, a MyService instance gets passed to MyClass when it is instantiated by DI (before it is injected the first time).

1. configure a custom injector like
 
		export class MyClass{
			constructor(private injector:Injector) { 
			  let resolvedProviders = ReflectiveInjector.resolve([MyClass]);
			  let childInjector = ReflectiveInjector.fromResolvedProviders(resolvedProviders, this.injector);
			
			  let myClass : MyClass = childInjector.get(MyClass);
			}
		}

	This way myClass will be a MyClass instance, instantiated by Angulars DI, and myService will be injected to MyClass when instantiated.

1. create the instance yourself:

	constructor(ms:myService)
	let myClass = new MyClass(ms);

1. declare a factory service to generate model objects. Then use it to create new objects

		// Component needing model objects
		@Component(...)
		class SomeComponent {
		    constructor(factory: FactoryService){
		        const sell = factory.getNewModelObject();
		}
	
		// factory
		@Injectable()
		class FactoryService {
		    constructor(private _userService: UserService){ 
		    }
		
		    getNewModelObject(){
		       const model = new Model();
		       model.userId = this._userService.id;
		       return model;
		    }
		}
		
		// Your sell class remains dumb (btw Sale would be a much better name for a model)
		export class Model {
		  userId: string;
		  price: number;
		}

1. change the service to static methods. In model class, call static methods directly.

# How to call a component method from service #
Usually, we cannot do this directly. We cannot inject the component inside a service constructor or any other component's constructor. 

But we can have some solutions to call a method of component from service

1. Use `Subject` or `BehaviorSubject` in service and then subscribe to it from the component.

	e.g. 

	service
	
		@Injectable()
		export class AuthGuardService implements CanActivate {
		    private authorized: Subject<boolean> = new Subject<boolean>();
			public authorized$ : this.authorized.asObservable();
		
		    constructor(private authService: AuthenticationService) {
		
		    }
		    
		    canActivate(route: ActivatedRouteSnapshot, state: RouterStateSnapshot): Observable<boolean> | boolean {
		        if (...) {
		            this.authorized.next(true);
		            return true;
		        }
		        else {
		            this.authorized.next(false);
		            return false;
		        }
		    }
		}

	component

		export class AppComponent{
		
		  constructor(
		    ...
		    , public authGuardService: AuthGuardService
		  ) {
		    this.authGuardService.authorized$.subscribe(authorized => {
		      if (!authorized) this.showUnthorized();
		    })
		  }
		  private showUnthorized(): void {
		    this.openDialog(new DialogModel('Warning', "Sorry, you are not authorized to access because ..."));
		  }		
		}


# How to pass string literal to the Input() property of component #
we have a `PageNavComponent` component

	import { Component, Input,OnInit } from '@angular/core';
	@Component({
	  selector: 'app-page-nav',
	  templateUrl: './page-nav.component.html',
	  styleUrls: ['./page-nav.component.scss']
	})
	export class PageNavComponent implements OnInit {
		@Input() title: string;
		constructor(
			public uiService : UiService
		) { 
		}
	}

In another component who references to this:

method 1:

	<app-page-nav title="{{uiService.getLabelAsync('backup.title') | async}}"></app-page-nav>

method 2: 

	<app-page-nav title="string"></app-page-nav>

method 3: 

	<app-page-nav [title]="'string'"></app-page-nav>

# Upgrade 7.x to 8.x #

`ng update @angular/cli @angular/core`

Upgrading Angular Material

`ng update @angular/material`

Replace /deep/ with ::ng-deep in `styles.scss`

If you use `ViewChild` or `ContentChild`, change all of them to `@ViewChild('foo', {static: false}) foo : ElementRef;`.


# References
[Global HTTP error catching in Angular 4.3+](https://hackernoon.com/global-http-error-catching-in-angular-4-3-9e15cc1e0a6b)

[https://hackernoon.com/import-json-into-typescript-8d465beded79](https://hackernoon.com/import-json-into-typescript-8d465beded79)

[How to inject Service into class (not component)](https://stackoverflow.com/questions/41432388/how-to-inject-service-into-class-not-component)

[Getting dependency from Injector manually inside a directive](https://stackoverflow.com/questions/40536409/getting-dependency-from-injector-manually-inside-a-directive/40537194#40537194)

[How to call component method from service? (angular2)](https://stackoverflow.com/questions/40788458/how-to-call-component-method-from-service-angular2)

[Component Interaction](https://angular.io/guide/component-interaction#!#bidirectional-service)