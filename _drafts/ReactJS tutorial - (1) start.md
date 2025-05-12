---
layout: post
title: ReactJS tutorial - start
author: Andy Feng
---

# Introduction
`React` is an open-source project created by Facebook. React is a JavaScript library for building user interfaces(UI) on the front end.

`React` is not a framework (unlike Angular, which is more opinionated). React is the view layer of an MVC application (Model View Controller)

`React` creates a virtual DOM in memory, where it does all the necessary manipulating, before making the changes in the browser DOM. React doesn't manipulate the browser's DOM directly. React only changes what needs to be changed and it is fast.

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
please note extends Component is legacy code
way1
	class App extends React.Component {
	  render() {
	    return (...) // use JSX
	  }
	}
	
	ReactDOM.render(<App />, document.getElementById('root'))

> React component extends `Component` class
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

way2:
export default function App{
	return (...) // use JSX
}


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

## way2: use [create-react-app](https://github.com/facebook/create-react-app), 2017 recommended
nodejs version > 14.x

1. Install create-react-app

	`npx create-react-app react-tutorial`

	![](/images/posts/20220120-react-1.jpg)
	> if previously installed create-react-app globally, it is recommended that you uninstall the package to ensure npx always uses the latest version of create-react-app. To uninstall, run: `npm uninstall -g create-react-app`

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
## way3: use [nextjs](https://nextjs.org/docs) 2025 recommend

```
npx create-next-app@latest
```
1. Run `npm run dev` to start the development server.
2. Visit `http://localhost:3000` to view your application.
3. Edit the`app/page.tsx` file and save it to see the updated result in your browser.
`npm run build` to build your application 
`npm run start` to start the Node.js server.
## way 4: use [react router](https://reactrouter.com/start/framework/installation) 2025 recommend
```
npx create-react-router@latest my-react-router-app
```

Now change into the new directory and start the app

```
cd my-react-router-app
npm i
npm run dev
```

# JSX: JavaScript + XML
JSX stands for JavaScript XML. With JSX, we can write write HTML inside the JavaScript code. JSX makes it easier to write and add HTML in React.

> JSX allows us to write HTML elements in JavaScript and place them in the DOM without any `createElement()` and/or `appendChild()` methods.
> 
> JSX converts HTML tags into react elements.
> 
> You are not required to use JSX, but JSX makes it easier to write React applications.

without jsx

	const myelement = React.createElement('h1', {}, 'I do not use JSX!');	
	ReactDOM.render(myelement, document.getElementById('root'));

with jsx

	const myelement = <h1>I Love JSX!</h1>;
	ReactDOM.render(myelement, document.getElementById('root'));
> As you can see, JSX allows us to write HTML directly within the JavaScript code.
> 
> JSX is an extension of the JavaScript language based on ES6, and is translated into regular JavaScript at runtime.
> 
> Unlike browser DOM elements, React elements are plain objects, and are cheap to create. React DOM takes care of updating the DOM to match the React elements.

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

## More
1. Expressions:

	With JSX you can write expressions inside curly braces { }.
	
	The expression can be a React variable, or property, or any other valid JavaScript expression. JSX will execute the expression and return the result:
	
	e.g. 
	
		const name = 'Josh Perez';
		const element = <h1>Hello, {name}</h1>;

1. insert a block of HTML

	To write HTML on multiple lines, put the HTML inside parentheses:
	
	e.g. Create a list with three list items:
	
		const myelement = (
		  <ul>
		    <li>Apples</li>
		    <li>Bananas</li>
		    <li>Cherries</li>
		  </ul>
		);

1. One Top Level Element

	The HTML code must be wrapped in ONE top level element.	So if you like to write two paragraphs, you must put them inside a parent element, like a div element.

		const myelement = (
		  <div>
		    <p>I am a paragraph.</p>
		    <p>I am a paragraph too.</p>
		  </div>
		);

	Alternatively, you can use a "fragment" to wrap multiple lines. This will prevent unnecessarily adding extra nodes to the DOM. A fragment looks like an empty HTML tag: `<></>`.
		
		const myelement = (
		  <>
		    <p>I am a paragraph.</p>
		    <p>I am a paragraph too.</p>
		  </>
		);

1. Elements Must be Closed

	JSX follows XML rules, and therefore HTML elements must be properly closed. Close empty elements with />

		const myelement = <input type="text" />;

1. Conditions - if statements

	React supports if statements, but you should put the if statements outside of the JSX, or you could use a ternary expression instead:

	Option 1: Write if statements outside of the JSX code:

	

		const x = 5;
		let text = "Goodbye";
		if (x < 10) {
		  text = "Hello";
		}		
		const myelement = <h1>{text}</h1>;
	> Write "Hello" if x is less than 10, otherwise "Goodbye":

	Option 2: Use ternary expressions instead:

		const x = 5;		
		const myelement = <h1>{(x) < 10 ? "Hello" : "Goodbye"}</h1>;

## Babel
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
Components are like functions that return HTML elements. They accept arbitrary inputs (called “props”) and return React elements describing what should appear on the screen.
> Components are independent and reusable bits of code. 
> If you want to reuse non-UI functionality between components, we suggest extracting it into a separate JavaScript module. Other components may import it and use that function, object, or a class, without extending it.

reactjx components are literally JavaScript functions.

There are 2 types of components

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

1. Class component has a `constructor()` function. It will be called when the component get initiated
	> If your component has a constructor function, the props should always be passed to the constructor and also to the React.Component via the `super()` method.

		class Car extends React.Component {
		  constructor(props) {
		    super(props);
		  }
		  render() {
		    return <h2>I am a {this.props.model}!</h2>;
		  }
		}
2. Class component have a built-in `state` object. The `state` object is where you store property values that belongs to the component. When the state object changes, the component re-renders.
	> The state object is initialized in the constructor:

		class Car extends React.Component {
		  constructor(props) {
		    super(props);
		  this.state = {brand: "Ford", model: "Mustang", year: 1964};
		  }
		  render() {
		    return (...)
		  }
		}
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

但是，函数组件有重大限制，必须是纯函数，不能包含状态(stateless)，也不支持生命周期方法，因此无法取代类组件。
> 直到hooks出现增强了函数组件，才使得函数组件能实现全功能的组件
> In older React code bases, you may find Class components primarily used. It is now suggested to use Function components along with Hooks, which were added in React 16.8.

Simple component 等同于function component
	const SimpleComponent = () => {
	  return <div>Example</div>
	}

## Props
Props can pass existing data from parent component to a child React component, however the child component cannot change the props - they're read-only and one way data flow (parent pass data to child)

All React components must NOT change their props.

When React sees an element representing a user-defined component, it passes JSX attributes and children to this component as a single object. We call this object “props”.

e.g. 

	const element = <Welcome name="Sara" />;
	ReactDOM.render(
	  element,
	  document.getElementById('root')
	);

> React calls the `Welcome` component with `{name: 'Sara'}` as the props.

e.g.

	function Car(props) {
	  return <h2>I am a {props.color} Car!</h2>;
	}
	ReactDOM.render(<Car color="red"/>, document.getElementById('root'));
> pass `color="red"` to Car component

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

- The only place where you can assign `this.state=xxx` is the constructor.

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

# Styling React Using CSS
There are many ways to style React with CSS, here are three common ways:

1. Inline styling

	To style an element with the inline style attribute, the value must be a JavaScript object:
	
	e.g. Insert an object with the styling information:

		const Header = () => {
		  return (
		    <>
		      <h1 style={{color: "red"}, {backgroundColor: "lightblue"}}>Hello Style!</h1>
		      <p>Add a little style!</p>
		    </>
		  );
		}
	> In JSX, JavaScript expressions are written inside curly braces, and since JavaScript objects also use curly braces, the styling in the example above is written inside two sets of curly braces {{}}.
	> camelCased Property Names: Since the inline CSS is written in a JavaScript object, properties with hyphen separators, like `background-color`, must be written with `backgroundColor`

	e.g. create an object with styling information, and refer to it in the style attribute

		const Header = () => {
		  const myStyle = {
		    color: "white",
		    backgroundColor: "DodgerBlue",
		    padding: "10px",
		    fontFamily: "Sans-Serif"
		  };
		  return (
		    <>
		      <h1 style={myStyle}>Hello Style!</h1>
		      <p>Add a little style!</p>
		    </>
		  );
		}

1. CSS stylesheets

	we can write your CSS styling in a separate file, just save the file with the .css file extension, and import it in your application.

	e.g. 

		body {
		  background-color: #282c34;
		  color: white;
		  padding: 40px;
		  font-family: Sans-Serif;
		  text-align: center;
		}

	import .css to index.js

		import React from 'react';
		import ReactDOM from 'react-dom';
		import './App.css';
		
		const Header = () => {
		  return (
		    <>
		      <h1>Hello Style!</h1>
		      <p>Add a little style!.</p>
		    </>
		  );
		}

ReactDOM.render(<Header />, document.getElementById('root'));

1. CSS Modules

	CSS Modules are used for components that are placed in separate files.

	e.g. car.module.css:
	
		.bigblue {
		  color: DodgerBlue;
		  padding: 40px;
		  font-family: Sans-Serif;
		  text-align: center;
		}

	Import the stylesheet in your component Car.js:

		import styles from './my-style.module.css'; 		
		const Car = () => {
		  return <h1 className={styles.bigblue}>Hello Car!</h1>;
		}
		export default Car;

	Import the component in your application index.js:

		import ReactDOM from 'react-dom';
		import Car from './Car.js';
		
		ReactDOM.render(<Car />, document.getElementById('root'));

## Styling React Using Sass
Sass is a CSS pre-processor. Sass files are executed on the server and sends CSS to the browser.

Install Sass by running this command in your terminal:

`npm i sass`

create my-sass.scss:

	$myColor: red;
	
	h1 {
	  color: $myColor;
	}
	> Create a variable to define the color of the text:

Import the Sass file the same way as you imported a CSS file:

	import React from 'react';
	import ReactDOM from 'react-dom';
	import './my-sass.scss';
	
	const Header = () => {
	  return (
	    <>
	      <h1>Hello Style!</h1>
	      <p>Add a little style!.</p>
	    </>
	  );
	}
	ReactDOM.render(<Header />, document.getElementById('root'));

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
component name MUST start with an Uppercase letter. component can be independent `js` files. filename must start with an uppercase character.

React events are written in camelCase syntax. 如果是响应js事件的函数，命名handleXxx()

普通的CRUD, 命名addUser(), editUser(), updateUser(), deleteUser(), searchUser()

# how to reuse some stateful logic between components
[higher-order components](https://reactjs.org/docs/higher-order-components.html)

[render props](https://reactjs.org/docs/render-props.html)

[user-defined hook](https://reactjs.org/docs/hooks-overview.html#building-your-own-hooks)

## How to pass functions in function component?
parent component:
	
	import React from "react";
	import "./styles.css";
	import Model from "./Model";
	
	function App() {
	  const [status, setState] = React.useState(false);
	  const [text, setText] = React.useState("");
	  const handleClick = () => {
	    setState(prev => ({ status: !prev.status }));
	  };
	  const handleChange = e => {
	    setState({ text: e.target.value });
	  };
	
	  return (
	    <>
	      <button onClick={handleClick}>Open photo entry dialog</button>
	      <ChildComponent
	        isOpen={status}
	        text={text}
	        handleChange={handleChange}
	        handleClick={handleClick}
	      />
	    </>
	  );
	}
	
	const ChildComponent = ({ isOpen, text, handleChange, handleClick }) => {
	  return (
	    <>
	      {isOpen && (
	        <Model
	          status={isOpen}
	          handleClick={handleClick}
	          text={text}
	          handleChange={handleChange}
	        />
	      )}
	    </>
	  );
	};
	export default App;

child component:

	import React from "react";
	import "bootstrap/dist/css/bootstrap.min.css";
	import { Button, Modal, Form } from "react-bootstrap";
	
	export default function Model({ handleClick, status, handleChange, text }) {
	  return (
	    <>
	      <Modal show={status} onHide={handleClick}>
	        <Modal.Header closeButton>
	          <Modal.Title>Gallary</Modal.Title>
	        </Modal.Header>
	        <Form.Group controlId="formBasicEmail">
	          <Form.Control
	            type="text"
	            value={text}
	            placeholder="Enter Something"
	            onChange={handleChange}
	          />
	        </Form.Group>
	        <Modal.Body>
	          Woohoo, you're reading this text in a modal from your input:{" "}
	          <strong>{text}</strong>
	        </Modal.Body>
	        <Modal.Footer>
	          <Button variant="secondary" onClick={handleClick}>
	            Close
	          </Button>
	          <Button variant="primary" onClick={handleClick}>
	            Save Changes
	          </Button>
	        </Modal.Footer>
	      </Modal>
	    </>
	  );
	}

# Reference
[reactjs Getting Started](https://reactjs.org/docs/getting-started.html)

[React Tutorial: An Overview and Walkthrough](https://www.taniarascia.com/getting-started-with-react/)

[reactjs basics](https://reactjs.org/docs/hello-world.html)

[reactjs tutorial](https://reactjs.org/tutorial/tutorial.html)

[React.Component API reference](https://reactjs.org/docs/react-component.html)

[React Tutorial](https://www.w3schools.com/react/)

[react playground - CodeSandbox](https://codesandbox.io/s/new?file=/src/App.js)

[react playground - Stackblitz](https://stackblitz.com/edit/react-7zt5we)

[React Mixin 的前世今生](https://zhuanlan.zhihu.com/p/20361937)

[React 技术栈系列教程](http://www.ruanyifeng.com/blog/2016/09/react-technology-stack.html)

[React 入门实例教程](https://www.ruanyifeng.com/blog/2015/03/react.html)

[React Architecture: How to Structure and Organize a React Application](https://www.taniarascia.com/react-architecture-directory-structure/)

[HTML to JSX](https://transform.tools/html-to-jsx)