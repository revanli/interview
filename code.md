#### 手写Promise
  [BAT前端经典面试问题：史上最最最详细的手写Promise教程](https://juejin.im/post/5b2f02cd5188252b937548ab)
  Promise/A+规范
  - 一个 Promise 必须处于 3 个状态中的其中一个  
    pending（可转换 fulfilled 或 rejected）  
    fulfilled（不能转换为其他状态）  
    rejected（不能转换为其他状态）  
  - thenable then 是可以串行的链式调用，且具有 onFulfilled 和 onRejected 为可选参数
  - value 是promise成功状态时的值
  - 错误时必须有抛出异常的原因（reason）

1、Promise声明  
  ```javascript
  class Promise {
    constructor(excutor) {
      // 成功
      let resolve = () => {}
      // 失败
      let reject = () => {}
      // 立即执行
      excutor(resolve, reject)
    }
  }
  ```

2、解决基本状态
  ```javascript
  class Promise {
    constructor(excutor) {
      // 初始化state为等待态
      this.state = 'pending'
      // 成功的值
      this.value = undefined
      // 失败的原因
      this.reason = undefined
      // 成功
      let resolve = value => {
        // state改变，resolve调用就会失败
        if (this.state === 'pending') {
          // resolve调用后，state转化为成功态
          this.state = 'fulfilled'
          this.value = value
        }
      }
      // 失败
      let reject = reason => {
        if (this.state == 'pending') {
          this.state = 'rejected'
          this.reason = reason
        }
      }
      // executor函数执行报错，直接执行reject
      try {
        executor(resolve, reject)
      } catch (err) {
        reject(err)
      }
    }
  }
  ```

3、then方法
  ```javascript
  class Promise {
    constructor(excutor) {
      ...
    }

    // then方法，有两个参数onFulfilled onRejected
    then(onFulfilled, onRejected) {
      // 状态为fulfilled, 执行onFulfilled, 传入成功的值
      if (this.state === 'fulfilled') {
        onFulfilled(this.value)
      }
      // 状态为rejected，执行onRejected，传入失败的原因
      if (this.state === 'rejected') {
        onRejected(this.reason)
      }
    }
  }
  ```

4、解决异步实现  
  ```javascript
  class Promise {
    constructor(executor) {
      ...
      // 成功存放的数组
      this.onResolvedCallbacks = []
      this.onRejectedCallbacks = []

      let resolve = value => {
        if (this.state === 'pending') {
          ...
          this.onResolvedCallbacks.forEach(fn => fn())
        }
      }
      let reject = value => {
        if (this.state === 'rejected') {
          ...
          this.onRejectedCallbacks.forEach(fn => fn())
        }
      }
      ...
    }
    then(onFulfilled, onRejected) {
      ...
      // 当状态state为pending时
      if (this.state === 'pending') {
        // onFulfilled传入到成功数组
        this.onResolvedCallbacks.push(() => {
          onFulfilled(this.value)
        })
        // onRejected传入到失败数组
        this.onRejectedCallbacks.push(() => {
          onRejected(this.reason)
        })
      }
    }
  }
  ```

5、解决链式调用
  ```javascript
  class Promise {
    constructor(excutor) {
      ...
    }

    // then方法，有两个参数onFulfilled onRejected
    then(onFulfilled, onRejected) {
      // 声明返回的promise2
      let promise2 = new Promise((resolve, reject) => {
        if (this.state === 'fulfilled') {
          let x = onFulfilled(this.value)
          resolvePromise(promise2, x, resolve, reject)
        }
        if (this.state === 'rejected') {
          let x = onRejected(this.reason)
          resolvePromise(promise2, x, resolve, reject)
        }
        if (this.state === 'pending') {
          this.onResolvedCallbacks.push(() => {
            let x = onFulfilled(this.value)
            resolvePromise(promise2, x, resolve, reject)
          })
          this.onRejectedCallbacks.push(() => {
            let x = onRejected(this.reason)
            resolvePromise(promise2, x, resolve, reject)
          })
        }
      })
      // 返回promise，完成链式
      return promise2
    }
  }
  ```

6、完成resolvePromise函数
  ```javascript
  function resolvePromise(promise2, x, resolve, reject){
    // 循环引用报错
    if(x === promise2){
      // reject报错
      return reject(new TypeError('Chaining cycle detected for promise'));
    }
    // 防止多次调用
    let called;
    // x不是null 且x是对象或者函数
    if (x != null && (typeof x === 'object' || typeof x === 'function')) {
      try {
        // A+规定，声明then = x的then方法
        let then = x.then;
        // 如果then是函数，就默认是promise了
        if (typeof then === 'function') { 
          // 就让then执行 第一个参数是this   后面是成功的回调 和 失败的回调
          then.call(x, y => {
            // 成功和失败只能调用一个
            if (called) return;
            called = true;
            // resolve的结果依旧是promise 那就继续解析
            resolvePromise(promise2, y, resolve, reject);
          }, err => {
            // 成功和失败只能调用一个
            if (called) return;
            called = true;
            reject(err);// 失败了就失败了
          })
        } else {
          resolve(x); // 直接成功即可
        }
      } catch (e) {
        // 也属于失败
        if (called) return;
        called = true;
        // 取then出错了那就不要在继续执行了
        reject(e); 
      }
    } else {
      resolve(x);
    }
  }
  ```

7、 最终版
  ```javascript
  class Promise{
    constructor(executor){
      this.state = 'pending';
      this.value = undefined;
      this.reason = undefined;
      this.onResolvedCallbacks = [];
      this.onRejectedCallbacks = [];
      let resolve = value => {
        if (this.state === 'pending') {
          this.state = 'fulfilled';
          this.value = value;
          this.onResolvedCallbacks.forEach(fn=>fn());
        }
      };
      let reject = reason => {
        if (this.state === 'pending') {
          this.state = 'rejected';
          this.reason = reason;
          this.onRejectedCallbacks.forEach(fn=>fn());
        }
      };
      try{
        executor(resolve, reject);
      } catch (err) {
        reject(err);
      }
    }
    then(onFulfilled,onRejected) {
      // onFulfilled如果不是函数，就忽略onFulfilled，直接返回value
      onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
      // onRejected如果不是函数，就忽略onRejected，直接扔出错误
      onRejected = typeof onRejected === 'function' ? onRejected : err => { throw err };
      let promise2 = new Promise((resolve, reject) => {
        if (this.state === 'fulfilled') {
          // 异步
          setTimeout(() => {
            try {
              let x = onFulfilled(this.value);
              resolvePromise(promise2, x, resolve, reject);
            } catch (e) {
              reject(e);
            }
          }, 0);
        };
        if (this.state === 'rejected') {
          // 异步
          setTimeout(() => {
            // 如果报错
            try {
              let x = onRejected(this.reason);
              resolvePromise(promise2, x, resolve, reject);
            } catch (e) {
              reject(e);
            }
          }, 0);
        };
        if (this.state === 'pending') {
          this.onResolvedCallbacks.push(() => {
            // 异步
            setTimeout(() => {
              try {
                let x = onFulfilled(this.value);
                resolvePromise(promise2, x, resolve, reject);
              } catch (e) {
                reject(e);
              }
            }, 0);
          });
          this.onRejectedCallbacks.push(() => {
            // 异步
            setTimeout(() => {
              try {
                let x = onRejected(this.reason);
                resolvePromise(promise2, x, resolve, reject);
              } catch (e) {
                reject(e);
              }
            }, 0)
          });
        };
      });
      // 返回promise，完成链式
      return promise2;
    }
  }
  ```

* 另一个版本
  ```javascript
  var Promise = function (fn) {
    var deffereds = [],
        value = null,
        state = 'pending'

    var resolve = function (newValue) {
      // 2. 引入状态判断与变换
      if (state === 'rejected') return ;
      if (newValue && (typeof newValue === 'object' || typeof newValue === 'function')) {
        var then = newValue.then
        if (typeof then === 'function') {
          then.call(newValue, resolve, reject)
          return
        }
      }
      value = newValue
      state = 'fulfilled'
      final()
    }

    function reject (reason) {
      // 2. 引入状态判断与变换
      if (state === 'fulfilled') return ;
      state = 'rejected'
      value = reason
      final()
    }

    // 3. 串行链式调用
    this.then = function (onFulfilled, onRejected) {
      return new Promise((resolve, reject) => {
        handle({
          onFulfilled: onFulfilled || null,
          onRejected: onRejected || null,
          resolve: resolve,
          reject: reject
        })
      })
    }

    // 4. 异常错误处理
    this.catch = function (errFn) {
      if (state === 'rejected') errFn(value)
    }

    // 1. 延时异步实现
    function final () {
      setTimeout(() => {
        deffereds.forEach(deffered => {
          handle(deffered)
        })
      }, 0)
    }

    function handle (deffered) {
      // 2. 引入状态判断与变换
      if (state === 'pending') {
        deffereds.push(deffered)
        return ;
      }
      var cb = state === 'fulfilled' ? deffered.onFulfilled : deffered.onRejected
      // 当出错并且没有传入 onRejected 时，直接执行 reject 方法将 state 变为 rejected 状态
      // 此时如果是 resolve 时代码出错时我们便需要监测到并且手动执行 reject 犯法，所以下面我们要使用 try catch 抓取此情况
      if (cb === null) {
        cb = state === 'fulfilled' ? deffered.resolve : deffered.reject
        cb(value)
        return ;
      }
      // 4. 异常错误处理
      try {
        // 如若执行 promise.resolve 时出错这里会抓取异常并执行 reject 方法将异常发出去
        var ret = cb(value)
        deffered.resolve(ret)
      } catch (err) {
        deffered.reject(err)
      }
    }

    fn(resolve, reject)
  }
  ```

* 手写XMLHttpRequest
  ```javascript
  var xhr = new XMLHttpRequest()
  xhr.onreadystatechange = function () {
    // 这里的函数异步执行，可参考之前 JS 基础中的异步模块
    if (xhr.readyState == 4) {
      if (xhr.status == 200) {
        alert(xhr.responseText)
      }
    }
  }
  xhr.open("GET", "/api", false)
  xhr.send(null)
  ```