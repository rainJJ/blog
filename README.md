# blog

- **实现new 方法**
  
    原理：
    
      1.新生成一个对象
      2.将构造函数的作用域赋值给新对象（即绑定新对象的 this）
      3.执行构造函数中的代码（即为这个新对象添加属性）
      4.返回新对象
  ```javascript
  function myNew(func) {
    return function () {
      // 创建一个新对象且将其隐式原型指向构造函数原型
      let obj = {};
      obj.__proto__ = func.prototype;
      // 执行构造函数，即绑定 this，并且为这个新对象添加属性
      func.apply(obj, arguments);
      return obj;
    }
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
