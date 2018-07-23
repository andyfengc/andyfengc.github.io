---
layout: post
title: Create Angular v2+ project (7) - ng-select
author: Andy Feng
---

## Introduction ##


## Steps ##
1. Install ng-select

	`npm install --save @ng-select/ng-select`

1. Import module, `app.module.ts`

		import { NgSelectModule } from '@ng-select/ng-select';
		import { FormsModule } from '@angular/forms';
		
		@NgModule({
		  declarations: [AppComponent],
		  imports: [NgSelectModule, FormsModule],
		  bootstrap: [AppComponent]
		})
		export class AppModule {}

1. Add theme

	method 1: styles.scss

		@import "~@ng-select/ng-select/themes/default.theme.css";
		// ... or 
		@import "~@ng-select/ng-select/themes/material.theme.css";

	method 2: .angular-cli.json (Angular v5 and below) or angular.json (Angular v6 onwards).

			"projects": {
				...
				"architect": {
					"build": {
					  ...,
					  "options": {
					    ...,
					    "styles": [
					      "./node_modules/@ng-select/ng-select/themes/material.theme.css"
					      ...
					    ],
					    ...
					  }

1. Create a component

	`.component.ts` file

		cars = [
			{ id: 1, name: 'Volvo' },
			{ id: 2, name: 'Saab', disabled: true },
			{ id: 3, name: 'Opel' },
			{ id: 4, name: 'Audi' },
		]
		selectedCarId = '5a15b13c36e7a7f00cf0d7cb';

	`.component.html` file

		<ng-select [items]="cars"
		           bindLabel="name"
		           bindValue="id"
		           [(ngModel)]="selectedCarId">
		</ng-select>

1. we got 

	![](/images/posts/20180718-ng-select-1.png)

# References
[https://github.com/ng-select/ng-select](https://github.com/ng-select/ng-select)
[https://ng-select.github.io/ng-select/](https://ng-select.github.io/ng-select/)