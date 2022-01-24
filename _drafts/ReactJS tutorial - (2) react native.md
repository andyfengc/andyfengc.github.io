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

	double click r > refresh
	ctrl + m > open menu

1. So far everything is done. Next, how restore the environment
	1. android studio > activate the emulator again
	2. run `react-native run-android` again

## Create the first entry component ##
1. open the project folder use atom or vs code

1. rewrite index.android.js

		// import library
		import React from 'react';
		import { Text, AppRegistry } from 'react-native';
		
		// create a component
		const App = () => {
		  return (
		    <Text>Main page 1</Text>
		  );
		};
		
		// render it to the device
		AppRegistry.registerComponent('start', () => App);
	![](/images/posts/20170912-react-native-12.png)

1. android studio > tools > android > avd manager > start the emulator

	![](/images/posts/20170912-react-native-7.png)

1. cmd > react-native run-android > start the reactnative app, it will automatically launch in the opened emulator

	![](/images/posts/20170912-react-native-13.png)

## Create a child component ##
1. create a file src > components > header.js

1. there are multiple ways to create a component
	1. way1, default exports, one per module
	
			// import library
			import React from 'react';
			import { Text } from 'react-native';			
			// create component
			const Header = () => {
			  return (
			    <Text>I am header</Text>
			  )
			}		
			export detault Header;	
	2. way2, default exports, one per module

			// export component
			export default Header;
			// import library
			import React, {Component} from 'react';
			import { Text } from 'react-native';			

			// create component
			class Header extends Component{
			  render(){
			    return (
			        <Text>I am header</Text>
			      )
			  }
			}	
			// export component
			export default Header;

	3. way3, default exports, one per module

			// import library
			import React, { Component } from 'react';
			import { Text } from 'react-native';
			
			// create component
			export default class Header extends Component {
			  render(){
			    return (
			        <Text>I am header</Text>
			      )
			  }
			}

	4. way4, name exports, several per module

			// import library
			import React, { Component } from 'react';
			import { Text } from 'react-native';
			
			// create component
			export class Header extends Component {
			  render(){
			    return (
			        <Text>I am header</Text>
			      )
			  }
			}

1. import child component to entry component

		// import library
		import React from 'react';
		import { Text, AppRegistry } from 'react-native';
		// way1-3
		//import Header from './src/components/header';
		// way4
		import {Header} from './src/components/header';
		
		// create a component
		const App = () => {
		  return (
		    <Header />
		  );
		};
		
		// render it to the device
		AppRegistry.registerComponent('start', () => App);

1. double click R to refresh emulator

	![](/images/posts/20170912-react-native-14.png)