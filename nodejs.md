#### Nodejs基础知识
（突出是学习的就好了）

* 内存泄露的几种情况
  - 全局变量 全局变量直接挂到root对象上，不会被清除掉
  - 闭包  如果闭包直接挂在到root上也会导致内存泄露
  - 事件监听 比如http模块，不通过keepAlive复用没什么问题，复用了之后就会有可能导致内存泄露
  - 其他原因  缓存， cpu占用过高都有可能

* 什么是错误优先的回调函数  
  错误优先的回调函数用于同时返回错误和数据，第一个参数返回错误，并且验证它是否出错，其他参数用于返回数据

* 什么是Stub  
  Stub用于模拟模块的行为。测试时，Stub可以为函数调用返回模拟的结果。比如说，当我们写文件时，实际上并不需要真正去写

* 测试金字塔  
  测试金字塔反映了需要写的**单元测试**、**集成测试**、以及**端到端测试**的比例

* 调试一个大型的项目时，可以使用监控unhandledRejection事件来捕获所有未处理的Promise错误

* 定位内存泄露
  - 重现内存泄露情况
  - 打印内存快照 - heapdump
  - 对比内存快照找出泄露位置

* 避免内存泄露
  - ESLint 检查非期望的全局变量
  - 清楚闭包对象何时清除
  - 绑定事件释放

* Node的Event Loop: 6个阶段
  - timer阶段：执行到期的setTimeout / setInterval回调
  - I/O阶段：执行上轮循环残留的callback
  - idle, prepare
  - poll：等待回调
    - 1. 执行回调
    - 2. 执行定时器
      - 如有到期的setTimeout / setInterval, 则返回timer阶段
      - 如有setImmediate则前往check阶段
  - check
    - 执行setImmediate
  - close callbacks

* 介绍下使用过的中间件
  - 因为Eggjs已经集成了路由、静态文件挂载、session等开源插件作为中间件处理，所以接触到的大部分是业务的中间件
  - 用户是否在站内（即是否登录）
    - 首先获取请求携带的用户信息态cookie, 过滤出用户id，与存储在session中的用户id做比较，查看是否相同，若相同则代表已登录过并处于缓存保留期间，若session中不存在或cookie过期则直接返回未登录状态码且前端调起登录页面
  - 检查活动是否过期
    - 获取中间件传入活动名的参数，找到config中对应活动的配置，查看当前日期是否符合活动配置所定日期

* require  
  require过的文件会加载到缓存，所以多次require同一个文件（模块）不会重复加载

* 循环引用  
  循环引用不会报错，导致的结果是require的结果是空对象{}, 原因是b require a, a 又去require b, 此时b还没初始化好，所以只能拿到初始值{}, 当产生循环引用时一般有两种方式解决：
  - 分离公用的代码到另一个文件解决
  - 不在最外层require，在用到的地方require, 通常在函数的内部

* Nblog涉及的知识点  
  - [博客链接](https://github.com/nswbmw/N-blog)
  - 功能：注册、登录、登出、查看文章、发表文章、修改文章、删除文章、留言
  - 权限控制：把用户状态的检查封装成中间件，在每个需要权限控制的路由加载该中间件，检查请求的session有没有user信息
  - 中间件
  ```javascript
  const express = require('express')
  const app = express()

  app.use(function (req, res, next) {
    console.log('1')
    next()
  })

  app.use(function (req, res, next) {
    console.log('2')
    res.status(200).end()
  })

  app.listen(3000)
  ```
  - 框架
    - express
    - supervisor（热更新）
    - path-to-regexp(express的路由)
    - express.Router
    - ejs
      - includes拆分模板
    - express-session: 会话支持中间件
      - cookie存储在浏览器，session存储在服务端
      - 通常session的实现是基于cookie的，session id 存储于cookie中
    - connect-mongo: 将session存储于mongodb, 结合express-session使用
    - connect-flash: 页面通知中间件，基于session实现
    - express-formidable：接受表单并文件上传的中间件
    - marked: markdown解析
    - express-winston: express的winston日志中间件
    - config-lite: 轻量级读取配置文件的模块
  - Robomongo Mongo可视化管理工具  
    - 首先启动mongodb服务：\> mongod
    - 根据用户名和密码建立连接，然后连接
  - 测试
    - mocha和supertest常用的测试组合，通常用来测试resful api
    - istanbul: 测试覆盖率
  - pm2 进程管理工具，进程守护工具
    - 

