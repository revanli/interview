* 原始类型有哪几种？null是对象嘛？

  i. 存在6中原始类型：boolean, null, undefined, number, string, symbol

  ii. null不是对象类型，虽然typeof null会输出object, 这是因为在js的最初版本中使用的是32位系统，
  为了性能考虑使用低位存储变量的类型信息，000开头代表是对象，然而null表示为全零，所以将它错误的判断为object


* 对象类型和原始类型的不同之处？函数参数是对象会发生什么问题？

  i. 在js中，除了原始类型其他的都是对象类型，原始类型存储的是值，对象类型存储的是地址（指针），当创建一个对象类型的时候，内存中会储存这个值，这个值有一个地址（指针），当把变量赋值给另一个变量时，进行数据修改时，另个变量的值都会发生改变

  ii. 函数传参是传递对象指针的副本，如果重新为参数对象赋值另一个对象，则这个参数对象有了一个全新的地址（指针），则两个变量的值就会不同

* typeof是否能正确判断类型？instanceof 能正确判断对象的原理是什么？

  i. typeof对于原始类型来说，除了null都可以显示正确的类型，对于对象来说来说，除了函数都会显示object, 所以typeof并不能判断变量是什么类型

  ii. instanceof 内部机制是通过原型链来判断的, 判断某个对象是否是某个构造函数的实例，判断原始类型的可以借助Symbol.hasInstance 属性判断该对象是否某个构造函数的实例
  ```javascript
  class MyClass {
    [Symbol.hasInstance](x) {
      return x instanceof Array
    }
  }
  var o = new MyClass()
  o instanceof Array
  ```

* 类型转换

  在js中，类型转换只有3种情况，分别是转换为布尔值，转换为数字，转换为字符串

  ![类型转换表格](./image/transform.png?raw=true '类型转换表格')

  在条件判断时，除了undefined, null, false, NaN, '', 0, -0其他所有值都转为true, 包括所有对象  
  对象在转换类型的时候，会调用内置的[[toPrimitive]]函数

* this

  首先，new 的方式优先级最高，接下来是 bind 这些函数，然后是 obj.foo() 这种调用方式，最后是 foo 这种调用方式，同时，箭头函数的 this 一旦被绑定，就不会再被任何方式所改变

  ![this](./image/this.png?raw=true 'this')

* 闭包

  可以简单的理解为：函数A内部有一个函数B，函数B可以访问到函数A中的变量，那么函数B就是闭包, 闭包的意义就是让我们可以间接的访问到函数内的变量

* 使用闭包解决var定义函数的问题

  ```javascript
  for (var i = 1; i <= 5; i++) {
    setTimeout(() => {
      console.log(i)
    }, i * 1000)
  }
  ```
  这里会输出5个6

  i. 方案一：
  ```javascript
  for (var i = 1; i <= 5; i++) {
    ;(function(j) {
      setTimeout(function timer() {
        console.log(j)
      }, j * 1000)
    })(i)
  }
  ```
  ii. 方案二：
  ```javascript
  for (var i = 1; i <= 5; i++) {
    setTimeout(
      function timer(j) {
        console.log(j)
      },
      i * 1000,
      i
    )
  }
  ```
  iii. 方案三：
  ```
  for (let i = 1; i <= 5; i++) {
    setTimeout(function timer() {
      console.log(i)
    }, i * 1000)
  }
  ```

* 什么是浅拷贝？如何实现浅拷贝？什么是深拷贝？如何实现深拷贝？

  对象类型在赋值的时候其实是复制了地址，从而导致改变了一方其他地方都被改变的情况，为了避免这个问题，可以使用浅拷贝来解决这个问题
  i. 浅拷贝：Object.assign() 拷贝所有属性到新对象，属性是对象的话拷贝的是地址, 还可以用...展开运算符实现浅拷贝  
  ii. 深拷贝：通常可以通过JSON.parse(JSON.stringify(object))来解决，这种方法的局限性在于：会忽略undifined, symbol, 不能序列化函数，不能解决循环引用的对象  

* 什么是原型？如何理解原型链？(补充资料: 王福朋的原型和闭包javascript)

  每个js对象都有__proto__属性，可以通过这个属性找到一个原型对象，原型的constructor属性指向构造函数，构造函数又通过prototype属性指向原型
  除了Function.prototype.bind()就没有这个属性

  ![原型链](./image/prototype_chain.png?raw=true '原型链')

  原型链就是多个对象通过__proto__的方式连接起来

  - Object是所有对象的父级，所有对象可以通过__proto__找到它
  - Function 是所有函数的父级，所有函数都可以通过__proto__找到它
  - 函数的prototype是一个对象
  - 对象的__proto__属性指向原型，__proto__将对象和原型链接起来组成原型链


* 什么是提升？什么是暂时性死区？var, let, const区别？

  在js中可以使用未被声明的变量，这种情况就叫提升，并且提升的是声明，js中会把要使用的变量声明提升。  
  var声明的变量会发生提升情况，不仅变量会提升而且函数也会提升，函数提升的优先级还优于变量提升  
  在全局作用域使用let和const声明变量，变量并不会挂载到window上，let和const因为存在暂时性死区，不能在声明之前就使用变量，let和const作用基本一致，但是后者声明的变量不能再次赋值
  

* 原型如何实现继承？Class如何实现继承？Class本质是什么？

  在js中不存在类，class只是语法糖，本质还是函数  

  - 组合继承
  ```javascript
  function Parent(value) {
    this.val = value
  }
  Parent.prototype.getValue = function() {
    console.log(this.val)
  }
  function Child(value) {
    Parent.call(this, value)
  }
  Child.prototype = new Parent()

  const child = new Child(1)

  child.getValue() // 1
  child instanceof Parent // true
  ```
  组合继承的核心是在子类的构造函数中通过Parent.call(this)继承父类的属性，然后改变子类的原型为父类的实例(new Parent())来继承父类的函数  
  优点：构造函数可以传参，不会与父类引用属性共享，可以复用父类的函数  
  缺点：继承父类函数的时候调用了父类构造函数（Child.prototype = new Parent()），导致了子类的原型多了不需要的父类属性（this.val），存在内存上的浪费

  - 寄生组合继承  
  对组合继承进行了优化
  ```javascript
  function Parent(value) {
    this.val = value
  }
  Parent.prototype.getValue = function() {
    console.log(this.val)
  }
  function Child(value) {
    Parent.call(this, value)
  }
  Child.prototype = Object.create(Parent.prototype, {
    constructor: {
      value: Child,
      enumerable: false,
      writable: true,
      configureable: true
    }
  })
  const child = new Child(1)

  child.getValue()
  child instanceof Parent
  ```
  核心是将父类的原型赋值给子类，并且将构造函数设置为子类，解决了无用的父级属性问题，还能正确找到子类的构造函数  

  - Class继承
  ```javascript
  class Parent {
    constructor(value) {
      this.val = value
    }
    getValue() {
      console.log(this.val)
    }
  }
  class Child extends Parent {
    constructor(value) {
      super(value)
      this.val = value
    }
  }

  let child = new Child(1)
  child.getValue() // 1
  child instanceof Parent // true
  ```
  class实现继承的核心是extends继承父类，并且在子类构造函数中必须调用super，可以看成Parent.call(this, value), class的本质还是函数

  
* 为什么要使用模块化？哪几种方式可以实现模块化，各有什么特点？  
  模块化的好处：解决命名冲突；提高复用性；提高代码可维护性；  
  - 立即执行函数，通过函数作用域解决命名冲突，污染全局作用域的问题
  - AMD和CMD
  ```javascript
  // AMD
  define(['./a', './b'], function (a, b) {
    // 加载模块完毕可以使用
    a.do()
    b.do()
  })
  // CMD
  define(function(require, exports, module) {
    // 加载模块
    var a = require('./a')
    a.doSomething()
  })
  ```
  - CommonJS  
  require
  ```javascript
  var module = require('./a.js')
  var module = require('./a.js')
  module.a 
  // 这里其实就是包装了一层立即执行函数，这样就不会污染全局变量了，
  // 重要的是 module 这里，module 是 Node 独有的一个变量
  module.exports = {
    a: 1
  }
  // module 基本实现
  var module = {
  id: 'xxxx', // 我总得知道怎么去找到他吧
  exports: {} // exports 就是个空对象
  }
  // 这个是为什么 exports 和 module.exports 用法相似的原因
  var exports = module.exports 
  var load = function (module) {
    // 导出的东西
    var a = 1
    module.exports = a
    return module.exports
  };
  // 然后当我 require 的时候去找到独特的
  // id，然后将要使用的东西用立即执行函数包装下，over
  ```
  exports 和 module.exports 享有相同的地址，通过改变对象的属性值可以对两者都起效，但是如果直接对exports赋值会导致两者不再指向供一个内存地址，修改并不会对module.exports起效

  - ES Module
  ES Module是原生实现的模块化方案，与CommonJS的区别是
  i. CommonJS支持动态导入，也就是require(${path}/xx.js)，后者目前不支持
  ii. CommonJS是同步导入，因为在服务端，文件都在本地，同步导入即使卡主主线程影响也不大，后者是异步导入，主要用户浏览器，需要下载文件，如果采用同步导入对渲染会有很大的影响
  iii. CommonJS在导出时是值拷贝，就算导出的值变了，导入的值也不会改变，所以要想更新值，必须重新导入一次，但是ES Module采用实时绑定的方式，导入导出的值都指向同一个内存地址，所以导入值会跟随导出值变化
  iiii. ES Module会编译成require/exports来执行