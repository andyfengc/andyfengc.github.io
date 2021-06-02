---
layout: post
title: Create Angular v2+ project (7) - ngx-bootstrap
author: Andy Feng
---

## Introduction ##
[https://valor-software.com/ngx-bootstrap](https://valor-software.com/ngx-bootstrap)

## Steps ##
1. install `ngx-bootstrap` library
	
	`npm install ngx-bootstrap --save`

1. install bootstrap

	- method 1: 
	
			Bootstrap 3
	
			<!-- index.html -->
			<link href="https://maxcdn.bootstrapcdn.com/bootstrap/3.3.7/css/bootstrap.min.css" rel="stylesheet">
			Or Bootstrap 4
			
			<!--- index.html -->
			<link href="https://maxcdn.bootstrapcdn.com/bootstrap/4.0.0/css/bootstrap.min.css" rel="stylesheet">

	- method 2: 

		`npm install bootstrap --save`

		angular.json or angular-cli.json

			"projects": {
				...
				"architect": {
					"build": {
					  ...,
					  "options": {
					    ...,
					    "styles": [
					      "./node_modules/bootstrap/dist/css/bootstrap.css",
					      "./node_modules/font-awesome/css/font-awesome.min.css",
					      ...
					    ],
					    ...
					  },

1. create a module `ngx-bootstrap.module.ts` to import dedicated ngx-bootstrap components

		import { NgModule } from '@angular/core';
		import { AlertModule } from 'ngx-bootstrap/alert';
		@NgModule({
		    imports: [
		        AlertModule.forRoot()
		    ],
		    exports: [
		        AlertModule
		    ],
		})
		export class NgxBootstrapModule { }

	Please note that here we just add one module `AlertModule`

1. modify `app.module.ts` to import ngx-bootstrap module

		import { BrowserModule } from '@angular/platform-browser';
		import { NgModule } from '@angular/core';
		...
		import { NgxBootstrapModule } from './ngx-bootstrap.module';
		
		@NgModule({
		  declarations: [
		    ...
		  ],
		  imports: [
		    BrowserModule,
		    ...,
		    NgxBootstrapModule
		  ],
		  ...
		})
		export class AppModule { }

1. Select a component, in template view html file, add:

		<alert type="success">
		    <strong>Well done!</strong> You successfully read this important alert message.
		</alert>
		<alert type="info">
		    <strong>Heads up!</strong> This alert needs your attention, but it's not super important.
		</alert>
		<alert type="warning">
		    <strong>Warning!</strong> Better check yourself, you're not looking too good.
		</alert>
		<alert type="danger">
		    <strong>Oh snap!</strong> Change a few things up and try submitting again.
		</alert>

1. we will got 

	![](/images/posts/20180718-ngx-bootstrap-1.png)


Please note that datepicker component has issue using [ngModel] make two-way bindings in template view with getter/setter property of component.
