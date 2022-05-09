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

    <br>

1. ### `Object.create` 的实现原理

    ```js
    // 不支持 null 版
    Object.create = function (proto, propertiesObject) {
      if (typeof proto !== 'object' && typeof proto !== 'function') {
        throw new TypeError('Object prototype may only be an Object: ' + proto)
      } else if (proto === null) {
        throw new Error("This browser's implementation of Object.create is a shim and doesn't support 'null' as the first argument.")
      }

      if (typeof propertiesObject !== 'undefined') throw new Error("This browser's implementation of Object.create is a shim and doesn't support a second argument.")

      function F() {}
      F.prototype = proto

      return new F()
    }
    ```

    <br>

1. ### `Promise` 的实现原理

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

      ![5-1监听模式的构建流程](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/Interview/实现原理/5-1监听模式的构建流程.png)

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
