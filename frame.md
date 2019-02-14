* 什么是MVVM?  
  Vue和React都不是MVVM框架，只是有借鉴MVVM的思路，MVVM采用双向绑定：View的变动会自动反映到ViewMod，反之亦然，Angular和Ember都是MVVM框架。Vue和React借鉴了MVVM的思路， 在MVVM架构中，引入了ViewModel的概念。ViewModel只关心数据和业务的处理，不关心View如何处理数据，在这种情况下，View和Model都可以独立出来，任何一方改变也不一定需要改变另一方，并且可以将一些可复用的逻辑放在ViewModel中，让多个View复用这个ViewModel。以Vue框架举例，ViewModel就是组件的实例。View就是模板，Model的话在引入Vuex的情况下是可以完全和组件分离的。除了这三个部分，MVVM中还引入一个隐式的Binder层，实现View和ViewMode的绑定。以Vue举例，这个隐式的Binder层就是Vue通过解析模板中的插值和指令从而实现View与ViewModel的绑定。对于 MVVM 来说，其实最重要的并不是通过双向绑定或者其他的方式将 View 与 ViewModel 绑定起来，而是通过 ViewModel 将视图中的状态和用户的行为分离出一个抽象，这才是 MVVM 的精髓。

* 什么是Virtual DOM? 为什么Virtual DOM比原生DOM快？  
  

* 前端路由原理？两种实现方式有什么区别？  
  原理是监听URL的变化，然后匹配路由规则，显示响应的页面，并且无需刷新页面，目前前端有两种实现方式：Hash模式，History模式  
  i. Hash模式：当URL#后面的哈希值发生变化时，可以通过hashchange时间监听到URL的变化，从而进行页面跳转  
  ii. History模式： History模式是HTML5新推出的功能，主要使用history.pushState和history.replaceState改变URL，点击后退按钮会触发popState事件  
  对比：1、Hash模式只可以更改#后面的内容，History模式可以通过API设置任意的同源URL  
  2、Hash模式无需后端配置，并且兼容性良好。History模式在用户手动输入或者刷新页面的时候会发起URL请求，后端需要配置index.html页面用于匹配不到静态资源的时候

* Vue和React之间的区别  
  i.Vue表单可以使用v-model支持双向绑定，相对于React来说开发上更加方便， 虽然v-model是语法糖，本质上和React写表单的方式没什么区别  
  ii. 改变数据方式不同，Vue在底层使用了依赖追踪，页面更新渲染已经是最优，React使用setState来改变状态，并且这个API也有一些坑的地方
  iii. React需要使用JSX，有一定的上手成本，但是可以通过JS来控制页面，更加灵活，颗粒度更小。Vue使用了模板语法，相对于JSX没那么灵活。

* Vue的生命周期函数
  i. beforeCreate钩子函数调用的时候，是获取不到props和data中的数据，这些数据的初始化在initState中
  ii. 然后执行created函数，可以访问之前不能访问到的数据，但是组件还没挂载  
  iii. 执行beforeMount函数，开始创建VDOM，最后执行mounted钩子，并将VDOM渲染为真实DOM并且渲染数据。组件中有子组件的话，会递归挂载子组件，子组件全部挂载完毕，才会执行根组件的挂载钩子  
  iv. 数据更新调用beforeUpdate和updated，分别是数据更新前和更新后调用  
  v. keep-alive独有生命周期。activated和deactivated，keep-alive包裹的组件在切换组件时不会进行销毁，而是缓存到内存中并执行deactivated钩子函数，命中缓存函数会执行activated钩子函数  
  vi. 最后是销毁组件的beforeDestory和destoryed函数。前者适合移除事件、定时器，如果有子组件，也会递归销毁子组件，所有子组件销毁完毕后执行根组件的destroyed钩子函数  

* 组件通信  
  i. 父子通信。通过props和emit实现单向数据流的父子通信方式。还可以用$parent和$children对象来访问组件实例中的方法和数据。$listeners属性会将父组件中的事件监听器传递给子组件，子组件可以通过访问$listeners来自定义监听器  
  ii. 兄弟组件通信。可以查找父组件中的子组件，也就是this.$parent.$children中可以通过name查询到需要的组件实例，然后进行通信  
  iii. 跨多层级组件通信。可以使用provide / inject  
  iv. 任意组件。Event Bus或者Vuex

* extend的作用  
  作用是扩展组件生成一个构造器，通常会与$mount一起使用

* mixin和mixins区别  
  mixin用于全局混入，会影响到每个组件实例。mixins是常用的扩展组件的方式，如果多个组件有相同的业务逻辑，可以将这些逻辑剥离出来，通过mixins混入代码，mixins混入的钩子函数会优先于组件内的钩子函数执行，遇到同名的选项的时候会选择性的进行合并

* computed和watch区别  
  computed是计算属性，依赖其他属性计算值，并且computed的值有缓存，只有当计算属性值变化才会返回内容。watch监听到值的变化就会执行回调，在回调中进行一些逻辑操作

* 响应式原理  
  i. vue在初始化的时候通过递归遍历对象所有属性的方式，把每个属性都通过Object.defineProperty的方式使对象变为可观察的。
  ii. 在对象被读到的时候，触发自定义的getter方法把观察者加入到订阅者数组中，之后当该对象被写的时候，会触发自定义的setter方法，通知订阅者调用notify函数来触发所有的观察者对象update()方法更新对应的视图

* Object.defineProperty的缺陷  
  通过下标方式修改数组数据或者给对象新增属性，Object.defineProperty是不能拦截到这些操作的，对于数组而言，大部分操作都是不能拦截到的，Vue内部通过重写函数的方式解决了这个问题。

* NextTick原理分析  
  因为vue是使用批量异步更新策略来更新视图的。在默认的情况下，每次触发某个数据的setter方法后，对应的观察者对象会被push进一个队列，在下一个tick的时候将队列全部拿出来跑一遍
  nextTick可以让我们在下次DOM更新循环结束之后执行延迟回调，用于获得更新后的DOM。默认使用microtasks,特殊情况下使用macrotasks,比如v-on，对于macrotasks，先判断能否使用setImmediate，不能的话降级为MessageChannel以上不行的话就使用setTimeout。
  
* 双向绑定原理  
  vue数据双向绑定是通过数据劫持结合发布者-订阅者模式的方式实现的。实现mvvm主要包含两个方面，数据变化更新视图，视图变化更新数据。关键点是在于data如何更新view, 因为view更新data只需要通过时间监听即可，比如input标签监听input事件就可以实现了。

* Vue和React数据绑定对比(https://www.jianshu.com/p/6e124ad23c68)  
  在数据绑定上，vue的特色是双向数据绑定，react是单向数据绑定。  
  数据从V(View)->M(model)的绑定是通过事件监听实现的，那么重点就放在对数据从M->V的绑定  
  i. 在React应用中，当某个组件的状态发生变化时，它会以该组件为根，重新渲染整个组件子树。当然可以通过shouleCompounentUpdate这个生命周期来进行控制purerender,但Vue将此视为默认优化。  
  ii. vue中实现数据绑定靠的是数据劫持(Object.defineProperty()) + 发布-订阅模式。在vue应用中，组件的依赖是在渲染过程中自动追踪的，所以能精确知道那个组件需要被重渲染，可以理解为每一个组件都自动获得了shouleComponentUpdate，并且没有上述子树问题限制。  
  ![react-update](./image/react-update.webp?raw=true 'react-update')
  ![vue-update](./image/vue-update.webp?raw=true 'vue-update')

* 简单比较vue和react的差异，并说出对应的优缺点？  
  i. react 是完全基于视图的单向绑定，通过 AST 抽象生成树来实现 Virtual DOM 到真实 DOM 的更新，通过 diff 虚拟 dom 树，使其能够局部更新对应的真实 DOM，是基于事件的双向绑定，通过元素如点击事件等响应改变状态从而触发 diff；
  ii. Vue 是基于劫持 dom 并重构以及 defineProperty 定义访问器所实现的双向绑定，通过触发属性 setter 访问器来实现 watch 队列的遍历更新视图；  
  iii. Vue 推崇模版写法，所以许多操作 Vue 已经代替开发者处理了，导致整体的 api 较多，而自由度相对没有这么高，比如有 计算属性 computed、通过 setter 监听的 watch、当没有在 data 中定义属性而后续加入双向绑定时使用 set 方法以及为了实现数组双向绑定而重写了 data 中数组的 push、pop、slice 等方法；  
  iv. 相比之下 React 则较自由，更考量开发者的 js 功底，基于事件的更新 state 触发 diff 算法而更新视图、由于对象或数组地址未变化而导致视图无法 diff 时通过合并为新对象或数组触发等，整体的开发思路与 Vue 有些许差异，但是纯 js 的写法会导致如果组件颗粒度不够细或抽象的不够好时，整个代码看起来会比较臃肿;  
  v. 但两者都做到了前端的组件化，所以个人认为没有优劣，还是要看项目的大小，如果是中大型项目且开发者水平都中上，对 js 比较熟悉，那么后续将组件抽象化程度以及颗粒度变细后，会极大发挥 react 自由度的优势，相比 vue 更好维护点;  
  而在中小型项目时，组件复用性并没有这么强的情况下，vue 快速开发的优势就会体现出来，并且是模版开发的模式，对开发者的水平要求没有这么高，因为有大量的 api 给予开发者使用，省去了开发者的学习成本，所以需要快速开发的中小型项目个人认为 vue 更有优势，还有最重要的一点是要看团队整体水平及方向来决定。

* React常考知识点  
  setState是异步API，只有同步代码执行完毕才会执行，异步的原因个人认为在于，setState可能会导致DOM重绘，设计成异步的话，可以将多次调用放入一个队列中，在恰当的时候统一进行更新过程。如果想每次调用setState都获得正确的state，可以使用setState的回调函数

* React事件机制  
  JSX上写的事件并没有绑定到对应的真实DOM上，而是通过事件代理的方式，将所有事件统一绑定到document上。不仅可以减少内存消耗，还能在组件挂载时统一订阅和移除事件，另外冒泡到document上的事件也不是原生浏览器事件，而是React自己实现的合成事件(SyntheticEvent)，好处有两点：  
  i. 合成事件首先抹平了浏览器之间的兼容问题，另外这是一个跨浏览器原生事件包装器，赋予了跨浏览器开发的能力  
  ii. 对于原生浏览器事件来说，浏览器会给监听器创建一个事件对象。如果你有很多的事件监听，那么就需要分配很多的事件对象，造成高额的内存分配问题。但是对于合成事件来说，有一个事件池专门来管理它们的创建和销毁，当事件需要被使用时，就会从池子中复用对象，事件回调结束后，就会销毁事件对象上的属性，从而便于下次复用事件对象。

* Vue原理流程图
  ![vue流程图](./image/vue-reactive.png?raw=true 'vue-reactive')