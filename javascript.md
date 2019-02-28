#### JS基础知识点

* 原始类型有哪几种？null是对象嘛？
  - 存在6中原始类型：boolean, null, undefined, number, string, symbol。object不是基本数据类型，是引用数据类型
  - null不是对象类型，虽然typeof null会输出object, 这是因为在js的最初版本中使用的是32位系统，
  为了性能考虑使用低位存储变量的类型信息，000开头代表是对象，然而null表示为全零，所以将它错误的判断为object

* 对象类型和原始类型的不同之处？函数参数是对象会发生什么问题？
  - 在js中，除了原始类型其他的都是对象类型，原始类型存储的是值，对象类型存储的是地址（指针），当创建一个对象类型的时候，内存中会储存这个值，这个值有一个地址（指针），当把变量赋值给另一个变量时，进行数据修改时，另个变量的值都会发生改变
  - 函数传参是传递对象指针的副本，如果重新为参数对象赋值另一个对象，则这个参数对象有了一个全新的地址（指针），则两个变量的值就会不同

* typeof是否能正确判断类型？instanceof 能正确判断对象的原理是什么？
  - typeof对于原始类型来说，除了null都可以显示正确的类型，对于对象来说，除了函数都会显示object, 所以typeof并不能判断变量是什么类型
  - instanceof 内部机制是通过原型链来判断的, 判断某个对象是否是某个构造函数的实例，判断原始类型的可以借助Symbol.hasInstance 属性判断该对象是否某个构造函数的实例
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
  - 在js中，类型转换只有3种情况，分别是转换为布尔值，转换为数字，转换为字符串
  ![类型转换表格](./image/transform.png?raw=true '类型转换表格')
  - 在条件判断时，除了undefined, null, false, NaN, '', 0, -0其他所有值都转为true, 包括所有对象  (undefined == null true 都转为false)
  - 对象转原始类型
    - 已经是原始类型就不要转换了
    - 调用x.valueOf(), 如果转换为基础类型，就返回转换的值
    - 调用x.toString(), 如果转换为基础类型，则返回转换的值
  ```javascript
  // add(2)(3)(4)..
  function add(x) {
    var sum = x
    // 递归返回函数
    var temp = function (y) {
      sum += y
      return temp
    }
    // 为函数对象添加一个toString方法
    temp.toString = function () {
      return sum
    }
    return temp
  }
  // console.log(add(1)(2)(3));  //6
  // console.log(add(1)(2)(3)(4));   //10
  ```
  - -、*、/、%：一律转换为数值后计算
  - +:
    - 数字 + 字符串 = 字符串
    - 数字 + 对象，优先调用valueOf -> toString
    - 数字 + boolean / null = 数字
    - 数字 + undifined == NaN
  - [1].toString() === '1'
  - {}.toString() === '[object object]'
  - NaN !== NaN 、+undifined === NaN
  - [] == ![], [1] == [1](false, 因为引用地址不一样), [] == []（同理）

* [] == ![] // true [链接](https://github.com/jawil/blog/issues/1) 
  1、！的运算优先级高于== , 先转[]
  2、对象 == Boolean，根据 js的抽象相等比较算法，[] == ToNumber(Boolean)进行比较
  3、[] == ToNumber(false)
  4、对象 == 数字，按照规则，ToPrimitive([]) == 0, ToPrimitive默认调用toString方法，所以'' == 0
  5、String == number, 变成toNumber('') == 0, 而toNumber('') == 0, 所以0 == 0，结束, true

* 总结的==规则
  - undefined == null，结果是true。且它俩与所有其他值比较的结果都是false。
  - String == Boolean，需要两个操作数同时转为Number。
  - String/Boolean == Number，需要String/Boolean转为Number。
  - Object == Primitive，需要Object转为Primitive(具体通过valueOf和toString方法)。

* 简单类型放在stack里，对象类型放在heap(堆)里，在stack放着对象类型的**引用变量（指针）**，堆比栈大，栈比堆的运算速度快。

* 类型判断
  - null: String(null)
  - string / number / boolean / undefined / function: typeof
  - Array / Date / RegExp Error: toString后根据[object XXX]判断

* 事件的触发过程是怎么样的？事件代理？  
  事件触发三个阶段：i. window往事件触发处传播，遇到注册的捕获事件会触发；ii. 传播到事件触发处时触发注册的事件；iii. 从事件触发处往window传播，遇到注册的冒泡事件会触发。如果给一个body的子节点同时注册冒泡和捕获事件，事件触发会按照注册的顺序执行

* DOM事件中的target和currentTarget的区别  
  event.target是触发事件的元素目标，event.currentTarget是指当前正在处理事件的元素，简单说就是当嵌套div时，点击事件同时注册了多个div，父级div会接受到子div通过事件冒泡上来的事件，此时内部触发这次事件的div就是target，父级div就是currentTarget, 记住触发事件是target，监听事件是currentTarget即可

* 事件委托  
  是指利用DOM事件冒泡的原理，使子元素的时间冒泡到父级组件时，由父级监听并处理的过程，只需要在父级监听对应子级事件并判断当前触发的节点是否为子级并执行对应业务操作即可，达到只监听一个元素与节点就可以处理每个指定节点对应的操作

* this
  - this的优先级：首先，new 的方式优先级最高，接下来是 bind 这些函数，然后是 obj.foo() 这种调用方式，最后是 foo 这种调用方式，同时，箭头函数的 this 一旦被绑定，就不会再被任何方式所改变
  ![this](./image/this.png?raw=true 'this')

* 闭包
  - 可以简单的理解为：函数A内部有一个函数B，函数B可以访问到函数A中的变量，那么函数B就是闭包, 闭包的意义就是让我们可以间接的访问到函数内的变量
  - 复杂的说，父函数被销毁的情况下，返回的子函数[[scope]]仍保留了父级的作用域链，可以继续访问父级的变量对象。

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
  变量可以通过函数参数形式传入，避免使用默认的[[scope]]向上查找
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
  通过setTimeout包裹，通过第三个参数传入
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
  使用块级作用域，让变量变为自己上下文的属性，避免共享
  ```javascript
  for (let i = 1; i <= 5; i++) {
    setTimeout(function timer() {
      console.log(i)
    }, i * 1000)
  }
  ```

* 什么是浅拷贝？如何实现浅拷贝？什么是深拷贝？如何实现深拷贝？
  - 浅拷贝: 以赋值的形式拷贝引用对象，仍指向同一个地址，修改时原对象也会受到影响
    - Object.assign()
    - 展开运算符(...)
  - 深拷贝：完全拷贝一个新对象，修改时原对象不再受到任何影响
    - JSON.parse(JSON.stringify(object))
      - 具有循环引用的对象时，报错
      - 当值为函数或undefined时，无法拷贝
    - 递归进行逐一赋值 

* 什么是原型？如何理解原型链？(补充资料: 王福朋的原型和闭包javascript)

  每个js对象都有__proto__属性，可以通过这个属性找到一个原型对象，原型对象的constructor属性指向构造函数，构造函数又通过prototype属性指向原型（除了Function.prototype.bind()就没有这个属性）
  ![原型链](./image/prototype-chain.png?raw=true '原型链')
  原型链就是多个对象通过__proto__的方式连接起来
  - Object是所有对象的父级，所有对象可以通过__proto__找到它
  - Function 是所有函数的父级，所有函数都可以通过__proto__找到它
  - 函数的prototype是一个对象
  - 对象的__proto__属性指向原型，__proto__将对象和原型链接起来组成原型链


* 什么是提升？什么是暂时性死区？var, let, const区别？
  - 在js中可以使用未被声明的变量，这种情况就叫提升，并且提升的是声明，js中会把要使用的变量声明提升。  
  - var声明的变量会发生提升情况，不仅变量会提升而且函数也会提升，函数提升的优先级还优于变量提升  
  - 在全局作用域使用let和const声明变量，变量并不会挂载到window上，let和const因为存在暂时性死区，不能在声明之前就使用变量，let和const作用基本一致，但是后者声明的变量不能再次赋值
  

* 原型如何实现继承？Class如何实现继承？Class本质是什么？

  在JS中，继承通常指的是原型链继承，也就是说通过**指定原型**，并可以通过**原型链继承原型上的属性或者方法**

  - 组合继承
  ```javascript
  function Parent(value) {
    this.val = value
  }
  Parent.prototype.getValue = function() {
    console.log(this.val)
  }
  function Child(value) {
    // 继承父类属性
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
  // Object.create(proto, [propertiesObj])
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

  - 补充。圣杯模式
  ```javascript
  function inherit(Target, Origin){
    function F() {};
    F.prototype = Origin.prototype;
    Target.prototype = new F();
    Target.prototype.constuctor = Target;  // constuctor归位
    Target.prototype.uber = Origin.prototype; // 信息储备，想知道继承自谁，先记录下来
  }

  // 高端的写法：利用闭包的私有化变量特性
  var inherit = (function(){
    var F = function(){};      // 闭包，将F当作私有变量，F在执行完后就会销毁，但是在传递原型的断开映射功能已经实现
    return function (Target, Origin){
      F.prototype = Origin.prototype;
      Target.prototype = new F();
      Target.prototype.constuctor = Target;
      Target.prototype.uber = Origin.prototype;
    }
  }())
  inherit(son, father)
  ```

  
* 为什么要使用模块化？哪几种方式可以实现模块化，各有什么特点？  
  模块化的好处：解决命名冲突；提高复用性；提高代码可维护性；  
  - 立即执行函数，通过函数作用域解决命名冲突，污染全局作用域的问题
  - AMD和CMD
    - AMD: 采用异步方式加载模块，加载完之后立即执行回调，AMD是依赖前置的，须在头部提前先定义好依赖
    - CMD: 推崇依赖就近，延迟执行。倾向于在使用过程中提出依赖
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

  * CommonJS与ES Module的区别
  ES Module是原生实现的模块化方案，与CommonJS的区别是  
  - CommonJS支持动态导入，也就是require(${path}/xx.js)，后者目前不支持  
  - CommonJS是同步导入，因为在服务端，文件都在本地，同步导入即使卡主主线程影响也不大，后者是异步导入，主要用户浏览器，需要下载文件，如果采用同步导入对渲染会有很大的影响  
  - CommonJS在导出时是值拷贝，就算导出的值变了，导入的值也不会改变，所以要想更新值，必须重新导入一次，但是ES Module采用实时绑定的方式，导入导出的值都指向同一个内存地址，所以导入值会跟随导出值变化  
  - ES Module会编译成require/exports来执行

* proxy可以实现什么功能  
  proxy可以理解成是目标对象的一个代理器，可以在外界对该对象访问的时候进行过滤和改写
  ```javascript
  var obj = new Proxy({}, {
    get: function (target, key, receiver) {
      console.log(`getting ${key}!`);
      return Reflect.get(target, key, receiver);
    },
    set: function (target, key, value, receiver) {
      console.log(`setting ${key}!`);
      return Reflect.set(target, key, value, receiver);
    }
  });
  ```
  proxy可以实现响应式，自定义set和get函数的方式，实现在对对象属性任何属性进行读写时发出通知，proxy无需一层层递归的为每个属性添加代理，一次完成，性能更好，而且可以完美监听任何方式的的数据改变


------


#### JS异步编程
* 并发(concurrency)和并行(parallelism)的区别  
  并发是宏观概念，比如有任务A和任务B，在一段时间内通过任务间的切换完成了这两个任务，称之为并发。  
  并行是微观概念，假设CPU同时存在两个核心，那么可以同时完成A、B，同时完成多个任务的情况称之为并行。  

* 什么是回调函数？回调函数有什么缺点？如何解决回调地狱问题？  
  把函数作为参数传入到另一个函数中，这个函数就是所谓的回调函数。回调函数比较容易写出回调地狱。回调地狱的根本原问题是：嵌套函数存在耦合性，一旦有所改动，就会牵一发动全身，嵌套函数一多，很难处理错误。不能使用try catch捕获错误，不能直接return。可以用Generator和Promise

* Promise的特点是什么？分别有什么优缺点？什么是Promise链？Promise构造函数执行和then函数执行有什么区别？  
  Promise有pending、resolved、rejectedw三种状态，一但从等待状态变为其他状态就不能再改变状态了，在构造函数Promise的时候，构造函数内部的代码是立即执行的，Promise实现了链式调用，每次调用then之后返回都是一个全新的Promise, 如果在then中使用return函数，那么return的值会被Promise.resolve()包装，Promise可以解决回调地狱，但是无法取消，错误需要通过回调函数捕获。

* async及await的特点，优缺点是什么？await原理是什么？  
  一个函数加上async就会返回Promise，将返回值使用Promise.resolve()包裹了下，和then中处理返回值一样，并且await只能配合async使用，可以说是异步终极解决方案了，好处是处理then的调用链，能够写出更清晰准确的代码，而且优雅的解决回调地狱的问题，缺点是await将异步代码改造成了同步代码，如果多个异步代码没有依赖性却使用await会导致性能上的降低。await是generator加上Promise的语法糖，且内部实现了自动执行generator。

* setTimeout、setInterval和requestAnimationFrame的特点？
  setTimeout设置程序延时多长时间后执行，因为JS是单线程的原因，如果代码发生了阻塞，就会导致setTimeout不会按时执行，setInterval也不能保证在预期的时间执行任务，还存在执行累积的问题，如果定时器执行过程出现耗时操作，多个回调函数会在耗时操作结束以后同时执行，可能会带来性能上的问题。requestAnimationFrame自带函数节流功能，可以保证在16毫秒内只执行一次（不掉帧的情况下），并且该函数的延时效果是精确的，没有其他定时器不准的问题

* 手写Promise(重要)

* 节流和防抖
  - 防抖(debounce): 将多次高频操作优化为只在最后一次执行，场景：用户输入，在输入完成后做校验
  - 节流(throttle): 将高频操作优化为低频操作，每隔一段时间(100~500ms)执行一次，场景：滚动条时间或者resize事件
  ```javascript
  // 节流
  const throttle = (func, wait, immediate) => {
    let timer = null
    let callNow = true

    return function() {
      let args = arguments
      let context = this

      if (callNow) {
        func.apply(context, args)
        callNow = true
      }

      if (!timer) {
        timer = setTimeout(() => {
          func.apply(context, args)
          timer = null
        }, wait)
      }
    }
  }

  // 防抖
  const debounce = (func, wait, immediate) => {
    let timer = null

    return function () {
      let args = arguments

      if (immediate && !timer) {
        func.apply(this, args)
      }

      if (timer) clearTimeout(timer)
      timer = setTimeout(() => {
        func.apply(this, args)
      }, wait)
    }
  }
  ```

* babel编译原理
  - babylon将ES6/ES7代码解析为AST
  - babel-traverse对AST进行遍历转义，得到新的AST
  - 新AST通过babel-generator转换成ES5

* 数组
  - slice(\[start\], \[end\]): 返回截断后的新数组，不改变原数组
  - splice(start, number, value...): 返回删除元素组成的数组，value为插入值，改变原数组

* 深克隆
  ```javascript
  const isType = (obj, type) => {
    if (typeof obj !== 'object') return false;
    // 可以访问到内部属性[[class]]的值
    const typeString = Object.prototype.toString.call(obj);
    let flag;
    switch (type) {
      case 'Array':
        flag = typeString === '[object Array]';
        break;
      case 'Date':
        flag = typeString === '[object Date]';
        break;
      case 'RegExp':
        flag = typeString === '[object RegExp]';
        break;
      default:
        flag = false;
    }
    return flag;
  };

  /**
  * deep clone
  * @param  {[type]} parent object 需要进行克隆的对象
  * @return {[type]}        深克隆后的对象
  */
  const clone = parent => {
    // 维护两个储存循环引用的数组
    const parents = [];
    const children = [];

    const _clone = parent => {
      if (parent === null) return null;
      if (typeof parent !== 'object') return parent;

      let child, proto;

      if (isType(parent, 'Array')) {
        // 对数组做特殊处理
        child = [];
      } else if (isType(parent, 'RegExp')) {
        // 对正则对象做特殊处理
        child = new RegExp(parent.source, getRegExp(parent));
        if (parent.lastIndex) child.lastIndex = parent.lastIndex;
      } else if (isType(parent, 'Date')) {
        // 对Date对象做特殊处理
        child = new Date(parent.getTime());
      } else {
        // 处理对象原型
        proto = Object.getPrototypeOf(parent);
        // 利用Object.create切断原型链
        child = Object.create(proto);
      }

      // 处理循环引用
      const index = parents.indexOf(parent);

      if (index != -1) {
        // 如果父数组存在本对象,说明之前已经被引用过,直接返回此对象
        return children[index];
      }
      parents.push(parent);
      children.push(child);

      for (let i in parent) {
        // 递归
        child[i] = _clone(parent[i]);
      }

      return child;
    };
    return _clone(parent);
  };
  ```


#### 进阶知识点和常考面试题
------

* call, apply以及bind函数内部实现是怎么样？
  ```javascript
  Function.prototype.myCall = function (context) {
    if (typeof this !== 'function') {
      throw new TypeError('Error')
    }
    context = context || window
    context.fn = this
    // 截取从索引1开始到末尾的参数
    const args = [...arguments].slice(1)
    const result = context.fn(...args)
    delete context.fn
    return result
  }
  ```
  ```javascript
  Function.prototype.myApply = function (context) {
    if (typeof this !== 'function') {
      throw new TypeError('Error')
    }
    context = context || window
    context.fn = this
    let result
    if (arguments[1]) {
      result = context.fn(...arguments[1])
    } else {
      result = context.fn()
    }
    delete context.fn
    return result
  }
  ```
  ```javascript
  Function.prototype.myBind = function (context) {
    if (typeof this !== 'function') {
      throw new TypeError('Error')
    }
    const _this = this
    const args = [...arguments].slice(1)
    // 返回一个函数
    return function F () {
      // 返回一个函数可以new F(), 所以需要判断
      if (this instanceof F) {
        // arguments是new F()传入的参数
        return new _this(...args, ...arguments)
      }
      // someFunc.myBind(obj, 1)(1, 2, 3)
      // arguments = [1, 2, 3]
      // 需要将两边的参数拼接
      return _this.apply(context, args.concat(...arguments))
    } 
  }
  ```
* new
  new的过程发生4件事
  - 新生成一个对象
  - 链接到原型
  - 绑定this
  - 返回新对象
  ```javascript
  function create() {
    let obj = {}
    // 获取构造函数
    let Con = [].shift.call(arguments)
    // 设置空对象原型
    obj.__proto__ = Con.prototype
    // 绑定this并执行构造函数
    let result = Con.apply(obj, arguments)
    // 确保返回值为对象
    return result instanceof Object ? result : obj
  }
  ```
* instanceof的原理
  内部机制是通过判断**对象的原型链是不是能找到类型的prototype**
  ```javascript
  function myInstanceof(left, right) {
    // 获取类型的原型
    let prototype = right.prototype
    // 获取对象的原型
    let left = left.__proto__
    // 循环判断对象的原型是否等于类型的原型，直到对象原型为null
    while (true) {
      if (left === null || left === undefined) {
        return false
      }
      if (prototype === left) {
        return true
      }
      left = left.__proto__
    }
  }
  ```