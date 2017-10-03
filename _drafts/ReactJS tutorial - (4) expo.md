---
layout: post
title: ReactJS tutorial (2) - firebase
author: Andy Feng
---

## Prepare environment ##

1. register a google account

1. create a new react/reactnative project

## Connect firebase ##

1. firebase > login > go to console > add a new project

	![](/images/posts/20170925-firebase-1.png)

1. select the new project > database > rules, temporarily disable authentication

	![](/images/posts/20170925-firebase-2.png)

1. new project > database > data, add a random text `hello world` to the root

	![](/images/posts/20170925-firebase-4.png)

1. new project > overview> add firebase to your web app, find the firebase key

	![](/images/posts/20170925-firebase-3.png)

1. install firebase library

	`npm install --save firebase`

1. create a new react component

		import React, {Component} from 'react';
		import { View, Text } from 'react-native';
		import firebase from 'firebase';
		
		export class FirebaseDemo extends Component {
		  state = { title: '' };
		
		  componentWillMount() {
		    // Initialize Firebase
		    firebase.initializeApp({
		      apiKey: "AIzaSyAa0fNiD75oUeC0OJuIjQaAKMSIUC1Pamg",
		      authDomain: "development-2b97f.firebaseapp.com",
		      databaseURL: "https://development-2b97f.firebaseio.com",
		      projectId: "development-2b97f",
		      storageBucket: "development-2b97f.appspot.com",
		      messagingSenderId: "444642293096"
		    });
		
		    let root = firebase.database().ref();
		    let self = this;
		    root.on('value', function(snapshot) {
		      self.setState({title: snapshot.val()});
		    });
		  }
		
		  render() {
		    return (
		      <View>
		        <Text>
		          {this.state.title}
		        </Text>
		      </View>
		    )
		  }
		}

1. add the component to main component

		// import library
		import React from 'react';
		import { Text, AppRegistry, View } from 'react-native';
		import Header from './src/components/header';
		import { FirebaseDemo } from './src/components/firebase-demo';
		
		// create a component
		const App = () => {
		  return (
		    <View>
		      <Header title={'I am title'}/>
		      <FirebaseDemo></FirebaseDemo>
		    </View>
		  );
		};		
		// render it to the device
		AppRegistry.registerComponent('start', () => App);


1. start the emulator

1. run `react-native run-android` to attach the app to emulator

	![](/images/posts/20170925-firebase-5.png)

1. update the firebase data

	![](/images/posts/20170925-firebase-6.png)

1. due to the websocket machanism of firebase, the app in emulator refreshed immediately automatically

	![](/images/posts/20170925-firebase-7.png)
