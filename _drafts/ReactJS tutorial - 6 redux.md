---
layout: post
title: ReactJS tutorial (4) - release
author: Andy Feng
---

# Introduction
Redux is a state container for JavaScript applications. It provides a library to manage components' state via store.
> Normally with React, you manage state at a component level, and pass state around via props. With Redux, the entire state of your application is managed in one immutable object. Every update to the Redux state results in a copy of sections of the state, plus the new change.

React components read data from Redux `store` and dispatch `actions` to the `store` to update `state`.

![](/images/posts/20220120-react-8.gif)

# why use redux?


## View
View即react组件，View与 State 一一对应，可以看作 State 的视觉层

## Store
Store 就是保存数据的地方，你可以把它看成一个容器。整个应用只能有一个 Store。
> Redux 提供createStore这个函数，用来生成 Store。

	import { createStore } from 'redux';
	const store = createStore(fn);
> createStore函数接受另一个函数作为参数，返回新生成的 Store 对象。

### State
Store对象包含所有数据。如果想得到某个时点的数据，就要对 Store 生成快照。这种时点的数据集合，就叫做 State。
> 当前时刻的 State，可以通过store.getState()拿到。
> Redux 规定， 一个 State 对应一个 View。只要 State 相同，View 就相同。你知道 State，就知道 View 是什么样，反之亦然。

### Subscribe
Store 允许使用store.subscribe方法设置监听函数，一旦 State 发生变化，就自动执行这个函数。

e.g.
	import { createStore } from 'redux';
	const store = createStore(reducer);
	
	store.subscribe(listener);
> 显然，只要把 View 的更新函数（对于 React 项目，就是组件的render方法或setState方法）放入listen，就会实现 View 的自动渲染。

store.subscribe方法返回一个函数，调用这个函数就可以解除监听。

	let unsubscribe = store.subscribe(() =>
	  console.log(store.getState())
	);
	
	unsubscribe();

## Action
用户不直接接触state，而是通过使用view来更新state数据。Action 就是 View 发出的通知，表示 State 应该要发生变化了。

Action是存放数据的对象，即消息的载体。Action 是一个简单对象，其中type属性是必须的，表示 Action 的名称，其他属性可以自由设置，用来传递数据，

e.g.

	const action = {
	  type: 'ADD_TODO',
	  payload: 'Learn Redux'
	};
> 这里，Action 的名称是ADD_TODO，它携带的信息是字符串Learn Redux。

### Action Creator
View 要发送多少种消息，就会有多少种 Action。如果都手写，会很麻烦。可以定义一个函数来生成 Action，这个函数就叫 Action Creator。

e.g. 

	const ADD_TODO = '添加 TODO';
	
	function addTodo(text) {
	  return {
	    type: ADD_TODO,
	    text
	  }
	}
	
	const action = addTodo('Learn Redux');
> addTodo函数就是一个 Action Creator。

## Dispatcher
store.dispatch()是 View 发出 Action 的唯一方法。

	import { createStore } from 'redux';
	const store = createStore(fn);
	
	store.dispatch({
	  type: 'ADD_TODO',
	  payload: 'Learn Redux'
	});
> store.dispatch接受一个 Action 对象作为参数，将它发送出去。

## Reducer
Store 收到 Action 以后，必须给出一个新的 State，这样 View 才会发生变化。这种 State 的计算过程就叫做 Reducer。

Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State。
> Reducer 是一个纯函数。也就是说，只要是同样的输入，必定得到同样的输出。

e.g.
	const reducer = function (state, action) {
	  // ...
	  return new_state;
	};

e.g.

	const defaultState = 0;
	const reducer = (state = defaultState, action) => {
	  switch (action.type) {
	    case 'ADD':
	      return state + action.payload;
	    default: 
	      return state;
	  }
	};
	
	const state = reducer(1, {
	  type: 'ADD',
	  payload: 2
	});

## Middleware中间件
中间件添加在dispatch前后，用来增加新的功能，譬如日志记录

e.g. 添加redux-logger模块

	import { applyMiddleware, createStore } from 'redux';
	import createLogger from 'redux-logger';
	const logger = createLogger();
	
	const store = createStore(
	  reducer,
	  applyMiddleware(logger)
	)

applyMiddlewares是 Redux 的原生方法，作用是将所有中间件组成一个数组，依次执行。

# React-Redux 
React-Redux 将所有组件分成两大类：UI 组件（presentational component）和容器组件（container component）。

1. UI 组件有以下几个特征。
	> 因为不含有状态，UI 组件又称为"纯组件"，即它纯函数一样，纯粹由参数决定它的值。
	- 只负责 UI 的呈现，不带有任何业务逻辑
	- 没有状态（即不使用this.state这个变量）
	- 所有数据都由参数（this.props）提供
	- 不使用任何 Redux 的 API

	e.g.

		const Title =
		  value => <h1>{value}</h1>;

1. 容器组件

	- 负责管理数据和业务逻辑，不负责 UI 的呈现
	- 带有内部状态
	- 使用 Redux 的 API

1. ui component vs. container component

	UI 组件负责 UI 的呈现，容器组件负责管理数据和逻辑。
	
	你可能会问，如果一个组件既有 UI 又有业务逻辑，那怎么办？回答是，将它拆分成下面的结构：外面是一个容器组件，里面包了一个UI 组件。前者负责与外部的通信，将数据传给后者，由后者渲染出视图。
	
	React-Redux 规定，所有的 UI 组件都由用户提供，容器组件则是由 React-Redux 自动生成。也就是说，用户负责视觉层，状态管理则是全部交给它。

## connect
React-Redux 提供connect方法，用于从 UI 组件生成容器组件。connect的意思，就是将这两种组件连起来。

	import { connect } from 'react-redux'
	const VisibleTodoList = connect()(TodoList);
> TodoList是 UI 组件，VisibleTodoList就是由 React-Redux 通过connect方法自动生成的容器组件。
> 
> 但是，因为没有定义业务逻辑，这个容器组件只是 UI 组件的一个单纯的包装层，并无意义。为了定义业务逻辑，需要给出下面两方面的信息。
> （1）输入逻辑：外部的数据（即state对象）如何转换为 UI 组件的参数（2）输出逻辑：用户发出的动作如何变为 Action 对象，从 UI 组件传出去

	import { connect } from 'react-redux'
	
	const VisibleTodoList = connect(
	  mapStateToProps,
	  mapDispatchToProps
	)(TodoList)
> connect方法接受两个参数：mapStateToProps和mapDispatchToProps。它们定义了 UI 组件的业务逻辑。前者负责输入逻辑，即将state映射到 UI 组件的参数（props），后者负责输出逻辑，即将用户对 UI 组件的操作映射成 Action

## Provider
React Redux includes a <Provider /> component, which makes the Redux store available to the rest of your app:

index.js
	
	import React from 'react'
	import ReactDOM from 'react-dom'	
	import { Provider } from 'react-redux'
	import store from './store'	
	import App from './App'
	
	const rootElement = document.getElementById('root')
	ReactDOM.render(
	  <Provider store={store}>
	    <App />
	  </Provider>,
	  rootElement
	)
> 这里，Provider在根组件外面包了一层，这样一来，App的所有子组件就默认都可以拿到state了

## Installation
way1: Create new product, recommended

	# Redux + Plain JS template
	npx create-react-app my-app --template redux
	
	# Redux + TypeScript template
	npx create-react-app my-app --template redux-typescript

way2: Add redux to existing project:
> You'll also need to install Redux and set up a Redux store in your app.

	npm install react-redux

## Hooks

React Redux provides a pair of custom React hooks that allow your React components to interact with the Redux store.

> `useSelector` reads a value from the store state and subscribes to updates

> `useDispatch` returns the store's dispatch method to let you dispatch actions.

# Reference
[redux.org](https://redux.js.org/)

[React为什么需要redux](https://www.mengfansheng.com/2018/12/25/React%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81redux/)

[Getting Started with React Redux](https://react-redux.js.org/introduction/getting-started)

[Getting Started with React Redux 中文](http://cn.redux.js.org/introduction/getting-started)

[Redux Tutorial: An Overview and Walkthrough](https://www.taniarascia.com/redux-react-guide/)

[Redux 入门教程（一）：基本用法](https://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)