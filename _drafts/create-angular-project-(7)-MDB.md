---
layout: post
title: Create Angular v2+ project (2) - MDB
author: Andy Feng
---

# Introduction #
[Material Design for Bootstrap (Angular version)](https://mdbootstrap.com/docs/angular/) is a popular UI KIT for building responsive, mobile-first websites and apps - free for personal & commercial use. It includes 500+ material UI elements, 600+ material icons, 75+ CSS animations, 2+ useful plugins, SASS files, templates, tutorials and many more.

# Outline #
1. Prepare Angular project
2. Install MDB
2. Add Navbar component 
3. Add more components

# Prepare Angular 2+ project
Here, we use [Angular CLI](https://github.com/angular/angular-cli) to build an empty project. 

	npm install -g @angular/cli 
	ng new demo --style=scss
	cd demo

## Install MDB
[https://mdbootstrap.com/docs/angular/getting-started/quick-start/](https://mdbootstrap.com/docs/angular/getting-started/quick-start/)

1. Install @angular/cdk package (required only for MDB Angular version 9.0.0 and later):

	`npm install @angular/cdk --save`

1. Install MDB Angular Free:

	`npm install angular-bootstrap-md --save`

1. Update the app.module.ts file with the following code:

		import { NgModule } from '@angular/core';
		import { MDBBootstrapModule } from 'angular-bootstrap-md';
		@NgModule({
		    imports: [
		        MDBBootstrapModule.forRoot()
		    ]
		});

1. Update styles and scripts arrays in angular.json file:

		"styles": [
		    "node_modules/@fortawesome/fontawesome-free/scss/fontawesome.scss",
		    "node_modules/@fortawesome/fontawesome-free/scss/solid.scss",
		    "node_modules/@fortawesome/fontawesome-free/scss/regular.scss",
		    "node_modules/@fortawesome/fontawesome-free/scss/brands.scss",
		    "node_modules/angular-bootstrap-md/assets/scss/bootstrap/bootstrap.scss",
		    "node_modules/angular-bootstrap-md/assets/scss/mdb.scss",
		    "node_modules/animate.css/animate.css",
		    "src/styles.scss"
		],
		"scripts": [
		  "node_modules/chart.js/dist/Chart.js",
		  "node_modules/hammerjs/hammer.min.js"
		],

1. Install external libraries

	`npm install -–save chart.js@2.5.0 @types/chart.js @fortawesome/fontawesome-free hammerjs animate.css`

1. Run application

	`ng serve --o`

## Add Navbar components ##
[https://mdbootstrap.com/docs/angular/navigation/navbar/](https://mdbootstrap.com/docs/angular/navigation/navbar/)

copy and past code, we got:

![](/images/posts/20200901-angular-1.png)
![](/images/posts/20200901-angular-11.png)
![](/images/posts/20200901-angular-12.png)

# References

