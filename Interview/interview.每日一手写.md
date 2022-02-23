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

1. ## 手写 `Promise` 及其所有方法

    ```js
    /**
     * 在 myPromise.js 基础上，根据规范实现了 Promise 的全部方法：
    * - Promise.resolve()
    * - Promise.reject()
    * - Promise.prototype.catch()
    * - Promise.prototype.finally()
    * - Promise.all()
    * - Promise.allSettled()
    * - Promise.any()
    * - Promise.race()
    */
    class myPromise {
        static PENDING = 'pending';
        static FULFILLED = 'fulfilled';
        static REJECTED = 'rejected';

        constructor(func) {
            this.PromiseState = myPromise.PENDING;
            this.PromiseResult = null;
            this.onFulfilledCallbacks = [];
            this.onRejectedCallbacks = [];
            try {
                func(this.resolve.bind(this), this.reject.bind(this));
            } catch (error) {
                this.reject(error)
            }
        }

        resolve(result) {
            if (this.PromiseState === myPromise.PENDING) {
                setTimeout(() => {
                    this.PromiseState = myPromise.FULFILLED;
                    this.PromiseResult = result;
                    this.onFulfilledCallbacks.forEach(callback => {
                        callback(result)
                    })
                });
            }
        }

        reject(reason) {
            if (this.PromiseState === myPromise.PENDING) {
                setTimeout(() => {
                    this.PromiseState = myPromise.REJECTED;
                    this.PromiseResult = reason;
                    this.onRejectedCallbacks.forEach(callback => {
                        callback(reason)
                    })
                });
            }
        }

        /**
         * [注册fulfilled状态/rejected状态对应的回调函数] 
        * @param {function} onFulfilled  fulfilled状态时 执行的函数
        * @param {function} onRejected  rejected状态时 执行的函数 
        * @returns {function} newPromsie  返回一个新的promise对象
        */
        then(onFulfilled, onRejected) {
            onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value;
            onRejected = typeof onRejected === 'function' ? onRejected : reason => {
                throw reason;
            };

            let promise2 = new myPromise((resolve, reject) => {
                if (this.PromiseState === myPromise.FULFILLED) {
                    setTimeout(() => {
                        try {
                            let x = onFulfilled(this.PromiseResult);
                            resolvePromise(promise2, x, resolve, reject);
                        } catch (e) {
                            reject(e);
                        }
                    });
                } else if (this.PromiseState === myPromise.REJECTED) {
                    setTimeout(() => {
                        try {
                            let x = onRejected(this.PromiseResult);
                            resolvePromise(promise2, x, resolve, reject);
                        } catch (e) {
                            reject(e)
                        }
                    });
                } else if (this.PromiseState === myPromise.PENDING) {
                    this.onFulfilledCallbacks.push(() => {
                        setTimeout(() => {
                            try {
                                let x = onFulfilled(this.PromiseResult);
                                resolvePromise(promise2, x, resolve, reject)
                            } catch (e) {
                                reject(e);
                            }
                        });
                    });
                    this.onRejectedCallbacks.push(() => {
                        setTimeout(() => {
                            try {
                                let x = onRejected(this.PromiseResult);
                                resolvePromise(promise2, x, resolve, reject);
                            } catch (e) {
                                reject(e);
                            }
                        });
                    });
                }
            })

            return promise2
        }

        /**
        * Promise.resolve()
        * @param {[type]} value 要解析为 Promise 对象的值 
        */
        static resolve(value) {
            // 如果这个值是一个 promise ，那么将返回这个 promise 
            if (value instanceof myPromise) {
                return value;
            } else if (value instanceof Object && 'then' in value) {
                // 如果这个值是thenable（即带有`"then" `方法），返回的promise会“跟随”这个thenable的对象，采用它的最终状态；
                return new myPromise((resolve, reject) => {
                    value.then(resolve, reject);
                })
            }

            // 否则返回的promise将以此值完成，即以此值执行`resolve()`方法 (状态为fulfilled)
            return new myPromise((resolve) => {
                resolve(value)
            })
        }

        /**
        * Promise.reject()
        * @param {*} reason 表示Promise被拒绝的原因
        * @returns 
        */
        static reject(reason) {
            return new myPromise((resolve, reject) => {
                reject(reason);
            })
        }

        /**
        * Promise.prototype.catch()
        * @param {*} onRejected 
        * @returns 
        */
        catch (onRejected) {
            return this.then(undefined, onRejected)
        }

        /**
        * Promise.prototype.finally()
        * @param {*} callBack 无论结果是fulfilled或者是rejected，都会执行的回调函数
        * @returns 
        */
        finally(callBack) {
            return this.then(callBack, callBack)
        }

        /**
        * Promise.all()
        * @param {iterable} promises 一个promise的iterable类型（注：Array，Map，Set都属于ES6的iterable类型）的输入
        * @returns 
        */
        static all(promises) {
            return new myPromise((resolve, reject) => {
                // 参数校验
                if (Array.isArray(promises)) {
                    let result = []; // 存储结果
                    let count = 0; // 计数器

                    // 如果传入的参数是一个空的可迭代对象，则返回一个已完成（already resolved）状态的 Promise
                    if (promises.length === 0) {
                        return resolve(promises);
                    }

                    promises.forEach((item, index) => {
                        // myPromise.resolve方法中已经判断了参数是否为promise与thenable对象，所以无需在该方法中再次判断
                        myPromise.resolve(item).then(
                            value => {
                                count++;
                                // 每个promise执行的结果存储在result中
                                result[index] = value;
                                // Promise.all 等待所有都完成（或第一个失败）
                                count === promises.length && resolve(result);
                            },
                            reason => {
                                /**
                                * 如果传入的 promise 中有一个失败（rejected），
                                * Promise.all 异步地将失败的那个结果给失败状态的回调函数，而不管其它 promise 是否完成
                                */
                                reject(reason);
                            }
                        )
                    })
                } else {
                    return reject(new TypeError('Argument is not iterable'))
                }
            })
        }

        /**
        * Promise.allSettled()
        * @param {iterable} promises 一个promise的iterable类型（注：Array，Map，Set都属于ES6的iterable类型）的输入
        * @returns 
        */
        static allSettled(promises) {
            return new myPromise((resolve, reject) => {
                // 参数校验
                if (Array.isArray(promises)) {
                    let result = []; // 存储结果
                    let count = 0; // 计数器

                    // 如果传入的是一个空数组，那么就直接返回一个resolved的空数组promise对象
                    if (promises.length === 0) return resolve(promises);

                    promises.forEach((item, index) => {
                        // 非promise值，通过Promise.resolve转换为promise进行统一处理
                        myPromise.resolve(item).then(
                            value => {
                                count++;
                                // 对于每个结果对象，都有一个 status 字符串。如果它的值为 fulfilled，则结果对象上存在一个 value 。
                                result[index] = {
                                    status: 'fulfilled',
                                    value
                                }
                                // 所有给定的promise都已经fulfilled或rejected后,返回这个promise
                                count === promises.length && resolve(result);
                            },
                            reason => {
                                count++;
                                /**
                                * 对于每个结果对象，都有一个 status 字符串。如果值为 rejected，则存在一个 reason 。
                                * value（或 reason ）反映了每个 promise 决议（或拒绝）的值。
                                */
                                result[index] = {
                                    status: 'rejected',
                                    reason
                                }
                                // 所有给定的promise都已经fulfilled或rejected后,返回这个promise
                                count === promises.length && resolve(result);
                            }
                        )
                    })
                } else {
                    return reject(new TypeError('Argument is not iterable'))
                }
            })
        }

        /**
        * Promise.any()
        * @param {iterable} promises 一个promise的iterable类型（注：Array，Map，Set都属于ES6的iterable类型）的输入
        * @returns 
        */
        static any(promises) {
            return new myPromise((resolve, reject) => {
                // 参数校验
                if (Array.isArray(promises)) {
                    let errors = []; // 
                    let count = 0; // 计数器

                    // 如果传入的参数是一个空的可迭代对象，则返回一个 已失败（already rejected） 状态的 Promise。
                    if (promises.length === 0) return reject(new AggregateError([], 'All promises were rejected'));

                    promises.forEach(item => {
                        // 非Promise值，通过Promise.resolve转换为Promise
                        myPromise.resolve(item).then(
                            value => {
                                // 只要其中的一个 promise 成功，就返回那个已经成功的 promise 
                                resolve(value);
                            },
                            reason => {
                                count++;
                                errors.push(reason);
                                /**
                                * 如果可迭代对象中没有一个 promise 成功，就返回一个失败的 promise 和AggregateError类型的实例，
                                * AggregateError是 Error 的一个子类，用于把单一的错误集合在一起。
                                */
                                count === promises.length && reject(new AggregateError(errors, 'All promises were rejected'));
                            }
                        )
                    })
                } else {
                    return reject(new TypeError('Argument is not iterable'))
                }
            })
        }

        /**
        * Promise.race()
        * @param {iterable} promises 可迭代对象，类似Array。详见 iterable。
        * @returns 
        */
        static race(promises) {
            return new myPromise((resolve, reject) => {
                // 参数校验
                if (Array.isArray(promises)) {
                    // 如果传入的迭代promises是空的，则返回的 promise 将永远等待。
                    if (promises.length > 0) {
                        promises.forEach(item => {
                            /**
                            * 如果迭代包含一个或多个非承诺值和/或已解决/拒绝的承诺，
                            * 则 Promise.race 将解析为迭代中找到的第一个值。
                            */
                            myPromise.resolve(item).then(resolve, reject);
                        })
                    }
                } else {
                    return reject(new TypeError('Argument is not iterable'))
                }
            })
        }
    }

    /**
    * 对resolve()、reject() 进行改造增强 针对resolve()和reject()中不同值情况 进行处理
    * @param  {promise} promise2 promise1.then方法返回的新的promise对象
    * @param  {[type]} x         promise1中onFulfilled或onRejected的返回值
    * @param  {[type]} resolve   promise2的resolve方法
    * @param  {[type]} reject    promise2的reject方法
    */
    function resolvePromise(promise2, x, resolve, reject) {
        if (x === promise2) {
            return reject(new TypeError('Chaining cycle detected for promise'));
        }

        if (x instanceof myPromise) {
            if (x.PromiseState === myPromise.PENDING) {
                x.then(y => {
                    resolvePromise(promise2, y, resolve, reject)
                }, reject);
            } else if (x.PromiseState === myPromise.FULFILLED) {
                resolve(x.PromiseResult);
            } else if (x.PromiseState === myPromise.REJECTED) {
                reject(x.PromiseResult);
            }
        } else if (x !== null && ((typeof x === 'object' || (typeof x === 'function')))) {
            try {
                var then = x.then;
            } catch (e) {
                return reject(e);
            }

            if (typeof then === 'function') {
                let called = false;
                try {
                    then.call(
                        x,
                        y => {
                            if (called) return;
                            called = true;
                            resolvePromise(promise2, y, resolve, reject);
                        },
                        r => {
                            if (called) return;
                            called = true;
                            reject(r);
                        }
                    )
                } catch (e) {
                    if (called) return;
                    called = true;

                    reject(e);
                }
            } else {
                resolve(x);
            }
        } else {
            return resolve(x);
        }
    }

    myPromise.deferred = function () {
        let result = {};
        result.promise = new myPromise((resolve, reject) => {
            result.resolve = resolve;
            result.reject = reject;
        });
        return result;
    }

    module.exports = myPromise;
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
