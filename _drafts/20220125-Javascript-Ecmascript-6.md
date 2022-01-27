---
layout: post
title: Javascript tutorial - ECMAScript 6
author: Andy Feng
categories: [javascript, ecmascript]
---

# Introduction

# Class
A class is a type of function, but instead of using the keyword function to initiate it, we use the keyword class, and the properties are assigned inside a constructor() method.

e.g. 

	class Car {
	  constructor(name) {
	    this.brand = name;
	  }
	}

Now you can create objects using the Car class:

	const mycar = new Car("Ford");

You can add your own methods in a class

	class Car {
	  ...  
	  present() {
	    return 'I have a ' + this.brand;
	  }
	}

use:

	const mycar = new Car("Ford");
	mycar.present();

Class supports inheritance, use the extends keyword.

	class Car {
	  ...
	  present() {
	    return 'I have a ' + this.brand;
	  }
	}
	
	class Model extends Car {
	  constructor(name, mod) {
	    super(name);
	    this.model = mod;
	  }  
	  show() {
	      return this.present() + ', it is a ' + this.model
	  }
	}

	const mycar = new Model("Ford", "Mustang");
	mycar.show();

# Arrow Function
Arrow functions allow us to write shorter function syntax:

Before:

	hello = function() {
	  return "Hello World!";
	}

after with Arrow Function:

	hello = () => {
	  return "Hello World!";
	}

If the function has only one statement, and the statement returns a value, you can remove the brackets and the return keyword to get shorter.

	hello = () => "Hello World!";
> This works only if the function has only one statement.

Arrow Function With Parameters:
	
	hello = (val) => "Hello " + val;

if you have only one parameter, you can skip the parentheses as well:

	hello = val => "Hello " + val;

Note that `this` is different in arrow functions compared to regular functions
> In regular functions the `this` keyword represented the object that called the function, which could be the window, the document, a button or whatever.
> 
	//Regular function:
	  changeColor = function() {
	    document.getElementById("demo").innerHTML += this;
	  }
> With arrow functions, there are no binding of this. The `this` keyword always represents the object that defined the arrow function.
>
	//Arrow function:
	  changeColor = () => {
	    document.getElementById("demo").innerHTML += this;
	  }

# Variable
Before ES6 there were only one way of defining your variables: with the var keyword. If you did not define them, they would be assigned to the global object. 
> if you are in strict model, you would get an error if your variables were undefined

In ES6, there are three ways of defining your variables: `var`, `let`, and `const`.

var has a function scope, not a block scope.

	var x = 5.6;

> If you use var outside of a function, it belongs to the global scope.
> 
> If you use var inside of a function, it belongs to that function.
> 
> If you use var inside of a block, i.e. a for loop, the variable is still available outside of that block.

let has a block scope.

	let x = 5.6;

> let is the block scoped version of var, and is limited to the block (or expression) where it is defined.
> 
> If you use let inside of a block, i.e. a for loop, the variable is only available inside of that loop.

const is a variable that once it has been created, its value can never change. const has a block scope.

	const x = 5.6;

# Array
1. .map()

	.map() method allows you to run a function on each item in the array, returning a new array as the result.

		const myArray = ['apple', 'banana', 'orange'];
		
		const myList = myArray.map((item) => <p>{item}</p>)

# Destructuring
Destructuring is we take out some items from a large object/collection. We have an array or object that we are working with, but we only need some of the items contained in these.

1. Destruture array

	before

		const vehicles = ['mustang', 'f-150', 'expedition'];
		
		// old way
		const car = vehicles[0];
		const truck = vehicles[1];
		const suv = vehicles[2];

	destructuring:

		const vehicles = ['mustang', 'f-150', 'expedition'];
		const [car, truck, suv] = vehicles;
	> here, we assign array items to variable(s)
	> please note that the order of variables matter 

1. Destructuring Objects

		const vehicleOne = {
		  brand: 'Ford',
		  model: 'Mustang',
		  type: 'car',
		  year: 2021, 
		  color: 'red',
		  registration: {
		    city: 'Houston',
		    state: 'Texas',
		    country: 'USA'
		  }
		}
		
		myVehicle(vehicleOne);
		
		// old way
		function myVehicle(vehicle) {
		  const message = 'My ' + vehicle.type + ' is a ' + vehicle.color + ' ' + vehicle.brand + ' ' + vehicle.model + '.';
		}

		// destructure
		function myVehicle({type, color, brand, model}) {
		  const message = 'My ' + type + ' is a ' + color + ' ' + brand + ' ' + model + '.';
		}
		// destructure partially
		function myVehicle({ model, registration: { state } }) {
		  const message = 'My ' + model + ' is registered in ' + state + '.';
		}
	> Tthe order of properties doen't matter

# Spread Operator
spread operator (...) 可以将一个数组转为用逗号分隔的参数序列

	onst numbersOne = [1, 2, 3];
	const numbersTwo = [4, 5, 6];
	const numbersCombined = [...numbersOne, ...numbersTwo];
> 将两个数组分别展开，然后合并成另一个数组

The spread operator is often used in combination with destructuring.

	const numbers = [1, 2, 3, 4, 5, 6];	
	const [one, two, ...rest] = numbers;
> 将一个数字第1、第2个，和剩余元素分别赋予3个变量

We can use the spread operator with objects too
	const myVehicle = {
	  brand: 'Ford',
	  model: 'Mustang',
	  color: 'red'
	}
	
	const updateMyVehicle = {
	  type: 'car',
	  year: 2021, 
	  color: 'yellow'
	}
	
	const myUpdatedVehicle = {...myVehicle, ...updateMyVehicle}
> 将两个对象的属性分别展开，并合并成一个对象，重复的属性color取最后对象的值

# Module
JavaScript modules allow you to break up your code into separate files. This makes it easier to maintain the code-base.

1. Export. There are two types of exports: Named and Default.

	Named Exports. You can create named exports two ways. In-line individually, or all at once at the bottom.
	
	In-line individually:
	
		export const name = "Jesse"
		export const age = "40"

	All at once at the bottom:
	
		const name = "Jesse"
		const age = "40"	
		export { name, age }

	Default Exports

		const message = () => {
		  const name = "Jesse";
		  const age = "40";
		  return name + ' is ' + age + 'years old.';
		};
		
		export default message;
	> You can only have one default export in a file.

1. Import. You can import modules into a file in two ways, based on if they are named exports or default exports.

	Named exports must be destructured using curly braces. Default exports do not.

	Import from named exports
		Import named exports from the file person.js:
		
		import { name, age } from "./person.js";

	Import from default exports
		
		import message from "./message.js";

1. Ternary Operator

	Syntax: `condition ? <expression if true> : <expression if false>`


# Reference
[ECMAScript 6 入门](https://es6.ruanyifeng.com/)

[https://www.w3schools.com/js/js_es5.asp](https://www.w3schools.com/js/js_es5.asp)