---
layout: post
title: ReactJS tutorial - router
author: Andy Feng
---

# Introduction
Flux类似MVC，是一个框架，解决大型应用的结构问题。Flux 组织代码和安排内部逻辑，使得你的应用更易于开发和维护。
> Flux存在多种实现（[至少15种](https://github.com/voronianski/flux-comparison)）
> Flux 是一种应用架构，或者说是一种思想，它跟 React 本身没什么关系，它可以用在 React 上，也可以用在别的框架上

# Structure
Flux将一个应用分成四个部分。

- View： 视图层(React组件)
- Action（动作）：视图层发出的消息（比如mouseClick）
- Dispatcher（派发器）：用来接收Actions、执行回调函数
- Store（数据层）：用来存放应用的状态，一旦发生变动，就提醒Views要更新页面

> 当用户与 View 进行交互时，View 通过 Dispatcher 将 Action 分发到保存应用程序数据和业务逻辑的 Store，然后再更新受影响的 View
![](/images/posts/20220120-react-7.jpg)

1. 所有数据都流经 Dispatcher，提供给 Dispatcher 的 Action 是通过 Action creator 方法创建的对象，这通常来自用户与视图的交互。
1. Dispatcher 通过调用注册在 Store 中的回调函数完成 Action 到 Store 的分发。在已注册的回调中，Store 将响应与其所维护状态相关的具体操作。
1. Store 触发 change 事件，将数据层的变化通知到 Controller-views。
1. Controller-views 监听这些事件，并从 event handler 中读取来自 Stores 的数据。
1. Controller-views 调用他们自己的 setState() 方法，从而在组件树中更新自身及其所有后代。

Flux 的最大特点，就是数据的"单向流动"。
> 数据总是"单向流动"，任何相邻的部分都不会发生数据的"双向流动"。这保证了流程的清晰。

![](/images/posts/20220120-react-8.jpg)

1. 用户访问 View
1. View 发出用户的 Action
1. Dispatcher 收到 Action，要求 Store 进行相应的更新
1. Store 更新后，发出一个"change"事件
1. View 收到"change"事件后，更新页面

# Flux vs. Redux
Redux 的作用跟 Flux 是一样的，它可以看作是 Flux 的一种实现，但是又有点不同，具体的不同总结起来就是：

1. Redux 只有一个 store 

	Flux 里面会有多个 store 存储应用数据，并在 store 里面执行更新逻辑，当 store 变化的时候再通知 controller-view 更新自己的数据，Redux 将各个 store 整合成一个完整的 store，并且可以根据这个 store 推导出应用完整的 state。同时 Redux 中更新的逻辑也不在 store 中执行而是放在 reducer 中。

2. 没有 Dispatcher 

	Redux 中没有 Dispatcher 的概念，它使用 reducer 来进行事件的处理，reducer 是一个纯函数，这个函数被表述为 (previousState, action) => newState，它根据应用的状态和当前的 action 推导出新的 state。Redux 中有多个 reducer，每个 reducer 负责维护应用整体 state 树中的某一部分，多个 reducer 可以通过 combineReducers 方法合成一个根reducer，这个根reducer负责维护完整的 state，当一个 action 被发出，store 会调用 dispatch 方法向某个特定的 reducer 传递该 action，reducer 收到 action 之后执行对应的更新逻辑然后返回一个新的 state，state 的更新最终会传递到根reducer处，返回一个全新的完整的 state，然后传递给 view。

在我看来，Redux 和 Flux 之间最大的区别就是对 store/reducer 的抽象，Flux 中 store 是各自为战的，每个 store 只对对应的 controller-view 负责，每次更新都只通知对应的 controller-view；而 Redux 中各子 reducer 都是由根reducer统一管理的，每个子reducer的变化都要经过根reducer的整合。

# Reference
[flux official](https://facebook.github.io/flux/)
[flux github](https://github.com/facebook/flux)

[Flux 架构入门教程](https://www.ruanyifeng.com/blog/2016/01/flux.html)

[Flux examples](https://github.com/ruanyf/extremely-simple-flux-demo)