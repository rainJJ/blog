# blog

- 实现new 方法
  
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
