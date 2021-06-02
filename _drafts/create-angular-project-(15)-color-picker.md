---
layout: post
title: Create Angular v2+ project (13) - angular-google-charts
author: Andy Feng
---

# [Angular Color Picker](https://www.npmjs.com/package/ngx-color-picker)

[Demo](https://zefoy.github.io/ngx-color-picker/)

[Playgroud](https://stackblitz.com/github/zefoy/ngx-color-picker/tree/master)

1. install `npm install ngx-color-picker --save`

1. load the module, app.module.ts

		import { ColorPickerModule } from 'ngx-color-picker';
		 
		@NgModule({
		  ...
		  imports: [
		    ...
		    ColorPickerModule
		  ]
		})

	not support ie v11

1. Use it in your HTML template:

	<input [(colorPicker)]="color" [style.background]="color"/>

please notice the version compatibilty with angular

# [ngx-color](https://www.npmjs.com/package/ngx-color)

[DEMO](https://ngx-color.vercel.app)

# [angular-color-picker](https://www.npmjs.com/package/@iplab/ngx-color-picker)

[https://pivan.github.io/ngx-color-picker/](https://pivan.github.io/ngx-color-picker/)

[https://github.com/pIvan/ngx-color-picker](https://github.com/pIvan/ngx-color-picker)

	angular 8 -> 1.0.8

# References
[https://github.com/ashishmondal/mondal-ui](https://github.com/ashishmondal/mondal-ui)

[https://github.com/mkarci26/org-chart](https://github.com/mkarci26/org-chart)

[https://github.com/MuminCelal/ngx-org-ui](https://github.com/MuminCelal/ngx-org-ui)