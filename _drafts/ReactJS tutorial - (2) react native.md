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

1. open enviornment variable
	1. add jdk>bin to PATH
	2. add JAVA_HOME
	3. add android\sdk\platform-tools to PATH

1. open android studio > open existing Android Studio project > select new created project\android > ok
	![](/images/posts/20170912-react-native-2.png)
	![](/images/posts/20170912-react-native-3.png)
	![](/images/posts/20170912-react-native-4.png)

1. android studio > tools > android > avd manager

	![](/images/posts/20170912-react-native-5.png)
	![](/images/posts/20170912-react-native-6.png)
	
	if the avd is not exising, create a new avd
	if the avd missing libs, download the library until the green button is there

	click the green play button, start the emulator

	![](/images/posts/20170912-react-native-7.png)

1. open commandline, switch to new created folder
	`react-native run-android`

	Now the reactnative will compile and package all scripts and attach to active android emulator

	![](/images/posts/20170912-react-native-8.png)
	![](/images/posts/20170912-react-native-9.png)
	![](/images/posts/20170912-react-native-11.png)
	![](/images/posts/20170912-react-native-10.png)

1. So far everything is done. Next, restore the environment
	1. android studio > activate the emulator again
	2. run `react-native init start` again