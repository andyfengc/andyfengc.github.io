---
layout: post
title: Jest testing framework tutorial
author: Andy Feng
categories: [javascript, unit testing, web]
---

[Download source](/download/jest-tutorial.zip)

## Introduction ##
Jest is a testing framework to test ReactJS components. It is developed by Facebook. Typically, we use it to test React apps easier.

Besides React applications, Jest can also be used to test any javascript applications.

## Install Jest ##

use npm

`npm install --save jest`

or use yarn

`yarn add jest`

For an existing application, we need to install babel-jest package and the react babel preset to transform our code inside of the test environment.

`npm install --save jest babel-jest babel-preset-env babel-preset-react react-test-renderer`

Your package.json should look something like this:

	// package.json
	  "dependencies": {
	    "react": "<current-version>",
	    "react-dom": "<current-version>"
	  },
	  "devDependencies": {
	    "babel-jest": "<current-version>",
	    "babel-preset-es2015": "<current-version>",
	    "babel-preset-react": "<current-version>",
	    "jest": "<current-version>",
	    "react-test-renderer": "<current-version>"
	  },
	  "scripts": {
	    "test": "jest"
	  }

Please add a .balbelrc file in the root of project like this:

	// .babelrc
	{
	  "presets": ["es2015", "react"]
	}

## Write a simple test ##
Create a sum.js file:

	function sum(a, b) {
	  return a + b;
	}
	module.exports = sum;

Then, create a sum.test.js file. Please note that the filename of unit test must end with .test.js. It contains our actual test:

	const sum = require('./sum');
	
	test('adds 1 + 2 to equal 3', () => {
	  expect(sum(1, 2)).toBe(3);
	});

Add the following section to our package.json:
	{
	  "scripts": {
	    "test": "jest"
	  }
	}

Finally, run `npm test` and we will get

![](/images/posts/20171107-jest-1.png)

Here is the package.json look like:

![](/images/posts/20171107-jest-2.png)

## Write a test to throw exceptions ##
Create a fail.js file. It simply throw an exception.

	module.exports = {
	    sum : function(a, b) {
	      throw new Error('sum fails');
	    }
	};

Next, create a fail.test.js file. 

	const fail = require('./fail');
	
	test('sum should fail', () => {
	    expect(function(){fail.sum(1, 2)}).toThrow();
	    expect(function(){fail.sum(1, 2)}).toThrow(Error);
	});

Run `npm test` to run tests

![](/images/posts/20171107-jest-3.png)

## References ##

[http://facebook.github.io/jest/docs/en/using-matchers.html](http://facebook.github.io/jest/docs/en/using-matchers.html)

[https://www.sitepoint.com/test-react-components-jest/](https://www.sitepoint.com/test-react-components-jest/)