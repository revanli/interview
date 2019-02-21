#### Nodejs基础知识

* 内存泄露的集中情况
  - 全局变量 全局变量直接挂到root对象上，不会被清除掉
  - 闭包  如果闭包直接挂在到root上也会导致内存泄露
  - 事件监听 比如http模块，不通过keepAlive复用没什么问题，复用了之后就会有可能导致内存泄露
  - 其他原因  缓存， cpu占用过高都有可能

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