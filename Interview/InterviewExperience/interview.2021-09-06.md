# 最新的前端大厂面经（详解答案）

## 简单

1. ### 从输入一个 `URL` 地址到浏览器完成渲染的整个过程

    面试简单版作答

    1. 浏览器地址栏输入 `URL` 并回车
    2. 浏览器查找当前 `URL` 是否存在缓存，并比较缓存是否过期
    3. `DNS` 解析 `URL` 对应的 `IP`
    4. 根据 `IP` 建立 `TCP` 连接（三次握手）
    5. 发送 `http` 请求
    6. 服务器处理请求，浏览器接受 `HTTP` 响应
    7. 浏览器解析并渲染页面
    8. 关闭 `TCP` 连接（四次握手）

    > 注意！ 面试官可以基于这道题进行前端很多知识点的考察从 `http` 网络知识到浏览器原理再到前端性能优化 这个题目都可以作为引子开始

    [史上最详细的经典面试题 从输入 URL 到看到页面发生了什么？](../summary/从输入URL到看到页面发生了什么？.pdf)
    <br>

1. ### 什么是事件代理（事件委托）有什么好处

    事件委托的原理：不给每个子节点单独设置事件监听器，而是设置在其父节点上，然后利用冒泡原理设置每个子节点。
    <br>

    优点：
    - 减少内存消耗和 `dom` 操作，提高性能 在 `JavaScript` 中，添加到页面上的事件处理程序数量将直接关系到页面的整体运行性能，因为需要不断的操作 `dom`，·那么引起浏览器重绘和回流的可能也就越多，页面交互的事件也就变的越长，这也就是为什么要减少 `dom` 操作的原因。每一个事件处理函数，都是一个对象，多一个事件处理函数，内存中就会被多占用一部分空间。如果要用事件委托，就会将所有的操作放到 `js` 程序里面，只对它的父级进行操作，与 `dom` 的操作就只需要交互一次，这样就能大大的减少与 `dom` 的交互次数，提高性能；
    - 动态绑定事件：因为事件绑定在父级元素，所以新增的元素也能触发同样的事件
    <br>

1. ### `addEventListener` 默认是捕获还是冒泡

    默认是**冒泡**

    `addEventListener` 第三个参数默认为 `false` 代表执行事件冒泡行为。

    当为 `true` 时执行事件捕获行为。
    <br>

1. ### `css` 的渲染层合成是什么？浏览器如何创建新的渲染层

    在 `DOM` 树中每个节点都会对应一个渲染对象（`RenderObject`），当它们的渲染对象处于相同的坐标空间（`z` 轴空间）时，就会形成一个 `RenderLayers`，也就是渲染层。渲染层将保证页面元素以正确的顺序堆叠，这时候就会出现层合成（`composite`），从而正确处理透明元素和重叠元素的显示。对于有位置重叠的元素的页面，这个过程尤其重要，因为一旦图层的合并顺序出错，将会导致元素显示异常。

    浏览器如何创建新的渲染层
    - 根元素 `document`
    - 有明确的定位属性（`relative、fixed、sticky、absolute`）
    - `opacity < 1`
    - 有 `CSS fliter` 属性
    - 有 `CSS mask` 属性
    - 有 `CSS mix-blend-mode` 属性且值不为 `normal`
    - 有 `CSS transform` 属性且值不为 `none`
    - `backface-visibility` 属性为 `hidden`
    - 有 `CSS reflection` 属性
    - 有 `CSS column-count` 属性且值不为 `auto` 或者有 `CSS column-width` 属性且值不为 `auto`
    - 当前有对于 `opacity、transform、fliter、backdrop-filter` 应用动画
    - `overflow` 不为 `visible`

    >注意！不少人会将这些合成层的条件和渲染层产生的条件混淆，这两种条件发生在两个不同的层处理环节，是完全不一样的 具体可以看看这篇文章 [浏览器层合成与页面渲染优化](https://juejin.cn/post/6844903966573068301)
    <br>

1. ### `webpack Plugin` 和 `Loader` 的区别

    - `Loader`：用于对模块源码的转换，`loader` 描述了 `webpack` 如何处理非 `Javascript` 模块，并且在 `build` 中引入这些依赖。`loader` 可以将文件从不同的语言（如 `TypeScript`）转换为 `JavaScript`，或者将内联图像转换为 `data URL`。比如说：`CSS-Loader`，`Style-Loader` 等。
    - `Plugin`：目的在于解决 `loader` 无法实现的其他事,它直接作用于 `webpack`，扩展了它的功能。在 `webpack` 运行的生命周期中会广播出许多事件，`plugin` 可以监听这些事件，在合适的时机通过 `webpack` 提供的 `API` 改变输出结果。插件的范围包括，从打包优化和压缩，一直到重新定义环境中的变量。插件接口功能极其强大，可以用来处理各种各样的任务。

    [Webpack 相关面试题](https://juejin.cn/post/6844904150988226574)
    <br>

1. ### `apply call bind` 区别

    - 三者都可以改变函数的 `this` 对象指向。
    - 三者第一个参数都是 `this` 要指向的对象，如果没有这个参数或参数为 `undefined ｜  null`，则默认指向全局 `window`。
    - 三者都可以传参，但是 `apply` 是数组，而 `call` 是参数列表，且 `apply` 和 `call` 是一次性传入参数，而 `bind` 可以分为多次传入。
    - `bind` 是返回绑定 `this` 之后的函数，便于稍后调用；`apply`、`call` 则是立即执行 。
    - `bind()` 会返回一个新的函数，如果返回的这个新函数作为构造函数创建一个新的对象，那么此时 `this` 不再指向传入给 `bind` 的第一个参数，而是指向用 `new` 创建的实例
    <br>

1. ### 举出闭包实际场景运用的例子

    ```js
    <!-- 比如常见的防抖节流 -->

    // 防抖
    function debounce(fn, delay = 300) {
      let timer; //闭包引用的外界变量
      return function () {
        const args = arguments;
        if (timer) {
          clearTimeout(timer);
        }
        timer = setTimeout(() => {
          fn.apply(this, args);
        }, delay);
      };
    }

    <!-- 使用闭包可以在 JavaScript 中模拟块级作用域 -->

    function outputNumbers(count) {
      (function () {
        for (var i = 0; i < count; i++) {
          alert(i);
        }
      })();
      alert(i); //导致一个错误！
    }

    <!-- 闭包可以用于在对象中创建私有变量 -->

    var aaa = (function () {
      var a = 1;
      function bbb() {
        a++;
        console.log(a);
      }
      function ccc() {
        a++;
        console.log(a);
      }
      return {
        b: bbb, //json结构
        c: ccc,
      };
    })();
    console.log(aaa.a); //undefined
    aaa.b(); //2
    aaa.c(); //3
    ```

    <br>

1. ### `css` 优先级是怎么计算的

    1. 第一优先级：`!important` 会覆盖页面内任何位置的元素样式
    2. 内联样式，如 `style="color: green"`，权值为 `1000`
    3. `ID` 选择器，如 `#app`，权值为 `0100`
    4. 类、伪类、属性选择器，如 `.foo, :first-child, div[class="foo"]`，权值为 `0010`
    5. 标签、伪元素选择器，如 `div::first-line`，权值为 `0001`
    6. 通配符、子类选择器、兄弟选择器，如 `*, >, +`，权值为 `0000`
    7. 继承的样式没有权值
    <br>

1. ### 事件循环相关题目--必考（一般是代码输出顺序判断）

    ```js
    setTimeout(function () {
      console.log("1");
    }, 0);
    async function async1() {
      console.log("2");
      const data = await async2();
      console.log("3");
      return data;
    }
    async function async2() {
      return new Promise((resolve) => {
        console.log("4");
        resolve("async2的结果");
      }).then((data) => {
        console.log("5");
        return data;
      });
    }
    async1().then((data) => {
      console.log("6");
      console.log(data);
    });
    new Promise(function (resolve) {
      console.log("7");
      // resolve() // 结果2
    }).then(function () {
      console.log("8");
    });

    // 输出结果：247536 async2的结果 Promise { pending } 1
    // 输出结果2: 2475836 async2的结果 Promise { fulfilled } 1
    ```

    <br>

1. ### `HTTP` 状态码 `204 301 302 304 400 401 403 404` 含义

    - `204` （无内容） 服务器成功处理了请求，但没有返回任何内容
    - `301` （永久移动） 请求的网页已永久移动到新位置。服务器返回此响应（对 `GET` 或 `HEAD` 请求的响应）时，会自动将请求者转到新位置。
    - `302` （临时移动） 服务器目前从不同位置的网页响应请求，但请求者应继续使用原有位置来进行以后的请求。
    - `304` （未修改） 自从上次请求后，请求的网页未修改过。服务器返回此响应时，不会返回网页内容。
    - `400` （错误请求） 服务器不理解请求的语法（一般为参数错误）。
    - `401` （未授权） 请求要求身份验证。对于需要登录的网页，服务器可能返回此响应。
    - `403` （禁止） 服务器拒绝请求。（一般为客户端的用户权限不够）
    - `404` （未找到） 服务器找不到请求的网页。
    <br>

1. ### `HTTP2.0` 做了哪些改进 `3.0` 呢

    `HTTP2.0` 特性如下
    - 二进制分帧传输
    - 多路复用
    - 头部压缩
    - 服务器推送

    `HTTP3.0` 相对于 `HTTP2.0` 是一种脱胎换骨的改变！

    `HTTP` 协议是应用层协议，都是建立在传输层之上的。我们也都知道传输层上面不只有 `TCP` 协议，还有另外一个强大的协议 `UDP` 协议，`2.0` 和 `1.0` 都是基于 `TCP` 的，因此都会有 `TCP` 带来的硬伤以及局限性。而 `HTTP3.0` 则是建立在 `UDP` 的基础上。所以其与 `HTTP2.0` 之间有质的不同。

    `HTTP3.0` 特性如下
    - 连接迁移
    - 无队头阻塞
    - 自定义的拥塞控制
    - 前向安全和前向纠错

    [HTTP2.0的一些思考以及HTTP3.0的优势](https://blog.csdn.net/m0_60360320/article/details/119812431)
    <br>

1. ### `position` 有哪些值，作用分别是什么

    - `static`
      `static`(没有定位)是 `position` 的默认值，元素处于正常的文档流中，会忽略 `left、top、right、bottom` 和 `z-index` 属性。
    - `relative`
      `relative`(相对定位)是指给元素设置相对于原本位置的定位，元素并不脱离文档流，因此元素原本的位置会被保留，其他的元素位置不会受到影响。
      使用场景：子元素相对于父元素进行定位
    - `absolute`
      `absolute`(绝对定位)是指给元素设置绝对的定位，相对定位的对象为设置了 `position: relative | absolute` 的祖先元素。如果没有则相对于 `body` 进行定位。
      使用场景：跟随图标，图标使用不依赖定位父级的 `absolute` 和 `margin` 属性进行定位，这样，当文本的字符个数改变时，图标的位置可以自适应
    - `fixed`
      可以简单说 `fixed` 是特殊版的 `absolute`，`fixed` 元素总是相对于 `body` 定位的。
      使用场景：侧边栏或者广告图
    - `inherit`
      继承父元素的 `position` 属性，但需要注意的是 `IE8` 以及往前的版本都不支持 `inherit` 属性。
    - `sticky`
      设置了 `sticky` 的元素，在屏幕范围（`viewport`）时该元素的位置并不受到定位影响（设置是 `top、left` 等属性无效），当该元素的位置将要移出偏移范围时，定位又会变成 `fixed`，根据设置的 `left、top` 等属性成固定位置的效果。当元素在容器中被滚动超过指定的偏移值时，元素在容器内固定在指定位置。亦即如果你设置了 `top: 50px`，那么在 `sticky` 元素到达距离相对定位的元素顶部 `50px` 的位置时固定，不再向上移动（相当于此时 `fixed` 定位）。
      使用场景：跟随窗口
    <br>

1. ### 垂直水平居中实现方式

    [面试官：你能实现多少种水平垂直居中的布局（定宽高和不定宽高）](https://juejin.cn/post/6844903982960214029)
    <br>

1. ### `Vue` 组件通讯方式有哪些方法

    - `props` 和 `$emit` 父组件向子组件传递数据是通过 `prop` 传递的，子组件传递数据给父组件是通过 `$emit` 触发事件来做到的
    - `$parent`, `$children` 获取当前组件的父组件和当前组件的子组件
    - `$attrs` 和 `$listeners` A->B->C。Vue 2.4 开始提供了 `$attrs` 和 `$listeners` 来解决这个问题
    - 父组件中通过 `provide` 来提供变量，然后在子组件中通过 `inject` 来注入变量。(官方不推荐在实际业务中使用，但是写组件库时很常用)
    - `$refs` 获取组件实例
    - `envetBus` 兄弟组件数据传递 这种情况下可以使用事件总线的方式
    - `vuex` 状态管理

1. ### `Vue` 响应式原理

    整体思路是数据劫持+观察者模式

    对象内部通过 `defineReactive` 方法，使用 `Object.defineProperty` 将属性进行劫持（只会劫持已经存在的属性），数组则是通过重写数组方法来实现。当页面使用对应属性时，每个属性都拥有自己的 `dep` 属性，存放他所依赖的 `watcher`（依赖收集），当属性变化后会通知自己对应的 `watcher` 去更新(派发更新)。

    相关代码如下

    ```js
    class Observer {
      // 观测值
      constructor(value) {
        this.walk(value);
      }
      walk(data) {
        // 对象上的所有属性依次进行观测
        let keys = Object.keys(data);
        for (let i = 0; i < keys.length; i++) {
          let key = keys[i];
          let value = data[key];
          defineReactive(data, key, value);
        }
      }
    }
    // Object.defineProperty数据劫持核心 兼容性在ie9以及以上
    function defineReactive(data, key, value) {
      observe(value); // 递归关键
      // --如果value还是一个对象会继续走一遍odefineReactive 层层遍历一直到value不是对象才停止
      //   思考？如果Vue数据嵌套层级过深 >>性能会受影响
      Object.defineProperty(data, key, {
        get() {
          console.log("获取值");

          //需要做依赖收集过程 这里代码没写出来
          return value;
        },
        set(newValue) {
          if (newValue === value) return;
          console.log("设置值");
          //需要做派发更新过程 这里代码没写出来
          value = newValue;
        },
      });
    }
    export function observe(value) {
      // 如果传过来的是对象或者数组 进行属性劫持
      if (
        Object.prototype.toString.call(value) === "[object Object]" ||
        Array.isArray(value)
      ) {
        return new Observer(value);
      }
    }
    ```

    [响应式数据原理详解](https://juejin.cn/post/6935344605424517128)
    <br>

1. ### `Vue nextTick` 原理

    `nextTick` 中的回调是在下次 `DOM` 更新循环结束之后执行的延迟回调。在修改数据之后立即使用这个方法，获取更新后的 `DOM`。主要思路就是采用微任务优先的方式调用异步方法去执行 `nextTick` 包装的方法

    相关代码如下

    ```js
    let callbacks = [];
    let pending = false;
    function flushCallbacks() {
      pending = false; //把标志还原为false
      // 依次执行回调
      for (let i = 0; i < callbacks.length; i++) {
        callbacks[i]();
      }
    }
    let timerFunc; //定义异步方法  采用优雅降级
    if (typeof Promise !== "undefined") {
      // 如果支持promise
      const p = Promise.resolve();
      timerFunc = () => {
        p.then(flushCallbacks);
      };
    } else if (typeof MutationObserver !== "undefined") {
      // MutationObserver 主要是监听dom变化 也是一个异步方法
      let counter = 1;
      const observer = new MutationObserver(flushCallbacks);
      const textNode = document.createTextNode(String(counter));
      observer.observe(textNode, {
        characterData: true,
      });
      timerFunc = () => {
        counter = (counter + 1) % 2;
        textNode.data = String(counter);
      };
    } else if (typeof setImmediate !== "undefined") {
      // 如果前面都不支持 判断setImmediate
      timerFunc = () => {
        setImmediate(flushCallbacks);
      };
    } else {
      // 最后降级采用setTimeout
      timerFunc = () => {
        setTimeout(flushCallbacks, 0);
      };
    }

    export function nextTick(cb) {
      // 除了渲染watcher  还有用户自己手动调用的nextTick 一起被收集到数组
      callbacks.push(cb);
      if (!pending) {
        // 如果多次调用nextTick  只会执行一次异步 等异步队列清空之后再把标志变为false
        pending = true;
        timerFunc();
      }
    }
    ```

    [nextTick 原理详解](https://juejin.cn/post/6939704519668432910#heading-4)
    <br>

1. ### `Vue diff` 原理

    ![diff 算法图解](../../images/Interview/InterviewExperience/2021-09-06/diff算法.awebp)
    [diff 算法详解](https://juejin.cn/post/6953433215218483236)
    <br>

1. ### 路由原理 `history` 和 `hash` 两种路由方式的特点

    - `hash` 模式

      1. `location.hash` 的值实际就是 `URL` 中 `#` 后面的东西，它的特点在于：`hash` 虽然出现 `URL` 中，但不会被包含在 `HTTP` 请求中，对后端完全没有影响，因此改变 `hash` 不会重新加载页面。
      2. 可以为 `hash` 的改变添加监听事件
      `window.addEventListener("hashchange", funcRef, false);`
      每一次改变 `hash(window.location.hash)`，都会在浏览器的访问历史中增加一个记录，利用 `hash` 的以上特点，就可以来实现前端路由“更新视图但不重新请求页面”的功能了

      ***特点：兼容性好但是不美观***

    - `history` 模式

      利用了 `HTML5 History Interface` 中新增的 `pushState()` 和 `replaceState()` 方法。

      这两个方法应用于浏览器的历史记录站，在当前已有的 `back、forward、go` 的基础之上，它们提供了对历史记录进行修改的功能。这两个方法有个共同的特点：当调用他们修改浏览器历史记录栈后，虽然当前 `URL` 改变了，但浏览器不会刷新页面，这就为单页应用前端路由“更新视图但不重新请求页面”提供了基础。

      ***特点：虽然美观，但是刷新会出现 404 需要后端进行配置***

## 中等

1. ### `Webpack` 有哪些优化手段

    [带你深度解锁 Webpack 系列(优化篇)](https://juejin.cn/post/6844904093463347208)
    <br>

1. ### `CSS` 怎么开启硬件加速(`GPU` 加速)

    浏览器在处理下面的 `css` 的时候，会使用 `GPU` 渲染
    - `transform`（当 `3D` 变换的样式出现时会使用 `GPU` 加速）
    - `opacity`
    - `filter`
    - `will-change`

    <br>

    例如：
    采用 `transform: translateZ(0)`
    采用 `transform: translate3d(0, 0, 0)`
    使用 `CSS` 的 `will-change` 属性。`will-change` 可以设置为 `opacity、transform、top、left、bottom、right`。
    <br>

    >注意！层爆炸，由于某些原因可能导致产生大量不在预期内的合成层，虽然有浏览器的层压缩机制，但是也有很多无法进行压缩的情况，这就可能出现层爆炸的现象（简单理解就是，很多不需要提升为合成层的元素因为某些不当操作成为了合成层）。解决层爆炸的问题，最佳方案是打破 `overlap` 的条件，也就是说让其他元素不要和合成层元素重叠。简单直接的方式：使用 `3D` 硬件加速提升动画性能时，最好给元素增加一个 `z-index` 属性，人为干扰合成的排序，可以有效减少创建不必要的合成层，提升渲染性能，移动端优化效果尤为明显。
    <br>

1. ### 常用设计模式有哪些并举例使用场景

    - 工厂模式 - 传入参数即可创建实例

      虚拟 DOM 根据参数的不同返回基础标签的 Vnode 和组件 Vnode

    - 单例模式 - 整个程序有且仅有一个实例

      vuex 和 vue-router 的插件注册方法 install 判断如果系统存在实例就直接返回掉

    - 发布-订阅模式 (vue 事件机制)

    - 观察者模式 (响应式数据原理)

    - 装饰模式: (@装饰器的用法)

    - 策略模式 策略模式指对象有某个行为,但是在不同的场景中,该行为有不同的实现方案-比如选项的合并策略

    ...其他模式欢迎补充
    <br>

1. ### 浏览器缓存策略是怎样的（强缓存 协商缓存）具体是什么过程？

    [前端浏览器缓存知识梳理](https://juejin.cn/post/6947936223126093861)
    <br>

1. ### `HTTPS` 加密过程是怎样的

    [HTTPS加密](https://juejin.cn/post/6844904150115827725)
    <br>

1. ### `flex:1` 是哪些属性组成的

    `flex` 实际上是 `flex-grow`、`flex-shrink` 和 `flex-basis` 三个属性的缩写。

    - `flex-grow`：定义项目的的放大比例；

      默认为 0，即使存在剩余空间，也不会放大；
      所有项目的 `flex-grow` 为 `1`：等分剩余空间（自动放大占位）；
      `flex-grow` 为 `n` 的项目，占据的空间（放大的比例）是 `flex-grow` 为 `1` 的 `n` 倍。

    - `flex-shrink`：定义项目的缩小比例；

      默认为 `1`，即 如果空间不足，该项目将缩小；
      所有项目的 `flex-shrink` 为 `1`：当空间不足时，缩小的比例相同；
      `flex-shrink` 为 `0`：空间不足时，该项目不会缩小；
      `flex-shrink` 为 `n` 的项目，空间不足时缩小的比例是 `flex-shrink` 为 `1` 的 `n` 倍。

    - `flex-basis`： 定义在分配多余空间之前，项目占据的主轴空间（`main size`），浏览器根据此属性计算主轴是否有多余空间

      默认值为 `auto`，即项目原本大小；
      设置后项目将占据固定空间。
    <br>

1. ### 304 是什么意思 一般什么场景出现 ，命中强缓存返回什么状态码

    **协商缓存命中返回 `304`**

    这种方式使用到了 `headers` 请求头里的两个字段，`Last-Modified & If-Modified-Since`。服务器通过响应头 `Last-Modified` 告知浏览器，资源最后被修改的时间：

    `Last-Modified: Thu, 20 Jun 2019 15:58:05 GMT`

    当再次请求该资源时，浏览器需要再次向服务器确认，资源是否过期，其中的凭证就是请求头 `If-Modified-Since` 字段，值为上次请求中响应头 `Last-Modified` 字段的值：

    `If-Modified-Since: Thu, 20 Jun 2019 15:58:05 GMT`

    浏览器在发送请求的时候服务器会检查请求头 `request header` 里面的 `If-modified-Since`，如果最后修改时间相同则返回 `304`，否则给返回头(`response header`)添加 `last-Modified` 并且返回数据(`response body`)。

    另外，浏览器在发送请求的时候服务器会检查请求头(`request header`)里面的 `if-none-match` 的值与当前文件的内容通过 `hash` 算法（例如 `nodejs: cryto.createHash('sha1')`）生成的内容摘要字符对比，相同则直接返回 `304`，否则给返回头(`response header`)添加 `etag` 属性为当前的内容摘要字符，并且返回内容。

    综上总结为：
    - 请求头 `last-modified` 的日期与响应头的 `last-modified` 一致
    - 请求头 `if-none-match` 的 `hash` 与响应头的 `etag` 一致

    这两种情况会返回 `Status Code: 304`
    <br>

    **强缓存命中返回 200 200（from cache）**
    <br>

1. ### 手写 `Vue.extend` 实现

    ```js
    //  src/global-api/initExtend.js
    import { mergeOptions } from "../util/index";
    export default function initExtend(Vue) {
      let cid = 0; //组件的唯一标识
      // 创建子类继承Vue父类 便于属性扩展
      Vue.extend = function (extendOptions) {
        // 创建子类的构造函数 并且调用初始化方法
        const Sub = function VueComponent(options) {
          this._init(options); //调用Vue初始化方法
        };
        Sub.cid = cid++;
        Sub.prototype = Object.create(this.prototype); // 子类原型指向父类
        Sub.prototype.constructor = Sub; //constructor指向自己
        Sub.options = mergeOptions(this.options, extendOptions); //合并自己的options和父类的options
        return Sub;
      };
    }
    ```

    <br>

1. ### `vue-router` 中路由方法 `pushState` 和 `replaceState` 能否触发 `popSate` 事件

    不能

    `pushState` 和 `replaceState` 是 `HTML5` 新接口，可以改变网址(存在跨域限制)而不刷新页面，这个强大的特性后来用到了单页面应用如：`vue-router，react-router-dom` 中。

    注意：仅改变网址，网页不会真的跳转，也不会获取到新的内容，本质上网页还停留在原页面

    ```js
    window.history.pushState(state, title, targetURL);
    @状态对象：传给目标路由的信息,可为空
    @页面标题：目前所有浏览器都不支持,填空字符串即可
    @可选 url：目标 url，不会检查 url 是否存在，且不能跨域。如不传该项，即给当前 url 添加 data

    window.history.replaceState(state, title, targetURL);
    @类似于 pushState，但是会直接替换掉当前 url，而不会在 history 中留下记录
    popstate 事件会在点击后退、前进按钮(或调用 history.back()、history.forward()、history.go()方法)时触发
    ```

    >注意：用 `history.pushState()` 或者 `history.replaceState()` 不会触发 `popstate` 事件
    <br>

1. ### `Tree shaking` 是什么，原理是什么

    `Tree shaking` 是一种通过清除多余代码方式来优化项目打包体积的技术，专业术语叫 `Dead code elimination`

    `tree shaking` 的原理是什么?

      `ES6 Module` 引入进行静态分析，故而编译的时候正确判断到底加载了那些模块

      静态分析程序流，判断那些模块和变量未被使用或者引用，进而删除对应代码
      <br>

    `Common.js` 和 `es6` 中模块引入的区别？
    `CommonJS` 是一种模块规范，最初被应用于 `Nodejs`，成为 `Nodejs` 的模块规范。运行在浏览器端的 `JavaScript` 由于也缺少类似的规范，在 `ES6` 出来之前，前端也实现了一套相同的模块规范 (例如: `AMD`)，用来对前端模块进行管理。自 `ES6` 起，引入了一套新的 `ES6 Module` 规范，在语言标准的层面上实现了模块功能，而且实现得相当简单，有望成为浏览器和服务器通用的模块解决方案。但目前浏览器对 `ES6 Module` 兼容还不太好，我们平时在 `Webpack` 中使用的 `export` 和 `impor`t，会经过 `Babel` 转换为 `CommonJS` 规范。在使用上的差别主要有：

    1. `CommonJS` 模块输出的是一个值的拷贝，`ES6` 模块输出的是值的引用。

    2. `CommonJS` 模块是运行时加载，`ES6` 模块是编译时输出接口（静态编译）。

    3. `CommonJs` 是单个值导出，`ES6 Module` 可以导出多个

    4. `CommonJs` 是动态语法可以写在判断里，`ES6 Module` 静态语法只能写在顶层

    5. `CommonJs` 的 `this` 是当前模块，`ES6 Module` 的 `this` 是 `undefined`
    <br>

1. ### `babel` 是什么？原理了解吗？

    `Babel` 是一个 `JavaScript` 编译器。他把最新版的 `Javascript` 编译成当下可以执行的版本，简言之，利用 `babel` 就可以让我们在当前的项目中随意的使用这些最新的 `es6`，甚至 `es7` 的语法。

    `Babel` 的三个主要处理步骤分别是： 解析（`parse`），转换（`transform`），生成（`generate`）。

    - 解析
      将代码解析成抽象语法树（`AST`），每个 `js` 引擎（比如 `Chrome` 浏览器中的 `V8` 引擎）都有自己的 `AST` 解析器，而 `Babel` 是通过 `Babylon` 实现的。在解析过程中有两个阶段：词法分析和语法分析，词法分析阶段把字符串形式的代码转换为令牌（`tokens`）流，令牌类似于 `AST` 中节点；而语法分析阶段则会把一个令牌流转换成 `AST` 的形式，同时这个阶段会把令牌中的信息转换成 `AST` 的表述结构。
    - 转换
      在这个阶段，`Babel` 接受得到 `AST` 并通过 `babel-traverse` 对其进行深度优先遍历，在此过程中对节点进行添加、更新及移除操作。这部分也是 `Babel` 插件介入工作的部分。
    - 生成
      将经过转换的 `AST` 通过 `babel-generator` 再转换成 `js` 代码，过程就是深度优先遍历整个 `AST`，然后构建可以表示转换后代码的字符串。
    [[实践系列]Babel 原理](https://juejin.cn/post/6844903760603398151)
    <br>

1. ### 原型链判断

    请写出下面的答案

    ```js
    Object.prototype.__proto__;                // null
    Function.prototype.__proto__;              // Object.prototype
    Object.__proto__;                          // Function.prototype
    Object instanceof Function;                // true
    Function instanceof Object;                // true
    Function.prototype === Function.__proto__; // true
    ```

    <br>

1. ### `RAF` 和 `RIC` 是什么？

    `requestAnimationFrame`：告诉浏览器在下次重绘之前执行传入的回调函数(通常是操纵 `dom`，更新动画的函数)；由于是每帧执行一次，那结果就是每秒的执行次数与浏览器屏幕刷新次数一样，通常是每秒 `60` 次。

    `requestIdleCallback`：会在浏览器空闲时间执行回调，也就是允许开发人员在主事件循环中执行低优先级任务，而不影响一些延迟关键事件。如果有多个回调，会按照先进先出原则执行，但是当传入了 `timeout`，为了避免超时，有可能会打乱这个顺序。

    [requestIdleCallback 和 requestAnimationFrame 详解](https://juejin.cn/post/6844903848981577735)
    <br>

## 困难

1. ### `ES6` 的 `let` 实现原理

    原始 `ES6` 代码

    ```js
    var funcs = [];
    for (let i = 0; i < 10; i++) {
      funcs[i] = function () {
        console.log(i);
      };
    }
    funcs[0](); // 0
    ```

    `babel` 编译之后的 `ES5` 代码（`polyfill`）

    ```js
    var funcs = [];

    var _loop = function _loop(i) {
      funcs[i] = function () {
        console.log(i);
      };
    };

    for (var i = 0; i < 10; i++) {
      _loop(i);
    }
    funcs[0](); // 0
    ```

    其实我们根据 `babel` 编译之后的结果可以看得出来 `let` 是借助闭包和函数作用域来实现块级作用域的效果的，在不同的情况下 `let` 的编译结果是不一样的
    <br>

1. ### 如何设计实现一个渲染引擎？

    [你不知道的浏览器页面渲染机制](https://juejin.cn/post/6844903815758479374)
    <br>

1. ### `require` 具体实现原理是什么？

    `require` 基本原理
    ![require基本原理](../../images/Interview/InterviewExperience/2021-09-06/require基本原理.awebp)

    `require` 查找路径
    ![require查找路径](../../images/Interview/InterviewExperience/2021-09-06/require查找路径.awebp)

    `require` 和 `module.exports` 干的事情并不复杂，我们先假设有一个全局对象 `{}`，初始情况下是空的，当你 `require` 某个文件时，就将这个文件拿出来执行，如果这个文件里面存在 `module.exports`，当运行到这行代码时将 `module.exports` 的值加入这个对象，键为对应的文件名，最终这个对象就长这样：

    ```js
    {
      "a.js": "hello world",
      "b.js": function add() {},
      "c.js": 2,
      "d.js": { num: 2 }
    }
    ```

    当你再次 `require` 某个文件时，如果这个对象里面有对应的值，就直接返回给你，如果没有就重复前面的步骤，执行目标文件，然后将它的 `module.exports` 加入这个全局对象，并返回给调用者。这个全局对象其实就是我们经常听说的缓存。所以 `require` 和 `module.exports` 并没有什么黑魔法，就只是运行并获取目标文件的值，然后加入缓存，用的时候拿出来用就行。
    <br>

1. ### 前端性能定位以及优化指标

    如何进行性能定位和分析
    - 前端性能监控-埋点以及 `window.performance` 相关的 `api` 去回答
    - 性能分析工具 `Performance` 和 `Lighthouse`
    - 性能指标 `LCP` `FCP` `FID` `CLS` 等去着手

    [5 分钟撸一个前端性能监控工具](https://juejin.cn/post/6844903662020460552)

    [前端性能优化之谈谈通用性能指标及上报策略](https://juejin.cn/post/6844904150057091086)

    [前端性能优化指标 + 检测工具](https://juejin.cn/post/6974565176427151397)
