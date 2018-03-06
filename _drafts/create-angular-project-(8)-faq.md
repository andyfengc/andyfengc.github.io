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