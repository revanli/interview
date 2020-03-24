## 性能优化

### 网络相关
  - DNS预解析
  `<link rel="dns-prefetch" href="//baidu.com">`
  - 缓存
  强缓存和协商缓存
  - 使用 http/2.0
  - 预加载
  `<link rel="preload" href="http://example.com" />`
  - 预渲染
  `<link rel="prerender" href="http://example.com" />`

### 优化渲染过程
  - 懒执行
  将某些逻辑延迟到使用时再计算，一般通过定时器或者事件调用唤起
  - 懒加载
  将不关键的资源延后加载。图片data-src懒加载，进入可视化区域才播放视频

### 文件优化
  - 图片优化
    - 图片加载优化
      - 不用图片
      - 请求屏幕相应大小的图片
      - 小图用base64
      - 雪碧图
      - SVG替代
  - 其他文件优化
    - css 文件放到 head 中
    - 服务端开启文件压缩功能
    - script 放到 body 底部，js执行阻塞渲染，可以加上defer, 并行下载，会放到html解析后顺序执行，async 加载和渲染dom元素和js文件加载和执行并行执行
  - CDN

### webpack优化
  - 代码压缩
  - tree shaking，移除没有使用的代码(https://webpack.docschina.org/guides/tree-shaking/)
  - 优化图片，小图可以使用base64写入到文件中
  - 按照路由拆分代码，实现按需加载
  - 为打包出来的文件添加哈希