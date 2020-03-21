#### React基础知识点

* 生命周期  
  为了解决复合组件改动最上层组件state，调用栈很长的问题，如果中间有复杂操作可能导致长时间阻塞主线程，带来不好的用户体验。V16引入Fiber机制，Fiber本质上是一个虚拟的堆栈帧，新的调度器会优先级自由调度这些帧，将之前的同步渲染改为异步渲染，在不影响体验的情况下去分段计算更新。这个机制一定程度影响了生命周期的调用，并且引入2个新的API来解决这个问题。对于动画这种实时性很高的东西，React会每16ms暂停一下更新，返回来继续渲染动画  
  异步渲染，现在渲染有两个阶段： `reconciliation(和解)`和`commit`， 前者可以打断，后者不能暂停，一直更新界面直到完成

  - Reconciliation阶段
    - `constructor`
    - `getDedivedStateFromProps`
    - `componentWillMount`
    - `componentWillReceiveProps`
    - `shouldComponentUpdate`(除了这个，其他这个阶段api尽量避免去使用)
    - `componentWillUpdate`
    - `render`
  - Commit阶段
    - `getSnapshotBeforeUpdate` 
    - `componentDidMount`
    - `componentDidUpdate`
    - `componentWillUnmount`

  初始化：初始进入页面 → constructor → componentWillMount → render → componentDidMount → componentWillUnmount
  更新：setState → componentWillReceiveProps → shouldComponentUpdate → componentWillUpdate → render → componentDidUpdate → componentWillUnmount

  
  引入新的API
  - `getDerivedStateFromProps` 用于替换`componentWillReceiveProps`, 该函数会在初始化和update调用
  - `getSnapshotBeforeUpdate` 用于替换componentWillUpdate, 该函数会在update后DOM更新前调用，用于读取最新的DOM数据

* React常考知识点  
  setState是异步API，只有同步代码执行完毕才会执行，异步的原因个人认为在于，setState可能会导致DOM重绘，设计成异步的话，可以将多次调用放入一个队列中，在恰当的时候统一进行更新过程。如果想每次调用setState都获得正确的state，可以使用setState的回调函数

* React事件机制  
  JSX上写的事件并没有绑定到对应的真实DOM上，而是通过事件代理的方式，将所有事件统一绑定到document上。不仅可以减少内存消耗，还能在组件挂载时统一订阅和移除事件，另外冒泡到document上的事件也不是原生浏览器事件，而是React自己实现的合成事件(SyntheticEvent)，好处有两点：  
  i. 合成事件首先抹平了浏览器之间的兼容问题，另外这是一个跨浏览器原生事件包装器，赋予了跨浏览器开发的能力  
  ii. 对于原生浏览器事件来说，浏览器会给监听器创建一个事件对象。如果你有很多的事件监听，那么就需要分配很多的事件对象，造成高额的内存分配问题。但是对于合成事件来说，有一个事件池专门来管理它们的创建和销毁，当事件需要被使用时，就会从池子中复用对象，事件回调结束后，就会销毁事件对象上的属性，从而便于下次复用事件对象。

* 谈谈你对Redux的理解
  Redux的设计思想：1、web应用是一个状态机，视图和状态是一一对应的；2、所有的状态保存到一个对象里面  
  ![redux流程图](./image/redux-flow.png?raw=true 'redux-flow')

* React Hooks  
  React Hooks可以在class以外使用state和其他React特性。useState, useEffect。

* React 那些生命周期可以 setState
  无意义使用：componentWillMount，componentWillUnmount；
  有条件使用：componentDidUpdate；
  禁止使用：componentWillUpdate，shouldComponentUpdate；
  正常使用：componentWIllReceiveProps，componentDidMount。

  componentWillMount和componentWillReceiveProps中，setState会被react内部处理，而不触发render；
  其他生命周期均正常触发正常渲染

  setState的执行原理，可以分为两类：

  1、批量更新类：即react内部的执行函数，执行setState的执行逻辑，都是批量更新处理，被收集起来延迟处理，其中包括：**react内部事件(合成事件)和生命周期**；

  2、非批量更新类：即上面两种情况以外的情况，经常见到的：原生事件、setTimeout、fetch等等；

其他见mindnode的思维导图


* webpack 优化
* 应用优化 - 