# JavaScript中的类方法、对象方法、原型方法

1. **类方法：也叫函数方法，在 JavaScript 中函数也是一个对象，所以可以为函数添加属性以及方法；**
2. **对象方法：包括构造函数中的方法以及其原型上面的方法；**
3. **原型方法：一般用于对象实例共享，在原型上面添加该方法，就能实现共享。**
    - ***Javascript 中的 `function` 作为构造函数时，就是一个类，搭配上 `new` 操作符，可以返回一个对象。***

  ```javascript
  function People(name) { 
    this.name = name; 
    // 对象方法 (实例方法)
    // 每个对象实例都有的方法
    // 只有对象实例才能调用的方法
    this.Speak = function () {
      console.log("Hello, My name is " + this.name); 
    } 
  } 
  // 类方法 
  // 只有原型才能调用的方法
  People.Move = function () { 
    console.log("move"); 
  } 
  // 原型方法 
  // 所有对象实例共享的方法
  // 原型可通过People.prototype.useTools()调用
  People.prototype.useTools = function () { 
    console.log('useTools'); 
  }
  var Myself = new People("Lilpang"); 
  Myself.Speak();      // 对象方法需要通过实例化对象去调用 
  People.Move();       // 类方法不需要通过实例化对象去调用 
  Myself.useTools();   // 原型方法也需要通过实例化对象去调用
  ```
