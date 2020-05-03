---
layout: post
title: Create Angular v2+ project (3) - Angular Material
author: Andy Feng
---

## Introduction ##

## Installation and configuration ##
1. create a new Angular app via [Angular CLI.](https://cli.angular.io)

1. Install angular material package via npm

	`npm install --save @angular/material @angular/cdk @angular/animations`

	or

	`ng add @angular/material`

1. Import module

		import {BrowserAnimationsModule} from '@angular/platform-browser/animations';
		...
		@NgModule({
		  ...
		  imports: [BrowserAnimationsModule],
		  ...
		})
		export class AppModule { ...}

1. Add gesture support. 

	Some components (mat-slide-toggle, mat-slider, matTooltip) rely on HammerJS for gestures. In order to get the full feature of these components, we need to install HammerJS.

	`npm install --save hammerjs`

	After installing, import it on your app's entry point (e.g. src/main.ts).
	
	`import 'hammerjs';`

1. Add components
	Next, we need to import the NgModule for each component we want to use. e.g. import MatButtonModule, MatCheckboxModule
	
	way1
	
		import {MatButtonModule, MatCheckboxModule} from '@angular/material';
		
		@NgModule({
		  ...
		  imports: [
			BrowserModule,
			FormsModule,
			HttpModule,
			BrowserAnimationsModule,
			MatButtonModule,
			MatCheckboxModule],
		  	...
		})
		export class AppModule { }
	
	way2: 
	
	create a separate NgModule that imports all of the Angular Material components that we will use in our application. We can then include this module wherever we'd like to use the components.
	
		import {MatButtonModule, MatCheckboxModule} from '@angular/material';
		
		@NgModule({
		  imports: [MatButtonModule, MatCheckboxModule],
		  exports: [MatButtonModule, MatCheckboxModule],
		})
		export class MyAngularMaterialModule { }

1. Add a theme
	Including a theme is required to apply styles to our application.

	We can include one of Angular Material's prebult themnes globally in our application. 

	- If we are using Angular CLI, we can add this to your styles.css:

		`@import "~@angular/material/prebuilt-themes/indigo-pink.css";`

	- If we are not using the Angular CLI, we can include a prebuilt theme via a <link> element in `index.html`

## Add Angular Material component ##

Here, let's assume we use a shared module to import/export Angular Material specific components

angular-material.module.ts is the shared module

	import { NgModule } from '@angular/core';
	import { MatButtonModule } from '@angular/material/button';
	import { MatCardModule } from "@angular/material/card";
	import { MatGridListModule } from "@angular/material/grid-list";
	import { MatInputModule } from "@angular/material/input";
	import { MatDialogModule } from "@angular/material/dialog";
	import { 
	    MatTabsModule, 
	    MatStepperModule, 
	    MatProgressSpinnerModule ,
	    MatSelectModule
	} from '@angular/material';
	
	@NgModule({
	    imports: [
	        MatButtonModule,
	        MatCardModule,
	        MatGridListModule, 
	        MatInputModule,
	        MatDialogModule,
	        MatTabsModule,
	        MatStepperModule,
	        MatProgressSpinnerModule,
	        MatSelectModule
	    ],
	    exports: [
	        MatButtonModule,
	        MatCardModule,
	        MatGridListModule,
	        MatInputModule,
	        MatDialogModule,
	        MatTabsModule,
	        MatStepperModule,
	        MatProgressSpinnerModule,
	        MatSelectModule
	    ],
	})
	export class AngularMaterialModule { }

app.module.ts imports this module

	import { BrowserModule } from '@angular/platform-browser';
	import { NgModule } from '@angular/core';
	import { FormsModule } from '@angular/forms';
	import { HttpClientModule } from "@angular/common/http";
	import { AngularMaterialModule } from "./angular-material.module";
	import { AppRoutingModule } from './app-routing.module';
	import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
	import { AppComponent } from './app.component';
	...
	@NgModule({
	  declarations: [
	    AppComponent
	  ],
	  imports: [
	    BrowserModule,
	    FormsModule,
	    HttpClientModule,
	    AppRoutingModule,
	    BrowserAnimationsModule,
	    AngularMaterialModule // imports here
	  ],
	  providers: [],
	  bootstrap: [AppComponent],
	  entryComponents: []
	})
	export class AppModule {
	}

Whenever we wants to add a Angular Material component, we just need to import/export in angular-material.module.ts

### Add select component ###
Create a new component, grab data to initialize <mat-option>, then set the selected value of <mat-select>
	
	import { Component, OnInit, Inject, Input, Output } from "@angular/core";
	import { AccessRequest } from '../models/access-request.model'; // a data model
	import { ServiceBase } from "../services/service.base"; // service
	
	@Component({
	    selector: 'my-component',
	    templateUrl: 'my-component.component.html',
	    styles: [ ``]
	})
	
	export class MyComponent implements OnInit {
	    @Input() selectedRequest: AccessRequest;
	    requests: AccessRequest[] = [];

		constructor(protected service: ServiceBase) {}
	
	    ngOnInit(): void {
	        this.service.getRequests()
	        .subscribe(data => {
	            this.requests = []
	            data.forEach(element => {
	                this.requests.push(element);
	            })
	        });
	    }
	
	    processRequest() : void{
	        this.service.processRequest(this.selectedRequest)
	        .subscribe(data => {
	            console.log('Processed request successfully!');
	        }, error => {
	            console.log('Failed to process request!');
	        })
	    }	
	}

Create the template 

	<div class="row">
	    <mat-form-field class="col-xs-8">
	            <mat-select placeholder="All requests" [(value)]="selectedRequest">
	                <mat-option *ngFor="let request of requests" [value]="request">
	                    {{ request.Name }}
	                </mat-option>
	            </mat-select>
	    </mat-form-field>
	</div>
	
	<button mat-button color="primary" (click)="processRequest()">Process request</button>

Result

![](/images/posts/20180205-angular-material-1.png)

## Add Covalent component ##

[Teradata Covalent UI](https://teradata.github.io/covalent) is an open-source UI platform which built on Angular & Angular Material (Design). It can be used to build enterprise desktop web apps.

### Installation ###
1. Install angular cli

	npm install -g @angular/cli@latest

1. Install Covalent Core module

	npm install --save @covalent/core

	## (optional) Additional Covalent Modules installs
	npm install --save @covalent/http @covalent/highlight @covalent/markdown @covalent/dynamic-forms @covalent/echarts

1. Import the Covalent Core NgModule

	create a shared module src/app/covalent.module.ts

		import { NgModule } from '@angular/core';
		import { CovalentLayoutModule } from '@covalent/core/layout';
		import { CovalentStepsModule  } from '@covalent/core/steps';
		/* any other core modules */
		// (optional) Additional Covalent Modules imports
		import { CovalentHttpModule } from '@covalent/http';
		import { CovalentHighlightModule } from '@covalent/highlight';
		import { CovalentMarkdownModule } from '@covalent/markdown';
		import { CovalentDynamicFormsModule } from '@covalent/dynamic-forms';
		import { CovalentPagingModule } from '@covalent/core/paging';		
		
		@NgModule({
		    imports: [
		        CovalentLayoutModule,
		        CovalentStepsModule,
		        // (optional) Additional Covalent Modules imports
		        CovalentHttpModule.forRoot(),
		        CovalentHighlightModule,
		        CovalentMarkdownModule,
		        CovalentDynamicFormsModule,
		        CovalentPagingModule
		    ],
		    exports: [
		        CovalentLayoutModule,
		        CovalentStepsModule,
		        // (optional) Additional Covalent Modules imports
		        CovalentHighlightModule,
		        CovalentMarkdownModule,
		        CovalentDynamicFormsModule,
		        CovalentPagingModule
		    ],
		})
		export class CovalentModule { }

	import to app.module.ts
	
		import { BrowserModule } from '@angular/platform-browser';
		import { NgModule } from '@angular/core';
		import { FormsModule } from '@angular/forms';
		import { HttpClientModule } from "@angular/common/http";
		import { AngularMaterialModule } from "./angular-material.module";
		import { CovalentModule } from "./covalent.module"; // import here
		import { AppRoutingModule } from './app-routing.module';
		import { BrowserAnimationsModule } from '@angular/platform-browser/animations';
		
		import { AppComponent } from './app.component';
		
		@NgModule({
		  declarations: [
		    AppComponent
		  ],
		  imports: [
		    BrowserModule,
		    FormsModule,
		    HttpClientModule,
		    AppRoutingModule,
		    BrowserAnimationsModule,
		    AngularMaterialModule,
		    CovalentModule // import here
		  ],
		  providers: [],
		  bootstrap: [AppComponent],
		  entryComponents: [  ]
		})
		export class AppModule {
		}

1. Include the core, theme and typography:
	
	Due to covalent theme uses scss, we need to change to scss if use css when create project via cli: ng new xxxx

	Create src/covalent-theme.scss
		
		@import '~@angular/material/theming';
		@import '~@covalent/core/theming/all-theme';
		@import '~@covalent/markdown/markdown-theme';
		@import '~@covalent/highlight/highlight-theme';
		// Plus imports for other components in your app.
		
		// Custom typography
		$custom-typography: mat-typography-config(
		    $button: mat-typography-level(14px, 14px, 400)
		);
		$custom-toolbar-typography: mat-typography-config(
		    $title: mat-typography-level(20px, 32px, 400)
		);
		
		// Include the base styles for Angular Material core. We include this here so that you only
		// have to load a single css file for Angular Material in your app.
		@include mat-core($custom-typography);
		
		// Setting the toolbar to the proper spec weight
		@include mat-toolbar-typography($custom-toolbar-typography);
		
		// Include the core styles for Covalent
		@include covalent-core();
		
		// Include pre-bundled material-icons
		$mat-font-url: '../node_modules/@covalent/core/common/styles/font/';
		@include covalent-material-icons();
		// Alternative way to include material-icons
		// @import '../node_modules/@covalent/core/common/material-icons.css';
		
		// Include covalent utility classes
		@include covalent-utilities();
		
		// Include flex layout classes
		@include covalent-layout();
		
		// Include covalent color classes
		@include covalent-colors();
		
		// Define the palettes for your theme using the Material Design palettes available in palette.scss
		// (imported above). For each palette, you can optionally specify a default, lighter, and darker
		// hue.
		$primary: mat-palette($mat-blue, 700);
		$accent:  mat-palette($mat-orange, 800, A100, A400);
		
		// The warn palette is optional (defaults to red).
		$warn:    mat-palette($mat-red, 600);
		
		// Create the theme object (a Sass map containing all of the palettes).
		$theme: mat-light-theme($primary, $accent, $warn);
		
		// Include theme styles for core and each component used in your app.
		// Alternatively, you can import and @include the theme mixins for each component
		// that you are using.
		@include angular-material-theme($theme);
		@include covalent-theme($theme);
		@include covalent-markdown-theme($theme);
		@include covalent-highlight-theme();
		
		// Active icon color in list nav
		mat-nav-list {
		    [mat-list-item].active {
		        mat-icon[matListAvatar] {
		            background-color: mat-color($accent);
		            color: mat-color($accent, default-contrast)
		        }
		        mat-icon[matListIcon] {
		            color: mat-color($accent);
		        }
		    }
		}
		
		// Custom theme examples
		.white-orange {
		    $primary2: mat-palette($mat-grey, 50);
		    $accent2:  mat-palette($mat-orange, 800);
		    $warn2:    mat-palette($mat-red, 600);
		
		    $white-orange: mat-light-theme($primary2, $accent2, $warn2);
		
		    @include angular-material-theme($white-orange);
		    @include covalent-theme($white-orange);
		}
		.dark-grey-blue {
		    $primary3: mat-palette($mat-blue-grey, 800);
		    $accent3:  mat-palette($mat-teal, 500);
		    $warn3:    mat-palette($mat-red, 600);
		
		    $dark-grey-blue: mat-dark-theme($primary3, $accent3, $warn3);
		
		    @include angular-material-theme($dark-grey-blue);
		    @include covalent-theme($dark-grey-blue);
		}
		.light-blue-red {
		    $primary4: mat-palette($mat-light-blue, 700);
		    $accent4:  mat-palette($mat-red, 700);
		    $warn4:    mat-palette($mat-deep-orange, 800);
		
		    $light-blue-red: mat-light-theme($primary4, $accent4, $warn4);
		
		    @include angular-material-theme($light-blue-red);
		    @include covalent-theme($light-blue-red);
		}
		
		/* ------------------------------------------------------------------------------- */
		$foreground: map-get($theme, foreground);
		$background: map-get($theme, background);
		
		// Apply theme for this app
		
		// NGX Charts
		[ngx-charts-axis-label] text {
		    fill: mat-color($foreground, secondary-text);
		}
		.tick text {
		    fill: mat-color($foreground, disabled);
		}
		.gridline-path {
		    &.gridline-path-horizontal,
		    &.gridline-path-vertical {
		        stroke: rgba(black, 0.06);
		    }
		}
		.legend-title-text {
		    color: mat-color($foreground, secondary-text);
		}
		ngx-charts-line-chart,
		ngx-charts-area-chart,
		ngx-charts-area-chart-stacked {
		    .gridline-path {
		        &.gridline-path-vertical {
		            display: none;
		        }
		    }
		}
		ngx-charts-line-chart {
		    .line-series {
		        .line {
		            stroke-width: 2;
		        }
		    }
		}

	optional: update .angular_cli.json, change style from css to scss

	      "styles": [
	        "styles.css",
	        "covalent-theme.scss"
	      ],
		...
		  "defaults": {
		    "styleExt": "scss",
		    "component": {}
		  }
		...
	
### Create Covalent components ###

#### Create a paging component ####

1. import paging module

	covolent.module.ts

		import { NgModule } from '@angular/core';
		...
		import { CovalentPagingModule } from '@covalent/core/paging';
		
		@NgModule({
		    imports: [
		        ...
		        CovalentPagingModule
		    ],
		    exports: [
		        ...
		        CovalentPagingModule
		    ],
		})
		export class CovalentModule { }

2. create a new component

	import { Component, EventEmitter, Output } from "@angular/core";
	@Component({
	    selector: 'paging-component',
	    template: `
	        <td-paging-bar #pagingBar
	                [pageSize]="pageSize"
	                [total]="1345"
	                (change)="change($event)">
	  <span hide-xs>Rows per page:</span>
	  <mat-select [style.width.px]="50" [(ngModel)]="pageSize">
	    <mat-option *ngFor="let size of [50,100,200,500,100]" [value]="size">
	      {{size}}
	    </mat-option>
	  </mat-select>
	  {{pagingBar.range}} <span hide-xs>of {{pagingBar.total}}</span>
	</td-paging-bar>
	    `
	})
	
	export class PagingComponent{
	}

1. result

![](/images/posts/20180205-angular-material-2.png)

# FAQ
## Angular v6 reports `has no exported member 'Observable'`

`npm install rxjs-compat --save`

## References ##

[https://teradata.github.io/covalent/#/docs](https://teradata.github.io/covalent/#/docs)