# 前端实现原理汇总

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

## `Javascript`

1. ### `Object.is` 的实现原理

    ```javascript
    Object.is = function (x, y) {
      if (x === y) {
        // +0 !== -0
        // 1/+0 = +Infinity; 1/-0 = -Infinity
        // +Infinity !== -Infinity
        // Infinity：正无穷大的数值；-Infinity：负无穷大的数值
        return x !== 0 || 1 / x === 1 / y;
      } else {
        // NaN == NaN
        // 一个变量不等于自身变量，那么它一定是 NaN
        // 两个都是 NaN 的时候返回 true
        return x !== x && y !== y;
      }
    };
    ```

1. ### `Promise` 的实现原理

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
      static PENDING = 'pending'
      static FULFILLED = 'fulfilled'
      static REJECTED = 'rejected'

      constructor (func) {
        this.PromiseState = myPromise.PENDING
        this.PromiseResult = null
        this.onFulfilledCallbacks = []
        this.onRejectedCallbacks = []
        try {
          func(this.resolve.bind(this), this.reject.bind(this))
        } catch (error) {
          this.reject(error)
        }
      }

      resolve(result) {
        if (this.PromiseState === myPromise.PENDING) {
          setTimeout(() => {
            this.PromiseState = myPromise.FULFILLED
            this.PromiseResult = result
            this.onFulfilledCallbacks.forEach(callback => callback(result))
          })
        }
      }

      reject(reason) {
        if (this.PromiseState === myPromise.PENDING) {
          setTimeout(() => {
            this.PromiseState = myPromise.REJECTED
            this.PromiseResult = reason
            this.onRejectedCallbacks.forEach(callback => callback(reason))
          })
        }
      }

      /**
       * [注册 fulfilled 状态 / rejected 状态对应的回调函数] 
      * @param {function} onFulfilled  fulfilled 状态时 执行的函数
      * @param {function} onRejected  rejected 状态时 执行的函数 
      * @returns {function} newPromsie  返回一个新的 promise 对象
      */
      then(onFulfilled, onRejected) {
        onFulfilled = typeof onFulfilled === 'function' ? onFulfilled : value => value
        onRejected = typeof onRejected === 'function' ? onRejected : reason => throw reason

        let promise2 = new myPromise((resolve, reject) => {
          if (this.PromiseState === myPromise.FULFILLED) {
            setTimeout(() => {
              try {
                let x = onFulfilled(this.PromiseResult)
                resolvePromise(promise2, x, resolve, reject)
              } catch (e) {
                reject(e)
              }
            })
          } else if (this.PromiseState === myPromise.REJECTED) {
            setTimeout(() => {
              try {
                let x = onRejected(this.PromiseResult)
                resolvePromise(promise2, x, resolve, reject)
              } catch (e) {
                reject(e)
              }
            });
          } else if (this.PromiseState === myPromise.PENDING) {
            this.onFulfilledCallbacks.push(() => {
              setTimeout(() => {
                try {
                  let x = onFulfilled(this.PromiseResult)
                  resolvePromise(promise2, x, resolve, reject)
                } catch (e) {
                  reject(e)
                }
              })
            })
            this.onRejectedCallbacks.push(() => {
              setTimeout(() => {
                try {
                  let x = onRejected(this.PromiseResult)
                  resolvePromise(promise2, x, resolve, reject)
                } catch (e) {
                  reject(e)
                }
              })
            })
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
          return value
        } else if (value instanceof Object && 'then' in value) {
          // 如果这个值是 thenable（即带有 "then" 方法），返回的 promise 会“跟随”这个 thenable 的对象，采用它的最终状态；
          return new myPromise((resolve, reject) => value.then(resolve, reject))
        }

        // 否则返回的 promise 将以此值完成，即以此值执行 resolve() 方法 (状态为 fulfilled)
        return new myPromise(resolve => resolve(value))
      }

      /**
      * Promise.reject()
      * @param {*} reason 表示 Promise 被拒绝的原因
      * @returns 
      */
      static reject(reason) {
        return new myPromise((resolve, reject) => reject(reason))
      }

      /**
      * Promise.prototype.catch()
      * @param {*} onRejected 
      * @returns 
      */
      catch(onRejected) {
        return this.then(undefined, onRejected)
      }

      /**
      * Promise.prototype.finally()
      * @param {*} callBack 无论结果是 fulfilled 或者是 rejected，都会执行的回调函数
      * @returns 
      */
      finally(callBack) {
        return this.then(callBack, callBack)
      }

      /**
      * Promise.all()
      * @param {iterable} promises 一个 promise 的 iterable 类型（注：Array，Map，Set 都属于 ES6 的 iterable 类型）的输入
      * @returns 
      */
      static all(promises) {
        return new myPromise((resolve, reject) => {
          // 参数校验
          if (Array.isArray(promises)) {
            let result = [] // 存储结果
            let count = 0 // 计数器

            // 如果传入的参数是一个空的可迭代对象，则返回一个已完成（already resolved）状态的 Promise
            if (promises.length === 0) return resolve(promises)

            promises.forEach((item, index) => {
              // myPromise.resolve 方法中已经判断了参数是否为 promise 与 thenable 对象，所以无需在该方法中再次判断
              myPromise.resolve(item).then(
                value => {
                  count++
                  // 每个promise执行的结果存储在result中
                  result[index] = value
                  // Promise.all 等待所有都完成（或第一个失败）
                  count === promises.length && resolve(result)
                },
                reason => {
                  /**
                  * 如果传入的 promise 中有一个失败（rejected），
                  * Promise.all 异步地将失败的那个结果给失败状态的回调函数，而不管其它 promise 是否完成
                  */
                  reject(reason)
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
      * @param {iterable} promises 一个 promise 的 iterable 类型（注：Array，Map，Set 都属于 ES6 的 iterable 类型）的输入
      * @returns 
      */
      static allSettled(promises) {
        return new myPromise((resolve, reject) => {
          // 参数校验
          if (Array.isArray(promises)) {
            let result = [] // 存储结果
            let count = 0 // 计数器

            // 如果传入的是一个空数组，那么就直接返回一个 resolved 的空数组 promise 对象
            if (promises.length === 0) return resolve(promises)

            promises.forEach((item, index) => {
              // 非 promise 值，通过 Promise.resolve 转换为 promise 进行统一处理
              myPromise.resolve(item).then(
                value => {
                  count++
                  // 对于每个结果对象，都有一个 status 字符串。如果它的值为 fulfilled，则结果对象上存在一个 value
                  result[index] = {
                    status: 'fulfilled',
                    value
                  }
                  // 所有给定的 promise 都已经 fulfilled 或 rejected 后，返回这个 promise
                  count === promises.length && resolve(result)
                },
                reason => {
                  count++
                  /**
                  * 对于每个结果对象，都有一个 status 字符串。如果值为 rejected，则存在一个 reason
                  * value（或 reason）反映了每个 promise 决议（或拒绝）的值。
                  */
                  result[index] = {
                    status: 'rejected',
                    reason
                  }
                  // 所有给定的 promise 都已经 fulfilled 或 rejected 后，返回这个 promise
                  count === promises.length && resolve(result)
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
      * @param {iterable} promises 一个 promise 的 iterable 类型（注：Array，Map，Set 都属于 ES6 的 iterable 类型）的输入
      * @returns 
      */
      static any(promises) {
        return new myPromise((resolve, reject) => {
          // 参数校验
          if (Array.isArray(promises)) {
            let errors = [] // 
            let count = 0 // 计数器

            // 如果传入的参数是一个空的可迭代对象，则返回一个 已失败（already rejected） 状态的 Promise。
            if (promises.length === 0) return reject(new AggregateError([], 'All promises were rejected'))

            promises.forEach(item => {
              // 非 Promise 值，通过 Promise.resolve 转换为 Promise
              myPromise.resolve(item).then(
                value => {
                  // 只要其中的一个 promise 成功，就返回那个已经成功的 promise 
                  resolve(value)
                },
                reason => {
                  count++
                  errors.push(reason)
                  /**
                  * 如果可迭代对象中没有一个 promise 成功，就返回一个失败的 promise 和 AggregateError 类型的实例，
                  * AggregateError 是 Error 的一个子类，用于把单一的错误集合在一起。
                  */
                  count === promises.length && reject(new AggregateError(errors, 'All promises were rejected'))
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
      * @param {iterable} promises 可迭代对象，类似 Array。详见 iterable。
      * @returns 
      */
      static race(promises) {
        return new myPromise((resolve, reject) => {
          // 参数校验
          if (Array.isArray(promises)) {
            // 如果传入的迭代 promises 是空的，则返回的 promise 将永远等待。
            if (promises.length > 0) {
              promises.forEach(item => {
                /**
                * 如果迭代包含一个或多个非承诺值和/或已解决/拒绝的承诺，
                * 则 Promise.race 将解析为迭代中找到的第一个值。
                */
                myPromise.resolve(item).then(resolve, reject)
              })
            }
          } else {
            return reject(new TypeError('Argument is not iterable'))
          }
        })
      }
    }

    /**
    * 对 resolve()、reject() 进行改造增强 针对 resolve() 和 reject() 中不同值情况 进行处理
    * @param  {promise} promise2  promise1.then 方法返回的新的 promise 对象
    * @param  {[type]}  x         promise1 中 onFulfilled 或 onRejected 的返回值
    * @param  {[type]}  resolve   promise2 的 resolve 方法
    * @param  {[type]}  reject    promise2 的 reject 方法
    */
    function resolvePromise(promise2, x, resolve, reject) {
      if (x === promise2) return reject(new TypeError('Chaining cycle detected for promise'))

      if (x instanceof myPromise) {
        if (x.PromiseState === myPromise.PENDING) {
          x.then(y => resolvePromise(promise2, y, resolve, reject), reject)
        } else if (x.PromiseState === myPromise.FULFILLED) {
          resolve(x.PromiseResult)
        } else if (x.PromiseState === myPromise.REJECTED) {
          reject(x.PromiseResult)
        }
      } else if (x !== null && ((typeof x === 'object' || (typeof x === 'function')))) {
        try {
          var then = x.then
        } catch (e) {
          return reject(e)
        }

        if (typeof then === 'function') {
          let called = false
          try {
            then.call(
              x,
              y => {
                if (called) return
                called = true
                resolvePromise(promise2, y, resolve, reject)
              },
              r => {
                if (called) return
                called = true
                reject(r)
              }
            )
          } catch (e) {
            if (called) return
            called = true

            reject(e)
          }
        } else {
          resolve(x)
        }
      } else {
        return resolve(x)
      }
    }

    myPromise.deferred = function () {
      let result = {}
      result.promise = new myPromise((resolve, reject) => {
        result.resolve = resolve
        result.reject = reject
      })
      return result
    }

    module.exports = myPromise
    ```

## `Vue`

1. ### `Vue2.0` 响应式实现原理

1. ### `Vue3.0` 响应式实现原理

1. ### `computed` 的实现原理

1. ### `watch` 的实现原理

1. ### `$nextTick` 的实现原理

## `React`

## `Webpack`

1. ### 说说 `Webpack` 的实现原理

1. ### 说说 `Webpack` 的工作原理

    - 基本概念
      - `Entry`：入口，`Webpack` 执行构建的第一步将从 `Entry` 开始，可抽象成输入。
      - `Module`：模块，在 `Webpack` 里一切皆模块，一个模块对应着一个文件。`Webpack` 会从配置的 `Entry` 开始递归找出所有依赖的模块。
      - `Chunk`：代码块，一个 `Chunk` 由多个模块组合而成，用于代码合并与分割。
      - `Loader`：模块转换器，用于把模块原内容按照需求转换成新内容。
      - `Plugin`：扩展插件，在 `Webpack` 构建流程中的特定时机会广播出对应的事件，插件可以监听这些事件的发生，在特定时机做对应的事情。
      <br>

    - 流程概括
      `Webpack` 的运行流程是一个串行的过程，从启动到结束会依次执行以下流程：
      1. 初始化参数：从配置文件和 `Shell` 语句中读取与合并参数，得出最终的参数；
      2. 开始编译：用上一步得到的参数初始化 `Compiler` 对象，加载所有配置的插件，执行对象的 `run` 方法开始执行编译；
      3. 确定入口：根据配置中的 `entry` 找出所有的入口文件；
      4. 编译模块：从入口文件出发，调用所有配置的 `Loader` 对模块进行翻译，再找出该模块依赖的模块，再递归本步骤直到所有入口依赖的文件都经过了本步骤的处理；
      5. 完成模块编译：在经过第4步使用 `Loader` 翻译完所有模块后，得到了每个模块被翻译后的最终内容以及它们之间的依赖关系；
      6. 输出资源：根据入口和模块之间的依赖关系，组装成一个个包含多个模块的 `Chunk`，再把每个 `Chunk` 转换成一个单独的文件加入到输出列表，这步是可以修改输出内容的最后机会；
      7. 输出完成：在确定好输出内容后，根据配置确定输出的路径和文件名，把文件内容写入到文件系统。

      在以上过程中，`Webpack` 会在特定的时间点广播出特定的事件，插件在监听到感兴趣的事件后会执行特定的逻辑，并且插件可以调用 `Webpack` 提供的 `API` 改变 `Webpack` 的运行结果。
      <br>

    - 流程细节
      `Webpack` 的构建流程可以分为以下三大阶段：

      1. 初始化：启动构建，读取与合并配置参数，加载 `Plugin`，实例化 `Compiler`。
      2. 编译：从 `Entry` 发出，针对每个 `Module` 串行调用对应的 `Loader` 去翻译文件内容，再找到该 `Module` 依赖的 `Module`，递归地进行编译处理。
      3. 输出：对编译后的 `Module` 组合成 `Chunk`，把 `Chunk` 转换成文件，输出到文件系统。
      <br>

      如果只执行一次构建，以上阶段将会按照顺序各执行一次。但在开启监听模式下，流程将变为如下：

      ![5-1监听模式的构建流程](../images/Interview/实现原理/5-1监听模式的构建流程.png)

      在每个大阶段中又会发生很多事件，`Webpack` 会把这些事件广播出来供给 `Plugin` 使用，下面来一一介绍。
      <br>

    - 初始化阶段

      |事件名|解释|
      |-|-|
      |初始化参数|从配置文件和 `Shell` 语句中读取与合并参数，得出最终的参数。 这个过程中还会执行配置文件中的插件实例化语句 `new Plugin()`。|
      |实例化 `Compiler`|用上一步得到的参数初始化 `Compiler` 实例，`Compiler` 负责文件监听和启动编译。`Compiler` 实例中包含了完整的 Webpack 配置，全局只有一个 `Compiler` 实例。|
      |加载插件|依次调用插件的 `apply` 方法，让插件可以监听后续的所有事件节点。同时给插件传入 `compiler` 实例的引用，以方便插件通过 `compiler` 调用 `Webpack` 提供的 `API`。|
      |`environment`|开始应用 `Node.js` 风格的文件系统到 `compiler` 对象，以方便后续的文件寻找和读取。|
      |`entry-option` |读取配置的 `Entrys`，为每个 `Entry` 实例化一个对应的 `EntryPlugin`，为后面该 `Entry` 的递归解析工作做准备。|
      |`after-plugins`|调用完所有内置的和配置的插件的 `apply` 方法。|
      |`after-resolvers`|根据配置初始化完 `resolver`，`resolver` 负责在文件系统中寻找指定路径的文件。|
      <br>

    - 编译阶段

      |事件名|解释|
      |-|-|
      |`run`|启动一次新的编译。|
      |`watch-run`|和 `run` 类似，区别在于它是在监听模式下启动的编译，在这个事件中可以获取到是哪些文件发生了变化导致重新启动一次新的编译。|
      |`compile`|该事件是为了告诉插件一次新的编译将要启动，同时会给插件带上 `compiler` 对象。|
      |`compilation`|当 `Webpack` 以开发模式运行时，每当检测到文件变化，一次新的 `Compilation` 将被创建。一个 `Compilation` 对象包含了当前的模块资源、编译生成资源、变化的文件等。`Compilation` 对象也提供了很多事件回调供插件做扩展。|
      |`make`|一个新的 `Compilation` 创建完毕，即将从 `Entry` 开始读取文件，根据文件类型和配置的 `Loader` 对文件进行编译，编译完后再找出该文件依赖的文件，递归的编译和解析。|
      |`after-compile`|一次 `Compilation` 执行完成。|
      |`invalid`|当遇到文件不存在、文件编译错误等异常时会触发该事件，该事件不会导致 `Webpack` 退出。|
      在编译阶段中，最重要的要数 `compilation` 事件了，因为在 `compilation` 阶段调用了 `Loader` 完成了每个模块的转换操作，在 `compilation` 阶段又包括很多小的事件，它们分别是：

      |事件名|解释|
      |-|-|
      |`build-module`|使用对应的 `Loader` 去转换一个模块。|
      |`normal-module-loader`|在用 `Loader` 对一个模块转换完后，使用 `acorn` 解析转换后的内容，输出对应的抽象语法树（`AST`），以方便 `Webpack` 后面对代码的分析。|
      |`program`|从配置的入口模块开始，分析其 `AST`，当遇到 `require` 等导入其它模块语句时，便将其加入到依赖的模块列表，同时对新找出的依赖模块递归分析，最终搞清所有模块的依赖关系。|
      |`seal`|所有模块及其依赖的模块都通过 `Loader` 转换完成后，根据依赖关系开始生成 `Chunk`。|
      <br>

    - 输出阶段

      |事件名|解释|
      |-|-|
      |`should-emit`|所有需要输出的文件已经生成好，询问插件哪些文件需要输出，哪些不需要。|
      |`emit`|确定好要输出哪些文件后，执行文件输出，可以在这里获取和修改输出内容。|
      |`after-emit`|文件输出完毕。|
      |`done`|成功完成一次完成的编译和输出流程。|
      |`failed`|如果在编译和输出流程中遇到异常导致 `Webpack` 退出时，就会直接跳转到本步骤，插件可以在本事件中获取到具体的错误原因。|
      在输出阶段已经得到了各个模块经过转换后的结果和其依赖关系，并且把相关模块组合在一起形成一个个 `Chunk`。 在输出阶段会根据 `Chunk` 的类型，使用对应的模版生成最终要要输出的文件内容。
      <br>

1. ### 说说 `Webpack` 的热更新原理

## 其它

1. ### 说说 `babel` 的实现原理

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
