---
layout: post
title: Create Angular v2+ project
author: Andy Feng
---

## Introduction ##
Angular is a framework for building client applications in HTML and either JavaScript or a language like TypeScript that compiles to JavaScript.

The framework consists of several libraries, some of them core and some optional. Typically, we build Angular applications by composing HTML templates in markup format.  

Steps:

1. Build a project via cli
1. Create component classes including view logic (.ts files, define properties) and they manage HTML templates(.html files) and component stylesheets(.css files)
2. Add application logic in services such as HTTP requests, calculations. Call services from components
3. Box components and services in modules, then import to the root module
4. Configure routing and import to the root module
5. Launch the app by bootstrapping the root module.
1. Debug the project

Environment: 

1. Node.js v8+
2. Visual Studio Code 

## Create a sample project via angular/cli ##

1. Install node.js

1. install angular/cli
	` npm install -g @angular/cli`

1. create a new project
	`ng new angular-demo`

	![](/images/posts/20180102-angular2-cli-1.png)

	![](/images/posts/20180102-angular2-cli-8.png)

1.  enter angular-demo folder, `ng serve` or `ng serve --port 6000`, by default, the port is 4200

	![](/images/posts/20180102-angular2-cli-2.png)

1. open browser, `http://localhost:4200`

	![](/images/posts/20180102-angular2-cli-3.png)

## Create component ##
1. create a new component heroes: `ng generate component heroes`

	![](/images/posts/20180102-angular2-cli-4.png)

1. a new folder heroes will be created with components source code in it 

	![](/images/posts/20180102-angular2-cli-5.png)

	heros.component.ts

		import { Component, OnInit } from '@angular/core';
		
		@Component({
		  selector: 'app-heroes',
		  templateUrl: './heroes.component.html',
		  styleUrls: ['./heroes.component.css']
		})
		export class HeroesComponent implements OnInit {		
		  constructor() { }
		
		  ngOnInit() {
		  }		
		} 

	heroes.component.spec.ts

		import { async, ComponentFixture, TestBed } from '@angular/core/testing';
		
		import { HeroesComponent } from './heroes.component';
		
		describe('HeroesComponent', () => {
		  let component: HeroesComponent;
		  let fixture: ComponentFixture<HeroesComponent>;
		
		  beforeEach(async(() => {
		    TestBed.configureTestingModule({
		      declarations: [ HeroesComponent ]
		    })
		    .compileComponents();
		  }));
		
		  beforeEach(() => {
		    fixture = TestBed.createComponent(HeroesComponent);
		    component = fixture.componentInstance;
		    fixture.detectChanges();
		  });
		
		  it('should create', () => {
		    expect(component).toBeTruthy();
		  });
		}); 

1. app.module.ts will be updated

		import { BrowserModule } from '@angular/platform-browser';
		import { NgModule } from '@angular/core';
		
	
		import { AppComponent } from './app.component';
		import { HeroesComponent } from './heroes/heroes.component';
		
		
		@NgModule({
		  declarations: [
		    AppComponent,
		    HeroesComponent
		  ],
		  imports: [
		    BrowserModule
		  ],
		  providers: [],
		  bootstrap: [AppComponent]
		})
		export class AppModule { } 

## Create a new service ##
1. `ng generate service services\hero`

	![](/images/posts/20180102-angular2-cli-6.png)

1. A new folder services will be created with service source code in it 

	![](/images/posts/20180102-angular2-cli-7.png)

	hero.service.ts

		import { Injectable } from '@angular/core';
		
		@Injectable()
		export class HeroService {		
		  constructor() { }		
		} 

	hero.service.spec.ts

		import { TestBed, inject } from '@angular/core/testing';	
		import { HeroService } from './hero.service';
		
		describe('HeroService', () => {
		  beforeEach(() => {
		    TestBed.configureTestingModule({
		      providers: [HeroService]
		    });
		  });
		
		  it('should be created', inject([HeroService], (service: HeroService) => {
		    expect(service).toBeTruthy();
		  }));
		}); 

## Create routing ##
1. `ng generate module app-routing --flat --module=app`
	> `--flat` puts the file in src/app instead of its own folder.  
	> `--module=app` tells the CLI to register it in the imports array of the AppModule.

1. a app-routing.module.ts routing config file will be created and app.module.ts will be updated

	![](/images/posts/20180102-angular2-cli-9.png)
	![](/images/posts/20180102-angular2-cli-10.png)

	app-routing.module.ts

		import { NgModule } from '@angular/core';
		import { CommonModule } from '@angular/common';
		
		@NgModule({
		  imports: [
		    CommonModule
		  ],
		  declarations: []
		})
		export class AppRoutingModule { } 

	app.module.ts

		...
		import { AppRoutingModule } from './app-routing.module';
		
		@NgModule({
		  declarations: [
		    AppComponent,
		    ...
		  ],
		  imports: [
		    ...
		    AppRoutingModule
		  ],
		  providers: [],
		  bootstrap: [AppComponent]
		})
		export class AppModule { } 

	Next, we will configure the router with Routes in the RouterModule

1. First, import `RouterModule, Routes`. Also, remove the @NgModule.declarations array and CommonModule because we don't declare components in a routing module .
 
	app-routing.module.ts

		import { NgModule }             from '@angular/core';
		import { RouterModule, Routes } from '@angular/router';
		
		@NgModule({
		  exports: [ RouterModule ]
		})
		export class AppRoutingModule {}

1. Add routes

	A typical Angular Route has two properties:	
	> path: a string that matches the URL in the browser address bar.  
	> component: the component that the router should create when navigating to this route.

	e.g. we create route for HeroesComponent. We hope `localhost:4200/heroes` will invoke HeroesComponent

	app-routing.module.ts

		import { HeroesComponent }      from './heroes/heroes.component';
		...
		const routes: Routes = [
		  { path: 'heroes', component: HeroesComponent }
		];

1. Add more routes
 
 	create new components:
	> `ng generate component hero-detail`
	> `ng generate component dashboard`

	update app-routing.module.ts

		import { DashboardComponent }   from './dashboard/dashboard.component';
		import { HeroDetailComponent }  from './hero-detail/hero-detail.component';
		...
		const routes: Routes = [
			...
			{ path: '', redirectTo: '/dashboard', pathMatch: 'full' }, // default route
			{ path: 'dashboard', component: DashboardComponent },
			{ path: 'detail/:id', component: HeroDetailComponent }, // parameterized route
		];	
	

1. Then, initialize the router and start it listening for browser location changes.

	app-routing.module.ts
		@NgModule({
			...
			imports: [ RouterModule.forRoot(routes) ],
		})

1. Finally, add RouterOutlet in template pages. Open the AppComponent template replace the <app-heroes> element with a <router-outlet> element.

	app.component.html

		...
		<h1>{{title}}</h1>
		<nav>
  			<a routerLink="/dashboard">Dashboard</a>
		  	<a routerLink="/heroes">Heroes</a>
		</nav>
		<router-outlet></router-outlet>
		...


	> `<router-outlet>` tells the router where to display routed views. Please note that RouterOutlet is already available to the AppComponent because AppModule imports AppRoutingModule which exported RouterModule.
	> `<routerLink>` is the selector for the RouterLink directive that turns user clicks into router navigations

	![](/images/posts/20180102-angular2-cli-11.png)
	![](/images/posts/20180102-angular2-cli-12.png)

1. Grab parameter from router

	update heroes.component.html
	
		...
		<ul class="heroes">
		  <li *ngFor="let hero of heroes">
		    <a routerLink="/detail/{{hero.id}}">
		      <span class="badge">{{hero.id}}</span> {{hero.name}}
		    </a>
		  </li>
		</ul>
		...

	because `detail/:id` is routed to HeroDetailComponent, we need to parse data inside this component

	hero-detail.component.ts

		...
		import { ActivatedRoute } from '@angular/router';
		import { Location } from '@angular/common';		
		
		@Component({
		  ...
		})
		export class HeroDetailComponent implements OnInit {
			constructor(
			  private route: ActivatedRoute,
			  private heroService: HeroService,
			  private location: Location
			) {}
			ngOnInit(): void {
				this.getHero();
			}
			getHero(): void {
				const id = +this.route.snapshot.paramMap.get('id'); // + can convert string to number
				this.heroService.getHero(id)
				.subscribe(hero => this.hero = hero);
			}
			goBack(): void {
				this.location.back();
			}
		}

## Add HTTP services ##
`HttpClient` is Angular's mechanism for communicating with a remote server over HTTP.

1. Install the module by importing `HttpClientModule` to `AppModule`

	app.module.ts
	
		import { HttpClientModule } from "@angular/common/http";
		@NgModule({
		  declarations: [
		    ...
			// components list
		  ],
		  imports: [
		    BrowserModule
		    ...
		    , HttpClientModule
		  ],
		}

1. Use Http service to handle Http requests

	After installing the module, the app will make requests to and receive responses from the HttpClient.

	hero.service.ts
	
		import { Injectable } from "@angular/core";
		import { HttpClient, HttpHeaders } from "@angular/common/http";
		import { Observable } from 'rxjs/Observable';
		import { of } from 'rxjs/observable/of';
		
		@Injectable()
		export class HeroService {
			private heroesUrl = 'api/heroes';  // URL to web api

			constructor(
			  private http: HttpClient,
			  private messageService: MessageService) { }

			// get mock data
			//getHeroes(): Observable<Hero[]> {
			//  return of(HEROES);
			//}

			getHeroes (): Observable<Hero[]> {
			  return this.http.get<Hero[]>(this.heroesUrl)
			}
		}

	All HttpClient methods return an RxJS Observable of something (Hero array).

1. error handling

	If things go wrong when we're getting data from a remote server. The HeroService.getHeroes() method should catch errors and do something appropriate.
	
	To catch errors, you "pipe" the observable result from http.get() through an RxJS catchError() operator. Then,  extend the observable result with the .pipe() method and give it a catchError() operator

	hero.service.ts

		import { catchError, map, tap } from 'rxjs/operators';
		...
		export class HeroService {
		...
			getHeroes (): Observable<Hero[]> {
			  return this.http.get<Hero[]>(this.heroesUrl)
			    .pipe(
					tap(heroes => this.log(`fetched heroes`)),
			      	catchError(this.handleError('getHeroes', []))
			    );
			}
			/**
			 * Handle Http operation that failed. Let the app continue.
			 * @param operation - name of the operation that failed
			 * @param result - optional value to return as the observable result
			 */
			private handleError<T> (operation = 'operation', result?: T) {
			  return (error: any): Observable<T> => {
			 
			    // TODO: send the error to remote logging infrastructure
			    console.error(error); // can log to other data repository
			 
			    // TODO: better job of transforming error for user consumption
			    this.log(`${operation} failed: ${error.message}`); // display in the client
			 
			    // Let the app keep running by returning an empty result.
			    return of(result as T);
			  };
			}
		...
		}

	Here, getHeroes() still returns an Observable<Hero[]> ("an observable of Hero array")

1. get data in component

	HeroService returns an Observable<Hero[]> and we need to subscribe it and render it in view component.

		...
		import { HeroService } from '../hero.service';
		@Component({
		  ...
		})
		export class HeroesComponent implements OnInit {
		  heroes: Hero[];
		 
		  constructor(private heroService: HeroService) { }
		 
		  ngOnInit() {
		    this.getHeroes();
		  } 
		
		  getHeroes(): void {
		    this.heroService.getHeroes()
		        .subscribe(heroes => this.heroes = heroes);
		  }
		}
		... 

1. Add hero update support

	update hero.service.ts
	
	/** PUT: update the hero on the server */
	updateHero (hero: Hero): Observable<any> {
	  return this.http.put(this.heroesUrl, hero, httpOptions).pipe(
	    tap(_ => this.log(`updated hero id=${hero.id}`)),
	    catchError(this.handleError<any>('updateHero'))
	  );
	}

	The HttpClient.put() method takes three parameters
	- the URL
	- the data to update (the modified hero in this case)
	- request options. e.g.
	
			const httpOptions = {
			  headers: new HttpHeaders({ 'Content-Type': 'application/json' })
			}; 

	Then, call save() method in hero detail component

	hero-detail.component.html

		<button (click)="save()">save</button>
	
	hero-detail.component.ts

		save(): void {
		   this.heroService.updateHero(this.hero)
		     .subscribe(() => this.goBack());
		 }

1. Add hero add support

	update hero.service.ts
	
		/** POST: add a new hero to the server */
		addHero (hero: Hero): Observable<Hero> {
		  return this.http.post<Hero>(this.heroesUrl, hero, httpOptions).pipe(
		    tap((hero: Hero) => this.log(`added hero w/ id=${hero.id}`)),
		    catchError(this.handleError<Hero>('addHero'))
		  );
		}

	Then, call add() method in hero detail component

	hero-detail.component.html

		<input #heroName />
		<button (click)="add(heroName.value); heroName.value=''">add</button>
	
	hero-detail.component.ts

		add(name: string): void {
		  name = name.trim();
		  if (!name) { return; }
		  this.heroService.addHero({ name } as Hero)
		    .subscribe(hero => {
		      this.heroes.push(hero);
		    });
		}

1. Add hero deletion support

	update hero.service.ts
	
		/** DELETE: delete the hero from the server */
		deleteHero (hero: Hero | number): Observable<Hero> {
		  const id = typeof hero === 'number' ? hero : hero.id;
		  const url = `${this.heroesUrl}/${id}`;
		
		  return this.http.delete<Hero>(url, httpOptions).pipe(
		    tap(_ => this.log(`deleted hero id=${id}`)),
		    catchError(this.handleError<Hero>('deleteHero'))
		  );
		}

	Then, call delete() method in hero list component

	hero-detail.component.html

		<ul class="heroes">
		  <li *ngFor="let hero of heroes">
		    <a routerLink="/detail/{{hero.id}}">
		      <span class="badge">{{hero.id}}</span> {{hero.name}}
		    </a>
		    <button class="delete" title="delete hero"
		    (click)="delete(hero)">x</button>
		  </li>
		</ul>

	heroes.component.ts

		delete(hero: Hero): void {
		  this.heroes = this.heroes.filter(h => h !== hero);
		  this.heroService.deleteHero(hero).subscribe();
		}

1. Add hero search support

	update hero.service.ts

		/* GET heroes whose name contains search term */
		searchHeroes(term: string): Observable<Hero[]> {
		  if (!term.trim()) {
		    // if not search term, return empty hero array.
		    return of([]);
		  }
		  return this.http.get<Hero[]>(`api/heroes/?name=${term}`).pipe(
		    tap(_ => this.log(`found heroes matching "${term}"`)),
		    catchError(this.handleError<Hero[]>('searchHeroes', []))
		  );
		}

	add a search component `ng generate component hero-search`

	![](/images/posts/20180102-angular2-cli-13.png)

	update hero-search.component.html

		<input #searchBox id="search-box" (keyup)="search(searchBox.value)" />		
		<ul>
			<li *ngFor="let hero of heroes$ | async" >
			  <a routerLink="/detail/{{hero.id}}">
			    {{hero.name}}
			  </a>
			</li>
		</ul>

	> The $ is a convention that indicates heroes$ is an Observable, not an array.   
	> The *ngFor can't do anything with an Observable. But there's also a pipe character (|) followed by async, which identifies Angular's AsyncPipe.   
	> The AsyncPipe subscribes to an Observable automatically so you won't have to do so in the component class.

	update hero-search.component.ts

		...
		import { Observable } from 'rxjs/Observable';
		import { Subject }    from 'rxjs/Subject';
		import { of }         from 'rxjs/observable/of';
		import {
		   debounceTime, distinctUntilChanged, switchMap
		 } from 'rxjs/operators';
		import { Hero } from '../hero';
		import { HeroService } from '../hero.service';
		
		@Component({
		  ...
		})
		export class HeroSearchComponent implements OnInit {
			heroes$: Observable<Hero[]>; // declare heroes$ as an Observable
			private searchTerms = new Subject<string>();
			
			constructor(private heroService: HeroService) {}
			
			// Push a search term into the observable stream.
			search(term: string): void {
				this.searchTerms.next(term);
			}
			
			ngOnInit(): void {
				this.heroes$ = this.searchTerms.pipe(
				// wait 300ms after each keystroke before considering the term
				debounceTime(300),
			
				// ignore new term if same as previous term
				distinctUntilChanged(),
				
				// switch to new search observable each time the term changes
				switchMap((term: string) => this.heroService.searchHeroes(term)),
			);
			}
		}

	> - A Subject is both a source of observable values and an Observable itself. You can subscribe to a Subject as you would any Observable.   
	> - You can also push values into that Observable by calling its next(value) method as the search() method does.
	> - debounceTime(300) waits until the flow of new string events pauses for 300 milliseconds before passing along the latest string. You'll never make requests more frequently than 300ms.   
	> - distinctUntilChanged ensures that a request is sent only if the filter text changed.   
	> - switchMap() calls the search service for each search term that makes it through debounce and distinctUntilChanged. It cancels and discards previous search observables, returning only the latest search service observable.

## Define feature modules ##
We could import and declare all components in the root module. Or, we can define feature modules to group and encapsulate components, then import to the root module.

### A typical root module ###

	import { NgModule }      from '@angular/core';
	import { BrowserModule } from '@angular/platform-browser';
	import { AppComponent } from './app.component';

	@NgModule({
	  imports:      [ BrowserModule ],
	  providers:    [],
	  declarations: [ AppComponent ],
	  exports:      [],
	  bootstrap:    [ AppComponent ]
	})
	export class AppModule { }

- declarations - the view classes that belong to this module. Angular has three kinds of view classes: components, directives, and pipes.
- exports - the subset of declarations that should be visible and usable in the component templates of other modules.
- imports - other modules whose exported classes are needed by component templates declared in this module.
- providers - creators of services that this module contributes to the global collection of services; they become accessible in all parts of the app.
- bootstrap - the main application view, called the root component, that hosts all other app views. Only the root module should set this bootstrap property.

### Create a feature module ###

### Import feature module to root module ###

## Debug the project ##
1. Install `Debugger for Chrome` in visual studio code

	![](/images/posts/20180119-angular-debug-1.png)

1. Create `.vscode/launch.json` file to enable debugger

		{
		    "version": "0.1.0",
		    "configurations": [
		        {
		            "name": "Launch localhost",
		            "type": "chrome",
		            "request": "launch",
		            "url": "http://localhost:4200",
		            "webRoot": "${workspaceFolder}/wwwroot"
		        },
		        {
		            "name": "Launch index.html (disable sourcemaps)",
		            "type": "chrome",
		            "request": "launch",
		            "sourceMaps": false,
		            "file": "${workspaceFolder}/index.html"
		        },
		    ]
		}

	Here, we specify visual studio to open a new Chrome window (request=launch) for debugging purpose. 

1. In visual studio code, we start the serve by Terminal > `ng serve` and add some debug breakpoints

	![](/images/posts/20180119-angular-debug-2.png)

1. Then, click the `Start debuggning` button in debug view. A chrome will be opened automatically, press f12 to open developer tools, then refresh the page.

	![](/images/posts/20180119-angular-debug-3.png)
	
## Install lodash ##

`npm install --save lodash`

`npm install --save @types/lodash`

Then, in your .ts file:

`import * as _ from "lodash";`

![](/images/posts/20180125-angular-lodash-1.png)

Next, simply call `_.lodash_function()`
