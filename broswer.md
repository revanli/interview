#### 浏览器基础知识点

* 事件的触发过程是怎么样的？事件代理？  
  事件触发三个阶段：i. window往事件触发处传播，遇到注册的捕获事件会触发；ii. 传播到事件触发处时触发注册的事件；iii. 从事件触发处往window传播，遇到注册的冒泡事件会触发。如果给一个body的子节点同时注册冒泡和捕获事件，事件触发会按照注册的顺序执行

* 跨域  
  i.JSONP：利用<script>标签没有跨域限制的漏洞，通过<script>标签指向一个需要访问的地址并提供一个回调函数来接受函数。
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
  重绘：当节点需要更改外观而不影响布局的  
  回流：布局或者几何属性需要改变  
  减少重绘和回流  
  i. 使用transform代替style位置的改变
  ii. 使用visibility代替display: none
  iii. 不要使用table，一个小的改动也会造成整个table重新布局
  iv. 动画实现的速度选择，动画速度也快，回流次数越多，也可以使用requestAnimationFrame实现
  v. css选择符从右往左匹配查找，避免节点层级过多
  vi. 将频繁重绘或者回流的节点设置为图层，图层能够阻止该节点的渲染行为影响别的节点

* 怎么加快渲染速度  
  DOMContentLoaded时间，提早这个时间就加快了渲染速度  
  i. 从文件大小考虑  
  ii. 从script标签使用上考虑
  iii. 从css, html的代码书写上考虑
  iv. 从需要下载的内容是否需要在首屏上使用来考虑
  
 
* 节流和防抖  
  节流是指频繁的操作，隔一段时间只操作一次；防抖是指频繁的操作，隔一段时间停止了操作在触发。
  ```javascript
  // 节流
  const throttle = (func, wait = 50) => {
    let lastTime = 0
    return function(...args) {
      let now = +new Date()
      if (now - lastTime > wait) {
        lastTime = now
        func.apply(this, args)
      }
    }
  }


  // 防抖
  const debounce = (func, wait = 50) => {
    let timer = 0
    return function (...args) {
      if (timer) clearTimeout(timer)
      timer = setTimeout(() => {
        func.apply(this, args)
      }, wait)
    }
  }
  ```