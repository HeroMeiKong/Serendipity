# JS 面试题目汇总★

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

1. ## 事件代理(事件委托)
  
    将自生的事件监控交由父元素监控，主要利用事件冒泡原理。

    使用场景：
    - 适合于: `click, mousedown, mouseup, keydown, keyup, keypress`
    - `mouseover` 和 `mouseout` 要经常计算它们的位置，处理起来麻烦。
    - 不适用于: `focu, blur` 之类的，自身没用冒泡特性。
    <br>

1. ## 请解释 `JavaScript` 中 `this` 是如何工作的

    - `this` 永远指向函数运行时所在的对象，而不是函数创建时所在的对象
    - 匿名函数和不处于任何对象中的函数，`this` 指向 `window`
    - `call`, `apply`, `with` 指的 `this` 是谁就是谁
    - 普通函数调用，函数被谁调用，`this` 就指向谁
    <br>

1. ## `JavaScript` 继承 ★★★

    - 原型链

      ```javascript
      function Gender() {
        this.gender = 'male';
      }

      Gender.prototype.getGender = function () {
        return this.gender;
      }

      function Name() {
        this.name = 'HeroMeiKong';
      }

      // 继承了 SuperType
      Name.prototype = new Gender();
      Name.prototype.getName = function () {
        return this.name;
      }

      var human = new Name();
      console.log(human.getGender()); // 'male'

      // human = {
      //   name: "HeroMeiKong",
      //   __proto__: {
      //     gender: "male",
      //     getName: f ()
      //     __proto__: {
      //       getGender: f (),
      //       __proto__: Object
      //     }
      //   }
      // }
      ```
  
    - 借用构造函数

      ```javascript
      function Animals() {
        this.title = ['温驯', '可爱'];
        this.getTitle = function () { return this.title; }
      }

      function Dog() {
        this.kind = 'dog';
        Animals.call(this);
      }

      var corgis = new Dog();
      corgis.title.push('短腿');

      var labrador = new Dog();

      console.log(corgis, labrador);

      // corgis = {
      //   kind: "dog",
      //   title: ["温驯", "可爱", "短腿"],
      //   getTitle: f ()
      // }

      // labrador = {
      //   kind: "dog",
      //   title: ["温驯", "可爱"],
      //   getTitle: f ()
      // }
      ```

    - 组合继承

      ```javascript
      function Animals(name) {
        this.name = name;
        this.colors = ['red', 'blue', 'green'];
        this.getColor = function () { return this.colors; }
      }

      Animals.prototype.sayName = function () {
        console.log(this.name);
      }

      function Dog(name, age) {
        // 继承属性
        Animals.call(this, name); // 第二次调用 Animals
        this.age = age;
        this.getAge = function () { return this.age; }
      }

      // 继承方法
      Dog.prototype = new Animals(); // 第一次调用 Animals

      Dog.prototype.sayAge = function() {
        console.log(this.age);
      }

      var corgis = new Dog('柯基', 24);
      console.log(corgis);
      corgis.colors.push('black');
      // corgis = {
      //   name: "柯基",
      //   age: 24,
      //   colors: ["red", "blue", "green", "black"],
      //   getAge: f (),
      //   getColor: f (),
      //   __proto__: {
      //     name: undefined,
      //     colors: ["red", "blue", "green"],
      //     getColor: f (),
      //     sayAge: f (),
      //     __proto__: {
      //       sayName: f (),
      //       __proto__: Object
      //     }
      //   }
      // }

      var labrador = new Dog('拉布拉多', 26);
      console.log(labrador);
      // labrador = {
      //   name: "拉布拉多",
      //   age: 26,
      //   colors: ["red", "blue", "green"],
      //   getAge: f (),
      //   getColor: f (),
      //   __proto__: {
      //     name: undefined,
      //     colors: ["red", "blue", "green"],
      //     getColor: f (),
      //     sayAge: f (),
      //     __proto__: {
      //       sayName: f (),
      //       __proto__: Object
      //     }
      //   }
      // }
      ```

    - 原型式继承

      ```javascript
      function createObject(o) { // 同 Object.create() 方法
        function F() {};
        F.prototype = o;
        return new F();
      }

      var dog = {
        name: 'dog',
        tags: ['animals']
      };

      var corgis = createObject(dog);
      corgis.name = 'Corgis';
      corgis.tags.push('lovely');
      // corgis = {
      //   name: "Corgis",
      //   __proto__: {
      //     name: "dog",
      //     tags: ["animals", "lovely", "tame"],
      //     __proto__: Obejct
      //   }
      // }

      var labrador = createObject(dog);
      labrador.name = 'Labrador';
      labrador.tags.push('tame');
      // labrador = {
      //   name: "Labrador",
      //   __proto__: {
      //     name: "dog",
      //     tags: ["animals", "lovely", "tame"],
      //     __proto__: Obejct
      //   }
      // }

      console.log(dog);
      // dog = {
      //   name: "dog",
      //   tags: ["animals", "lovely", "tame"]
      //   __proto__: Object
      // }
      ```

    - 寄生式继承

      ```javascript
      function createObject(o) {
        function F() {}
        F.prototype = o;
        return new F();
      }

      function parasitize(parasitifer) {
        var parasite = createObject(parasitifer);
        parasite.name = 'parasite'; // 默认值，设置便会替代
        parasite.getInfo = function () {
          console.log('I\'m a parasite');
        };
        return parasite;
      }

      var person = {
        name: 'HeroMeiKong',
        germs: ['Bacillus coli']
      };

      var Helicobacter_pylori = parasitize(person);
      Helicobacter_pylori.name = 'Helicobacter pylori';
      Helicobacter_pylori.germs.push('Helicobacter pylori');
      Helicobacter_pylori.getInfo(); // I'm a parasite
      // Helicobacter_pylori = {
      //   name: "Helicobacter pylori",
      //   getInfo: f (),
      //   __proto__: {
      //     name: "HeroMeiKong",
      //     germs: ["Bacillus coli", "Helicobacter pylori"],
      //     __proto__: Object
      //   }
      // }
      ```

    - 寄生组合式继承

      ```javascript
      function createObject(o) {
        function F() {}
        F.prototype = o;
        return new F();
      }

      function inheritPrototype(subType, superType) {
        var _prototype = createObject(superType.prototype);
        _prototype.coustructor = subType;
        subType.prototype = _prototype;
      }

      function SuperType(name) {
        this.name = name;
        this.colors = ['red', 'blue', 'green'];
      }

      SuperType.prototype.sayName = function () {
        console.log(this.name);
      }

      function SubType(name, age) {
        // 继承属性
        SuperType.call(this, name);
        this.age = age;
      }

      inheritPrototype(SubType, SuperType);

      SubType.prototype.sayAge = function () {
        console.log(this.age);
      }

      var person1 = new SuperType('SoulWalker', 26);
      var person2 = new SubType('HeroMeiKong', 26);
        
      person1.sayName(); // SoulWalker
      // person1 = {
      //   name: "SoulWalker",
      //   colors: ["red", "blue", "green"],
      //   __proto__: {
      //     constructor: SuperType,
      //     sayName: f (),
      //     __proto__: Object
      //   }
      // }
      person2.sayName(); // HeroMeiKong
      // person2 = {
      //   age: 26,
      //   colors: ["red", "blue", "green"],
      //   name: "HeroMeiKong",
      //   __proto__: {
      //     constructor: SubType,
      //     sayAge: f (),
      //     __proto__: {
      //       constructor: SuperType,
      //       sayName: f (),
      //       __proto__: Object
      //     }
      //   }
      // }
      ```

      <br>

1. ## `JavaScript` 模块化

    理解模块化发展过程，理解 `commonJS，AMD，CMD，ES6` 模块化
    <br>

1. ## `IIFE` 立即执行函数

1. ## `null undefined` 区别

1. ## 闭包 与 作用域 ★★★

1. ## 匿名函数

1. ## 你是如何组织自己的代码？是使用模块模式，还是使用经典继承的方法？

1. ## 宿主对象 (`host objects`) 和原生对象 (`native objects`)

1. ## 请指出以下代码的区别：`function Person() {}`、`var person = Person()`、`var person = new Person()`？

1. ## `document.write()`

1. ## 特性检测 特性推断 UA字符串嗅探

1. ## `Ajax` 工作原理

    着重理解 `XMLHttpRequest`！！
    <br>

1. ## 跨域 ★★★

    图片`ping, JSONP, CORS`。
    <br>

1. ## 变量声明提升

1. ## 冒泡机制

1. ## `attribute` 和 `property`

1. ## `document load` 和 `document DOMContentLoaded` ★★★

1. ## `==` 和 `===` 有什么不同

1. ## 同源策略 (`same-origin policy`)

    `Cookie，iframe，AJAX` 同源
    <br>

1. ## `strict` 模式

1. ## 为何通常会认为保留网站现有的全局作用域 (`global scope`) 不去改变它，是较好的选择

1. ## 为何你会使用 `load` 之类的事件 (`event`)？此事件有缺点吗？你是否知道其他替代品，以及为何使用它们？

1. ## 请解释什么是单页应用 (`single page app`), 以及如何使其对搜索引擎友好 (`SEO-friendly`) ★★

1. ## `Promise`

    怎么用？源码如何实现的？
    推荐文章：[xieranmaya/blog#3](https://github.com/xieranmaya/blog/issues/3)
    <br>

1. ## 使用一种可以编译成 `JavaScript` 的语言来写 `JavaScript` 代码有哪些优缺点？

1. ## `JavaScript` 调试工具

1. ## 对象遍历 和 数组遍历

1. ## 可变对象和不可变对象

1. ## 什么是事件循环 (`event loop`) ★★★

    深入原理，宏任务，微任务等等
    <br>

1. ## `let var const`

1. ## 数组的方法

1. ## `web worker`

1. ## 柯里化

1. ## 创建对象的三种方法

1. ## 深拷贝和浅拷贝

    可以实现手写深拷贝
    <br>

1. ## 图片懒加载

1. ## 网页各种高度

1. ## 实现页面加载进度条

1. ## 箭头函数 `ES5` 如何实现

    箭头函数和普通函数的区别
    <br>

1. ## `Object.create` 做了什么？

    ```javascript
    const person = {
      isHuman: false,
      printIntroduction: function() {
        console.log(`My name is ${this.name}. Am I human? ${this.isHuman}`);
      }
    };

    const me = Object.create(person);

    me.name = 'HeroMeiKong';
    me.isHuman = true;

    me.printIntroduction(); // My name is HeroMeiKong. Am I human? true
    console.log(me);
    // me = {
    //   isHuman: true,
    //   name: "HeroMeiKong",
    //   __proto__: {
    //     isHuman: false,
    //     printIntroduction: f (),
    //     __proto__: Object
    //   }
    // }

    // 完整用法
    Object.create(proto，[propertiesObject])
    // propertiesObject 为 undefined 表示不传值，即不选，若为 null 则会报错
    // propertiesObject 传入对象, 且类型为 Object.defineProperties()
    const obj = Object.create({ name: 'HeroMeiKong' }, {
      age: {
        value: 25,
        writable: true,
        enumerable: true,
        configurable: true
      }
    });
    // obj = {
    //   age: 25,
    //   __proto__: {
    //     name: "HeroMeiKong",
    //     __proto__: Object
    //   }
    // }

    // 它创建一个已经选择了原型的新对象，但没有把第二个参数考虑在内。
    // ECMAScript5 以前版本限制，此 polyfill 无法支持设置 null。
    if (typeof Object.create !== "function") {
      Object.create = function (proto, propertiesObject) {
        if (typeof proto !== 'object' && typeof proto !== 'function') {
          throw new TypeError('Object prototype may only be an Object: ' + proto);
        } else if (proto === null) {
          throw new Error("This browser's implementation of Object.create is a shim and doesn't support 'null' as the first argument.");
        }

        if (typeof propertiesObject !== 'undefined') throw new Error("This browser's implementation of Object.create is a shim and doesn't support a second argument.");

        function F() {}
        F.prototype = proto;

        return new F();
      };
    }
    ```

    <br>

1. ## 不能使用 `__proto__` 与 `[[Prototype]]`，如何访问原型(父类)属性？

    可以通过 `Object.getPrototypeOf()` 和 `Object.setPrototypeOf()` 访问器来访问
    <br>

1. ## 实现继承的方法(使用不同的方法来创建对象和生成原型链)

    [参考文章](https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Inheritance_and_the_prototype_chain#%E4%BD%BF%E7%94%A8%E4%B8%8D%E5%90%8C%E7%9A%84%E6%96%B9%E6%B3%95%E6%9D%A5%E5%88%9B%E5%BB%BA%E5%AF%B9%E8%B1%A1%E5%92%8C%E7%94%9F%E6%88%90%E5%8E%9F%E5%9E%8B%E9%93%BE)
    - 使用语法结构创建的对象

      ```javascript
      var o = { a: 1 };

      // o 这个对象继承了 Object.prototype 上面的所有属性
      // o 自身没有名为 hasOwnProperty 的属性
      // hasOwnProperty 是 Object.prototype 的属性
      // 因此 o 继承了 Object.prototype 的 hasOwnProperty
      // Object.prototype 的原型为 null
      // 原型链如下:
      // o ---> Object.prototype ---> null

      var a = ["yo", "whadup", "?"];

      // 数组都继承于 Array.prototype
      // (Array.prototype 中包含 indexOf, forEach 等方法)
      // 原型链如下:
      // a ---> Array.prototype ---> Object.prototype ---> null

      function f() {
        return 2;
      }

      // 函数都继承于 Function.prototype
      // (Function.prototype 中包含 call, bind等方法)
      // 原型链如下:
      // f ---> Function.prototype ---> Object.prototype ---> null
      ```

    - 使用构造器创建的对象(new)

      ```javascript
      function Graph() {
        this.vertices = [];
        this.edges = [];
      }

      Graph.prototype = {
        addVertex: function (v) {
          this.vertices.push(v);
        }
      };

      var g = new Graph();
      // g 是生成的对象，他的自身属性有 'vertices' 和 'edges'
      // 在 g 被实例化时，g.[[Prototype]] 指向了 Graph.prototype
      ```

    - `Object.create()`

      ```javascript
      var a = { a: 1 };
      // a ---> Object.prototype ---> null

      var b = Object.create(a);
      // b ---> a ---> Object.prototype ---> null
      console.log(b.a); // 1 (继承而来)

      var c = Object.create(b);
      // c ---> b ---> a ---> Object.prototype ---> null

      var d = Object.create(null);
      // d ---> null
      console.log(d.hasOwnProperty); // undefined, 因为d没有继承Object.prototype
      ```

    - `class`

      ```javascript
      "use strict";

      class Polygon {
        constructor (height, width) {
          this.height = height;
          this.width = width;
        }
      }

      class Square extends Polygon {
        constructor (sideLength) {
          super(sideLength, sideLength);
        }
        get area() {
          return this.height * this.width;
        }
        set sideLength(newLength) {
          this.height = newLength;
          this.width = newLength;
        }
      }

      var square = new Square(2);
      ```

      <br>

1. ## 列举原型链上属性的方法

    **注意：检查属性是否为 `undefined` 是不能够检查其是否存在的。该属性可能已存在，但其值恰好被设置成了 `undefined`。**
    - `Object.prototype.hasOwnProperty()`: 返回一个布尔值，指示对象自身属性中是否具有指定的属性（键）。**不会遍历原型链，只查询自身**
    `object1.hasOwnProperty('toString');`
    - `Object.keys()`: 返回一个由一个给定对象的**自身可枚举属性**组成的**字符串数组**，数组中属性名的排列顺序和正常循环遍历该对象时返回的顺序一致
    `console.log(Object.keys(arr));`
    - `Object.getOwnPropertyNames()`: 返回一个由指定对象的**所有自身属性**的属性名（包括不可枚举属性但不包括 `Symbol` 值作为名称的属性）组成的**字符串数组**
    `console.log(Object.getOwnPropertyNames(arr));`
    <br>

1. ## ℹ️`clearInterval(timer)` 和 `timer = null` 的区别？

1. ## ℹ️`for...of` 和 `for...in` 的区别？

    <https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Statements/for...of#for...of%E4%B8%8Efor...in%E7%9A%84%E5%8C%BA%E5%88%AB>
    <br>

1. ## ℹ️`hasOwnProperty` 与 `hasAttributes` 的区别？

1. ## ℹ️`Object.freeze()` 和 `Object.seal()` 的区别？

1. ## ℹ️`Proxy` 与 `Reflect` 的区别？

1. ## `MessageChannel` 和 `MutationObserver`

1. ## 递归 `setTimeout()` 和 `setInterval()` 有何不同？

    ```javascript
    let i = 1;

    setTimeout(function run() {
      console.log(i);
      i++;
      setTimeout(run, 100);
    }, 100);

    setInterval(function run() {
      console.log(i);
      i++
    }, 100);
    ```

   - 递归 `setTimeout()` 保证执行之间的延迟相同，例如在上述情况下为 `100ms`。 代码将运行，然后在它再次运行之前等待 `100ms`，因此无论代码运行多长时间，间隔都是相同的。
   - 使用 `setInterval()` 的示例有些不同。 我们选择的间隔包括执行我们想要运行的代码所花费的时间。假设代码需要 `40ms` 才能运行 - 然后间隔最终只有 `60ms`。
   - 当递归使用 `setTimeout()` 时，每次迭代都可以在运行下一次迭代之前计算不同的延迟。 换句话说，第二个参数的值可以指定在再次运行代码之前等待的不同时间（以毫秒为单位）。
   <br>

    当你的代码有可能比你分配的时间间隔，花费更长时间运行时，最好使用递归的 `setTimeout()` - 这将使执行之间的时间间隔保持不变，无论代码执行多长时间，你不会得到错误。
    <br>

1. ## `i++` 与 `++i` 的区别？

    区别：
    - `i++` 返回原来的值，`++i` 返回加 1 后的值。
    - `i++` 不能作为左值，而 `++i` 可以。
    <br>

    什么是左值？
    左值是对应内存中有确定存储地址的对象的表达式的值，而右值是所有不是左值的表达式的值。
    一般来说，左值是可以放到赋值符号左边的变量。
    左值与右值的根本区别在于是否允许取地址 `&` 获得对应的内存地址。
    为什么 `i++` 不能做左值，而 `++i` 可以？
    `i++` 最后返回的是一个临时变量，而临时变量是右值。

1. ## `for of` 与 `for in` 的区别？

    区别：
    - 循环数组：`for in` 和 `for of` 都可以循环数组，`for in` 输出的是数组的 `index` 下标，而 `for of` 输出的是数组的每一项的值。
    - 循环对象：`for in` 可以遍历对象，`for of` 不能遍历对象，只能遍历带有 `iterator` 接口的，例如 `Set, Map, String, Array`

      ```js
      const arr = [1, 2, 3, 4]
      // for ... in
      for (const key in arr) {
        console.log(key) // 输出 0,1,2,3
      }
      
      // for ... of
      for (const key of arr) {
        console.log(key) // 输出 1,2,3,4
      }

      const object = { name: 'lx', age: 23 }
      // for ... in
      for (const key in object) {
        console.log(key) // 输出 name,age
        console.log(object[key]) // 输出 lx,23
      }

      // for ... of
      for (const key of object) {
        console.log(key) // 报错 Uncaught TypeError: object is not iterable
      }
      ```

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
