---
layout: post
title: Create Angular v2+ project (6) - mdboostrap
author: Andy Feng
---

# Overview #
install `npm i angular-bootstrap-md --save`
`npm install chart.js --save`
npm install animate.css --save
npm install @types/chart.js --save
npm install hammerjs --save
npm install @fortawesome/fontawesome-free --save


modify `app.module.ts`

import { NgModule } from '@angular/core';
import { MDBBootstrapModule } from 'angular-bootstrap-md';

@NgModule({
    imports: [
        MDBBootstrapModule.forRoot()
    ]
});

nav bar
https://mdbootstrap.com/docs/angular/navigation/navbar/#introduction