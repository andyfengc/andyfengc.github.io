---
layout: post
title: Blazor tutorial 2 - Razor
author: Andy Feng
---

# Syntax
Razor is one of the view engines supported since ASP.NET MVC. Razor allows us to write a mix of HTML and C#/Basic. 

## Declare Variables
Declare a variable in a code block enclosed in brackets and then use those variables inside HTML with @ symbol.

e.g.

	@{ 
	    string str = "";
	
	    if(1 > 0)
	    {
	        str = "Hello World!";
	    }
	}	
	<p>@str</p>

## Inline expressions
`@variableName`

e.g. 
	
	<h1>@DateTime.Now.ToShortDateString()</h1>

## Multi statement code block
`@{
...
}`

e.g.

	@{
	    var date = DateTime.Now.ToShortDateString();
	    var message = "Hello World";
	}
	
	<h2>Today's date is: @date </h2>
	<h3>@message</h3>

## display text
`@:text`

or

`<text></text>`

e.g.

	@{
	    var date = DateTime.Now.ToShortDateString();
	    string message = "Hello World!";
	    @:Today's date is: @date <br />
	    @message                               
	}

## if-else condition
e.g.

	@if(DateTime.IsLeapYear(DateTime.Now.Year) )
	{
	    @DateTime.Now.Year @:is a leap year.
	}
	else { 
	    @DateTime.Now.Year @:is not a leap year.
	}

## for loop
e.g.

	@for (int i = 0; i < 5; i++) { 
	    @i.ToString() <br />
	}

## Model
Use `@model` to use model object anywhere in the view.

e.g.

	@model Student
	
	<h2>Student Detail:</h2>
	<ul>s
	    <li>Student Id: @Model.StudentId</li>
	    <li>Student Name: @Model.StudentName</li>
	    <li>Age: @Model.Age</li>
	</ul>

# Razor pages
## @page directive
@page is used to declare the page as router


# Reference
[Razor Syntax](https://www.tutorialsteacher.com/mvc/razor-syntax)

[Compare Razor Pages to ASP.NET MVC](https://learn.microsoft.com/en-us/dotnet/architecture/porting-existing-aspnet-apps/comparing-razor-pages-aspnet-mvc)

[Introduction to ASP.NET Web Programming Using the Razor Syntax (C#)](https://learn.microsoft.com/en-us/aspnet/web-pages/overview/getting-started/introducing-razor-syntax-c)

[Multiple Models in Single View in MVC](https://www.c-sharpcorner.com/UploadFile/ff2f08/multiple-models-in-single-view-in-mvc/)