---
layout: post
title: ReactJS tutorial - start
author: Andy Feng
---

# Introduction
React is a JavaScript library for building user interfaces(UI) on the front end.

React is not a framework (unlike Angular, which is more opinionated).

React is an open-source project created by Facebook.

React is the view layer of an MVC application (Model View Controller)

# syntax
html:

	<!DOCTYPE html>
	<html>
	  <head>
	    <meta charset="utf-8" />	
	    <title>Hello React!</title>
	
	    <script src="https://unpkg.com/react@^16/umd/react.production.min.js"></script>
	    <script src="https://unpkg.com/react-dom@16.13.0/umd/react-dom.production.min.js"></script>
	    <script src="https://unpkg.com/babel-standalone@6.26.0/babel.js"></script>
	  </head>
	
	  <body>
	    <div id="root"></div>
	
	    <script type="text/babel">
	      // React code will go here
	    </script>
	  </body>
	</html>

react code:

	class App extends React.Component {
	  render() {
	    return (...) // use JSX
	  }
	}
	
	ReactDOM.render(<App />, document.getElementById('root'))

> React component extends Component class
> 
> React component implement `render()` method
> 
> `render()` takes input data via `this.props` and returns what to display
> 
> `JSX` is optional. If it is used, use `Babel` to compile JSX to raw javascript code
> 
> In addition to taking input data using (via `this.props`), a component can maintain internal state data (via `this.state`). When a component’s state data changes, the rendered markup will be updated by re-invoking `render()`

e.g.

	class HelloMessage extends React.Component {
	  render() {
	    return (
	      <div>
	        Hello {this.props.name}
	      </div>
	    );
	  }
	}
	
	ReactDOM.render(
	  <HelloMessage name="Taylor" />,
	  document.getElementById('hello-example')
	);

# Prepare environment
## Way1: use npm
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

## way2: use [create-react-app](https://github.com/facebook/create-react-app)
nodejs version > 14.x

1. Install create-react-app

	`npx create-react-app react-tutorial`

	![](/images/posts/20220120-react-1.jpg)

1. `npm start`

	![](/images/posts/20220120-react-2.jpg)

	![](/images/posts/20220120-react-3.jpg)

1. simplify

	remove all files in src folder

	create index.js

		class App extends React.Component {
		  render() {
		    return (
		      <div className="App">
		        <h1>Hello, React!</h1>
		      </div>
		    )
		  }
		}

1. re-engineer
	
	modify index.js:

		import React from 'react';
		import ReactDOM from 'react-dom';
		import App from './app.js'
		
		ReactDOM.render(<App />, document.getElementById('root'))

	create app.js

		import { Component } from "react";
		class App extends Component{
		    render(){
		        return (
		            <div className="App">
		                <h1>Hello React!!</h1>
		            </div>
		        )
		    }
		}
		export default App;

# JSX: JavaScript + XML
JSX stands for JavaScript XML. With JSX, we can write what looks like HTML, and also we can create and use our own XML-like tags.

- JSX is closer to JavaScript than to HTML. 
- Properties and methods in JSX are camelCase instead of HTML attribute names. e.g. class becomes `className` in JSX, and tabindex becomes `tabIndex`. onclick will become `onClick`.
- - Self-closing tags must end in a slash - e.g. `<img />`
- JavaScript expressions can also be embedded inside JSX using curly braces, including variables, functions, and properties. e.g. `const heading = <h1>Hello, {name}</h1>`
- After compilation, JSX expressions become regular JavaScript function calls and evaluate to JavaScript objects.
- either use quotes (for string values) or curly braces (for expressions), but not both in the same attribute. 
- e.g. `const element = <img src={user.avatarUrl}></img>;` or `const element = <a href="https://www.reactjs.org"> link </a>;`
- Applications built with just React usually have a single root DOM node. If you are integrating React into an existing app, you may have as many isolated root DOM nodes as you like. e.g. html: `<div id="root"></div>`, jsx: `const element = <h1>Hello, world</h1>;
ReactDOM.render(element, document.getElementById('root'));`
- React Only Updates What’s Necessary. React DOM compares the element and its children to the previous one, and only applies the DOM updates necessary to bring the DOM to the desired state.

e.g. 

	const name = 'Josh Perez';
	const element = <h1>Hello, {name}</h1>;

> Unlike browser DOM elements, React elements are plain objects, and are cheap to create. React DOM takes care of updating the DOM to match the React elements.

Babel compiles JSX down to React.createElement() calls. below two examples are identical:

1

	const element = (
	  <h1 className="greeting">
	    Hello, world!
	  </h1>
	);

2

	const element = React.createElement(
	  'h1',
	  {className: 'greeting'},
	  'Hello, world!'
	);

> React.createElement() essentially creates an object like this:
> 
	const element = {
	  type: 'h1',
	  props: {
	    className: 'greeting',
	    children: 'Hello, world!'
	  }
	};

# Components
Conceptually, reactjx components are like JavaScript functions. They accept arbitrary inputs (called “props”) and return React elements describing what should appear on the screen.
> If you want to reuse non-UI functionality between components, we suggest extracting it into a separate JavaScript module. Other components may import it and use that function, object, or a class, without extending it.

reactjx components are literally JavaScript functions.

There are 3 types of components

## Class component
	class ClassComponent extends Component {
	  render() {
	    return <div>Example</div>
	  }
	}

define a component:

	import React, {Component} from 'react'
	
	class Table extends Component {
	  render() {
	    return (
	      <table>
	        <thead>
	          <tr>
	            <th>Name</th>
	            <th>Job</th>
	          </tr>
	        </thead>
	        <tbody>
	          <tr>
	            <td>Charlie</td>
	            <td>Janitor</td>
	          </tr>
	          <tr>
	            <td>Mac</td>
	            <td>Bouncer</td>
	          </tr>
	          <tr>
	            <td>Dee</td>
	            <td>Aspiring actress</td>
	          </tr>
	          <tr>
	            <td>Dennis</td>
	            <td>Bartender</td>
	          </tr>
	        </tbody>
	      </table>
	    )
	  }
	}	
	export default Table

Use the component:

	import React, {Component} from 'react'
	import Table from './Table'
	
	class App extends Component {
	  render() {
	    return (
	      <div className="container">
	        <Table />
	      </div>
	    )
	  }
	}	
	export default App

## function component
	function Welcome(props) {
	  return <h1>Hello, {props.name}</h1>;
	}

equivalent to

	class Welcome extends React.Component {
	  render() {
	    return <h1>Hello, {this.props.name}</h1>;
	  }
	}

但是，函数组件有重大限制，必须是纯函数，不能包含状态，也不支持生命周期方法，因此无法取代类组件。
> 直到hooks出现增强了函数组件，才使得函数组件能实现全功能的组件

## Simple component 等同于function component
	const SimpleComponent = () => {
	  return <div>Example</div>
	}

define 2 components:

TableHeader:

	const TableHeader = () => {
	  return (
	    <thead>
	      <tr>
	        <th>Name</th>
	        <th>Job</th>
	      </tr>
	    </thead>
	  )
	}

TableBody: 

	const TableBody = () => {
	  return (
	    <tbody>
	      <tr>
	        <td>Charlie</td>
	        <td>Janitor</td>
	      </tr>
	      <tr>
	        <td>Mac</td>
	        <td>Bouncer</td>
	      </tr>
	      <tr>
	        <td>Dee</td>
	        <td>Aspiring actress</td>
	      </tr>
	      <tr>
	        <td>Dennis</td>
	        <td>Bartender</td>
	      </tr>
	    </tbody>
	  )
	}

Use components:

	const TableHeader = () => { ... }
	const TableBody = () => { ... }
	
	class Table extends Component {
	  render() {
	    return (
	      <table>
	        <TableHeader />
	        <TableBody />
	      </table>
	    )
	  }
	}

## Props
Props can pass existing data to a child React component, however the child component cannot change the props - they're read-only and one way data flow (parent pass data to child)

All React components must NOT change their props.

When React sees an element representing a user-defined component, it passes JSX attributes and children to this component as a single object. We call this object “props”.

e.g. 

	const element = <Welcome name="Sara" />;
	ReactDOM.render(
	  element,
	  document.getElementById('root')
	);

> React calls the `Welcome` component with `{name: 'Sara'}` as the props.

## State
State can update private data in a component. Component is called stateful is has State data.

State allows React components to change their output over time in response to user actions, network responses, and anything else

in compononent, must call setState() to notify reactjs to call render() to update UI
> Do Not Modify State Directly
> 
>     // Wrong
	this.state.comment = 'Hello';
> 
> Instead, use setState():
> 
> 	// Correct
	this.setState({comment: 'Hello'});

- The only place where you can assign this.state is the constructor.

e.g.
 
	class App extends Component {
		state = {}
		constructor(props) {
			super(props);
			this.state = {date: new Date()};
		}

		render() {
			return (
			  <div>
			    <h1>Hello, world!</h1>
			    <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
			  </div>
			);
		}
	}

## props vs state
reactjs里，父component和子component并不知道其他component是stateful 或stateless，也不必关心。只需要通过props传递数据即可

state被封装在component内部，只有本component能读取和修改

一个component可以以props的形式，向子component传递state数据

reactjs的数据流是top-down从顶层往下的，即单向的。state总是封装在某个component里，state的数据可以影响到子component

in a React application. Usually, the state is first added to the component that needs it for rendering. Then, if other components also need it, you can lift it up to their closest common ancestor. Instead of trying to sync the state between different components, you should rely on the top-down data flow. [Lifting State Up](https://reactjs.org/docs/lifting-state-up.html) 也就是说，如果需要同步两个component的数据，将state从两个component中去掉，然后lift state到共同的父component里，由父component负责维护一份数据，并将数据传到两个子component的props中，两个子component没有state，只从props中读取数据

# Create a demo (2017)
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

1. modify package.json, add
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

# Add another component #

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

# release
way1:

[add React to an HTML page](https://reactjs.org/docs/add-react-to-a-website.html)

way2, use create-react-app:

`npm run build` This will create a build folder which will contain your app. 

# FAQ
## best practice
拿到需求后，先用reactjs创建静态网页做mockup。这个阶段需要大量typing，不需要很多思考
> mockup没有交互，纯内容。
> mockup只用props，不用state，state留作下一步做交互使用
> 采用top-down做mockup，需要的话，分解出子component

mockup确定以后，需要做交互实现功能，这个阶段需要写若干方法，并做正确调用，需要很多思考

## reactjs vs angular
react props 类似angular 的@Input()，由父component传值给子component

react没有angular的output()概念，有间接方法可以实现：从父component通过props传入一个callback方法，然后子component在事件触发时调用这个父component的callback

angular采用typescript语法，强类型的，必须先定义class才能用；reactjs基于javascript，弱类型，可以处理任意primitive values, React elements, or functions

reactjs是one-way data flow from top-down, angular是two way binding

## naming convention
如果是响应js事件的函数，命名handleXxx()

普通的CRUD, 命名addUser(), editUser(), updateUser(), deleteUser(), searchUser()

# how to reuse some stateful logic between components
[higher-order components](https://reactjs.org/docs/higher-order-components.html)

[render props](https://reactjs.org/docs/render-props.html)

[user-defined hook](https://reactjs.org/docs/hooks-overview.html#building-your-own-hooks)

# Reference
[reactjs Getting Started](https://reactjs.org/docs/getting-started.html)

[React Tutorial: An Overview and Walkthrough](https://www.taniarascia.com/getting-started-with-react/)

[reactjs basics](https://reactjs.org/docs/hello-world.html)

[reactjs tutorial](https://reactjs.org/tutorial/tutorial.html)

[React.Component API reference](https://reactjs.org/docs/react-component.html)

[react playground - CodeSandbox](https://codesandbox.io/s/new?file=/src/App.js)

[react playground - Stackblitz](https://stackblitz.com/edit/react-7zt5we)

[React Mixin 的前世今生](https://zhuanlan.zhihu.com/p/20361937)

[React 技术栈系列教程](http://www.ruanyifeng.com/blog/2016/09/react-technology-stack.html)

[React 入门实例教程](https://www.ruanyifeng.com/blog/2015/03/react.html)