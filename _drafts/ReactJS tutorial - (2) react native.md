---
layout: post
title: ReactJS tutorial (2) - react native
author: Andy Feng
---

## Prepare environment ##

1. Install JDK

1. Install node.js

1. install python 2.x, optional

1. Install Android Studio bundle

1. Install react native cli
	`npm install -g react-native-cli`

1. Init react native project
	`react-native init start`

	![](/images/posts/20170912-react-native-1.png)
	
1. open android studio, create a new virtual device

1. open enviornment variable
	1. add jdk>bin to PATH
	2. add JAVA_HOME
	3. add android\sdk\platform-tools to PATH

1. open android studio > open existing Android Studio project > select new created project\android > ok
1. open commandline, switch to new created folder
	`react-native run-android`

1. Restore the environment
	1. open the emulator
	2. run `react-native init start` again