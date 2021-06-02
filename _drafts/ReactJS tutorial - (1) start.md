---
layout: post
title: ReactJS tutorial - start
author: Andy Feng
---

## Prepare environment ##
### Way1: use npm ###
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
	`npm install babel-core babel-loader --save`

1. Install watchify - instantly compile react jsx files to js files  
	`npm install --save watchify`

1. Install babel-preset-react - enable babel compile jsx files to raw js files
	`npm install --save babel-preset-react`  
	It lets us write modern JavaScript code that still works in older browsers.

1. Install babel-preset-es2015 - enable babel compile ecma script 2015 syntax
	`npm install babel-preset-es2015 babel-preset-stage-0 --save`

	**we can assume that angularjs 2.x/4.x cli is equivalent the combination of babel, browserify, babelify, watchify, babel-preset-react, babelo-reset-es2015 **

1. Install react  
	`npm install --save react react-dom`

1. create src\components folder  
	current structure
	![](/images/posts/20170908-react-1.png)

## Create a simple demo ##
1. create src\main.jsx

		var React = require('react');
		var ReactDom = require('react-dom');
		var List = require('./components/list.jsx');

		ReactDom.render(<List/>, document.getElementById('persons'));

1. create src\components\list.jsx

		var React = require('react');
		var ListItem = require('./listitem.jsx');
		
		var persons = [
		  {  "id" : 1, "name" : "kevin"}
		  , {  "id" : 2, "name" : "andy"}
		  , {  "id" : 3, "name" : "john"}
		]
		
		var List = React.createClass({
		  render: function(){
		    var listitems = persons.map(function(item){
		      return <ListItem key={item.id} name={item.name}/>;
		    });
		
		    return (<ul>{listitems}</ul>);
		  }
		})
		
		module.exports = List;

1. create src\components\listitem.jsx

		var React = require('react');
		var ListItem = React.createClass({
		  render: function(){
		    return (
		      <li>
		        {this.props.name}
		      </li>
		    );
		  }
		})
		
		module.exports = ListItem;

1. create public\index.html, and public\js folder

	<html>
	    <head>
	        <title>React Demo</title>
	    </head>
	    <body>
	        <div id="persons"></div>
	        <script type="text/javascript" src="js/main.js">            
	        </script>
	    </body>
	</html>

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

## Add another component ##

modify index.html
	...
	<div id="persons"></div>	

modify main.jsx
	
	...
	import {List} from './components/list.jsx';

	ReactDom.render(<List/>, document.getElementById('persons'));

add list.jsx

	import React, {Component} from 'react';
	import {ListItem} from './list-item.jsx';
	
	var persons = [
	    {  "id" : 1, "name" : "kevin"}
	    , {  "id" : 2, "name" : "andy"}
	    , {  "id" : 3, "name" : "john"}
	  ];
	
	export class List extends Component{
	    render(){
	        var listitems = persons.map(function(item){
	            return <ListItem key={item.id} name={item.name}/>;
	        });
	        return (
	            <ul>{listitems}</ul>            
	        )
	    }
	}
	
	ReactDom.render(<List/>, document.getElementById('persons'));

add list-item.jsx
	
	import React, {Component} from 'react';
	
	export class ListItem extends Component{
	    render(){
	        return (
	            <li>
	                {this.props.name}
	            </li>
	        )
	    }
	}

# Reference
[React Mixin 的前世今生](https://zhuanlan.zhihu.com/p/20361937)