# JS 面试题目汇总★

1. 事件代理(事件委托)
  
    将自生的事件监控交由父元素监控，主要利用事件冒泡原理。

    使用场景：
    - 适合于: `click, mousedown, mouseup, keydown, keyup, keypress`
    - `mouseover` 和 `mouseout` 要经常计算它们的位置，处理起来麻烦。
    - 不适用于: `focu, blur` 之类的，自身没用冒泡特性。

2. 请解释 JavaScript 中 `this` 是如何工作的
    - `this` 永远指向函数运行时所在的对象，而不是函数创建时所在的对象
    - 匿名函数和不处于任何对象中的函数，`this` 指向 `window`
    - `call`, `apply`, `with` 指的 `this` 是谁就是谁
    - 普通函数调用，函数被谁调用，`this` 就指向谁

3. ★★★JavaScript 继承
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

4. JavaScript 模块化
    理解模块化发展过程，理解 `commonJS，AMD，CMD，ES6` 模块化

5. `IIFE` 立即执行函数

6. `null undefined` 区别

7. ★★★闭包 与 作用域

8. 匿名函数

9. 你是如何组织自己的代码？是使用模块模式，还是使用经典继承的方法？

10. 宿主对象 (`host objects`) 和原生对象 (`native objects`)

11. 请指出以下代码的区别：`function Person() {}`、`var person = Person()`、`var person = new Person()`？

12. ★★★`apply call bind`
    - apply 接收的是数组，并会立即执行
    - call 接收的是用逗号隔开的参数，并会立即执行
    - bind 接收的是用逗号隔开的参数，但是不会立即执行，而是返回一个新的函数

    你能讲讲它们三个的实现原理吗？能自己实现一下这三个函数吗？
    - call:

      ```javascript
      // call 用法
      function add(c, d) {
        console.log(this.a + this.b + c + d);
      }
      var o = { a: 1, b: 2 };
      add.call(o, 3, 4);

      // 如果我们不用 call，怎么实现这样的效果？
      function add(c, d) {
        console.log(this.a + this.b + c + d);
      }
      var o = { a: 1, b: 2 };
      o.add = add;
      o.add(3, 4);
      delete o.add;

      // 源码实现, 用箭头函数不行，this 指向问题
      Function.prototype.call = function (context, ...args) {
        context = context || window;  // context 如果是 null，则指向 window
        context.fn = this;
        var result = context.fn(...args);
        delete context.fn;
        return result;
      }
      ```

    - apply:

      ```javascript
      // apply 用法
      function add(c, d) {
        console.log(this.a + this.b + c + d);
      }
      var o = { a: 1, b: 2 };
      var bindAdd = add.bind(o, 3);
      bindAdd(4);

      // 源码实现, 用箭头函数不行，this 指向问题
      Function.prototype.apply = function (context, args) {
        context = context || window;  // context 如果是 null，则指向 window
        context.fn = this;
        var result = context.fn(...args);
        delete context.fn;
        return result;
      }
      ```

    - bind:

      ```javascript
      // bind 用法
      function add(c, d) {
        console.log(this.a + this.b + c + d);
      }
      var o = { a: 1, b: 2 };
      add.bind(o, [3, 4]);

      // 源码实现, 用箭头函数不行，this 指向问题
      Function.prototype.bind = function (context, ...rest) {
        var self = this;
        return function F(...args) {
            return self.apply(context, rest.concat(args)); // 两次的参数 rest，args 合并到一起，作为函数的参数
        }
      }

      // 如果使用 new 结果不一样
      // 原因：bind 返回的函数作为构造函数的时候，bind 时指定的 this 值会失效，但传入的参数依然生效
      function add(c, d) {
        // undefined undefined 3 4
        console.log(this.a + this.b + c + d);
      }
      var o = {a: 1, b: 2};

      var bindAdd = add.bind(o, 3);
      new bindAdd(4); // NaN

      // 
      Function.prototype.bind = function (context, ...rest) {
        var self = this;

        return function F(...args) {
            /*如果是 new 的，则不要之前的 context 啦*/
            if (this instanceof F) {
                return self(...rest, ...args);
            }
            return self.apply(context, rest.concat(args));
        }
      }
      ```

13. `new`
    源码如何实现的？

14. `document.write()`

15. 特性检测 特性推断 UA字符串嗅探

16. Ajax 工作原理
    着重理解 XMLHttpRequest！！

17. ★★★跨域

    图片`ping, JSONP, CORS`。

18. 变量声明提升

19. 冒泡机制

20. `attribute` 和 `property`

21. ★★★`document load` 和 `document DOMContentLoaded`

22. `==` 和 `===` 有什么不同

23. 同源策略 (`same-origin policy`)
    `Cookie，iframe，AJAX` 同源

24. `strict` 模式

25. 为何通常会认为保留网站现有的全局作用域 (`global scope`) 不去改变它，是较好的选择

26. 为何你会使用 `load` 之类的事件 (`event`)？此事件有缺点吗？你是否知道其他替代品，以及为何使用它们？

27. ★★请解释什么是单页应用 (`single page app`), 以及如何使其对搜索引擎友好 (`SEO-friendly`)

28. `Promise`
    怎么用？源码如何实现的？
    推荐文章：[xieranmaya/blog#3](https://github.com/xieranmaya/blog/issues/3)

29. 使用一种可以编译成 JavaScript 的语言来写 JavaScript 代码有哪些优缺点？

30. JavaScript 调试工具

31. 对象遍历 和 数组遍历

32. 可变对象和不可变对象

33. ★★★什么是事件循环 (`event loop`)
    深入原理，宏任务，微任务等等

34. `let var const`

35. 数组的方法

36. `web worker`

37. 柯里化

38. 创建对象的三种方法

39. 深拷贝和浅拷贝
    可以实现手写深拷贝

40. 图片懒加载
    咋实现的？

41. 网页各种高度

42. 实现页面加载进度条

43. 箭头函数 ES5 如何实现
    箭头函数和普通函数的区别
44. `Object.create` 做了什么？

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
