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
