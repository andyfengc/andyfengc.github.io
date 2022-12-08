---
layout: post
title: Create Angular v2+ project (6) - service
author: Andy Feng
---

# Create singleton service #
There are two ways to make a service a singleton in Angular:

1. Declare that the service should be provided in the application root. This is the preferred way and will create the uniqueroot service instance.

	src/app/user.service.ts

		import { Injectable } from '@angular/core';
		
		@Injectable({
		  providedIn: 'root',
		})
		export class UserService {
		}

	In component, import it and add it to the component's constructor

		import { UserService } from '../user.service';
		
		@Component({
		  selector: 'sample',
		  templateUrl: '../sample.component.html',
		  styleUrls: ['../sample.component.scss']
		})
		export class SampleComponent implements OnInit {		
			constructor(private userService: UserService) { }
			...
		}

	On the other hand, to use a service as a new instance in each component (per instance per component, not singleton), we need to add `service` as a Provider to our component:

		import { Service } from '../service';
		
		@Component({
		  selector: 'sample',
		  templateUrl: '../sample.component.html',
		  styleUrls: ['../sample.component.scss'],
		  providers: [Service]
		})
		export class SampleComponent implements OnInit {		
			constructor(private userService: UserService) { }
			...
		}

1. Include the service in the AppModule or in a module that is only imported by the AppModule.

	app.module.ts

		@NgModule({
		  declarations: [
		    AppComponent,
		    NotFoundComponent,
		    SearchComponent,
		    ...
		    
		  ],
		  imports: [
		    BrowserModule,
		    FormsModule,
		    HttpClientModule,
		    ...
		  ],
		  providers: [
		    UserService,
			...
		  ],
		  bootstrap: [AppComponent],
		  entryComponents: [
		    ...
		  ]
		})
		export class AppModule { }

# References #

