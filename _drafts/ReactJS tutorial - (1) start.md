---
layout: post
title: ReactJS tutorial - start
author: Andy Feng
---

## Prepare environment ##

1. Install tools
	1. nodejs
	1. atom + languagebabel (jsx syntax)

1. init an empty folder  
	`npm init`

1. Install browserify globally - package all js files into one js file for browser usage
	`npm install -g browserify`  
	It enable us to write modular code and bundle it together into small packages to optimize load time.

1. Install babelify - connect babel with browserify
	`npm install --save babelify`

1. Install watchify - instantly compile react jsx files to js files  
	`npm install --save watchify`

1. Install babel-preset-react - enable babel compile jsx files to raw js files
	`npm install --save babel-preset-react`  
	It lets us write modern JavaScript code that still works in older browsers.

1. Install babel-preset-es2015 - enable babel compile ecma script 2015 syntax
	`npm install babel-preset-es2015 --save`

	**we can assume that angularjs 2.x/4.x cli is equivalent the combination of babel, browserify, babelify, watchify, babel-preset-react, babelo-reset-es2015 **

1. Install react  
	`npm install --save react react-dom`

1. create src\components folder  
	current structure
	![](/images/posts/20170908-react-1.png)

## Create a simple demo ##
1. create src\components\list.jsx, src\components\listitem.jsx

1. create src\main.jsx, call list.jsx

1. create public\index.html

1. modify packet.json, add
	>
		scripts:{
	    	"start" : "watchify src/main.jsx -v -t [ babelify --presets [ es2015 react ] ] -o public/js/main.js",
			...
		}
	>
1. current structure

	![](/images/posts/20170908-react-2.png)

1. run npm start

	![](/images/posts/20170908-react-3.png)

	main.js was already generated automatically and watchify will watch and compile it constantly.

1. open index.html

	![](/images/posts/20170908-react-4.png)