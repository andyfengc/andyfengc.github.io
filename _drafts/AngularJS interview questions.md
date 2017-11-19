---
layout: post
title: AngularJS interview questions
author: Andy Feng
---

This article introduces common interview questions for AngularJS 1.x. 

## What is MVC? ##

In an AngularJs application:

- **View** is the HTML.
- **Model** is the data available for the current view, specially the scope object
- **Controller** is the JavaScript function that creates/searches/updates/removes the data.
 
## What is scope? ##

Scope is the object that refers to the application model. It connects controller and view. scope object includes all available properties and methods. 

Every controller has an associated $scope object. When we create a controller in AngularJS, we pass the $scope object as an argument:

e.g.

	<script>
	var app = angular.module('myApp', []);
	
	app.controller('myCtrl', function($scope) {
	    $scope.title = "I am title";
	});
	</script>

## What is directive? ##

Directive is customized HTML markers. It helps us to create new markers to extend DOM elements. Directive could be an attribute, an element, a css class or a comment. When AngularJS compiler find a directive, it automatically attaches a specified behavior to that DOM element or even transform that DOM element and its children.

AngularJS comes with a set of built-in directives, such as ng-app, ng-model, ng-class, ng-init, ng-repeat.

## What is controller? ##

Controller is javascript function that used to provide data and implement logic.

## What is DI? ##

Dependency Injection (DI) is a software design pattern that process how components manage their dependencies.

AngularJS has an injector subsystem is in charge of creating components, resolving their dependencies, and providing them to other components as requested.

In AngularJS,
 
1. we define some fundamental components such as services, directives, filters, and animations by a factory method or constructor function. These components are injectable as dependencies.
1. Controllers are defined by a constructor function, which can be injected with any of the "service" and "value" components as dependencies

## What is two way binding? ##

Data binding is the synchronization between the model and the view.

The data model is a collection of data available for the application, specificially, the scope object.

The view is HTML.

Two way binding is the synchronization between them. When data in the model changes, the view reflects the change, and when data in the view changes, the model is updated as well. This happens immediately and automatically, which makes sure that the model and the view is updated at all times.

## What is routing? ##

Routing represents how we navigate within the application via urls. We can to navigate to different pages in the application. AngularJS uses routing machanism (ngRoute module) to build SPA (Single Page Application), In SPA, we can go through different pages without page reloading. It is faster and has better user experience.

Sample code to integrate AngularJS + ASP.NET MVC

Project structure:

![](/images/posts/20171031-angularjs-1.png)

Sample MVC controller:

    public class HomeController : Controller
    {
        // GET: Home
        public ActionResult Index()
        {
            return View();
        }
    }

Sample main page (here is a mvc layout page: _Layout.cshtml):

	@{
	    Layout = null;
	}
	
	<!DOCTYPE html>
	
	<html ng-app="myApp">
	<head>
	    <meta name="viewport" content="width=device-width" />
	
	    <link href="~/Content/bootstrap.css" rel="stylesheet" />
	    <link href="~/Content/bootstrap-theme.css" rel="stylesheet" />
	    <title>_Layout</title>
	</head>
	<body>
	    <div class="container" ng-controller="myController">
	        <div class="header clearfix">
	            <nav>
	                <ul class="nav nav-pills pull-right">
	                    <li role="presentation" class="active"><a href="#">Page1</a></li>
	                    <li role="presentation"><a href="#!/page2">Page2</a></li>
	                    <li role="presentation"><a href="#!/page3">Page3</a></li>
	                </ul>
	            </nav>
	            <h3 class="text-muted">Project name @DateTime.Now.ToString("yyyy-MM-dd HH:mm:ss")</h3>
	        </div>
	        <div>
	            @RenderBody()
	        </div>
	
	       
	    </div>
	
	    <script src="~/Scripts/jquery-1.9.1.js"></script>
	    <script src="~/Scripts/bootstrap.js"></script>
	    <script src="~/Scripts/angular.js"></script>
	    <script src="~/Scripts/angular-route.js"></script>
	    <script src="~/Scripts/app.js"></script>
	</body>
	</html>

Sample index view page:

	@model dynamic
	
	@{
	    ViewBag.Title = "title";
	    Layout = "~/Views/Shared/_Layout.cshtml";
	}
	
	<ng-view></ng-view>

Sample router app.js:

	'use strict';
	var myApp = angular.module('myApp', ['ngRoute']);
	
	myApp.config(function($routeProvider) {
	    $routeProvider
	        .when('/', {
	            templateUrl: '../../pages/Page1.html'
	        })
	        .when('/page2', {
	            templateUrl: '../../pages/Page2.html'
	        })
	        .when('/page3', {
	            templateUrl: '../../pages/Page3.html'
	        });
	});
	
	myApp.controller('myController', [
	    '$scope', '$http', function ($scope, $http) {
	
	    }
	]);

Result:

![](/images/posts/20171031-angularjs-2.png)

![](/images/posts/20171031-angularjs-3.png)

This is a SPA project. When we navigate pages via different links, there is no fresh and no timestamp changes for this page.

## What is promise? ##

AngularJS promise is a mechanism to defer an action to the moment when the promise is resolved. Promises is usually for asynchronous operations. 

## How angular2 is better than angular1? ##

angular2 is Component-Based. It splits the big page into smaller pieces. reusability

typescript syntax, follow the specification of esmascript 6. it supports inheritance, interfaces to make it easier to develop a component. also, typescript supports strongly-type and the compiler can find issues

performance is better