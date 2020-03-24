* 说一下你项目中用到的技术栈，以及觉得得意和出色的点，以及让你头疼的点，怎么解决的。

  用到的技术栈：vuejs，reactjs, nodejs, eggjs, wepy, vuex, redux，antd-mobile。(通读一遍vue, react, egg, vuex的官方文档)

  - 框架方面
    - 浏览器缓存：为了解决因为浏览器缓存导致客户改动了需求但是没有看到更新的问题
      - html-webpack-plugin: 在项目根目录中，根据模板生成带有动态hash值的build文件的html文件
      - clean-webpack-plugin: 删除之前dist文件夹下的build文件
    - 解决重复性配置：根据webpack-simple模板配置vue-template和脚手架ypweb-cli
      - ypweb-cli: 使用Node进行脚手架开发，使用async/await解决异步回调问题，使用babel编译
        ![ypweb-cli](./image/ypweb-cli.png?raw=true 'ypweb-cli')

  - 业务方面
    - 活动的时效性和用户信息：活动的一个业务需求就是页面在活动的有效期间以及有个人信息的页面在悦跑圈App内才能打开，在Vue的做法是在每个页面的路由守卫beforeRouteEnter函数中请求后端接口做校验，在用React的时候没有这个api, 于是从react-router-dom入手，封装一个组件用来做路由跳转，校验通过就渲染页面并把接口数据传入组件中，不通过则返回到index页面，这样做的好处是不用在每个页面做校验，减少请求次数。

    - 企悦跑小程序：由于业务比较复杂，所以使用wepy让小程序支持组件化开发和redux来管理用户状态。
      - 组件化：按照业务需求，把每个大的业务需求（比如跑团功能，跑团有成员、活动的增删查改，有成员的PK活动, 招商银行有达标榜），按照增删查改的功能，拆分成独立的组件，在父组件用$invoke直接调用子组件的主方法，子组件用$emit和父组件通信。

      - redux管理用户状态：36个页面，其中30个页面需要微信登陆或者悦跑圈的账户信息，因此需要判断如果在storage没有角色信息则需要通过异步请求来获取角色信息，也就是说需要对页面进行过滤，有微信登陆信息和悦跑圈账户信息才通过，不通过则需要登陆或者绑定悦跑圈账号。但是小程序没有中间件的概念，所以要从Page实例的onShow后者onRoute入手来进行身份校验，如果每个页面都这样做会导致很多冗余的代码，于是考虑用装饰者模式，把这部分角色校验的函数单独抽出作为过滤函数，将page实例作为参数传入进行角色判断，在过滤函数中把异步获取后的角色信息存入redux(在页面中定义保存信息的action)中，把角色信息共享到其他页面，还可以在页面添加回调函数，用于校验身份后的页面跳转，这样来达到校验函数和组件代码解耦的目的。

    - 移动端图片压缩上传的功能的坑：主要是利用canvas绘制图片并导出base64来压缩图片。
      - 在IOS下，竖着拍完照上传会出现照片自动旋转的情况，横着拍照并上传则不会出现这种情况。用exif.js读取图片的元信息，对图片进行相应的旋转修复
      - 在IOS下，图片大于2M，会出现图片压扁的情况，比例失调（ios的bug,超过 2 megapixel）。而且在低版本的safari下，绘制超过2M的图片会出现不能正常渲染的情况，这个时候就使用瓦片绘制的方式避免这个问题
      用lrz解决了上述的两个问题

    - 单页面分享失败的问题：在每个页面使用mixins混入分享设置
  

* janos-admin 项目

  - 保证代码质量
    ESlint 规范保证代码质量, 使用 git hooks 在 husky 或者 pre-commit 在本地提交之前做一次 Lint 校验， lint-staged
    只校验检查你提交或者你修改部分的内容
  - 路由懒加载
    - babel 的插件 `babel-plugin-dynamic-import-node`, 将所有 import 转化为 require()
  - axios
    封装 axios ，每一个请求携带 token，response 拦截器，根据code处理不同的状态逻辑
  - 前后端交互
    - 跨域问题：cors  Access-Control-Allow-Origin: ''
    - dev环境：`webpack-dev-server`的`proxy`
    - 开发环境：nginx 反向代理
  - mock数据
    - mockjs
  - 登录权限
    - 登录后拿到 token ，前端根据 token 发起一个 user_info 的接口来获取用户的详细信息
    - 权限校验：通过 token 获取用户对应的 role, 根据用户 role 算出对应权限的路由，然后通过 router.addRoutes 动态挂载这些路由
    - 后端会验证每一个涉及请求的操作，验证是否有该操作权限
  - 动态换肤
  - icon
    - icon font

* react 动态路由 动态组件

* 前端编辑器的实现
  - 编辑器生成页面 JSON 数据，服务端负责存取 JSON 数据，渲染时从服务端取数据 JSON 交给前端处理即可。

  - 工程数据接口
  ```
  {
    name: '',
    description: '',
    coverImage: '',
    author: '',
    pages: []
  }
  ```
  - 页面配置信息
  ```
  {
    name: '',
    elements: [],
    commonStyle: {

    },
    config: {}
  }
  ```
  - 元素配置
  ```
  {
    elName: '',
    animations: [],
    commonStyle: {
      position: 'absolute',
      size: ,
      padding: ,
      margin: ,
      padding: ,
      border: ,
      font: ,
      background: ,
      zIndex: ,
    }
  }
  ```
  - 基础组件： 文字、图片、按钮、表单组件、swiper组件、弹窗组件
  - 核心： Vue 动态组件 is判断 component name
  - 组件管理：本地plugin文件管理，也可以上传到局域网 npm 管理
  - 元素属性编辑：
    - 公共属性样式编辑：commonStyle 编辑
    - props属性编辑：1、为每一个prop属性开发一个属性编辑组件 2、获取组件 props 对象 3、通过 key 判断显示哪些属性编辑组件
  - 元素添加事件
    - 提供事件mixins混入到组件中，每个事件方法返回promise，元素被点击时按顺序执行事件方法
  - 页面插入js脚本
    - 将脚本以 script 标签形式嵌入
  - 渲染模板
    - `"vue-cli-service build --mode library --target lib --name page-engine --dest ./src/output/library/h5-swiper ./src/material/h5-swiper/index.js"`, 用 vue-cli 库打包命令将组件打包成库文件，ejs 模板引入该页面组件，配合 json 数据就可以渲染出页面

  ```js
  <!-- 全局注册组件 -->
  Vue.component(id, [function | Object])
  ```