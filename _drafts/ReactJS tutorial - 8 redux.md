---
layout: post
title: ReactJS tutorial (4) - release
author: Andy Feng
---

# Introduction
Redux is a state container for JavaScript applications. It provides a library to manage components' state via store.
> Normally with React, you manage state at a component level, and pass state around via props. With Redux, the entire state of your application is managed in one immutable object. Every update to the Redux state results in a copy of sections of the state, plus the new change.

React components read data from Redux `store` and dispatch `actions` to the `store` to update `state`.

![](/images/posts/20220120-react-9.jpg)

![](/images/posts/20220120-react-7.gif)

# Why use redux?
1. Easily manage global state - access or update any part of the state from any Redux-connected component
1. Easily keep track of changes with Redux DevTools - any action or state change is tracked and easy to follow with Redux. The fact that the entire state of the application is tracked with each change means you can easily do time-travel debugging to move back and forth between changes.

缺点是使用redux需要设置大量初始boilerplate和维护，特别是如果不使用 Redux Toolkit，而使用原始redux的话。

简单的应用可以先用Context API进行开发，后期需要大量扩展的话再转化成redux

## View
View即react组件，View与 State 一一对应，可以看作 State 的视觉层

## Store
Store 就是保存数据的地方，你可以把它看成一个容器。整个redux应用只能有一个 Store，该store使用reducer进行初始化
> Redux 提供createStore这个函数，用来生成 Store。

	import { createStore } from 'redux';
	const store = createStore(fn);
> createStore函数接受另一个函数作为参数，返回新生成的 Store 对象。
> redux中，通常用Provider来wrap整个应用，因为store使用reducer进行初始化，所以整个应用的任何component都能访问到reducer；但store不直接对component直接开放

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
用户不直接使用state，而是通过使用view发送action，来更新state数据

Action是存放数据的对象，即消息的载体。Action 是一个简单对象，其中type属性是必须的，表示 Action 的名称，其他属性可以自由设置，如payload，用来传递数据，

e.g.

	const action = {
	  type: 'ADD_TODO',
	  payload: 'Learn Redux'
	};
> type习惯用全大写的字符串常量，用来描述action。这里，Action 的名称是ADD_TODO，它携带的信息是字符串Learn Redux。
> payload是数据
 
### Action Creator
View 要发送多少种消息，就会有多少种 Action。如果都手写，会很麻烦。可以定义一个函数来生成 Action，这个函数就叫 Action Creator。
> action creator is a function that returns an action.

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
> A reducer is immutable and always returns a copy of the entire state. 
> A reducer typically consists of a switch statement that goes through all the possible action types.

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

## Connect
The connect() function is one typical way to connect React to Redux. A connected component is sometimes referred to as a container.

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
> TodoList是 UI 组件(view)，VisibleTodoList就是由 React-Redux 通过connect方法自动生成的容器组件。
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

## Hooks
React Redux provides a pair of custom React hooks that allow your React components to interact with the Redux store.

> `useSelector` reads a value from the store state and subscribes to updates

> `useDispatch` returns the store's dispatch method to let you dispatch actions.

# Installation
## way1: Create new product, recommended

	# Redux + Plain JS template
	npx create-react-app my-app --template redux
	
	# Redux + TypeScript template
	npx create-react-app my-app --template redux-typescript

## way2: Add redux to existing project:
Redux requires a few dependencies.

- Redux Core library
- React Redux React bindings for Redux
- Redux Thunk Async middleware for Redux
- Redux DevTools Extension Connects Redux app to Redux DevTools

> You'll also need to install Redux and set up a Redux store in your app.

	npm i \
	redux \
	react-redux \
	redux-thunk \
	redux-devtools-extension \
	react-router-dom

## folder structure

	└── src/
	    ├── assets/ 
	    ├── actions/ - 保存actions，及获取远程数据
	    ├── components/ - 纯组件
	    ├── pages/ - or views, 指containers 
	    ├── reducers/ - reducer纯函数
	        ├── rootReducer
	        ├── xxxReducer
	    ├── services/ - 使用action，获取远程数据。。。
	    ├── utils/ 
	    ├── App.js -- main app
	    ├── index.css
	    └── index.js

## 开发
1. 写actions
2. 写reducers，基于actions
3. 写middlewares
4. 生成store，使用reducers和middlewares
5. 写views
	> 用store.dispatch(action)调度事件;
	> 用store.getState()更新页面数据

# Redux Toolkit
Redux Toolkit, or RTK是官方的简化Redux的方法。Redux has a lot of boilerplate for setup and requires many more folders and files than plain React would. 有一些模式，譬如Redux Toolkit，可以简化Redux

install: `npm i @reduxjs/toolkit`
> and it no longer requires you to install the `redux-thunk` or `redux-devtools-extension` dependencies.
> 
The main advantages to using RTK are:
- 
- Easier to set up (less dependencies)
- Reduction of boilerplate code (one slice vs. many files for actions and reducers)
- Sensible defaults (Redux Thunk, Redux DevTools built-in)
- The ability to use direct state mutation, since RTK uses immer under the hood. This means you no longer need to return { ...state } with every reducer.

## Slice
Instead of dealing with reducers, actions, and all as separate files and individually creating all those action types, RTK gives us the concept of slices. A slice automatically generates reducers, action types, and action creators. As such, you'll only have to create one folder - `slices`.
> we don't need `actions`, `reducers` folder as them merged into `slices` folder

e.g. PostSlice.js

	// A slice for posts to replace original multiple reducers
	const postsSlice = createSlice({
	  name: 'posts',
	  initialState,
	  reducers: {
	    getPosts: (state) => {
	      state.loading = true
	    },
	    getPostsSuccess: (state, { payload }) => {
	      state.posts = payload
	      state.loading = false
	      state.hasErrors = false
	    },
	    getPostsFailure: (state) => {
	      state.loading = false
	      state.hasErrors = true
	    },
	  },
	})

# Reference
[redux.org](https://redux.js.org/)

[React为什么需要redux](https://www.mengfansheng.com/2018/12/25/React%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81redux/)

[Getting Started with React Redux](https://react-redux.js.org/introduction/getting-started)

[Getting Started with React Redux 中文](http://cn.redux.js.org/introduction/getting-started)

[Redux Tutorial: An Overview and Walkthrough](https://www.taniarascia.com/redux-react-guide/)

[Redux 入门教程（一）：基本用法](https://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)

[Redux Toolkit](https://redux-toolkit.js.org/)