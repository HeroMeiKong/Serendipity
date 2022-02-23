# 每日一手写

1. ## `JavaScript` 基本数据类型

    ```js
    // 7个基本类型 其中es6新增symbol ES2020引入了大整数BigInt
    boolean number string null undefined symbol bigint

    // 注意：对象 (object)也是 js 内置类型，非基本类型
    ```

    <br>

1. ## `instanceof` 实现原理

    如果 `A` 沿着原型链能找到 `B.prototype` 那么`A instanceof B` 为 `true`
    遍历 `A` 的原型链找到 `B.prototype` 返回 `true`

    ```js
    const instanceof = (A, B) => {
      let p = A
      while (p) {
        if (p === B.prototype) return true
        p = p.__proto__
      }
      return false
    }
    ```

    <br>

1. ## 手写 `call`

    ```js
    Function.prototype.call = function (context, ...args) {
      context = context || window  // context 如果是 null，则指向 window
      var fn = Symbol()
      context[fn] = this
      var result = context[fn](...args)
      delete context[fn]
      return result
    }
    ```

    <br>

1. ## 手写 `apply`

    ```js
    Function.prototype.apply = function (context, args) {
      context = context || window  // context 如果是 null，则指向 window
      var fn = Symbol()
      context[fn] = this
      var result
      args ? (result = context[fn](...args)) : (result = context[fn]())
      delete context[fn]
      return result
    }
    ```

    <br>

1. ## 手写 `bind`

    `bind` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数

    ```js
    //bind实现要复杂一点  因为他考虑的情况比较多 还要涉及到参数合并(类似函数柯里化)
    Function.prototype.bind = function (context, ...args) {
      if (!context || context === null) {
        context = window
      }
      // 创造唯一的key值  作为我们构造的context内部方法名
      let fn = Symbol()
      context[fn] = this
      let _this = this
      //  bind情况要复杂一点
      const result = function (...innerArgs) {
        // 第一种情况 :若是将 bind 绑定之后的函数当作构造函数，通过 new 操作符使用，则不绑定传入的 this，而是将 this 指向实例化出来的对象
        // 此时由于new操作符作用  this指向result实例对象  而result又继承自传入的_this 根据原型链知识可得出以下结论
        // this.__proto__ === result.prototype   //this instanceof result =>true
        // this.__proto__.__proto__ === result.prototype.__proto__ === _this.prototype; //this instanceof _this =>true
        if (this instanceof _this === true) {
          // 此时this指向指向result的实例  这时候不需要改变this指向
          this[fn] = _this
          this[fn](...[...args, ...innerArgs]) //这里使用es6的方法让bind支持参数合并
          delete this[fn]
        } else {
          // 如果只是作为普通函数调用  那就很简单了 直接改变this指向为传入的context
          context[fn](...[...args, ...innerArgs])
          delete context[fn]
        }
      };
      // 如果绑定的是构造函数 那么需要继承构造函数原型属性和方法
      // 实现继承的方式: 使用Object.create
      result.prototype = Object.create(this.prototype)
      return result
    }
    ```

1. ## `new` 实现原理

    ```js
    function myNew(fn, ...args) {
      // 1.创建一个空对象，并将对象的 __proto__ 指向构造函数的 prototype 这里我两步一起做了
      // var obj = new Object() | Object.create(null)
      // obj.__proto__ = fn.prototype
      
      const obj = Object.create(fn.prototype)
      // 2.将构造函数中的 this 指向 obj，执行构造函数代码，获取返回值
      // const res = fn.call(obj, ...args)
      const res = fn.apply(obj, args)
      // 3.判断返回值类型
      // return res && (typeof res === "object" || typeof res === "function") ? res : obj
      return res instanceof Object ? res : obj
    }
    ```

1. ## 手写 `Promise` 简版

    符合 A+规范

    ```js
    function myPromise(constructor) {
      let self = this;
      self.status = "pending";
      self.resolveVal = undefined;
      self.rejectedVal = undefined;
      function resolve(value) {
        if (self.status === "pending") {
          self.resolveVal = value;
          self.status = "resolved";
        }
      }
      function reject(reason) {
        if (self.status === "pending") {
          self.rejectedVal = reason;
          self.status = "rejected";
        }
      }
      try {
        constructor(resolve, reject);
      } catch (e) {
        reject(e);
      }
    }

    myPromise.prototype.then = function(onFullfilled, onRejected) {
      switch (this.status) {
        case "resolved":
          onFullfilled(this.resolveVal);
          break;
        case "rejected":
          onRejected(this.rejectedVal);
          break;
      }
    };
    ```

1. ## 手写 `Promise` （考虑异步）

```js
const PENDING = 'pending';
const RESOLVED = 'resolved';
const REJECTED = 'rejected';

function myPromise(fn) {
  const self = this; // 在function找到正确this指向
  self.status = PENDING;
  self.value = null; // 保存resolve/reject传入的值
  self.resolvedCallbacks = []; // 保存then中的回调,promise执行完后，state状态还可能是padding
  self.rejectedCallbacks = []; // 因此把回到函数保存起来，等待state状态改变时执行
  // 实现resolve
  funtion resolve(value) {
    if(value instanceof myPromise) {
      return value.then(resolve, reject)
    }
    setTimeout(()=> {
      if(self.status === PENDING) {
        self.status = RESOLVED;
        self.value = value;
        self.resolvedCallbacks.forEach(cb => cb(self.value)) // 执行回调
      }
    },0);
  }
  function reject(value) {
     setTimeout(()=> {
      if(self.status === PENDING) {
        self.status = REJECTED;
        self.value = value;
        self.rejectedCallbacks.forEach(cb => cb(self.value)) // 执行回调
      }
    },0);
  }
  // 执行函数fn
  try {
    fn(resolve,reject);
  } catch(e) {
    reject(e)
  }
}

myPromise.prototype.then = function(onFulfilled, onRejected) {
  const self = this;
  // 参数为可选参数 判断下
  onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : v => v;
  onRejected = typeof onRejected === 'function' ? onRejected : v => {throw v};
  // 如果状态还是padding, 往回掉函数中push函数, 否则执行函数
  if(self.status === PENDING) {
    self.resolvedCallbacks.push(onFulfilled);
    self.rejectedCallbacks.push(onRejected);
  }
  if(self.status === RESOLVED) {
    onFulfilled(self.value);
  }
  if(self.status === REJECTED) {
    onRejected(self.value);
  }
}
```

## 手写 promise.all

将多个 Promise 实例包装成一个 Promise 实例，接收包含 Promise 对象或普通值的数组(或其它可迭代对象)作为参数，成功时返回值为 Promise 实例数组对应的结果数组，失败时返回第一个 rejected 的 Promise 实例的结果。

```js
const p1 = new Promise((resolve) => {
  setTimeout(resolve, 200, 1);
});
const p2 = new Promise((resolve) => {
  resolve(2);
});
const p3 = 3;
console.log(Promise.all([p1, p2, p3])); // [1, 2, 3]

Promise.prototype.all = function(promiseArr) {
  let resArr = [];
  let count = 0;
  let len = promiseArr.length;
  // 返回一个新的promise实例
  return new Promise(function(resolve, reject) {
    for (let promise of promiseArr) {
      Promise.resolve(promise).then(
        function(res) {
          resArr[count] = res;
          count++;
          if (count === len) {
            return resolve(resArr);
          }
        },
        function(err) {
          return reject(err);
        }
      );
    }
  });
};
```

## 手写 promise.race

与 Promise.all 一样，Promise.race 也接收包含 Promise 对象或普通值的数组(或其它可迭代对象)作为参数，返回一个 Promise 实例对象。与 Promise.all 不同的是，一旦有一个 Promise 实例对象 resolve ，立即把这个 resolve 的值作为 Promise.race resolve 的值。一旦有一个对象 reject， Promise.race 也会立即 reject。

```js
Promise.prototype.race = function(promiseArr) {
  return new Promise(function(resolve, reject) {
    for (let promise of promiseArr) {
      if (typeof promise === "object" && typeof promise.then === "function") {
        promise.then(resolve, reject);
      } else {
        // 不是promise实例对象直接返回
        resolve(promise);
      }
    }
  });
};
```

<br>

## 手写防抖

### 非立即执行

作用是在短时间内多次触发同一个函数，只执行最后一次，或者只在开始时执行。
非立即执行：如果在一个事件触发的 `n` 秒内又触发了此事件，那就以新事件的时间为准，`n` 秒后才执行

```js
function debounce(fn, delay) {
  let timerId = null
  return function () {
    let context = this
    let args = arguments
    if (timerId) clearTimeout(timerId)
    timerId = setTimeout(() => {
      fn.apply(context, args)
    }, delay)
  }
}
```

### 立即执行

我不希望非要等到事件停止触发后才执行，我希望立刻执行函数，然后等到停止触发 `n` 秒后，才可以重新触发执行, 我们也可以为 `debounce` 函数加一个参数，可以选择是否立即执行函数

```js
function debounce(fn, delay, immediate) {
  let timerId = null
  return function () {
    let context = this
    let args = arguments
    if (timerId) clearTimeout(timerId)
    if (immediate) {
      const callNow = !timerId
      timerId = setTimeout(() => {
        timerId = null
      }, delay)

      if (callNow) {
        fn.apply(context, args)
      }
    } else {
      timerId = setTimeout(() => {
        fn.apply(context, args)
      }, delay)
    }
  };
}
```

## 手写节流

持续触发事件，每隔一段时间，只执行一次事件。

```js
// 时间戳版
function throttle(fn, delay) {
  var prev = Date.now()
  return function() {
    var context = this
    var args = arguments
    var now = Date.now()
    if (now - prev >= delay) {
      fn.apply(context, args)
      prev = now
    }
  }
}

// 定时器版
function throttle(fn, delay) {
  let timerId = null
  return function () {
    let context = this
    let args = arguments
    if (!timerId) {
      timerId = setTimeout(() => {
        timerId = null
        fn.apply(context, args)
      }, delay)
    }
  }
}
```

<br>

## 手写浅拷贝

```js
function copy(obj) {
  if (typeof obj !== "object" || obj === null) return obj
  let newObj = {}
  if (obj.constructor === Array) newObj = []
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      newObj[key] = obj[key]
    }
  }
  return newObj
}
```

<br>

## 手写深拷贝

### 方法一：`JSON`

`const b = JSON.parse(JSON.stringify(a))`

答题要点是指出这个方法有如下缺点：

- 不支持 Date、正则、undefined、函数等数据
- 不支持引用（即环状结构）
- 必须说自己还会方法二

### 方法二：递归

要点：

- 递归
- 判断类型
- 检查环
- 不拷贝原型上的属性

```js
const deepClone = (a, cache) => {
  if(!cache){
    cache = new Map() // 缓存不能全局，最好临时创建并递归传递
  }
  if(a instanceof Object) { // 不考虑跨 iframe
    if(cache.get(a)) { return cache.get(a) }
    let result 
    if(a instanceof Function) {
      if(a.prototype) { // 有 prototype 就是普通函数
        result = function(){ return a.apply(this, arguments) }
      } else {
        result = (...args) => { return a.call(undefined, ...args) }
      }
    } else if(a instanceof Array) {
      result = []
    } else if(a instanceof Date) {
      result = new Date(a - 0)
    } else if(a instanceof RegExp) {
      result = new RegExp(a.source, a.flags)
    } else {
      result = {}
    }
    cache.set(a, result)
    for(let key in a) { 
      if(a.hasOwnProperty(key)){
        result[key] = deepClone(a[key], cache) 
      }
    }
    return result
  } else {
    return a
  }
}
```

该实现有可能引起爆栈，可考虑使用 weakMap 代替，应付面试足矣。

```js
function deepClone(obj) {
  // 如果是值类型或 null，则直接return
  if (typeof obj !== "object" || obj === null) {
    return obj;
  }
  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);

  // 定义结果对象
  let copy = {};
  // 如果对象是数组，则定义结果数组
  if (obj.constructor === Array) {
    copy = [];
  }
  // 遍历对象的key
  for (let key in obj) {
    // 如果key是对象的自有属性
    if (obj.hasOwnProperty(key)) {
      // 递归调用深拷贝方法
      copy[key] = deepClone(obj[key]);
    }
  }
  return copy;
}

// weakMap
function deepClone(obj, hash = new WeakMap()) {
  if (typeof obj !== "object" || obj === null) return obj;

  if (obj instanceof Date) return new Date(obj);
  if (obj instanceof RegExp) return new RegExp(obj);

  if (hash.get(obj)) return hash.get(obj);

  let cloneObj = new obj.constructor();
  hash.set(obj, cloneObj);
  for (let key in obj) {
    if (obj.hasOwnProperty(key)) {
      cloneObj[key] = deepClone(obj[key], hash);
    }
  }
  return cloneObj;
}
```

<br>

## 手写 ajax

(1)创建 XMLHttpRequest 对象,也就是创建一个异步调用对象.
(2)创建一个新的 HTTP 请求,并指定该 HTTP 请求的方法、URL 及验证信息.
(3)设置响应 HTTP 请求状态变化的函数.
(4)发送 HTTP 请求.
(5)获取异步调用返回的数据.
(6)使用 JavaScript 和 DOM 实现局部刷新.

```js
// 步骤
var XHR = new XMLHttpRequest();
XHR.open("get", "./a.php");
XHR.send(null);
XHR.onreadystatechange = function() {
  if (XHR.readyState === 4) {
    if (XHR.status === 200) {
      alert(XHR.responseText);
    }
  }
};

// 封装
function ajax(url, method, body, success, fail) {
  let request = new XMLHttpRequest();
  request.open(method, url);
  request.onreadystatechange = () => {
    if (request.readyState === 4) {
      if (request.status >= 200 && request.status < 300) {
        success.call(undefined, request.responseText);
      } else if (request.status >= 400) {
        fail.call(undefined, request);
      }
    }
  };
  request.send(body);
}
```

## 手写 Promise 版 ajax

```js
window.ajax = function({ url, method }) {
  return new Promise(function(resolve, reject) {
    let request = new XMLHttpRequest();
    request.open(method, url);
    request.onreadystatechange = () => {
      if (request.readyState === 4) {
        if (request.status >= 200 && request.status < 300) {
          resolve.call(undefined, request.responseText);
        } else if (request.status >= 400) {
          reject.call(undefined, request);
        }
      }
    };
    request.send();
  });
};
```

## es5 实现继承

```js
// 使用寄生组合继承
function Person(name, like) {
  this.name = name;
  this.like = like;
  this.money = 10000;
}
Person.prototype.say = function() {
  console.log(this.name);
};

function Person1(name, like) {
  Person.call(this, name, like);
}
Person1.prototype = Object.create(Person.prototype);

let p = new Person1("明", "篮球");
p.say(); // 明
```

## es6 实现继承

```js
class Father {
  constructor(name, like) {
    this.name = name;
    this.like = like;
  }
  say() {
    console.log(this.name);
  }
}

class Children extends Father {
  constructor(name, like) {
    super(name, like);
  }
}
let p = new Children("C罗", "足球");
p.say(); // C罗
```

## TypeScript 中内置方法实现原理

```js
// Partial实现原理
type Partial<T> = { [P in keyof T]?: T[P] };

// Required实现原理
type Required<T> = { [P in keyof T]-?: T[P] };

// Pick实现原理
type Pick<T, K extends keyof T> = { [P in K]: T[P] };

// Exclude实现原理
type Exclude<T, U> = T extends U ? never : T;

// Omit实现原理
type Omit = Pick<T, Exclude<keyof T, K>>
```

## 实现一个函数组合 compose

```js
const a1 = (x) => x + 1;
const d2 = (x) => x / 2;
const m3 = (x) => x * 3;
d2(m3(a1(a1(0)))); // => 3

// 而这样的写法可读性明显太差了，我们可以构建一个compose函数,它接受任意多个函数作为参数（这些函数都只接受一个参数），然后compose返回的也是一个函数，达到以下的效果：
const calcNum = compose(
  d2,
  m3,
  a1,
  a1
);
calcNum(0); // => d2(m3(a1(a(0))))

function compose() {
  let args = argumnets;
  let start = args.length - 1;
  return function() {
    let i = start;
    let result = args[i].apply(this, arguments);
    while (i-- && i >= 0) {
      result = args[i].call(this, result);
    }
    return result;
  };
}
```
