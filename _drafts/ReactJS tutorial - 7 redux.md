---
layout: post
title: ReactJS tutorial (7) - redux
author: Andy Feng
---

# Introduction
Redux 是 JavaScript 状态容器，提供可预测化的状态管理方案，可以看做flux的一种实现。
> Redux is a state container for JavaScript applications. It provides a library to manage components' state via store.
> Traditionally with React, you manage state at a component level, and pass state around via props. With Redux, the entire state of your application is managed in one immutable object. Every update to the Redux state results in a copy of sections of the state, plus the new change.
> redux已经变成一种pattern，有其他语言的实现。譬如React-Redux, Vue-Vedux, Angular-NgRx

React components read data from Redux `store` and dispatch `actions` to the `store` to update `state`.

![](/images/posts/20220120-react-12.jpg)
> action 可以视作event
> reducer可以视作event handler
> 可以宏观理解成，我们有view components，有数据state，剩下主要问题就是交互了；交互可以用经典的event, event handler来解决，redux原则是event handler不存数据，必须纯函数

![](/images/posts/20220120-react-9.jpg)

![](/images/posts/20220120-react-7.gif)

# Why use redux?
因为有中央的immutable state tree，state变化将可以预测，而且具备新的功能，譬如time travel debugging, record/reply, hot reloading.

1. Easily manage global state - access or update any part of the state from any Redux-connected component
1. Easily keep track of changes with Redux DevTools - any action or state change is tracked and easy to follow with Redux. The fact that the entire state of the application is tracked with each change means you can easily do time-travel debugging to move back and forth between changes.
2. better testability. pure function make it easier to test.

# How to use redux?
三大原则

1. 单一数据源 : 整个应用的 state 都存储在一颗 state tree 中，并且只存在与唯一一个 store 中
1. state 是只读的 : 唯一改变 state 的方法只能通过触发 action，然后通过 action 的 type 进而分发 dispatch 。不能直接改变应用的状态
1. 状态修改均由纯函数reducer完成 : 为了描述 action 如何改变 state tree，需要编写 reducers

简单的应用可以先用Context API进行开发，后期需要大量扩展的话再转化成redux

原始redux的缺点是使用redux需要设置大量初始boilerplate和维护，可以进一步使用 Redux Toolkit

## View
View即react组件，View与 State 一一对应，可以看作 State 的视觉层

## Store
Store 就是保存数据的地方，可以看成客户端数据库。整个redux应用只能有一个 Store，该store使用reducer进行初始化。Redux 提供createStore这个函数，用来生成 Store。
> store 数据是可以serial的，被多个component共享的数据
> unshared state，如果只限于单个component，不必放在store中；form表单数据，通常是self-contained, 不必放在store中

e.g.

	import { createStore, applyMiddleware } from 'redux'
	import thunkMiddleware from 'redux-thunk' // 这里用到了redux-thunk
	
	const store = createStore(
		reducers,
		state,
		applyMiddleware(thunkMiddleware) // applyMiddleware首先接收thunkMiddleware作为参数，两者组合成为一个新的函数（enhance）
	)

> 第一个参数 reducers必须；
> 第二个可选参数初始化状态(preloadedState)；
> 第三个参数一般为中间件 applyMiddleware(thunkMiddleware)
> 
> redux中，通常用Provider来wrap整个应用，因为store使用reducer进行初始化，所以整个应用的任何component都能访问到reducer；component可以读但不能直接更改store

通过 createStore 方法创建的store是一个对象，它本身包含4个方法 :

1. getState() : 获取 store 中当前的状态。
2. dispatch(action) : 分发一个 action，并返回这个 action，这是唯一能改变 store 中数据的方式。
3. subscribe(listener) : 注册一个监听者，它在 store 发生变化时被调用。
4. replaceReducer(nextReducer) : 更新当前 store 里的 reducer，一般只会在开发模式中调用该方法。
 
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

Action是消息的载体，所有用户event通过action来派送。Action 是一个简单对象，其中type属性是必须的，表示 Action 的名称，其他属性可以自由设置，flux的convention，该属性名叫做payload，用来传递数据
> action的例子如用户点击提交表单按钮发送数据，点击按钮toggle菜单，初始化component时获取数据

e.g.

	const action = {
	  type: 'ADD_TODO',
	  payload: {
		message: 'Learn Redux'
		}
	};

 	const action2 = {
		type: 'LOGIN',
	  	payload: {
			user: {username: 'john', password: 'secret' }
		}
	}
> type习惯用全大写的字符串常量，用来描述action。这里，Action 的名称是ADD_TODO，它携带的信息是字符串Learn Redux。
> payload是数据
 
### Action Creator
View 要发送多少种消息，就会有多少种 Action。如果都手写，会很麻烦。可以定义一个函数来生成 Action，这个函数就叫 Action Creator。它只是用来帮助生成action
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
> bindActionCreators() 可以自动把多个 action 创建函数绑定到 dispatch() 方法上。

### Dispatch action
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
> Reducers are functions to specify how the application's state changes in response to actions sent to the store. 
> 例子如，登陆成功后设置user detail的store；用户点击toogle切换按钮后设置boolean值；component 初始化获取数据后设置返回数据给store

Reducer 是一个函数，它接受 Action 和当前 State 作为参数，返回一个新的 State。
> Reducer 是一个纯函数。也就是说，只要是同样的输入，必定得到同样的输出。
> reducer根据action处理state的更新，如果没有更新或遇到未知action，则返回旧state；否则返回一个新state对象。__注意：不能修改旧state，必须先拷贝一份整个state，再进行修改；也可以使用Object.assign函数生成新的state。
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

永远不要在 reducer 里做这些操作：
1. 修改传入参数；
1. 执行有副作用的操作，如 API 请求和路由跳转；
1. 调用非纯函数，如 Date.now() 或 Math.random()；

e.g.

	function reducers(state, action) {
		switch(action.type) {
	  	case SET_OTHER_FILTERS: 
	    return Object.assign({}, state, {
	      otherFilters: action.payload.data
	    })
	  case START_FETCH_API:
	    return Object.assign({}, state, {
	      isFetching: true
	    })
	  case STOP_FETCH_API:
	    return Object.assign({}, state, {
	      isFetching: false
	    })
	  case RECEIVE_DATA_LIST:
	    return Object.assign({}, state, {
	      list: [ ...action.payload.data ]
	    })
	  default: 
	    return state
	}

> 不要修改 state。 使用 Object.assign() 新建了一个副本。不能这样使用 Object.assign(state, { otherFilters: action.payload.data })，因为它会改变第一个参数的值。你必须把第一个参数设置为空对象
> 在 default 情况下返回旧的 state。遇到未知的 action 时，一定要返回旧的 state。

### Pure function
Pure function requirements:
- no random values
- No current date/time
- No global state (DOM, files, db, etc)
- No mutation of parameters

Pure function benefits:

- self documenting，易懂
- easily testable
- concurrency，可以并行执行，不担心global state变化导致输出不同
- cacheable，同样输入生成同样输出，可以缓存输出结果

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
React-Redux 提供connect()方法，用于从 UI 组件生成容器组件。connect的意思，就是将这两种组件连起来。其主要作用是将普通组件跟store连起来，使得普通组件能直接访问到store的state和dispatch

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
> connect 将当前组件和 state，action 联系起来，使得可以在组件中调用action中的方法和state中的数据
> connect 方法传入 mapStateToProps 和 mapDispatchToProps 两个参数，这两个参数均为对象
> mapStateToProps，将 state 转换为 props，可以通过 this.props.xxx 获取 state中的数据，这个数据是响应式的，只要 state 变化，这里就更新，如果组件不需要从 state 中获取数据，这里参数可以为 null
> mapDispatchToProps 将 action 中的方法转换为 props， 可以通过 this.props.xxx 调用

The connect() function is one typical way to connect React to Redux. A connected component is sometimes referred to as a container.

store 里能直接通过 store.dispatch() 调用 dispatch() 方法，但是多数情况下，我们都会使用 react-redux 提供的 connect() 帮助器来调用
> react-redux后来增加了hooks，useSelector useDispatch 可以用来替代connect

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
	    <Router>
	      <Route path="/" component={App} />
	    </Router>
	  </Provider>,
	  rootElement
	)
> 这里，Provider在根组件外面包了一层，这样一来，App的所有子组件就默认都可以拿到state了

## redux-thunk中间件
store.dispatch方法正常情况下，参数只能是对象，不能是函数。redux-thunk可以使store.dispatch()可以接受函数作为参数

add middleware:

	import { createStore, applyMiddleware } from 'redux';
	import thunk from 'redux-thunk';
	import reducer from './reducers';
	
	// Note: this API requires redux@>=3.1.0
	const store = createStore(
	  reducer,
	  applyMiddleware(thunk)
	);
> applyMiddleware() 其实applyMiddleware就是Redux的一个原生方法，将所有中间件组成一个数组，依次执行。 中间件多了可以当做参数依次传进去

## redux-promise 中间件
通常Action Creator 返回action，也可以返回函数。另一种异步操作的解决方案，redux-promise可以使 Action Creator 返回一个 Promise异步对象，并让store.dispatch()可以接受Promise对象作为参数

add middleware:

	import { createStore, applyMiddleware } from 'redux';
	import promiseMiddleware from 'redux-promise';
	import reducer from './reducers';
	
	const store = createStore(
	  reducer,
	  applyMiddleware(promiseMiddleware)
	); 

way1，Action Creator 返回Promise对象

	const fetchPosts = 
	  (dispatch, postTitle) => new Promise(function (resolve, reject) {
	     dispatch(requestPosts(postTitle));
	     return fetch(`/some/API/${postTitle}.json`)
	       .then(response => {
	         type: 'FETCH_POSTS',
	         payload: response.json()
	       });
	});

way2, Action 对象的payload属性是一个 Promise 对象。这需要从redux-actions模块引入createAction方法

	import { createAction } from 'redux-actions';
	
	class AsyncApp extends Component {
	  componentDidMount() {
	    const { dispatch, selectedPost } = this.props
	    // 发出同步 Action
	    dispatch(requestPosts(selectedPost));
	    // 发出异步 Action
	    dispatch(createAction(
	      'FETCH_POSTS', 
	      fetch(`/some/API/${postTitle}.json`)
	        .then(response => response.json())
	    ));
	  }
> 第二个dispatch方法发出的是异步 Action，只有等到操作结束，这个 Action 才会实际发出。注意，createAction的第二个参数必须是一个 Promise 对象。

# React Redux
## Installation
way1: Create new product, recommended

	# Redux + Plain JS template
	npx create-react-app my-app --template redux
	
	# Redux + TypeScript template
	npx create-react-app my-app --template redux-typescript

way2: Add redux to existing project:
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
	redux-devtools-extension

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

## 开发步骤
1. 设计store
1. 定义actions
2. 创建reducers，基于actions
3. 写middlewares, optional
4. 生成store，使用reducers和middlewares
5. 写views
	> 用store.dispatch(action)调度事件;
	> 用store.getState()更新页面数据

# Redux Toolkit
Redux Toolkit, or RTK是官方的简化Redux的方法。Redux has a lot of boilerplate for setup and requires many more folders and files than plain React would. 有一些模式，譬如Redux Toolkit，可以简化Redux

install: `npm i @reduxjs/toolkit react-redux`
> and it no longer requires you to install the `redux-thunk` or `redux-devtools-extension` dependencies.

The main advantages to using RTK are:

- Easier to set up (less dependencies)
- Reduction of boilerplate code (one slice vs. many files for actions and reducers)
- Sensible defaults (Redux Thunk, Redux DevTools built-in)
- The ability to use direct state mutation, since RTK uses immer under the hood. This means you no longer need to return { ...state } with every reducer.

## folder structure

	└── src/
	    ├── assets/ 
	    ├── common/ - 保存reusable hooks, generic components, utils...
	    ├── features/
	        ├── Feature1
	            ├── Feature1Slice.js - redux reducer logic and associated actions
	            ├── Feature1.js - react component
	        ├── ...
	    ├── services/ - 使用action，获取远程数据。。。
	    ├── App.js -- main app
	        ├── App.js -- main app
	        ├── Store.js - 创建store
	        ├── rootReducer
	    ├── Store.js - 创建store
	    ├── index.css
	    └── index.js

Store.js:
	
	import { configureStore } from '@reduxjs/toolkit'
	
	export default configureStore({
	  reducer: {},
	})

## Slice
Instead of dealing with reducers, actions, and all as separate files and individually creating all those action types, RTK gives us the concept of slices. A slice automatically generates reducers, action types, and action creators. 

A slice requires a string name to identify the slice, an initial state value, and one or more reducer functions to define how the state can be updated. 

> convention1: As such, you'll only have to create one folder - `slices`. we don't need `actions`, `reducers` folder as them merged into `slices` folder
> convention2: slice under each feature's folder

e.g. PostSlice.js

	import { createSlice } from '@reduxjs/toolkit'

	// A slice for posts to replace original multiple reducer files
	const postsSlice = createSlice({
	  name: 'posts',
	  initialState: [],
	  reducers: {
	    getPosts: (state, action) => {
	      state.loading = true
	    },
	    getPostsSuccess: (state, action) => {
	      state.posts = action.payload
	      state.loading = false
	      state.hasErrors = false
	    },
	    getPostsFailure: (state, action) => {
	      state.loading = false
	      state.hasErrors = true
	    },
	  },
	})

	// Action creators are generated for each case reducer function
	export const { getPosts, getPostsSuccess, getPostsFailure } = postsSlice.actions
	export default postsSlice.reducer

> Once a slice is created, we can export the generated Redux action creators and the reducer function for the whole slice.
> Redux Toolkit allows us to write "mutating" logic in reducers. It doesn't actually mutate the state because it uses the Immer library, which detects changes to a "draft state" and produces a brand new immutable state based off those changes

[https://react-redux.js.org/tutorials/quick-start](https://react-redux.js.org/tutorials/quick-start)

# React Context
[https://reactjs.org/docs/context.html](https://reactjs.org/docs/context.html)

[https://www.taniarascia.com/using-context-api-in-react/](https://www.taniarascia.com/using-context-api-in-react/)

# Reference
[redux.js.org](https://redux.js.org/)

[react-redux.js.org Getting Started with React Redux](https://react-redux.js.org/introduction/getting-started)

[react-redux.js.org Getting Started with React Redux 中文](http://cn.redux.js.org/introduction/getting-started)

[When do I know I’m ready for Redux?](https://medium.com/dailyjs/when-do-i-know-im-ready-for-redux-f34da253c85f)

[Redux Tutorial: An Overview and Walkthrough](https://www.taniarascia.com/redux-react-guide/)

[Redux 入门教程（一）：基本用法](https://www.ruanyifeng.com/blog/2016/09/redux_tutorial_part_one_basic_usages.html)

[Redux Toolkit](https://redux-toolkit.js.org/)

[React为什么需要redux](https://www.mengfansheng.com/2018/12/25/React%E4%B8%BA%E4%BB%80%E4%B9%88%E9%9C%80%E8%A6%81redux/)

[别再问React Hooks能否代替Redux了](https://www.mengfansheng.com/2019/09/02/%E5%88%AB%E5%86%8D%E9%97%AEReact-Hooks%E8%83%BD%E5%90%A6%E4%BB%A3%E6%9B%BFRedux%E4%BA%86/)

[redux source code](https://github.com/reduxjs/redux/tree/master/src)

[https://github.com/reduxjs/redux-thunk](https://github.com/reduxjs/redux-thunk)

[https://github.com/redux-utilities/redux-actions](https://github.com/redux-utilities/redux-actions)

[https://github.com/reduxjs/redux-toolkit](https://github.com/reduxjs/redux-toolkit/tree/master/packages/toolkit)

[Redux Tutorial - Learn Redux from Scratch](https://www.youtube.com/watch?v=poQXNp9ItL4&t=1236s)

[React Redux Tutorial For Beginners | Redux Toolkit Tutorial 2021](https://www.youtube.com/watch?v=k68j9xlbHHk)