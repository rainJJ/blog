# blog

- **实现new 方法**
  
    原理：
    
      1.新生成一个对象
      2.将构造函数的作用域赋值给新对象（即绑定新对象的 this）
      3.执行构造函数中的代码（即为这个新对象添加属性）
      4.返回新对象
  ```javascript
  function myNew(func, ...args) {
    // 创建一个新对象且将其隐式原型指向构造函数原型
    const obj = Object.create(func.prototype);
    // 执行构造函数，即绑定 this，并且为这个新对象添加属性
    func.apply(obj, args);
    return obj;
  }
  ```

- **call 实现**
  
  原理：将要改变this指向的方法挂到目标this上执行并返回
  
  ```javascript
  Function.prototype.myCall = function (context) {
    if (typeof this !== 'function') {
      throw new TypeError('not a function');
    }
    context = context || window
    // 获取调用call的函数
    context.fn = this
    let arg = [...arguments].slice(1)
    // 执行该函数
    let result = context.fn(...arg)
    // 删除该函数
    delete context.fn
    return result
  }
  ```
  
- **apply 实现**
  
  原理：将要改变this指向的方法挂到目标this上执行并返回
  
  ```javascript
  Function.prototype.myApply = function (context) {
    if (typeof this !== 'function') {
      throw new TypeError('not funciton')
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
  
- **bind 实现**
  ```javascript
  Function.prototype.myBind = function (context) {
    if (typeof this !== 'function') {
      throw new TypeError('Error')
    }
    let _this = this
    let arg = [...arguments].slice(1)
    return function F() {
      // 处理函数使用new的情况
      if (this instanceof F) {
        return new _this(...arg, ...arguments)
      } else {
        return _this.apply(context, arg.concat(...arguments))
      }
    }
  }
  ```

- **instanceof的原理**

  思路原理：右边变量的原型存在于左边变量的原型链上

  ```javascript
  function instanceOf(left, right) {
    let leftValue = left.__proto__
    let rightValue = right.prototype
    while (true) {
      if (leftValue === null) {
        return false
      }
      if (leftValue === rightValue) {
        return true
      }
      leftValue = leftValue.__proto__
    }
  }
  ```

- **Object.create的基本实现原理**

  思路：将传入的对象作为原型

  ```javascript
  function myCreate (obj) {
    function F() {};
    F.prototype = obj;
    return new F();
  }
  ```

- **函数节流与函数防抖**

  ##### 函数节流

  > 高频事件触发，但在n秒内只会执行一次，所以节流会稀释函数的执行频率

  ```javascript
  function throttle (fn, delay) {
    // 利用闭包保存时间
    let prev = Date.now();
    return function () {
      let context = this;
      let now = Date.now();
      if (now - prev >= delay) {
        fn.apply(context, arguments);
        prev = Date.now();
      }
    }
  }

  function fn () {
    console.log('节流');
  }
  addEventListener('scroll', throttle(fn, 1000))
  ```

  ##### 函数防抖

  > 触发高频事件后n秒内函数只会执行一次，如果n秒内高频事件再次被触发，则重新计算时间

  ```javascript
  function debounce (fn, delay) {
    // 利用闭包保存定时器
    let timer = null;
    return function () {
      let context = this;
      let arg = arguments;
      // 在规定时间内再次触发会先清除定时器后再重设定时器
      clearTimeout(timer)
      timer = setTimeout(function () {
        fn.apply(context, arg);
      }, delay);
    }
  }

  function fn () {
    console.log('防抖');
  }
  addEventListener('scroll', debounce(fn, 1000))
  ```


- **算法题**

  ```javascript
  // 编写一个程序将数组扁平化去并除其中重复部分数据，最终得到一个升序且不重复的数组
  const arr = [ [1, 2, 2], [3, 4, 5, 5], [6, 7, 8, 9, [11, 12, [12, 13, [14] ] ] ], 10];

  // [...new Set(arr.flat(Infinity))].sort((a, b) => a-b)
  ```
