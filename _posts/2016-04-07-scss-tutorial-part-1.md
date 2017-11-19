---
title: Improve CSS development productivity using Sass and Compass Part1 - configure environment
categories: [CSS, CodeProject]
layout: post
author: Andy Feng
---

This article introduces CSS preprocessor technique with Sass and Compass to develop clean, organized and efficient CSS code. 

Download source code at [here](/uploads/sass-test.zip) 

## Background ##
CSS is an fundamental technique to format HTML pages. It uses a style sheet language to describe how elements should be rendered and displayed inside browser. Because CSS is so simple and friendly to designers, it is generally used to define the layout, font, color and alignment of pages. Nowadays, web development has evolved and becomes increasingly complex. As the presentation technique, CSS also grows with the web page and shows some drawbacks in some cases.

- CSS lacks hierarchy structure and is hard to describe complex HTML page
- CSS is not programming language and cannot declare variables and control structure such as condition and looping
- Write generic CSS is challenging due to the different interpret mechanism of various browsers

Overall, it is hard to maintain CSS file especially in the case of complex Html page such as single page development. Developers need to spend extra time to maintain a consistent style even if some colors need to be changed. One of the solutions is apply CSS preprocessor. As the name says, CSS preprocessors take code written in the preprocessed language and then convert that code into the equivalent css. Among CSS proprocessors, Sass and Compass are great tools to help you write scalable, maintainable CSS.

## Introduction ##
[Scss](http://sass-lang.com/) is one of popular CSS extension languages. Other alternatives include [LESS](http://lesscss.org/), [Stylus](http://learnboost.github.com/stylus/ "Stylus") and [Swith CSS](http://sourceforge.net/projects/switchcss/). Scss is essentially a scripting language that is interpreted into Cascading Style Sheets (CSS). It is designed to facilitate to develop complex CSS. Sass is  compatible with all versions of CSS and it is a superset of CSS. By using Sass, we can improve  front-end CSS workflow in maintaining large-scale web applications.

[Scss](http://compass-style.org/ "Compass") is based on Scss and a toolkit for Scss and includes a set of libraries(mixins) to make the work of styling smooth and efficient. 

![](/images/2015-11-06-sass-structure.png)

## Outline ##
Here are steps of this serial tutorials to learn Sass and Compass.

1. Set up environment
	1. Install Ruby
	2. Install Scss
	3. Install Compass
	4. Test environment
	4. Configure editor/IDE
	5. Other tools
1. Sass fundamentals
2. Compass fundamentals
3. Samples

In this article, we will introduces how to set up the compilation and editor environment.

### Setup environment ###
1. Install Ruby

Ruby comes prepackaged with Mac OS. For Windows operating system, please download the installer at [here](http://rubyinstaller.org/downloads/). Follow the wizard to install Ruby runtime environment. Please be sure to select "Add Ruby executables to your path". It enables the quick locating of Ruby executables.

Check if ruby is installed, please open up a command prompt (click start and type “cmd” in the search box) and enter:
	
	ruby -v

Test installations:
	
	sass -v

1. Install Scss

	On Windows Command prompt:

		gem install sass
		
	On Linux / OS X Terminal:

		sudo gem install sass

1. Install Compass

	On Windows Command prompt
    
		gem install compass

	On Linux / OS X Terminal:

		sudo gem install compass
	
	Test installations:
	
		compass -v

1. Test environment
	
	**create a sample project**
	    
		compass create sass-test
	
	![](/images/2015-11-06-compass-create.png)
	
	Please note that two folders: sass and stylesheets as well as a configuration file config.rb are created.
	
	![](/images/2015-11-06-compass-create-result.png)
	
	Since we already set up the environment, compile Sass to CSS is very easy.Sass and Compass does all the hard work, the only thing we need to do is run the following command to let Compass do its thing. Please enter the new created project folder sass-test and enter:
	
		compass watch
	
	This command does all the magic stuff - it watches Sass files for changes and automatically compiles Sass to CSS. The Compass configuration (config.rb) defines a number of variables and specifies how the Compass compile CSS such as output path, image path and style.

	**Create a sample sass file and a html file.**

	In the root of sass-test, create index.

		<!DOCTYPE html>
		<html>
		<head>
			<title>Quick sample of Sass</title>
			<link rel="stylesheet" type="text/css" href="stylesheets/main.css">
		</head>
		<body>
		<section>
			I am parent container
			<div>
				I am rounded child.
			</div>
		</section>
		</body>
		</html>
	
	Enter sass-test/sass folder, create a main.sass file.

		@import "compass/css3";

		section{
			width:500px;
			height: 500px;
			position: relative;
			margin: 50px auto;
			background-color: #ddd;
			div{
				width: 300px;
				height:300px;
				background-color:#ccc;
				position: absolute;
				margin:100px;
				vertical-align: middle;
				line-height:300px;
				text-align: center;
				@include border-radius(20px);
			}
		}

	Here @import "compass/css" specifies the Sass file uses compass's css3 module. Please note that the indentation of section and div element improves the readability of Sass.

	Make sure that you enter start the Compass service through

		compass watch

	You will find that a new file main.css is created under test-project/stylesheets folder.
	
		/* line 3, ../sass/main.scss */
		section {
		  width: 500px;
		  height: 500px;
		  position: relative;
		  margin: 50px auto;
		  background-color: #ddd;
		}
		/* line 9, ../sass/main.scss */
		section div {
		  width: 300px;
		  height: 300px;
		  background-color: #ccc;
		  position: absolute;
		  margin: 100px;
		  vertical-align: middle;
		  line-height: 300px;
		  text-align: center;
		  -moz-border-radius: 20px;
		  -webkit-border-radius: 20px;
		  border-radius: 20px;
		}

	Here is the final result. Please note that the style of inner rounded div is generated by Compass module.

	![](/images/2015-11-06-sass-result.png)

4. Configure editor/IDE
	- [SassyStudio](https://visualstudiogallery.msdn.microsoft.com/15fe2c57-d22e-4321-8d5f-e74ea01b1e4d) is an extension of Visual studio. It can compile CSS via libsass, compass, or the sass gem. Basically, whenever you create a new scss file, it generates the same file name of .css in the same folder.
	
	![](/images/2015-11-06-sassystudio.png)

	- [Sass](https://packagecontrol.io/packages/Sass) is an add-on for for Sublime. It add syntax hightlighting and tab/code completion for Sass files. 
	
	![](/images/2015-11-06-sass-sublime.png) 

5. Other tools

	In this article we will focus on command-line usuage for this guide. Besides command-based compilation, there are multiple GUI apps for Sass/Compass to enable visualized compile. It is very easy and the steps can be available at official websites.

	[http://mhs.github.com/scout-app/](http://mhs.github.com/scout-app/ "Scout")

	[http://compass.handlino.com/](http://compass.handlino.com/ "Compass.app")

Please follow up my articles for further tuturials of Sass and Compass.