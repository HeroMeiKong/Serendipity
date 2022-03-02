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

    <br>

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

    <br>

1. ## 手写 `Promise` 及其所有方法

    ```js
    // 先定义三个常量表示状态
    const PENDING = 'pending'
    const FULFILLED = 'fulfilled'
    const REJECTED = 'rejected'

    class MyPromise {
      constructor(executor) {
        // executor 是个执行器，进入会立即执行
        // 并传入 resolve 和 reject 方法
        try {
          executor(this.resolve, this.reject)
        } catch (error) {
          this.reject(error)
        }
      }

      // 初始化储存状态的变量
      status = PENDING

      // resolve / reject 为什么曜使用箭头函数？
      // 如果直接调用，普通函数 this 指向是 window 或 undefined
      // 用箭头函数可以让 this 指向当前实例对象

      // 成功之后的值
      value = null
      // 失败之后的原因
      reason = null
      // 存储成功回调函数
      onFulfilledCallbacks = []
      // 存储失败回调函数
      onRejectedCallbacks = []

      // 更改成功后的状态
      resolve = value => {
        // 只有状态是等待，才执行状态修改
        if (this.status === PENDING) {
          // 状态修改为成功
          this.status = FULFILLED
          // 保存成功之后的值
          this.value = value
          // 执行 resolve 里面所有成功回调
          while (this.onFulfilledCallbacks.length) {
            this.onFulfilledCallbacks.shift()(value)
          }
        }
      }
      // 更改失败后的状态
      reject = reason => {
        // 只有状态是等待，才执行状态修改
        if (this.status === PENDING) {
          // 状态修改为失败
          this.status = REJECTED
          // 保存失败后的原因
          this.reason = reason
          // 执行 rejecte 里面所有失败回调
          while (this.onRejectedCallbacks.length) {
            this.onRejectedCallbacks.shift()(reason)
          }
        }
      }

      then(onFulfilled, onRejected) {
        // 如果不传，就使用默认函数
        const realOnFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value
        const realOnRejected = typeof onRejected === 'function' ? onRejected : reason => { throw reason }

        const currentPromise = new MyPromise((resolve, reject) => {
          const fulfilledMicrotask = () => {
            // 创建一个微任务等待 currentPromise 完成初始化，否则会报错
            queueMicrotask(() => {
              // 新增错误捕获
              try {
                // 调用成功回调，并返回值
                resolvePromise(currentPromise, realOnFulfilled(this.value), resolve, reject)
              } catch (error) {
                reject(error)
              }
            })
          }

          const rejectedMicrotask = () => {
            // 创建一个微任务等待 currentPromise 完成初始化，否则会报错
            queueMicrotask(() => {
              try {
                // 调用失败回调，并返回原因
                resolvePromise(currentPromise, realOnRejected(this.reason), resolve, reject)
              } catch(error) {
                reject(error)
              }
            })
          }
          // 这里的内容在执行器中会立即执行
          // 判断状态
          if (this.status === FULFILLED) {
            fulfilledMicrotask()
          } else if (this.status === REJECTED) {
            rejectedMicrotask()
          } else if (this.status === PENDING) {
            // 因为不知道后面状态的变化情况，所有将成功/失败回调存储起来，等待后续调用
            this.onFulfilledCallbacks.push(fulfilledMicrotask)
            this.onRejectedCallbacks.push(rejectedMicrotask)
          }
        })
        return currentPromise
      }

      catch(onRejected) {
        // 进行错误处理
        this.then(undefined, onRejected)
      }

      finally(fn) {
        return this.then(
          value => MyPromise.resolve(fn()).then(() => value),
          error => MyPromise.resolve(fn()).then(() => { throw error })
        )
      }

      // resolve 静态方法，方便通过 MyPromise.resolve() 直接调用
      static resolve(value) {
        // 如果传入 MyPromise 就直接返回
        if (value instanceof MyPromise) return value
        return new MyPromise(resolve => resolve(value))
      }

      // reject 静态方法，方便通过 MyPromise.reject() 直接调用
      static reject(reason) {
        return new MyPromise((resolve, reject) => reject(reason))
      }

      static all(promiseList) {
        const result = []
        const length = promiseList.length
        let count = 0

        return new MyPromise((resolve, reject) => {
          if (!length) return resolve(result)

          promiseList.forEach((promise, index) => {
            MyPromise.resolve(promise)
              .then(
                value => {
                  count++
                  result[index] = value
                  if (count === length) resolve(result)
                },
                reason => reject(reason))
          })
        })
      }

      static allSettled(promiseList) {
        const result = []
        const length = promiseList.length
        let count = 0

        return new MyPromise(resolve => {
          if (!length) return resolve(result)

          for (let i = 0; i < length; i++) {
            const currentPromise = MyPromise.resolve(promiseList[i])
            currentPromise
              .then(
                value => {
                  count++
                  result[i] = { status: 'fulfilled', value }
                  if (count === length) return resolve(result)
                },
                reason => {
                  count++
                  result[i] = { status: 'rejected', reason }
                  if (count === length) return resolve(result)
                })
          }
        })
      }

      static race(promiseList) {
        const length = promiseList.length

        return new MyPromise((resolve, reject) => {
          if (!length) return resolve()

          for (let i = 0; i < length; i++) {
            MyPromise.resolve(promiseList[i])
              .then(
                value => resolve(result),
                reason => reject(reason)
              )
          }
        })
      }

      static any(promiseList) {
        const length = promiseList.length
        const errors = []
        let count = 0

        return new MyPromise((resolve, reject) => {
          if (!length) return resolve()

          for (let i = 0; i < length; i++) {
            MyPromise.resolve(promiseList[i])
              .then(
                value => resolve(value),
                reason => {
                  count++
                  errors[i] = { status: 'rejected', reason }
                  if (count === length) return reject(new AggregateError(errors, 'All promises were rejected'))
                }
              )
          }
        })
      }
    }

    function resolvePromise(promise, x, resolve, reject) {
      // return 自己，防止死循环，抛错
      if (promise === x) return reject(new TypeError('循环调用错误'))
      if (typeof x === 'object' || typeof x === 'function') {
        // x 为 null，直接返回
        if (x === null) return resolve(x)

        let then
        try {
          // 把 x.then 赋值给 then
          then = x.then
        } catch(error) {
          // 如果取 x.then 的值时抛错 error，则以 error 为拒因拒绝 promise
          return reject(error)
        }

        // 如果 then 是函数
        if (typeof then === 'function') {
          let called = false
          try {
            then.call(
              x, // this 指向 x
              // 如果 resolvePromise 以值 y 为参数被调用，则运行 [[Resolve]](promise, y)
              y => {
                // 如果 resolvePromise 和 rejectPromise 均被调用，
                // 或者被同一参数调用多次，则优先采用首次调用并忽略剩下的调用
                // 实现这条需要前面加一个变量 called
                if (called) return
                called = true
                resolvePromise(promise, y, resolve, reject)
              },
              // 如果 rejectPromise 以拒因 reason 为参数被调用，则以拒因 reason 拒绝 promise
              reason => {
                if (called) return
                called = true
                reject(reason)
              })
          } catch(error) {
            // 如果调用 then 方法抛出了异常 error
            // 如果 resolvePromise 或 rejectPromise 已经被调用，直接返回
            if (called) return
            // 否则以 error 为拒因拒绝 promise
            reject(error)
          }
        } else {
          // 如果 then 不是函数，以 x 为参数执行 promise
          resolve(x)
        }
      } else {
        // 如果 x 不为对象/函数，以 x 为参数执行 promise
        resolve(x)
      }
    }

    module.exports = MyPromise
    ```

    <br>

1. ## 手写防抖

    - 非立即执行

      作用是在短时间内多次触发同一个函数，只执行最后一次，或者只在开始时执行。
      非立即执行：如果在一个事件触发的 `n` 秒内又触发了此事件，那就以新事件的时间为准，`n` 秒后才执行

      ```js
      function debounce(fn, delay) {
        let timerId = null
        return function () {
          let context = this
          let args = arguments
          if (timerId) clearTimeout(timerId)
          timerId = setTimeout(() => fn.apply(context, args), delay)
        }
      }
      ```

    - 立即执行

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
            timerId = setTimeout(() => timerId = null, delay)

            if (callNow) {
              fn.apply(context, args)
            }
          } else {
            timerId = setTimeout(() => fn.apply(context, args), delay)
          }
        };
      }
      ```

      <br>

1. ## 手写节流

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

1. ## 手写浅拷贝

    ```js
    // Array || Object
    function copy(obj) {
      if (typeof obj !== "object" || obj === null) return obj
      let newObj
      obj.constructor === Array ? newObj = [] : newObj = {}
      for (let key in obj) {
        if (obj.hasOwnProperty(key)) {
          newObj[key] = obj[key]
        }
      }
      return newObj
    }

    // Array || Object
    var copy = {...obj}

    var copy = [...arr]

    // Object
    var copy = Object.assign({}, obj)

    // Array
    var copy = Array.from(arr)

    var copy = arr.slice()

    var copy = [].concat(arr)
    ```

    <br>

1. ## 手写深拷贝

    - 方法一：`JSON`

      `const copy = JSON.parse(JSON.stringify(value))`

      答题要点是指出这个方法有如下缺点：

      - 不支持 `Date`、正则、`undefined`、函数等数据
      - 不支持引用（即环状结构）
      - 必须说自己还会方法二

    - 方法二：递归

      要点：

      - 递归
      - 判断类型
      - 检查环
      - 不拷贝原型上的属性

      ```js
      // weakMap
      // 解决的问题：
      // 1. 不能处理循环引用：使用 WeakMap 作为 hash 来查询
      // 2. 只考了 Object 对象：当参数为 Date、RegExp、Function、Map、Set 则直接生成一个新实例并返回
      // 3. 属性名为 Symbol：针对能够遍历对象的不可枚举属性以及 Symbol 类型，可使用 Reflect.ownKeys()
      // 相当于 Object.getOwnPropertyNames(target).concat(Object.getOwnPropertySymbols(target))
      // 4. 原型上的属性：Object.getOwnPropertyDescriptors() 设置属性描述对象，以及 Object.create()
      // 方式继承原型链
      function deepClone(target) {
        // 防止循环引用
        const map = new WeakMap()
        
        // 是否为 Object 类型
        function isObject(target) {
          return (typeof target === 'object' && target ) || typeof target === 'function'
        }

        function clone(data) {
          // 基础类型直接返回值
          if (!isObject(data)) return data

          // 日期/正则 直接构造一个新的对象返回
          if ([Date, RegExp].includes(data.constructor)) return new data.constructor(data)

          // 处理函数对象
          // new Function('return ' + data.toString()) 会在函数外套一层空函数，调用 => copyFn()() 
          if (typeof data === 'function') return new Function('return ' + data.toString())()

          // 如果对象已存在，则直接返回对象
          const exist = map.get(data)
          if (exist) return exist

          // 处理 Map 对象
          if (data instanceof Map) {
            const result = new Map()
            map.set(data, result)
            data.forEach((val, key) => {
              // map 中的值为 Object 的话也需要深拷贝
              if (isObject(val)) {
                result.set(key, clone(val))
              } else {
                result.set(key, val)
              }
            })
            return result
          }

          // 处理 Set 对象
          if (data instanceof Set) {
            const result = new Set()
            map.set(data, result)
            data.forEach(val => {
              // set 中的值为 Object 的话也需要深拷贝
              if (isObject(val)) {
                result.add(clone(val))
              } else {
                result.add(val)
              }
            })
            return result
          }

          // 收集键名（考虑了以 Symbol 作为 key 以及不可枚举属性）
          const keys = Reflect.ownKeys(data)
          // 利用 Object 的 getOwnPropertyDescriptors 方法可以获取对象的所有属性以及对应的属性描述
          const allDesc = Object.getOwnPropertyDescriptors(data)
          // 结合 Object 的 create 方法创建一个新对象，并继承传入原对象的原型链，的到的 result 是对 data 的浅拷贝
          const result = Object.create(Object.getPrototypeOf(data), allDesc)

          // 新对象加入 map 中，进行记录
          map.set(data, result)

          // Object.create() 是浅拷贝，所以要判断并递归执行深拷贝
          keys.forEach(key => {
            const val = data[key]
            if (isObject(val)) {
              // 属性为 Object 的话也需要深拷贝
              result[key] = clone(val)
            } else {
              result[key] = val
            }
          })
          return result
        }

        return clone(target)
      }
      ```

      <br>

1. ## 手写 ajax

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

    <br>

1. ## 手写 Promise 版 ajax

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

    <br>

1. ## es5 实现继承

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

    <br>

1. ## es6 实现继承

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

    <br>

1. ## TypeScript 中内置方法实现原理

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

    <br>

1. ## 实现一个函数组合 compose

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
