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

## Add ReactFire ##

ReactFire provides an easy way to integrate Firebase into React apps. It exposes the ReactFireMixin which extends the functionality of a React component, adding additional Firebase-specific methods to it. These methods allow us to create a one-way data binding from our Firebase database to our component's this.state variable. 

Please note that ReactFire creates a one-way data binding from our database to our component, not the other way around.

1 install reactfire library   
  `npm install --save reactfire`

  by default, reactfire doesn't suppor es6 and we have to use it like 

	var ReactFireMixin = require('reactfire');
	var Firebase = require('firebase');

	var Profile = React.createClass({
	  	mixins: [ReactFireMixin],
		  componentDidMount: function(){
		    this.ref = new Firebase('https://amber-fire-5168.firebaseio.com/');
		    var childRef = this.ref.child(this.props.params.username);
		    this.bindAsArray(childRef, 'notes');
		  },
		componentWillMount: function() {
		  var ref = firebase.database().ref("items");
		  this.bindAsArray(ref, "items");
		},
		//...
	});

   in order to add mixin syntax to react, add another library:
   `npm install --save react-mixin`

1. create a new react component

		import React, {Component} from 'react';
		import { View, Text } from 'react-native';
		import firebase from 'firebase';
		import ReactFireMixin from 'reactfire';
		import reactMixin from 'react-mixin';
		
		export class FirebaseDemo extends Component {
		  constructor(props, context) {
		    super(props, context)
		    this.state = {
		      title: ''
		      , tasks: []
		    };
		  }
		
		  componentWillMount() {
		    const tasks = firebase.database().ref('task');
		    this.bindAsArray(tasks, 'tasks');
		  }
		
		  render() {
		    return (
		      <View>
		        <Text>
		          {this.state.title}
		          {this.state.tasks.map(task => {
		            return <Text key={task.key}>{task.title}</Text>;
		          })}
		        </Text>
		      </View>
		    )
		  }
		}
		reactMixin(FirebaseDemo.prototype, ReactFireMixin);

   Here, 

	{this.state.tasks.map(task => {
            return <Text key={task.key}>{task.title}</Text>;
          })}

   is equivalent to 

	{this.state.tasks.map(function(task, index){
            return task.title;
          })}

# Reference
[Getting Started with React and Firebase - Firecasts](https://www.youtube.com/watch?v=mwNATxfUsgI&ab_channel=Firebase)

[Getting started with React Native and Firebase](https://medium.com/@jamesmarino/getting-started-with-react-native-and-firebase-ab1f396db549)

[How to delete/remove nodes on Firebase](https://stackoverflow.com/questions/26537720/how-to-delete-remove-nodes-on-firebase)

[Firebase : Delete entry from database](https://www.youtube.com/watch?v=lTs-KHLMPC4&ab_channel=Melardev)

[Firebase update or set](https://stackoverflow.com/questions/29374013/firebase-update-or-set)

[Firebase DB - How to update particular value of child in Firebase Database](https://stackoverflow.com/questions/40589397/firebase-db-how-to-update-particular-value-of-child-in-firebase-database)