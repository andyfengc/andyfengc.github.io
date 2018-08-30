---
layout: post
title: Create Angular v2+ project (4) - release
author: Andy Feng
---

## Introduction ##
1. Enable polyfills
2. Build and release
3. Host

## Enable polyfills ##
In JavaScript, a polyfill is a browser fallback that allows functionality you expect to work in modern browsers to work in older browsers, e.g., to support canvas (an HTML5 feature) in older browsers. Usually, when we are using some new features HTML5 but not supported by old browsers i.e. IE8, IE9, we should consider polyfills solution.

Angular CLI enables polyfills through the `src/polyfills.ts` file that the CLI created with your project. Uncomment the browser support statements.

![](/images/posts/20180119-angular-release-4.png)

## Build and release ##
Create a dev build: `ng build`

build in production: `ng build --prod`

![](/images/posts/20180119-angular-release-1.png)

A dist folder will be created automatically with packed js files in it. Just release the folder in server.

![](/images/posts/20180119-angular-release-2.png)

build in development: `ng build --dev`

![](/images/posts/20180119-angular-release-3.png)

## Reference ##
[https://angular.io/guide/browser-support](https://angular.io/guide/browser-support)