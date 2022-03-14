# 每日一手写

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

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
      var result = args ? context[fn](...args) : context[fn]()
      delete context[fn]
      return result
    }
    ```

    <br>

1. ## 手写 `bind`

    `bind` 方法创建一个新的函数，在 `bind()` 被调用时，这个新函数的 `this` 被指定为 `bind()` 的第一个参数，而其余参数将作为新函数的参数

    ```js
    Function.prototype.bind = function (context, ...args) {
      context = context || window
      let fn = Symbol()
      context[fn] = this
      let _this = this
      const result = function (...innerArgs) {
        // 第一种情况：若是将 bind 绑定之后的函数当作构造函数，通过 new 操作符使用，则不绑定传入的 this，
        // 而是将 this 指向实例化出来的对象，此时由于 new 操作符作用 this 指向 result 实例对象，
        // 而 result 又继承自传入的 _this 根据原型链知识可得出以下结论

        // 1. this instanceof result => true
        // this.__proto__ === result.prototype

        // 2. this instanceof _this => true
        // this.__proto__.__proto__ === result.prototype.__proto__ === _this.prototype
        if (this instanceof _this) {
          // 此时 this 指向指向 result 的实例，这时候不需要改变 this 指向
          this[fn] = _this
          this[fn](...[...args, ...innerArgs]) // 使用 es6 的方法让 bind 支持参数合并
          delete this[fn]
        } else {
          // 如果只是作为普通函数调用，直接改变 this 指向为传入的 context
          context[fn](...[...args, ...innerArgs])
          delete context[fn]
        }
      };
      // 如果绑定的是构造函数 那么需要继承构造函数原型属性和方法
      // 实现继承的方式: 使用 Object.create
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

            if (callNow) fn.apply(context, args)
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
      return function () {
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
      let newObj = obj.constructor === Array ? [] : {}
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

    var copy = Object.create(obj)

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
          return (typeof target === 'object' && target) || typeof target === 'function'
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
          // 结合 Object 的 create 方法创建一个新对象，并继承传入原对象的原型链，的到的 result 是对 data
          // 的浅拷贝
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

1. ## 手写 `ajax`

    (1)创建 `XMLHttpRequest` 对象,也就是创建一个异步调用对象.
    (2)创建一个新的 `HTTP` 请求,并指定该 `HTTP` 请求的方法、`URL` 及验证信息.
    (3)设置响应 `HTTP` 请求状态变化的函数.
    (4)发送 `HTTP` 请求.
    (5)获取异步调用返回的数据.
    (6)使用 `JavaScript` 和 `DOM` 实现局部刷新.

    ```js
    // 步骤
    var xhr = new XMLHttpRequest()
    // xhr.open(method, url, [async, user, password])
    // async：一个可选的布尔参数，表示是否异步执行操作，默认为 true。
    // 如果值为 false，send() 方法直到收到答复前不会返回。如果 true，已完成事务的通知可供事件监听器使用。
    // 如果 multipart 属性为 true 则这个必须为 true，否则将引发异常。
    xhr.open("get", "./a.php")
    xhr.send(null)
    xhr.onreadystatechange = function () {
      if (xhr.readyState === 4) {
        if (xhr.status === 200) {
          alert(xhr.responseText)
        }
      }
    }

    // 封装
    function ajax(url, method, body, success, fail) {
      let xhr = new XMLHttpRequest()
      xhr.open(method, url)
      xhr.onreadystatechange = () => {
        if (xhr.readyState === 4) {
          if (xhr.status >= 200 && xhr.status < 300 || xhr.status === 304) {
            success.call(undefined, xhr.responseText)
          } else if (xhr.status >= 400) {
            fail.call(undefined, xhr)
          }
        }
      }
      xhr.send(body)
    }

    // Promise 版 ajax
    window.ajax = function ({ url, method }) {
      return new Promise((resolve, reject) => {
        let xhr = new XMLHttpRequest()
        xhr.open(method, url)
        // xhr.setRequestHeader("Content-Type", "application/json")
        xhr.onreadystatechange = () => {
          if (xhr.readyState === 4) {
            if (xhr.status >= 200 && xhr.status < 300) {
              resolve.call(undefined, xhr.responseText)
            } else if (xhr.status >= 400) {
              reject.call(undefined, xhr)
            }
          }
        }
        xhr.send()
      })
    }
    ```

    <br>

1. ## 实现继承

    ```js
    // ES5 实现继承
    // 原型链继承
    function Gender() {
      this.gender = 'male'
    }

    Gender.prototype.getGender = function () {
      return this.gender
    }

    function Name() {
      this.name = 'HeroMeiKong'
    }

    // 继承了 SuperType
    Name.prototype = new Gender()
    Name.prototype.getName = function () {
      return this.name
    }

    var human = new Name()
    console.log(human.getGender()) // 'male'

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

    
    // 寄生组合式继承
    function createObject(o) {
      function F() {}
      F.prototype = o
      return new F()
    }

    function inheritPrototype(subType, superType) {
      var _prototype = createObject(superType.prototype)
      _prototype.coustructor = subType
      subType.prototype = _prototype
    }

    function SuperType(name) {
      this.name = name
      this.colors = ['red', 'blue', 'green']
    }

    SuperType.prototype.sayName = function () {
      console.log(this.name)
    }

    function SubType(name, age) {
      // 继承属性
      SuperType.call(this, name)
      this.age = age
    }

    inheritPrototype(SubType, SuperType)

    SubType.prototype.sayAge = function () {
      console.log(this.age)
    }

    var person1 = new SuperType('SoulWalker', 26)
    var person2 = new SubType('HeroMeiKong', 26)
      
    person1.sayName() // SoulWalker
    // person1 = {
    //   name: "SoulWalker",
    //   colors: ["red", "blue", "green"],
    //   __proto__: {
    //     constructor: SuperType,
    //     sayName: f (),
    //     __proto__: Object
    //   }
    // }
    person2.sayName() // HeroMeiKong
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


    // 使用寄生组合继承
    function Person(name, like) {
      this.name = name
      this.like = like
      this.money = 10000
    }
    Person.prototype.say = function () {
      console.log(this.name)
    };

    function Person1(name, like) {
      Person.call(this, name, like)
    }
    Person1.prototype = Object.create(Person.prototype)
    Person1.prototype.constructor = Person1

    let p = new Person1("明", "篮球")
    p.say() // 明


    // ES6 实现继承
    class Father {
      constructor(name, like) {
        this.name = name
        this.like = like
      }
      say() {
        console.log(this.name)
      }
    }

    class Children extends Father {
      constructor(name, like) {
        super(name, like)
      }
    }

    let p = new Children("C罗", "足球")
    p.say() // C罗
    ```

    <br>

1. ## 实现一个函数组合 `compose`

    ```js
    const a1 = x => x + 1
    const d2 = x => x / 2
    const m3 = x => x * 3
    d2(m3(a1(a1(0)))) // => 3
    // 此写法可读性明显太差，可以构建一个 compose 函数，它接受任意多个函数作为参数（这些函数都只接受一个参数），
    // 然后 compose 返回的也是一个函数，达到以下的效果：
    const calcNum = compose(
      d2,
      m3,
      a1,
      a1
    );
    calcNum(0); // => d2(m3(a1(a(0))))

    function compose() {
      let args = arguments
      let start = args.length - 1
      return function () {
        let i = start
        let result = args[i].apply(this, arguments)
        while (i-- && i >= 0) {
          result = args[i].call(this, result)
        }
        return result
      }
    }

    function compose(...fn) {
      if (!fn.length) return v => v
      if (fn.length === 1) return fn[0]
      return fn.reduce(
        (pre, cur) =>
          (...args) =>
            pre(cur(...args)) // === a1(a2(a3()))
            // cur(pre(...args)) === a3(a2(a1()))
      )
    }
    ```

    <br>

1. ## 发布订阅

    ```js
    class EventHub {
      constructor() {
        this.events = {}
      }
      // 实现订阅
      on(type, callback) {
        if (!this.events[type]) {
          this.events[type] = [callback]
        } else {
          this.events[type].push(callback)
        }
      }
      // 删除订阅
      off(type, callback) {
        if (!this.events[type]) return
        this.events[type] = this.events[type].filter(item => item !== callback)
      }
      // 只执行一次订阅事件
      once(type, callback) {
        function fn() {
          callback()
          this.off(type, fn)
        }
        this.on(type, fn)
      }
      // 触发事件
      emit(type, ...rest) {
        this.events[type] && this.events[type].forEach(fn => fn.apply(this, rest))
      }
    }
    ```

    <br>

1. ## 用 `setTimeout` 实现 `setInterval`

    ```js
    // var timer = mySetInterval(fn, 1000), 在运行一次 timer() 清除
    function mySetInterval(fn, time = 1000) {
      let timer = null, isClear = false
      function interval() {
        if (isClear) {
          isClear = false
          clearTimeout(timer)
          return
        }
        fn()
        timer = setTimeout(interval, time)
      }
      timer = setTimeout(interval, time)
      return () => { isClear = true }
    }
    ```

    <br>

1. ## 将虚拟 `DOM` 转化为真实 `DOM`

    ```js
    // 虚拟 DOM 结构
    {
      tag: 'DIV',
      attrs: {
        id:'app'
      },
      children: [
        {
          tag: 'SPAN',
          children: [
            { tag: 'A', children: [] }
          ]
        },
        {
          tag: 'SPAN',
          children: [
            { tag: 'A', children: [] },
            { tag: 'A', children: [] }
          ]
        }
      ]
    }
    ```

    ```html
    <!-- 需实现的 DOM 结构 -->
    <div id="app">
      <span>
        <a></a>
      </span>
      <span>
        <a></a>
        <a></a>
      </span>
    </div>
    ```

    ```js
    // 真正的渲染函数
    function render(vnode) {
      // 如果是数字类型转化为字符串
      if (typeof vnode === "number") vnode = String(vnode)
      // 字符串类型直接就是文本节点
      if (typeof vnode === "string") return document.createTextNode(vnode)
      // 普通DOM
      const dom = document.createElement(vnode.tag)
      if (vnode.attrs) {
        // 遍历属性
        Object.keys(vnode.attrs).forEach(key => dom.setAttribute(key, vnode.attrs[key]))
      }
      // 子数组进行递归操作 这一步是关键
      vnode.children.forEach(child => dom.appendChild(render(child)))
      return dom
    }
    ```

    <br>

1. ## 实现一个对象的 `flatten` 方法

    ```js
    const obj = {
      a: {
        b: 1,
        c: 2,
        d: { e: 5 }
      },
      b: [1, 3, { a: 2, b: 3 }],
      c: 3
    }

    flatten(obj)
    // 扁平化结果返回如下
    // {
    //  'a.b': 1,
    //  'a.c': 2,
    //  'a.d.e': 5,
    //  'b[0]': 1,
    //  'b[1]': 3,
    //  'b[2].a': 2,
    //  'b[2].b': 3
    //   c: 3
    // }

    // 实现 flatten
    function isObject(val) {
      return typeof val === "object" && val !== null
    }

    function flatten(obj) {
      if (!isObject(obj)) return

      let res = {}
      const dfs = (cur, prefix) => {
        if (isObject(cur)) {
          if (Array.isArray(cur)) {
            cur.forEach((item, index) => dfs(item, `${prefix}[${index}]`))
          } else {
            for (let k in cur) {
              dfs(cur[k], `${prefix}${prefix ? "." : ""}${k}`)
            }
          }
        } else {
          res[prefix] = cur
        }
      }
      dfs(obj, "")

      return res
    }
    ```

    <br>

1. ## 判断括号字符串是否有效

    ```js
    给定一个只包括 '('，')'，'{'，'}'，'['，']' 的字符串 s ，判断字符串是否有效。

    有效字符串需满足：

        - 左括号必须用相同类型的右括号闭合。
        - 左括号必须以正确的顺序闭合。

    示例 1：

    输入：s = "()"
    输出：true

    示例 2：

    输入：s = "()[]{}"
    输出：true

    示例 3：

    输入：s = "(]"
    输出：false


    const isValid = function (s) {
      if (s.length % 2) return false

      const regObj = {
        "{": "}",
        "(": ")",
        "[": "]",
      }
      let stack = []
      for (let i = 0, length = s.length; i < length; i++) {
        if (s[i] === "{" || s[i] === "(" || s[i] === "[") {
          stack.push(s[i])
        } else {
          if (s[i] !== regObj[stack.pop()]) return false
        }
      }

      if (stack.length) return false

      return true
    }
    ```

    <br>

1. ## 查找数组公共前缀

    ```js
    编写一个函数来查找字符串数组中的最长公共前缀。
    如果不存在公共前缀，返回空字符串 ""。

    示例 1：

    输入：strs = ["flower","flow","flight"]
    输出："fl"

    示例 2：

    输入：strs = ["dog","racecar","car"]
    输出：""


    function longestCommonPrefix(strs) {
      if (!strs.length) return ""
      const str = strs[0]
      let index = 0
      while (index < str.length) {
        const strCur = str.slice(0, index + 1)
        for (let i = 0, length = strs.length; i < length; i++) {
          if (!strs[i] || !strs[i].startsWith(strCur)) {
            return str.slice(0, index)
          }
        }
        index++
      }
      return str
    }
    ```

    <br>

1. ## 字符串中最长不重复子串

    ```js
    给定一个字符串 s ，请你找出其中不含有重复字符的 最长子串 的长度。

    示例 1:

    输入: s = "abcabcbb"
    输出: 3
    解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。

    示例 2:

    输入: s = "bbbbb"
    输出: 1
    解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。

    示例 3:

    输入: s = "pwwkew"
    输出: 3
    解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
        请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。

    示例 4:

    输入: s = ""
    输出: 0


    // 移动窗口
    function lengthOfLongestSubstring(s) {
      if (!s.length) return 0
      let start = end = 0, max = length = 0, hash = {}
      for (let i = 0, len = s.length; i < len; i++) {
        if (hash[s[i]] > -1 && s.substring(start, i).indexOf(s[i]) > -1) {
          start = hash[s[i]] + 1
          end = i
          length = end - start + 1
        } else {
          end = i
          length ++
          max = Math.max(max, length)
        }
        hash[s[i]] = i
      }
      return max
    }

    // 动态规划 + 哈希表
    function lengthOfLongestSubstring(s) {
      if (!s.length) return 0
      if (s.length === 1) return 1
      const hash = {}
      let res = tmp = 0
      for (let i = 0, length = s.length; i < length; i++) {
        const index = hash[s[i]] === undefined ? -1 : hash[s[i]]
        hash[s[i]] = i
        tmp = tmp < i - index ? tmp + 1 : i - index
        res = Math.max(res, tmp)
      }
      return res
    }

    // 双指针 + 哈希表
    function lengthOfLongestSubstring(s) {
      if (!s.length) return 0
      if (s.length === 1) return 1
      const hash = {}
      let res = 0, i = -1
      for (let j = 0, length = s.length; j < length; j++) {
        if (hash[s[j]] !== undefined) i = Math.max(hash[s[j]], i)
        hash[s[j]] = j
        res = Math.max(res, j - i)
      }
      return res
    }

    // 动态规划 + 线性遍历
    function lengthOfLongestSubstring(s) {
      if (!s.length) return 0
      if (s.length === 1) return 1
      let res = tmp = i = 0
      for (let j = 0, length = s.length; j < length; j++) {
        i = j - 1
        while (i >= 0 && s[i] !== s[j]) i = i - 1
        tmp = tmp < j - i ? tmp + 1 : j - i
        res = Math.max(res, tmp)
      }
      return res
    }
    ```

    <br>

1. ## 数组中未出现的最小正整数

    ```js
    给你一个未排序的整数数组 nums ，请你找出其中没有出现的最小的正整数。
    请你实现时间复杂度为 O(n) 并且只使用常数级别额外空间的解决方案。

    示例 1：

    输入：nums = [1,2,0]
    输出：3

    示例 2：

    输入：nums = [3,4,-1,1]
    输出：2

    示例 3：

    输入：nums = [7,8,9,11,12]
    输出：1

    function firstMissingPositive(nums) {
      for (let i = 0; i < nums.length; i++) {
        while (
          nums[i] >= 1 &&
          nums[i] <= nums.length && // 对 1~nums.length 范围内的元素进行安排
          nums[nums[i] - 1] !== nums[i] // 已经出现在理想位置的，就不用交换
        ) {
          const temp = nums[nums[i] - 1] // 交换
          nums[nums[i] - 1] = nums[i]
          nums[i] = temp
        }
      }
      // 现在期待的是 [1,2,3,...]，如果遍历到不是放着该放的元素
      for (let i = 0; i < nums.length; i++) {
        if (nums[i] !== i + 1) return i + 1
      }
      return nums.length + 1 // 发现元素 1~nums.length 占满了数组，一个没缺
    }
    ```

    <br>

1. ## 数组去重

    ```js
    // 使用 Set
    Array.from(new Set(arr))
    [...new Set(arr)]


    // 使用 indexOf、for、push
    var arr1 = [1,2,2,2,3,3,3,4,5,6], arr2 = []
    for (let i = 0, length = arr1.length; i < length; i++) {
      if (arr2.indexOf(arr1[i]) < 0) arr2.push(arr1[i])
    }
    console.log(arr2)


    // object、push
    function fn(arr) {
      let tmp = {}, ret = []
      for (let i = 0, length = arr.length; i < length; i++) {
        if (!tmp[arr[i]]) {
          tmp[arr[i]] = 1
          ret.push(arr[i])
        }
      }
      return ret
    }

    // indexOf、forEach
    function fn(arr) {
      let ret = []
      arr.forEach((value, i) => {
        if (arr.indexOf(value) === i) ret.push(value)
      })
      return ret
    }


    // 使用 Map
    function uniq(arr) {
      var map = new Map()
      for (let i = 0, length = arr.length; i < length; i++) {
        const number = arr[i]
        if (number === undefined || map.has(number)) continue
        map.set(number, true)
        
      }
      return [...map.keys()]
    }
    ```

    <br>

1. ## 手写功能型函数管道 `pipe`

    ```js
    // Building-blocks to use for composition
    const double = x => x + x
    const triple = x => 3 * x
    const quadruple = x => 4 * x

    // Function composition enabling pipe functionality
    const pipe = (...functions) => input => functions.reduce(
      (acc, fn) => fn(acc),
      input
    )

    // Composed functions for multiplication of specific values
    const multiply6 = pipe(double, triple)
    const multiply9 = pipe(triple, triple)
    const multiply16 = pipe(quadruple, quadruple)
    const multiply24 = pipe(double, triple, quadruple)

    // Usage
    multiply6(6) // 36
    multiply9(9) // 81
    multiply16(16) // 256
    multiply24(10) // 240
    ```

    <br>

1. ## 使用 `reduce` 实现 `map`

    ```js
    Array.prototype.mapUsingReduce = function (callback, thisArg) {
      return this.reduce(function (mappedArray, currentValue, index, array) {
        mappedArray[index] = callback.call(thisArg, currentValue, index, array)
        return mappedArray
      }, [])
    }

    [1, 2, , 3].mapUsingReduce(
      (currentValue, index, array) => currentValue + index + array.length
    ) // [5, 7, , 10]
    ```

    <br>

1. ## 怎么在指定数据源里面生成一个长度为 `n` 的不重复随机数组？能有几种方法？时间复杂度多少？

    - 时间复杂度为 `O(n^2)`

    ```js
    function getTenNum(arr, n) {
      let result = []
      for (let i = 0; i < n; i++) {
        const random = Math.floor(Math.random() * arr.length)
        const cur = arr[random]
        if (result.includes(cur)) {
          i--
          break
        }
        result.push(cur)
      }
      return result
    }
    const arr = [1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14]
    const resArr = getTenNum(arr, 10)
    ```

    - 标记法 / 自定义属性法：时间复杂度为 `O(n)`

    ```js
    function getTenNum(arr, n) {
      let hash = {}
      let result = []
      let ranNum = n
      while (ranNum > 0) {
        const ran = Math.floor(Math.random() * arr.length)
        if (!hash[ran]) {
          hash[ran] = true
          result.push(ran)
          ranNum--
        }
      }
      return result
    }
    ```

    - 交换法：时间复杂度为 `O(n)`

    ```js
    function getTenNum(arr, n) {
      const cloneArr = [...arr]
      let result = []
      for (let i = 0; i < n; i++) {
        const ran = Math.floor(Math.random() * (cloneArr.length - i))
        result.push(cloneArr[ran])
        cloneArr[ran] = cloneArr[cloneArr.length - i - 1]
      }
      return result
    }
    ```

    - 边遍历边删除：时间复杂度为 `O(n)`

    ```js
    function getTenNum(arr, n) {
      const cloneArr = [...arr]
      let result = []
      for (let i = 0; i < n; i++) {
        const random = Math.floor(Math.random() * cloneArr.length)
        const cur = cloneArr[random]
        result.push(cur)
        cloneArr.splice(random, 1)
      }
      return result
    }
    ```

    <br>

1. ## 检测循环引用

    ```js
    // 对 传入的subject对象 内部存储的所有内容执行回调
    function execRecursively(fn, subject, _refs = null) {
      if (!_refs) _refs = new WeakSet()

      // 避免无限递归
      if (_refs.has(subject)) return

      fn(subject)
      if ("object" === typeof subject) {
        _refs.add(subject)
        for (let key in subject)
          execRecursively(fn, subject[key], _refs)
      }
    }

    const foo = {
      foo: "Foo",
      bar: {
        bar: "Bar"
      }
    }

    foo.bar.baz = foo // 循环引用!
    execRecursively(obj => console.log(obj), foo)
    ```

    <br>

1. ## `TypeScript` 中内置方法实现原理

    ```ts
    // Partial 实现原理
    type Partial<T> = { [P in keyof T]?: T[P] };

    // Required 实现原理
    type Required<T> = { [P in keyof T]-?: T[P] };

    // Pick 实现原理
    type Pick<T, K extends keyof T> = { [P in K]: T[P] };

    // Exclude 实现原理
    type Exclude<T, U> = T extends U ? never : T;

    // Omit 实现原理
    type Omit = Pick<T, Exclude<keyof T, K>>
    ```
