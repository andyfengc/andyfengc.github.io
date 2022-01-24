---
layout: post
title: ReactJS tutorial - life cycle
author: Andy Feng
---

# Introduction
组件的生命周期分成三个状态：

- Mounting：已插入真实 DOM
- Updating：正在被重新渲染
- Unmounting：已移出真实 DOM

React 为每个状态都提供了两种处理函数，`will` 函数在进入状态之前调用，`did` 函数在进入状态之后调用，三种状态共计五种处理函数。

- componentWillMount()
- componentDidMount()
	> 通常使用 componentDidMount 方法通过 Ajax 请求数据，等到数据请求成功，再用 this.setState
- componentWillUpdate(object nextProps, object nextState)
- componentDidUpdate(object prevProps, object prevState)
- componentWillUnmount()

此外，React 还提供两种特殊状态的处理函数。

c- omponentWillReceiveProps(object nextProps)：已加载组件收到新的参数时调用
- shouldComponentUpdate(object nextProps, object nextState)：组件判断是否重新渲染时调用

# Demo
clock demo, display a clock and automatically refresh every second: 

	class Clock extends React.Component {
	  constructor(props) {
	    super(props);
	    this.state = {date: new Date()};
	  }
	
	  componentDidMount() {
	    this.timerID = setInterval(
	      () => this.tick(),
	      1000
	    );
	  }
	
	  componentWillUnmount() {
	    clearInterval(this.timerID);
	  }
	
	  tick() {
	    this.setState({
	      date: new Date()
	    });
	  }
	
	  render() {
	    return (
	      <div>
	        <h1>Hello, world!</h1>
	        <h2>It is {this.state.date.toLocaleTimeString()}.</h2>
	      </div>
	    );
	  }
	}
	
	ReactDOM.render(
	  <Clock />,
	  document.getElementById('root')
	);

1. When <Clock /> is passed to ReactDOM.render(), React calls the constructor of the Clock component. Since Clock needs to display the current time, it initializes this.state with an object including the current time. We will later update this state.

1. React then calls the Clock component’s render() method. This is how React learns what should be displayed on the screen. React then updates the DOM to match the Clock’s render output.

1. When the Clock output is inserted in the DOM, React calls the componentDidMount() lifecycle method. Inside it, the Clock component asks the browser to set up a timer to call the component’s tick() method once a second.

1. Every second the browser calls the tick() method. Inside it, the Clock component schedules a UI update by calling setState() with an object containing the current time. Thanks to the setState() call, React knows the state has changed, and calls the render() method again to learn what should be on the screen. This time, this.state.date in the render() method will be different, and so the render output will include the updated time. React updates the DOM accordingly.

1. If the Clock component is ever removed from the DOM, React calls the componentWillUnmount() lifecycle method so the timer is stopped.


# Reference
