---
layout: post
title: Angular2 unit testing tutorial
author: Andy Feng
categories: [angularjs, ionic, unit testing, e2e testing, jasmine, karma]
---

## Introduction ##

This guide offers tips and techniques for testing Angular v2/v4 applications. Though this page includes some general testing principles and techniques, the focus is on testing applications written with Angular.

Usually, we use **Jasmine** and **Karma** to unit test our application. Jasmine is what we use to create the unit tests, and Karma is what runs them. 

- Karma is the test runner and it loads our application's source code and executes our tests. 
- Jasmine is unit testing library and it provides functions to help us create tests and make assertions.

Due to Ionic boilerplate doesn't integrate with **Jasmine** and **Karma**, we can add unit testing support via below tutorial. 

## Set up Jasmine and Karma ##

### Install libraries ###
First, install unit test libraries

{% highlight shell %}

npm install @angular/cli --save-dev
npm install @angular/compiler-cli --save-dev
npm install @angular/language-service --save-dev
npm install @types/jasmine --save-dev
npm install @types/jasminewd2 --save-dev
npm install @types/node --save-dev
npm install codelyzer --save-dev
npm install jasmine-core --save-dev
npm install jasmine-spec-reporter --save-dev
npm install karma --save-dev
npm install karma-chrome-launcher --save-dev
npm install karma-cli --save-dev
npm install karma-coverage-istanbul-reporter --save-dev
npm install karma-jasmine --save-dev
npm install karma-jasmine-html-reporter --save-dev
npm install protractor --save-dev
npm install ts-node --save-dev
npm install tslint --save-dev

{% endhighlight %}

these libraries will be added to package.json > devDependencies

![](/images/posts/20170826-angular2-unit-test-1.png)

Alternatively, we can create a new angular project via cli 

{% highlight shell %}
ng new project-name
cd project-name
{% endhighlight %}

then find all missing libs within package.json > devDependencies

### Configuration ###

Next, generate a Karma configuration file by using the Karma CLI. Run the following command and accept all of the default options:

{% highlight shell %}
karma init karma.conf.js
{% endhighlight %}

Answer and a few simple questions by accepting all default settings. Then, karma.conf.js will be created.

![](/images/posts/20170826-angular2-unit-test-2.png)

Alternatively, we can simply copy karma.conf.js from new cli project

![](/images/posts/20170826-angular2-unit-test-3.png)

Next, copy and overwrite following missing files

>
karma.conf.js
angular-cli.json
protractor.conf.js
src > main.ts
src > test.ts
src > polyfills.ts
src > tsconfig.app.json
src > tsconfig.spec.json
src > typings.d.ts
src > environments > *
src > styles.css
e2e > *

### Verify environment ###
Add the following entry to the scripts object in package.json

{% highlight javascripot %}
"test": "ng test"
{% endhighlight %}

Let's write a sample unit test, please be advised that all unit testing files must like *.spec.ts

1. create a file sample-test.spec.ts
1. write code

{% highlight javascripot %}
describe('Sample test', () => { 
    it('should do nothing', () => { 
        expect(true).toBeTruthy(); 
    }); 
});
{% endhighlight %}

Open visual studio code, ctrl + ` to open terminal panel

{% highlight shell %}
npm test
{% endhighlight %}

Jasmine will try to search all *.spec.ts and run all of our tests. To ensure that everything is set up correctly, if you run the command now you should see the following result:
![](/images/posts/20170826-angular2-unit-test-4.png)

Also, a new chrome window will be opened for display unit testing results
![](/images/posts/20170826-angular2-unit-test-5.png)

### Issues ###

1. if it reports
	>
		Can not load "webpack"!
	  	TypeError: Cannot read property 'plugin' of undefined
	    at PathsPlugin.apply (/Users/yan/Dev/work/marriages-ng/node_modules/@ngtools/webpack/src/paths-plugin.js:75:18)
	    at Resolver.apply (/Users/yan/Dev/work/marriages-ng/node_modules/tapable/lib/Tapable.js:375:16)
	    at /Users/yan/Dev/work/marriages-ng/node_modules/enhanced-resolve/lib/ResolverFactory.js:249:12>
	
	or
	>
		The "@angular/compiler-cli" package was not properly installed. #6590
	
	it is because @angular/cli not proper installed
	
	solutions:
	
	- use `npm uninstall angular-cli --save-dev` and `npm install @angular/cli --save-dev`
	- upgrade node.js from 6.x to 8.x  

1. if it reports `Cannot find name 'describe'.`
	![](/images/posts/20170826-angular2-unit-test-6.png)

	Solutions: 
	1. make sure @types/jasmine is installed in package.json>devDependencies
		`npm install --save-dev @types/jasmine`
	1. in each *.spec.ts file add `import {} from 'jasmine';` 



## Jasmine syntax ##
Jasmine is a framework for writing code that tests your code. It does that primarily through the following three functions: describe, it, and expect:

- describe() defines a suite of tests (or “specs”)
- it() defines a test or “spec”, and it lives inside of a suite (describe()). This is what defines the expected behaviour of the code you are testing, i.e. “it should do this”, “it should do that”
- expect() defines the expected result of a test and lives inside of it().

So a skeleton for a test might look something like this:

{% highlight shell %}
describe('My Service', () => {
     it('should correctly add numbers', () => { 
        expect(1 + 1).toBe(2); 
    }); 
});
{% endhighlight %}

In this example we have a test suite called "My Service" that contains a test for correctly adding numbers. We use expect to check that the result of 1 + 1 is 2. To do this we use the toBe() matcher function which is provided by Jasmine. There’s a whole range of these methods available for testing different scenarios, for example:

- expect(fn).toThrow(e);
- expect(instance).toBe(instance);
- expect(mixed).toBeDefined();
- expect(mixed).toBeFalsy();
- expect(number).toBeGreaterThan(number);
- expect(number).toBeLessThan(number);
- expect(mixed).toBeNull();
- expect(mixed).toBeTruthy();
- expect(mixed).toBeUndefined();
- expect(array).toContain(member);
- expect(string).toContain(substring);
- expect(mixed).toEqual(mixed);
- expect(mixed).toMatch(pattern);

## Write unit tests ##

Here is the basic steps to write a unit test

1. Write a test
1. Run the test (it will fail)
1. Write your code
1. Rerun the test (until it pass)

### Write a test ###
As a conventions, unit tests usually locate the same folder with original classes and ends with `.spec.ts`

{% highlight shell %}
import { } from 'jasmine';
import { MortgageService } from "./mortgage.service";

describe("get monthly payment", () => {
    it('pay off 25 years of $200k should return proper monthyly payment', () => {
        let mortgageService = new MortgageService();
        expect(mortgageService.getMonthlyPayment(0.0209, 25 * 12, 200000)).toBeCloseTo(856.5, 1);
    })
})
{% endhighlight %}

### Run the test ###
`npm test`

Then observe the result.

### Write your code ###
If Jasmine reports error, fix the MortgageService class.

### Reretun the test ###
`npm test`

## Write end to end tests ##

End to end(e2e) tests is used to simulate browser and user behaviour for integration tests.

Since we already added `e2e` folder in the root of project. Let's create a sample test.

1. Add the following entry to the scripts object in package.json

	{% highlight javascripot %}
	"test": "ng e2e"
	{% endhighlight %}

1. Modify e2e > app.po.ts file

	{% highlight javascript %}
	import { browser, by, element } from 'protractor';
	
	export class AppPage {
	  navigateTo() {
	    return browser.get('/');
	  }
	
	  getParagraphText() {
	    return element(by.css('app-root h1')).getText();
	  }
	
	  getSampleText(){
	    return ('Welcome to app!');
	  }
	}
	{% endhighlight %}

1. Modify e2e > app.e2e-spec.ts file
	{% highlight javascript %}
	import { AppPage } from './app.po';
	
	describe('test2 App', () => {
	  let page: AppPage;
	
	  beforeEach(() => {
	    page = new AppPage();
	  });
	
	  it('should display welcome message', () => {
	    page.navigateTo();
	    expect(page.getSampleText()).toEqual('Welcome to app!');
	  });
	});
	{% endhighlight %}

1. visual studio code > open terminal panel > `ng e2e`, we can find a chrome window will be automatically opened for e2e testing, then below output printed
	![](/images/posts/20170826-angular2-unit-test-7.png)