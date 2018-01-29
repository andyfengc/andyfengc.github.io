---
layout: post
title: AngularJS v1 tutorial (2) angularjs-material
author: Andy Feng
---

## Introduction ##

[https://material.angularjs.org](https://material.angularjs.org)

[https://github.com/angular/material](https://github.com/angular/material)

## Install angularjs-material ##

Download library via npm: `npm install angular-material`

![](/images/posts/20180128-angularjs-3.png)

Add angular-material to html page:

	<html lang="en" >
	<head>
	  <meta name="viewport" content="width=device-width, initial-scale=1">
	  <!-- Angular Material style sheet -->
	  <link rel="stylesheet" href="https://ajax.googleapis.com/ajax/libs/angular_material/1.1.0/angular-material.min.css">
	</head>
	<body ng-app="BlankApp" ng-cloak>
	  <!--
	    Your HTML content here
	  -->  
	  
	  <!-- Angular Material requires Angular.js Libraries -->
	  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.5/angular.min.js"></script>
	  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.5/angular-animate.min.js"></script>
	  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.5/angular-aria.min.js"></script>
	  <script src="https://ajax.googleapis.com/ajax/libs/angularjs/1.5.5/angular-messages.min.js"></script>
	
	  <!-- Angular Material Library -->
	  <script src="https://ajax.googleapis.com/ajax/libs/angular_material/1.1.0/angular-material.min.js"></script>
	  
	  <!-- Your application bootstrap  -->
	  <script type="text/javascript">    
	    var app = angular.module('BlankApp', ['ngMaterial']);
		// add controllers...
	  </script>
	  
	</body>
	</html>

## add components ##