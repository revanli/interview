#### 浏览器基础知识点

* 事件的触发过程是怎么样的？事件代理？  
  事件触发三个阶段：i. window往事件触发处传播，遇到注册的捕获事件会触发；ii. 传播到事件触发处时触发注册的事件；iii. 从事件触发处往window传播，遇到注册的冒泡事件会触发。如果给一个body的子节点同时注册冒泡和捕获事件，事件触发会按照注册的顺序执行

* 跨域  
  i.JSONP：利用\<script\>标签没有跨域限制的漏洞，通过\<script\>标签指向一个需要访问的地址并提供一个回调函数来接受函数。
  ii.CORS：服务端设置Access-Control-Allow-Origin就可以开启CORS, 这种方式发送的请求会出现简单请求和复杂请求两种情况。
  iii. PostMessage 这种方式通常用于获取嵌入页面中的第三方页面数据，一个页面发送消息，另一个页面判断来源并接受消息
  ```javascript
  // 发送消息端
  window.parent.postMessage('message', 'http://test.com')
  // 接收消息端
  var mc = new MessageChannel()
  mc.addEventListener('message', event => {
    var origin = event.origin || event.originalEvent.origin
    if (origin === 'http://test.com') {
      console.log('验证通过')
    }
  })
  ```

* 浏览器缓存机制  
* 浏览器渲染原理  

* 重绘和回流  
  - 重绘：当节点需要更改外观而不影响布局的  
  - 回流：布局或者几何属性需要改变，会触发回流操作
    - 页面初次渲染
    - 浏览器窗口大小改变
    - 元素尺寸、位置、内容发生变化
    - 元素字体大小变化
    - 添加或删除可见dom
    - 激活CSS伪类
  - 减少重绘和回流  
    - 使用transform代替style位置的改变
    - 使用visibility代替display: none
    - 不要使用table，一个小的改动也会造成整个table重新布局
    - 动画实现的速度选择，动画速度也快，回流次数越多，也可以使用requestAnimationFrame实现
    - css选择符从右往左匹配查找，避免节点层级过多
    - 将频繁重绘或者回流的节点设置为图层，图层能够阻止该节点的渲染行为影响别的节点

* 怎么加快渲染速度  
  DOMContentLoaded时间，提早这个时间就加快了渲染速度  
  i. 从文件大小考虑  
  ii. 从script标签使用上考虑
  iii. 从css, html的代码书写上考虑
  iv. 从需要下载的内容是否需要在首屏上使用来考虑
  
 
* 跨标签页通讯
  - 父页面window.open()和子页面postMessage
    - window.open('about: blank')和tab.location.href = '*'
  - 设置同域下共享的localStorage和监听window.onstorage
    - 重复写入相同的值无法触发
    - 受浏览器隐身模式等的限制
  - 设置共享cookie和不断轮询脏检查(setinterval)
  - 借助服务端或中间层实现


* 进程与线程区别？JS单线程带来的好处？  
  进程描述了CPU在运行指令及加载和保存上下文所需的时间，放在应用上来说就代表了一个程序。线程是进程中的更小单位，描述了执行一段指令所需的时间。JS是单线程运行的，可以节省内存，节省上下文切换时间，没有锁的问题的好处，

* 异步代码执行顺序？解释一下Event Loop?  
  事件循环是指：执行一个宏任务，然后执行清空微任务列表，循环再执行宏任务，再清微任务列表，不停循环
  - 微任务 microtask(jobs): promise / ajax / Object.observe / process.nextTick()
  - 宏任务 macrotask(task): setTimout / setInmediate / setInterval / script / IO / UI Rendering

* 从浏览器输入URL到页面渲染的整个过程  
  - DNS解析
  - TCP三次握手
  - 发送请求，分析URL，设置请求报文(头，主体)
  - 服务器返回请求文件(html)
  - 浏览器渲染
    - HTML parser --> DOM Tree
      - 标记化算法，进行元素状态标记
      - dom树构建
    - CSS parser --> Style Tree
      - 解析css样式，生成样式树
    - attachment --> Render Tree
      - 结合dom树与style树，生成渲染树
    - layout: 布局
    - GPU渲染

* V8垃圾回收机制
垃圾回收: 将内存中不再使用的数据进行清理，释放出内存空间。V8 将内存分成 新生代空间 和 老生代空间。
- 新生代空间: 用于存活较短的对象
  - 又分成两个空间: from 空间 与 to 空间
  - Scavenge GC算法: 当 from 空间被占满时，启动 GC 算法
    - 存活的对象从 from space 转移到 to space
    - 清空 from space
    - from space 与 to space 互换
    - 完成一次新生代GC
- 老生代空间: 用于存活时间较长的对象
  - 从 新生代空间 转移到 老生代空间 的条件
    - 经历过一次以上 Scavenge GC 的对象
    - 当 to space 体积超过25%
  - 标记清除算法: 标记存活的对象，未被标记的则被释放
    - 增量标记: 小模块标记，在代码执行间隙执，GC 会影响性能
    - 并发标记(最新技术): 不阻塞 js 执行
  - 压缩算法: 将内存中清除后导致的碎片化对象往内存堆的一端移动，解决 内存的碎片化

- 内存泄露
  - 意外的全局变量：无法被回收
  - 定时器： 未被正确的关闭
  - 事件监听： 未被正确的销毁
  - 闭包： 父级中变量无法释放
  - dom引用：dom元素被删除时，内存中的引用未被正确清空
  可用 chrome 中的 performance 进行内存标记，可视化查看内存的变化情况，找出异常点


* cookie, localStorage, sessionStorage, indexDB
  |特性|cookie|localStorage|sessionStorage|indexDB|微信storage|
  |:-----:|:-----------------:|:------------:|:-------------:|:--------------:|:--------------:|
  |数据生命周期|一般由服务器生成，可以设置过期时间|除非清理，否则一直存在|页面关闭就清理|除非清理，否则一直存在|除非清理，否则一直存在|
  |数据存储大小|4k|5M|5M|无限|10M|
  |服务端通信|在header携带|不参与|不参与|不参与|不参与|
