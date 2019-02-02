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

