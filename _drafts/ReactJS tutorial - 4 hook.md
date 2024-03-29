---
layout: post
title: ReactJS tutorial - hook
author: Andy Feng
---

# Introduction
React 的核心是组件。v16.8 版本之前，组件的标准写法是类（class）

但是类组件的代码很"重"。真实的 React App 由多个类按照层级，一层层构成，复杂度成倍增长。再加入 Redux，就变得更复杂。组件类的几个缺点:

- 大型组件很难拆分和重构，也很难测试。
- 业务逻辑分散在组件的各个方法之中，导致重复逻辑或关联逻辑。
- 组件类引入了复杂的编程模式，比如 render props 和高阶(higher-order)组件。

React 团队希望，组件不要变成复杂的容器，最好只是数据流的管道。开发者根据需要，组合管道即可。 组件的最佳写法应该是函数，而不是类。所以对函数组件进行了增强，添加了hooks，使得完全不使用"类"，就能写出一个全功能的组件。
> 传统函数组件有重大限制，必须是纯函数，不能包含状态state，也不支持生命周期方法lifecycle methods，因此无法取代类。

# Hooks are an alternative to classes
Hooks were added to React in version 16.8 (2019). It enables us to use state and other React features without writing a class. Hooks allow us to "hook" into React features such as state and lifecycle methods.

> React Hooks 的意思是，组件尽量写成纯函数，如果需要外部功能和副作用，就用钩子把外部代码"钩"进来。 React Hooks 就是那些钩子。
> 
> 你需要什么功能，就使用什么钩子。React 默认提供了一些常用钩子，你也可以封装自己的钩子。
> 
> 所有的钩子都是为函数引入外部功能，所以 React 约定，钩子一律使用use前缀命名，便于识别。你要使用 xxx 功能，钩子就命名为 useXxx。
> 
> 纯组件不含有任何状态，可以方便测试和复用

Hooks are functions that let you “hook into” React state and lifecycle features from function components. Hooks don’t work inside classes — they let you use React without classes. Hooks allow function components to have access to state and other React features. Because of this, class components are generally no longer needed.

Hook rules:

- Hooks can only be called inside React function components. Don’t call Hooks from regular JavaScript functions. 
- Hooks can only be called at the top level of a component. Don’t call Hooks inside loops, conditions, or nested functions.
- Hooks cannot be conditional
- Hooks will not work in React class components.

## 类组件(class component) vs 函数组件(functional component)
以前，React API 只有一套类(class)API，现在有两套：类（class）API 和基于函数的钩子（hooks） API。任何一个组件，可以用类来写，也可以用钩子来写。

类的写法

	class Welcome extends React.Component {
	  render() {
	    return <h1>Hello, {this.props.name}</h1>;
	  }
	}

再来看钩子的写法，也就是函数。

	function Welcome(props) {
	  return <h1>Hello, {props.name}</h1>;
	}

官方推荐使用钩子（函数），而不是类。因为钩子更简洁，代码量少，用起来比较"轻"，而类比较"重"。而且，钩子是函数，更符合 React 函数式的本质。
> 但是，钩子的灵活性太大，初学者不太容易理解。很多人一知半解，很容易写出混乱不堪、无法维护的代码。那就不如使用类了。因为类有很多强制的语法约束，不容易搞乱。

类（class）是数据和逻辑的封装。 也就是说，组件的状态和操作方法是封装在一起的。如果选择了类的写法，就应该把相关的数据和操作，都写在同一个 class 里面。也就是说，状态state和render方法写在类组件中，这也使得随着需求增长和不断维护，类组件会比较重

函数一般来说，只应该做一件事，就是返回一个值。 如果你有多个操作，每个操作应该写成一个单独的函数。而且，数据的状态应该与操作方法分离。根据这种理念，React 的函数组件只应该做一件事情：返回组件的 HTML 代码，而没有其他的功能。也就是说，函数应该只做一件事，就是根据输入的参数，返回组件的 HTML 代码。这种只进行单纯的数据计算（换算）的函数，在函数式编程里面称为 "纯函数"（pure function）。即理想情况下，react的函数组件应该是纯函数
> 纯函数只能进行数据计算，那些不涉及计算的操作（比如生成日志、储存数据、改变应用状态等等），都称为 "副效应" （side effect） 。如果函数内部直接包含产生副效应的操作，就不再是纯函数了，我们称之为不纯的函数。

钩子（hook）就是 React 函数组件的副效应解决方案，用来为函数组件引入副效应。 函数组件的主体只应该用来返回组件的 HTML 代码，所有的其他操作（副效应）都必须通过钩子引入。
> 副效应非常多，所以钩子有许多种。React 为许多常见的操作（副效应），都提供了专用的钩子。如 `useState()：保存状态`，`useContext()：保存上下文`，`useRef()：保存引用` ......这些钩子引入某种特定的副效应，而 useEffect()是通用的副效应钩子 。找不到对应的钩子时，就可以用它

# Hooks
react默认提供若干钩子，用户也可以写自己的钩子

## State hook 状态钩子
useState()用于为函数组件引入状态（state）。纯函数不能有状态，所以把状态放在钩子里面。
> State generally refers to data or properites that need to be tracking in an application.
> useState Hook can be used to keep track of strings, numbers, booleans, arrays, objects, and any combination of these!
> We could create multiple state Hooks to track individual values.

useState():

- only accept 1 argument: the initial state
- returns 2 values: the current state value(当前值, naming convention is Noun） and a function that lets you update the state（更新状态，naming convention setXxx - setNoun）

e.g.

	import React, { useState } from 'react';
	
	function Example() {
	  	// Declare a new state variable, which we'll call "count"
		// declare a function setCount()
		// initial value of count is 0
	  const [count, setCount] = useState(0);
      // declare an array state variable
      const [todos, setTodos] = useState(["todo 1", "todo 2"]);
	
	  return (
	    <div>
	      <p>You clicked {count} times</p>
	      <button onClick={() => setCount(count + 1)}>
	        Click me
	      </button>
	    </div>
	  );
	}

e.g. 譬如一个按钮组件，用户点击按钮，会导致按钮的文字改变，文字变量取决于用户是否点击的动作，文字就是状态，按钮。用函数组件+useState的写法

	import React, { useState } from "react";
	
	export default function  Button()  {
	  const  [buttonText, setButtonText] =  useState("Click me,   please");
	
	  function handleClick()  {
	    return setButtonText("Thanks, been clicked!");
	  }
	
	  return  <button  onClick={handleClick}>{buttonText}</button>;
	}

equivalent to 类组件

	import React, { Component } from "react";
	
	export default class Button extends Component {
	  constructor() {
	    super();
	    this.state = { buttonText: "Click me, please" };
	    this.handleClick = this.handleClick.bind(this);
	  }
	  handleClick() {
	    this.setState(() => {
	      return { buttonText: "Thanks, been clicked!" };
	    });
	  }
	  render() {
	    const { buttonText } = this.state;
	    return <button onClick={this.handleClick}>{buttonText}</button>;
	  }
	}

e.g. 函数组件

	const App = () => {
	  const initialBookState = {
	    title: '',
	    available: false,
	  }
	
	  const [book, setBook] = useState(initialBookState)
	
	  const updateBook = (book) => {
	    setBook({ title: book.title, available: book.available })
	  }
	}

equivalent to 类组件

	class App extends Component {
	  initialState = {
	    title: '',
	    available: false,
	  }
	
	  state = initialState
	
	  updateBook = (book) => {
	    this.setState({ title: book.title, available: book.available })
	  }
	}

> 每一个属性都需要getter and setter，对于类组件来说，默认的state已经支持了；对于函数组件，如果用useState() hook，也自动支持
> 有了属性的getter, setter后，下一步就根据动作action需要写function，function里面做必要的intialize和cleanup，也可以调用getter/setter

e.g.

App.js

	import { useState } from "react";
	import Count from "./Count";
	import Todos from "./Todos";
	
	export default function App() {
      
	  	// Declare a new state variable, which we'll call "count"
		// declare a function setCount()
		// initial value of count is 0
	  const [count, setCount] = useState(0);
      // declare an array state variable
      const [todos, setTodos] = useState(["todo 1", "todo 2"]);
	
	  const increment = () => {
	    setCount((c) => c + 1);
	  }
	  
	  return (
	    <>
	      <Count count={count} increment={increment} />
	      <hr />
	      <Todos todos={todos} />
	    </>
	  );
	}

Count.js

	const Count = (props) => {
	  return (
	    <div>
	        Count: {props.count}
	        <button onClick={props.increment}>+</button>
	      </div>
	  )
	}
	export default Count;

Todos.js
	import { memo } from "react";

	const Todos = ({ todos }) => {
	  console.log("child render");
	  return (
	    <>
	      <h2>My Todos</h2>
	      {todos.map((todo, index) => {
	        return <p key={index}>{todo}</p>;
	      })}
	    </>
	  );
	};	
	export default memo(Todos);
> Here, we use `memo` to keep the Todos component from needlessly re-rendering. The reason is, whenever you click the increment button, the Todos component re-renders. If this component was complex, it could cause performance issues. Using `memo` will cause React to skip rendering a component if its props have not changed.

result:

![](/images/posts/20220120-react-11.jpg)

### Updating Objects and Arrays in State
Please When state is updated, the entire state gets overwritten.

If we only called `setCar({color: "blue"})`, this would remove the brand, model, and year from our state. We can use the JavaScript spread operator to help us.

	import { useState } from "react";
	import ReactDOM from "react-dom";
	
	function Car() {
	  const [car, setCar] = useState({
	    brand: "Ford",
	    model: "Mustang",
	    year: "1964",
	    color: "red"
	  });
	
	  const updateColor = () => {
	    setCar(previousState => {
	      return { ...previousState, color: "blue" }
	    });
	  }
	
	  return (
	    <>
	      <h1>My {car.brand}</h1>
	      <p>
	        It is a {car.color} {car.model} from {car.year}.
	      </p>
	      <button
	        type="button"
	        onClick={updateColor}
	      >Blue</button>
	    </>
	  )
	}
	
	ReactDOM.render(<Car />, document.getElementById('root'));
> Because we need the current value of state, we pass a function into our setCar function. This function receives the previous value.
> We then return an object, spreading the previousState and overwriting only the color.

## useEffect
`useEffect` Hook allows you to perform side effects in your components. Some examples of side effects are: fetching data, directly updating the DOM, and timers. `useEffect` is a special function can do some work after rendering. By default, React runs the effects after every render — including the first render.
> 最常见的就是向服务器请求数据。以前，放在componentDidMount里面的代码，现在可以放在useEffect()。

it can be used to:

- 获取数据（data fetching）
- 事件监听或订阅（setting up a subscription）
- 改变 DOM（changing the DOM）
- 输出日志（logging）

useEffect accepts two arguments. The second argument is optional.

`useEffect(<function>, <dependency>)`

e.g.

	useEffect(()  =>  {
	  // Async Action
	}, [dependencies])

> useEffect()接受两个参数。第一个参数是一个函数，异步操作的代码放在里面。第二个参数是一个数组，用于给出 Effect 的依赖项，只要这个数组发生变化，useEffect()就会执行。第二个参数可以省略，这时每次组件渲染时，就会执行useEffect()。

All possible cases to call `useEffect()`

1. No dependency passed:

		useEffect(() => {
		  //Runs on every render
		});

1. An empty array:

		useEffect(() => {
		  //Runs only on the first render
		}, []);

1. Props or state values:

		useEffect(() => {
		  //Runs on the first render
		  //And any time any dependency value changes
		}, [prop, state]);

e.g.

	import React, { useState, useEffect } from 'react';
	
	function Example() {
	  const [count, setCount] = useState(0);
	
	  // Similar to componentDidMount and componentDidUpdate:
	  useEffect(() => {
	    // Update the document title using the browser API
	    document.title = `You clicked ${count} times`;
	  });
	
	  return (
	    <div>
	      <p>You clicked {count} times</p>
	      <button onClick={() => setCount(count + 1)}>
	        Click me
	      </button>
	    </div>
	  );
	}

Please note some effects require cleanup to reduce memory leaks. such as Timeouts, subscriptions, event listeners, and other effects that are no longer needed should be disposed. We do this by including a return function at the end of the useEffect Hook.

e.g. Clean up the timer at the end of the useEffect Hook:

	import { useState, useEffect } from "react";
	import ReactDOM from "react-dom";
	
	function Timer() {
	  const [count, setCount] = useState(0);
	
	  useEffect(() => {
	    let timer = setTimeout(() => {
	    setCount((count) => count + 1);
	  }, 1000);
	
	  return () => clearTimeout(timer)
	  }, []);
	
	  return <h1>I've rendered {count} times!</h1>;
	}
	
	ReactDOM.render(<Timer />, document.getElementById("root"));

> To clear the timer, we had to name it.

## useContext() 共享状态钩子
useContext()用于在组件之间共享状态

譬如有两个组件 Navbar 和 Messages，我们希望它们之间共享状态。

	<div className="App">
	  <Navbar/>
	  <Messages/>
	</div>

传统类组件共享状态的做法是，在两个组件的共同父组件中定义state，然后传给两个子组件的props，子组件没有state，仅分别通过各自props显示数据

用hook做法是，在共同父组件创建一个 Context对象，然后通过该Context对象.Provider传入数据，然后在子组件中分别通过useContext(Context对象)来获取数据

父组件

	const AppContext = React.createContext({});

	function App() {
	  return (
	    <AppContext.Provider value={{
	      username: 'superawesome'
	    }}>
	      <div className="App">
	        <Navbar />
	        <Messages />
	      </div>
	    </AppContext.Provider>
	  );
	}

子组件

	const Navbar = () => {
	  const { username } = useContext(AppContext)
	
	  return (
	    <div className="navbar">
	      <p>AwesomeSite</p>
	      <p>{username}</p>
	    </div>
	  )
	}

子组件

	const Messages = () => {
	  const { username } = useContext(AppContext)
	
	  return (
	    <div className="messages">
	      <h1>Messages</h1>
	      <p>1 message for {username}</p>
	      <p className="message">useContext is awesome!</p>
	    </div>
	  )
	}

譬如，传统做法，如果多个component是层级嵌套结构，State放在最顶级父component那里，然后逐级向下通过`props`一路传值到底
e.g. Passing "props" through nested components:

	import { useState } from "react";
	
	export default function Component1() {
	  const [user, setUser] = useState("Jesse Hall");
	  return (
	    <>
	      <h1>{`Hello ${user}!`}</h1>
	      <Component2 user={user} />
	    </>
	  );
	}
	
	function Component2({ user }) {
	  return (
	    <>
	      <h1>Component 2</h1>
	      <Component3 user={user} />
	    </>
	  );
	}
	
	function Component3({ user }) {
	  return (
	    <>
	      <h1>Component 3</h1>
	      <Component4 user={user} />
	    </>
	  );
	}
	
	function Component4({ user }) {
	  return (
	    <>
	      <h1>Component 4</h1>
	      <Component5 user={user} />
	    </>
	  );
	}
	
	function Component5({ user }) {
	  return (
	    <>
	      <h1>Component 5</h1>
	      <h2>{`Hello ${user} again!`}</h2>
	    </>
	  );
	}
	
	ReactDOM.render(<Component1 />, document.getElementById("root"));
> Here, only component5 need the state. But even though components 2-4 did not need the state, they had to pass the state along so that it could reach component 5.

Solution is to create Context

1. create Context
 
		import { useState, createContext } from "react";
		import ReactDOM from "react-dom";
		
		const UserContext = createContext()

1. create Context Provider. wrap child components and supply `state` data. remove all top-down passing props

		function Component1() {
		  const [user, setUser] = useState("Jesse Hall");
		
		  return (
		    <UserContext.Provider value={user}>
		      <h1>{`Hello ${user}!`}</h1>
		      <Component2 user={user} />
		    </UserContext.Provider>
		  );
		}

1. Use the useContext Hook. In order to use the Context in a child component, we need to access it using the useContext Hook.

		import { useState, createContext, useContext } from "react";
		
		function Component5() {
		  const user = useContext(UserContext);
		
		  return (
		    <>
		      <h1>Component 5</h1>
		      <h2>{`Hello ${user} again!`}</h2>
		    </>
		  );
		}

full code:

	import { useState, createContext, useContext } from "react";
	const UserContext = createContext()
	
	export default function Component1() {
	  const [user, setUser] = useState("Jesse Hall");
	  return (
	    <UserContext.Provider value={user}>
	      <h1>{`Hello ${user}!`}</h1>
	      <Component2 user={user} />
	    </UserContext.Provider>
	  );
	}
	
	function Component2() {
	  return (
	    <>
	      <h1>Component 2</h1>
	      <Component3 />
	    </>
	  );
	}
	
	function Component3() {
	  return (
	    <>
	      <h1>Component 3</h1>
	      <Component4 />
	    </>
	  );
	}
	
	function Component4() {
	  return (
	    <>
	      <h1>Component 4</h1>
	      <Component5/>
	    </>
	  );
	}
	
	function Component5() {
	const user = useContext(UserContext);
	  return (
	    <>
	      <h1>Component 5</h1>
	      <h2>{`Hello ${user} again!`}</h2>
	    </>
	  );
	}

## useReducer()：action 钩子
`useReducer` Hook is similar to the useState Hook. It allows for custom state logic. If you find yourself keeping track of multiple pieces of state that rely on complex logic, useReducer may be useful.

useReducer syntax:

`const [state, dispatch] = useReducer(<reducer>, <initialState>)`
> `reducer` function contains your custom state logic. Reducer 函数的形式是`(state, action) => newState`
> `initialState` can be a simple value but generally will contain an object.
> `useReducer` Hook returns the current state(new state) and a dispatch method.

e.g.

	import { useReducer } from "react";
	import ReactDOM from "react-dom";
	
	const initialTodos = [
	  {
	    id: 1,
	    title: "Todo 1",
	    complete: false,
	  },
	  {
	    id: 2,
	    title: "Todo 2",
	    complete: false,
	  },
	];
	
	const reducer = (state, action) => {
	  switch (action.type) {
	    case "COMPLETE":
	      return state.map((todo) => {
	        if (todo.id === action.id) {
	          return { ...todo, complete: !todo.complete };
	        } else {
	          return todo;
	        }
	      });
	    default:
	      return state;
	  }
	};
	
	function Todos() {
	  const [todos, dispatch] = useReducer(reducer, initialTodos);
	
	  const handleComplete = (todo) => {
	    dispatch({ type: "COMPLETE", id: todo.id });
	  };
	
	  return (
	    <>
	      {todos.map((todo) => (
	        <div key={todo.id}>
	          <label>
	            <input
	              type="checkbox"
	              checked={todo.complete}
	              onChange={() => handleComplete(todo)}
	            />
	            {todo.title}
	          </label>
	        </div>
	      ))}
	    </>
	  );
	}
	
	ReactDOM.render(<Todos />, document.getElementById('root'));

> This is just the logic to keep track of the todo complete status.
> 
> All of the logic to add, delete, and complete a todo could be contained within a single useReducer Hook by adding more actions.

## useRef
`useRef` Hook allows you to persist values between renders. 

1. It can be used to store a mutable value that does not cause a re-render when updated. 
	>  It can resolve annoying infinite re-render issue.

	`useRef()` accept a initial value parameter. It only returns one Object called `current`. 

	e.g.

		import { useState, useEffect, useRef } from "react";
		import ReactDOM from "react-dom";
		
		function App() {
		  const [inputValue, setInputValue] = useState("");
		  const count = useRef(0);
		
		  useEffect(() => {
		    count.current = count.current + 1;
		  });
		
		  return (
		    <>
		      <input
		        type="text"
		        value={inputValue}
		        onChange={(e) => setInputValue(e.target.value)}
		      />
		      <h1>Render Count: {count.current}</h1>
		    </>
		  );
		}
		
		ReactDOM.render(<App />, document.getElementById('root'));
	> here we use useRef to track application renders.

1. It can also be used to access a DOM element directly.

	In general, we want to let React handle all DOM manipulation. But there are some instances where useRef can be used. In React, we can add a ref attribute to an element to access it directly in the DOM.
	
	e.g. Use useRef to focus the input:

		import { useRef } from "react";
		import ReactDOM from "react-dom";
		
		function App() {
		  const inputElement = useRef();
		
		  const focusInput = () => {
		    inputElement.current.focus();
		  };
		
		  return (
		    <>
		      <input type="text" ref={inputElement} />
		      <button onClick={focusInput}>Focus Input</button>
		    </>
		  );
		}
		
		ReactDOM.render(<App />, document.getElementById('root'));

1. useRef Hook can also be used to keep track of previous state values. This is because we are able to persist useRef values between renders.

		import { useState, useEffect, useRef } from "react";
		import ReactDOM from "react-dom";
		
		function App() {
		  const [inputValue, setInputValue] = useState("");
		  const previousInputValue = useRef("");
		
		  useEffect(() => {
		    previousInputValue.current = inputValue;
		  }, [inputValue]);
		
		  return (
		    <>
		      <input
		        type="text"
		        value={inputValue}
		        onChange={(e) => setInputValue(e.target.value)}
		      />
		      <h2>Current Value: {inputValue}</h2>
		      <h2>Previous Value: {previousInputValue.current}</h2>
		    </>
		  );
		}
		
		ReactDOM.render(<App />, document.getElementById('root'));
	> In the useEffect, we are updating the useRef current value each time the inputValue is updated by entering text into the input field.

useRef() save reference

## useCallback
[https://www.w3schools.com/react/react_usecallback.asp](https://www.w3schools.com/react/react_usecallback.asp)

useCallback 返回的是函数的缓存，是一个函数

useCallback(fn, deps) 相当于 useMemo(() => fn, deps)


## useMemo 
[https://www.w3schools.com/react/react_usememo.asp](https://www.w3schools.com/react/react_usememo.asp)


useMemo 返回的是函数的执行结果，是一个值

## User-defined hook
You can write custom Hooks that cover a wide range of use cases like form handling, animation, declarative subscriptions, timers

自定义hook其实就是通过封装组合内置hooks，形成自定义的hook，然后分享给其他组件

# Other Hooks
useContext lets you subscribe to React context without introducing nesting:

	function Example() {
	  const locale = useContext(LocaleContext);
	  const theme = useContext(ThemeContext);
	  // ...
	}

useReducer lets you manage local state of complex components with a reducer

	function Todos() {
	  const [todos, dispatch] = useReducer(todosReducer);
	  // ...

## Redux hooks
React-Redux provides a pair of custom React hooks that allow your React components to interact with the Redux store. 实际上，它主要用来读取state数据

### useSelector
`useSelector` reads a value from the store state and subscribes to updates.  从redux的store对象中提取数据(state)。
> 选择器函数应该是纯函数，因为它可能在任意时间点多次执行。 
> 这个selector方法类似于之前的connect的mapStateToProps参数的概念。并且useSelector会订阅store, 当action被dispatched的时候，会运行selector。

e.g.

	import React from 'react'
	import { useSelector } from 'react-redux'
	
	export const CounterComponent = () => {
	  const counter = useSelector(state => state.counter)
	  return <div>{counter}</div>
	}

### useDispatch
`useDispatch` returns the store's dispatch method to let you dispatch actions. 返回Redux store中对dispatch函数的引用。你可以根据需要使用它。实际上，它主要用来修改state的数据

e.g.

	import React from 'react'
	import { useDispatch } from 'react-redux'
	
	export const CounterComponent = ({ value }) => {
	  const dispatch = useDispatch()
	
	  return (
	    <div>
	      <span>{value}</span>
	      <button onClick={() => dispatch({ type: 'increment-counter' })}>
	        Increment counter
	      </button>
	    </div>
	  )
	}

### useStore()
这个Hook返回redux <Provider>组件的store对象的引用。

这个钩子应该不长被使用。useSelector应该作为你的首选。因为如果store中的state改变，这个将不会自动更新

# Reference
[Introducing Hooks](https://reactjs.org/docs/hooks-intro.html)

[React Hooks 入门教程](https://www.ruanyifeng.com/blog/2019/09/react-hooks.html)

[轻松学会 React 钩子：以 useEffect() 为例](https://www.ruanyifeng.com/blog/2020/09/react-hooks-useeffect-tutorial.html)

[Making Sense of React Hooks](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889)

[Build a CRUD App in React with Hooks](https://www.taniarascia.com/crud-app-in-react-with-hooks/)

[为什么Redux需要reducers是纯函数](https://mingjiezhang.github.io/2017/02/11/%E7%BF%BB%E8%AF%91-%E4%B8%BA%E4%BB%80%E4%B9%88Redux%E9%9C%80%E8%A6%81reducers%E6%98%AF%E7%BA%AF%E5%87%BD%E6%95%B0-md/)

[useSelector: 别啦 connect](https://juejin.cn/post/6844903874197880840)

[React 实践心得：react-redux 之 connect 方法详解](https://segmentfault.com/a/1190000015042646)