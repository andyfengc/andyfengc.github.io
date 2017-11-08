---
layout: post
title:  Using regular expression in Javascript
categories: [Javascript, Regular expression, Web, CodeProject]
author: Andy Feng
---

This article introduces the basics of applying regular expression in Javascript.

## Introduction ##

What Is a Regular Expression? 

A regular expression is a sequence of characters that forms a search pattern. The search pattern can be used for text search and text replace operations. When you search for data in a text, you can use this search pattern to describe what you are searching for. In practice, a regular expression can be a single character, or a more complicated pattern.

In JavaScript, everything is object and so does with regular expression. Regular expressions are patterns used to match character combinations in strings. These patterns are used with the exec() and test() methods of RegExp, and with the match, replace, search, and split methods of String. It can be used to perform all types of text search and text replace operations.

## Preview ##
Here is a simple Javascript regular expression:

    var pattern = /example/i

/example/i  is a regular expression.
example  is a pattern (to be used in a search).
i  is a modifier (modifies the search to be case-insensitive).

## Steps to use regular expression ##
- Javascript regular expression preparation
	- regular expression pattern
	- regular expression modifier
- Using regular expression object
- Using regular expression for string	

### Javascript regular expression preparation ###

In javascript, regular expression object is consist of regular expression pattern and modifiers. Syntax is like: 

	/pattern/modifiers;

Regular expression pattern specifies the search pattern. A pattern is composed of simple characters, such as /abc/, or a combination of simple and special characters, such as /ab*c/ or /Chapter (\d+)\.\d*/. 

Pattern table
![](/images/posts/20151130-regular-expression-pattern.png)
[Adapted from w3schools](http://www.w3schools.com/js/js_regexp.asp)

Modifiers specify if our search should be case-sensitive, global, etc. They are used to perform case-insensitive and global searches:

Modifiers table
![](/images/posts/20151130-regular-expression-modifier.png)
[Adapted from w3schools](http://www.w3schools.com/jsref/jsref_obj_regexp.asp)

Now we are ready to apply regular expression in js. There are two basic ways to apply regular expression in Javascript

- Using regular expression object
- Using regular expression for string

### Using regular expression object ###

#### Create a regular expression object ####

A regular expression object describes a pattern of characters.  It is used to perform pattern-matching.  There are two ways to construct a regular expression object

way1 - Using a regular expression literal, which consists of a pattern enclosed between slashes, as follows:

	var reg = /ab+c/;

Regular expression literals provide compilation of the regular expression when the script is loaded. When the regular expression remains constant, use this for better performance.

way2 - calling the constructor function of the RegExp object, as follows:

	var reg = new RegExp("ab+c");

Using the constructor function provides runtime compilation of the regular expression. Use the constructor function when you know the regular expression pattern will be changing, or you don't know the pattern and are getting it from another source, such as user input.

#### Regular expression object methods ####
First, let's get familiar with some common methods of regular expression object :

	compile()	Deprecated in version 1.5. Compiles a regular expression
	exec()	Tests for a match in a string. Returns the first match
	test()	Tests for a match in a string. Returns true or false
	toString()	Returns the string value of the regular expression

Examples

#### Using test() ####
The test() method is a RegExp expression method. It searches a string for a pattern, and returns true or false, depending on the result.

The following example searches a string for the character "e":

	var patt = /e/;
	patt.test("The best things in life are free!");

Since there is an "e" in the string, the output of the code above will be:

	true

You don't have to put the regular expression in a variable first. The two lines above can be shortened to one:

	/e/.test("The best things in life are free!");

#### Using exec() ####
The exec() method is a RegExp expression method. It searches a string for a specified pattern, and returns the found text. If no match is found, it returns null.

The following example searches a string for the character "e":

	/e/.exec("The best things in life are free!");

Since there is an "e" in the string, the output of the code above will be:
	
	.e

### Using regular expression for string  ###
In JavaScript, regular expressions can also be used with the two string methods: search() and replace(). They are used to handle two fundamental operations - make search and replace functions on text.

> The search() method uses an expression to search for a match, and returns the position of the match.
> 
> The replace() method returns a modified string where the pattern is replaced.

examples

Use a regular expression to do a case-insensitive search for "w3schools" in a string:

	var str = "Visit W3Schools";
	var n = str.search(/w3schools/i);

The result in n will be:

	6

The search method will also accept a string as search argument. The string argument will be converted to a regular expression:

Use a string to do a search for "W3schools" in a string:

	var str = "Visit W3Schools!";
	var n = str.search("W3Schools");

Use a case insensitive regular expression to replace Microsoft with W3Schools in a string:

	var	str = "Visit Microsoft!";
	var res = str.replace(/microsoft/i, "W3Schools");

The result in res will be:

	Visit W3Schools!

The replace() method will also accept a string as search argument:
var str = "Visit Microsoft!";
var res = str.replace("Microsoft", "W3Schools");

## More information ##

[Regular Expressions Cheat Sheet](https://www.debuggex.com/cheatsheet/regex/javascript)

[Introductory Guide to regular expressions](http://www.javascriptkit.com/javatutors/re.shtml)

[Regular Expressions patterns](http://www.javascriptkit.com/javatutors/redev2.shtml)

[Regular Expressions methods and usage](http://www.javascriptkit.com/javatutors/redev3.shtml)