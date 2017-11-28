---
layout: post
title: ReactJS tutorial (7) - router
author: Andy Feng
---

## react-native-router-flux ##

### Preparation ###
install the router library

`npm install --save react-native-router-flux`

Create two components:

- TaskList
- AddTask

Create a configuration file router.js in the src root

	import React from 'react';
	import { Router, Scene } from 'react-native-router-flux';
	import { TaskList } from './components/task-list.component';
	import { AddTask } from './components/add-task.component';
	
	const RouterComponent = () => {
	  return (
	    <Router>
	      <Scene key="root">
	        <Scene key="taskList" component={TaskList} title="Task List" initial />
	        <Scene key="addTask" component={AddTask} title="AddTask" />
	      </Scene>
	    </Router>
	  );
	};
	
	export default RouterComponent;

Note:

1. Router component is the root of routering configuration
1. Scene represent each page
2. we specify {TaskList} component as the main page, it will navigate to other pages

### Create routing ###
In the bootstrap app.js, import the configuration file router.js:

	...
	import RouterComponent from './src/router';
	...
	
	export default class App extends React.Component {
	  render() {
		<RouterComponent />
	    );
	  }
	}

In the TaskList component, create navigation action for any event such as click event

	...
	import { Actions } from 'react-native-router-flux';
	...
	
	export class TaskList extends Component{
	render(){
	    return (
	      <View>
	        ...
	            <Ionicons name="ios-add-circle-outline" size={32} onPress={ () => Actions.addTask() } />
	        ...
	      </View>
	    )
	}

Here, we specify when we click the Ionicons component, it will navigate the component with the key of addTask.

Here is the main page

![](/images/posts/20171107-react-native-router-1.png)

When we click the icon

![](/images/posts/20171107-react-native-router-2.png)