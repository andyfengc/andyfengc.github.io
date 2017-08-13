---
layout:     post
title:      "Hello 2015"
subtitle:   " \"Hello World, Hello Blog\""
date:       2015-01-29 12:00:00
author:     "Andy Feng"
header-img: "img/post-bg-2015.jpg"
catalog: true
tags:
    - blog
---

# Create a new blog #

## Initialize a blog ##
open command line
    
	jekyll new blog

## start the server ##
command line

	cd blog
    jekyll serve

open browser, enter http://localhost:4000

![](/images/20151103-start-server.png)

## create github page ##
create a new responsitory andyfengc.github.io

![](/images/20151103-create-github-page.png)

settings > options > launch automatic page generator

## download the github page ##
download a new copy

	git clone https://github.com/andyfengc/andyfengc.github.io.git

## copy the blog to github page ##

- Please note that for User Pages, use the master branch in your username.github.io repository. for Project Pages, use the gh-pages branch in your project's repository. 

    git checkout --orphan gh-pages

- copy the blog files to this branch
- push chanes to github
- visit http://username.github.com/

## push the changes ##