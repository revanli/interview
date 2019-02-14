* 说一下你项目中用到的技术栈，以及觉得得意和出色的点，以及让你头疼的点，怎么解决的

  用到的技术栈：vuejs，reactjs, nodejs, eggjs, wepy, vuex, redux。(通读一遍vue, react, egg, vuex的官方文档)

  1. 企悦跑小程序：由于业务比较复杂，所以使用wepy让小程序支持组件化开发和redux来管理用户状态。
    i. 组件化：按照业务需求，把每个大的业务需求（比如跑团功能，跑团有成员、活动的增删查改，有成员的PK活动, 招商银行有达标榜），按照增删查改的功能，拆分成独立的组件，在父组件用$invoke直接调用子组件的主方法，子组件用$emit和父组件通信。
    ii. redux管理用户状态：36个页面，其中30个页面需要微信登陆或者悦跑圈的账户信息，因此需要判断如果在storage没有角色信息则需要通过异步请求来获取角色信息，也就是说需要对页面进行过滤，有微信登陆信息和悦跑圈账户信息才通过，不通过则需要登陆或者绑定悦跑圈账号。但是小程序没有中间件的概念，所以要从Page实例的onShow后者onRoute入手来进行身份校验，如果每个页面都这样做会导致很多冗余的代码，于是考虑用装饰者模式，把这部分角色校验的函数单独抽出作为过滤函数，将page实例作为参数传入进行角色判断，在过滤函数中把异步获取后的角色信息存入redux(在页面中定义保存信息的action)中，把角色信息共享到其他页面，还可以在页面添加回调函数，用于校验身份后的页面跳转，这样来达到校验函数和组件代码解耦的目的。

  2. 活动的时效性和用户信息：活动的一个业务需求就是页面在活动的有效期间以及有个人信息的页面在悦跑圈App内才能打开，在Vue的做法是在每个页面的路由守卫beforeRouteEnter函数中请求后端接口做校验，在用React的时候没有这个api, 于是从react-router-dom入手，封装一个组件用来做路由跳转，校验通过就渲染页面并把接口数据传入组件中，不通过则返回到index页面，这样做的好处是不用在每个页面做校验，减少请求次数。

  3. 根据vue-cli开发适合公司H5活动的vue-template和ypweb-cli脚手架
  使用Node进行脚手架开发，使用async/await解决异步回调问题，使用babel编译
  ![ypweb-cli](./image/ypweb-cli.png?raw=true 'ypweb-cli')  

--------



#### 可能会问到的问题  
细节看博客[深入wepy小程序组件化框架](https://juejin.im/post/5987370e6fb9a03c42430a30)

* 为什么选用wepy?  
  - 开发风格接近Vue。支持组件Props传值，自定义时间，mixin, computed, slot等
  - 组件化。组件化开发，解决组件隔离，组件嵌套，组件通信问题
  - NPM。支持第三方NPM资源
  - Promise。解决回调烦恼
  - 优化。对小程序本身的优化，请求队列的处理，生命周期补充，性能优化等

* wepy如何实现单文件组件.wpy编译？  
  先看整体的流程图
  ![整体流程图](./image/wepy-cli-compile.png?raw=true 'wepy-cli-compile')  
  wepy框架通过wepy-cli对.wpy编译，拆解为style, script(+config), template几部分，再分别处理，生成到dist文件对应xxx.wxss, xxx.script, xxx.json, xxx.wxml

* 如何隔离组件作用域  
  通过组件在不同page的命名作为前缀，并且以父级为起点，依次为$child, 再子级就是$child$child, 依次类推，不同组件在不同的component实例下，data set到page就是带上前缀，同样method也是加入前缀放在Page({})中

* 如何实现组件通信  
  答：通过编译获取component的路径注入代码，在小程序代码运行时，根据逐层require获取，new component，并记下父级$parent，构建组件树。如果向子组件传props和events?编译时就会收集在template中传入的props和events注入到代码中$props和$events,然后子组件init的时候获取父级$parent的$props并加入前缀$prefix去setData（子组件的在page中的元素表现已经在编译的时候被替换成了$prefix$data的样子），这样就实现了传值。调用$emit触发父组件event，直接寻找父级$parent apply调用相应方法即可。广播事件broadcast就是直接广度优先去遍历组件树就行了。  

* 如何实现加载外部npm包  
  答：wepy-cli在处理script部分，根据require的内容判断是否是npm内容或者带有npm标识，如果是require('xxx') require('xxx/yyy')的形式获取package.json中的main部分找到引用文件，就去compile该文件（带上npm标识继续去resolveDeps），如果判断不是npm内容修正require即可，带有npm标识最后会打包到npm文件夹。
  
* 谈谈你对Redux的理解
  Redux的设计思想：1、web应用是一个状态机，视图和状态是一一对应的；2、所有的状态保存到一个对象里面  
  ![redux流程图](./image/redux-flow.png?raw=true 'redux-flow')

* vuex的理解  
  ![vuex流程图](./image/vuex.png?raw=true 'vuex-flow')


* 怎么编写eggjs的中间件  
  eggjs是基于Koa实现的，所以Egg的中间件的形式和Koa的中间件形式是一样的，都是基于洋葱圈模型，每次编写一个中间件，就相当于在洋葱外面包了一层。eggjs约定一个中间件放在app/middleware目录下的单独文件，exports一个普通的function, 接受两个参数：中间件配置项options和当前Application实例app，如果需要在应用中全局使用中间件，则在config.default.js中加入到middleware的数组中，在应用层定义的中间件(app.config.appMiddleware)和框架默认中间件（app.config.coreMiddleware）都会被加载器加载，并挂载到app.middleware上，也可以在router上使用中间件，针对单个路由生效。检验活动和校验UID都是针对单个路由生效的。
  