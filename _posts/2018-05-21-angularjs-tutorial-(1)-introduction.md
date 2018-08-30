---
layout: post
title: AngularJS v1 tutorial (1) - tutorial
author: Andy Feng
---

[Download source](/download/angular1-demo.zip)

## Project configuration ##

Create an empty project folder

### Install via npm ###

Create package.json `npm init`

![](/images/posts/20171220-npm-init.png)

Edit package.json

	{
	  "name": "test",
	  "version": "1.0.0",
	  "description": "",
	  "main": "index.js",
	  "devDependencies": {
	    "bower": "^1.7.7",
	    "http-server": "^0.9.0",
	    "jasmine-core": "^2.4.1",
	    "karma": "^0.13.22",
	    "karma-chrome-launcher": "^0.2.3",
	    "karma-firefox-launcher": "^0.1.7",
	    "karma-jasmine": "^0.3.8",
	    "protractor": "^4.0.9"
	  },
	  "scripts": {
	    "test": "karma start karma.conf.js",
	    "test-single-run": "karma start karma.conf.js --single-run",
	    "preprotractor": "npm run update-webdriver",
	    "protractor": "protractor e2e-tests/protractor.conf.js"
	  },
	  "author": "",
	  "license": "ISC"
	}

`npm install`

![](/images/posts/20171220-npm-install.png)

### Install via bower ###
`npm install -g bower`

Create `bower.json`

	{
	  "name": "bower-project",
	  "description": "A starter project for AngularJS",
	  "version": "0.0.0",
	  "license": "MIT",
	  "private": true,
	  "dependencies": {
	    "angular": "1.6.x",
	    "angular-animate": "1.6.x",
	    "angular-mocks": "1.6.x",
	    "angular-resource": "1.6.x",
	    "angular-route": "1.6.x",
	    "bootstrap": "3.3.x",
	    "jquery": "2.2.x"
	  }
	}

Create `.bowerrc`
	
	{
	  "directory": "app/bower_components",
	  "interactive": false
	}

`bower install`

![](/images/posts/20171220-npm-install.png)

### Install Karma unit testing ###
Create Karma configuration file `karma.conf.js`

//jshint strict: false
module.exports = function(config) {
  config.set({

    basePath: './app',

    files: [
      'bower_components/angular/angular.js',
      'bower_components/angular-animate/angular-animate.js',
      'bower_components/angular-resource/angular-resource.js',
      'bower_components/angular-route/angular-route.js',
      'bower_components/angular-mocks/angular-mocks.js',
      '**/*.module.js',
      '*!(.module|.spec).js',
      '!(bower_components)/**/*!(.module|.spec).js',
      '**/*.spec.js'
    ],

    autoWatch: true,

    frameworks: ['jasmine'],

    browsers: ['Chrome'],

    plugins: [
      'karma-chrome-launcher',
      'karma-firefox-launcher',
      'karma-jasmine'
    ]

  });
};

## Write code ##

Create `app` folder if not exists, all source code here

Create `index.html` page in `app` folder

	<!doctype html>
	<html lang="en" ng-app="phonecatApp">
	  <head>
	    <meta charset="utf-8">
	    <title>My HTML File</title>
	    <script src="bower_components/jquery/dist/jquery.js"></script>
	    <script src="bower_components/angular/angular.js"></script>
	    <script src="bower_components/angular-animate/angular-animate.js"></script>
	    <script src="bower_components/angular-resource/angular-resource.js"></script>
	    <script src="bower_components/angular-route/angular-route.js"></script>
	    <script src="app.js"></script>
	  </head>
	  <body ng-controller="PhoneListController">
	      <ul>
	        <li ng-repeat="phone in phones">
	          <span>{{phone.name}}</span>
	          <p>{{phone.snippet}}</p>
	        </li>
	      </ul>
	  </body>
	</html>

Create `app.js`
	'use strict';
	// Define the `phonecatApp` module
	var phonecatApp = angular.module('phonecatApp', []);
	
	// Define the `PhoneListController` controller on the `phonecatApp` module
	phonecatApp.controller('PhoneListController', function PhoneListController($scope) {
	  $scope.phones = [
	    {
	      name: 'Nexus S',
	      snippet: 'Fast just got faster with Nexus S.'
	    }, {
	      name: 'Motorola XOOM™ with Wi-Fi',
	      snippet: 'The Next, Next Generation tablet.'
	    }, {
	      name: 'MOTOROLA XOOM™',
	      snippet: 'The Next, Next Generation tablet.'
	    }
	  ];
	});

Create a unit test `app.spec.js`

	describe('PhoneListController', function() {
	
	  beforeEach(module('phonecatApp'));
	
	  it('should create a `phones` model with 3 phones', inject(function($controller) {
	    var scope = {};
	    var ctrl = $controller('PhoneListController', {$scope: scope});
	    expect(scope.phones.length).toBe(3);
	  }));
	
	});

### Review ###
The project structure looks like

![](/images/posts/20171220-project-structure.png)

#### Run page ####
open `index.html`

![](/images/posts/20171220-index.html.png)

#### Run jasmine ####
cmd > `npm test`

![](/images/posts/20171220-jasmine.png)

also, a chrome window will be opened

![](/images/posts/20171220-jasmine-chrome.png)
