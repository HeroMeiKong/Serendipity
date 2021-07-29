# HTML 面试题目汇总★

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

1. ★`addEventListener()`
    `EventTarget.addEventListener()` 方法将指定的监听器注册到 `EventTarget`（一个 `DOM` 接口，由可以接收事件、并且可以创建侦听器的对象实现） 上，当该对象触发指定的事件时，指定的回调函数就会被执行。事件目标可以是一个文档上的元素 `Element,Document` 和 `Window` 或者任何其他支持事件的对象 (比如 `XMLHttpRequest`)。

    `addEventListener()` 的工作原理是将实现 `EventListener` 的函数或对象添加到调用它的 `EventTarget` 上的指定事件类型的事件侦听器列表中。

    - 语法

      ```javascript
      target.addEventListener(type, listener, options);
      target.addEventListener(type, listener, useCapture);
      target.addEventListener(type, listener, useCapture, wantsUntrusted); // Gecko/Mozilla only
      ```

    - 参数
      - `type`：表示**监听事件类型**的**字符串**
      - `listener`：当所监听的事件类型触发时，会接收到一个事件通知（实现了 `Event` 接口的对象）对象。**`listener` 必须是一个实现了 `EventListener` 接口的对象，或者是一个函数。**
      - `options`（可选）：一个指定有关 `listener` 属性的可选参数对象。可用的选项如下：
        - `capture`：`Boolean`，表示 `listener` 会在该类型的事件捕获阶段传播到该 `EventTarget` 时触发。
        - `once`：`Boolean`，表示 `listener` 在添加之后最多只调用一次。如果是 `true`，`listener` 会在其被调用之后自动移除。
        - `passive`：`Boolean`，设置为 `true` 时，表示 `listener` 永远不会调用 `preventDefault()`。如果 `listener` 仍然调用了这个函数，客户端将会忽略它并抛出一个控制台警告。
        - `signal`：`AbortSignal`，该 `AbortSignal` 的 `abort()` 方法被调用时，监听器会被移除。
        - `mozSystemGroup`（不推荐）：只能在 `XBL` 或者是 `Firefox' chrome` 使用，这是个 `Boolean`，表示 `listener` 被添加到 `system group`。
      - `useCapture`（可选）：事件传播模式决定了元素以哪种顺序接收事件
        - `false`（默认）：冒泡事件
        - `true`：捕获事件，沿着 `DOM` 树向上冒泡的事件，不会触发 `listener`
        >注意: 对于附加到事件目标的事件监听器，事件处于目标阶段，而不是捕获和冒泡阶段。现在一定是先捕获再冒泡（新版浏览器对事件做了排序处理）。在目标阶段的事件会触发该元素（即事件目标）上的所有监听器，而不在乎这个监听器在注册时 `useCapture` 的参数值是 `true` 或者 `false`。
      - `wantsUntrusted`（不推荐）：如果为 `true`，则事件处理程序会接收网页自定义的事件。此参数只适用于 `Gecko`（`chrome` 的默认值为 `true`，其他常规网页的默认值为 `false`），主要用于附加组件的代码和浏览器本身。

    - `option` 支持的安全检测

      ```javascript
      let passiveSupported = false;

      try {
        const options = {
          get passive() { // This function will be called when the browser
                          //   attempts to access the passive property.
            passiveSupported = true;
            return false;
          }
        };

        window.addEventListener("test", null, options); // 设置空事件处理器
        window.removeEventListener("test", null, options); // 清除空事件处理器
      } catch(err) {
        passiveSupported = false;
      }

      someElement.addEventListener("mouseup", handleMouseUp, passiveSupported ? { passive: true } : false);
      ```

    - 为什么要使用 `addEventListener`?
      - 优点
        - 它允许给一个事件注册多个监听器。
        - 它提供了一种更精细的手段控制 `listener` 的触发阶段。（即可以选择捕获或者冒泡）。
        - 它对任何 `DOM` 元素都是有效的，而不仅仅只对 `HTML` 元素有效。
        - `onclick` 是属性，只执行一次，易重复被覆盖，唯一
        - `addEventListener` 是队列模式，先绑定就先触发，非唯一

    - 在事件分派时添加事件处理器
      当一个 `EventListener` 在 `EventTarget` 正在处理事件的时候被注册到 `EventTarget` 上，它不会被立即触发，但可能在事件流后面的事件触发阶段被触发，例如可能在捕获阶段添加，然后在冒泡阶段被触发。

    - 多个相同的事件处理器
      同一个 `EventTarget` 注册了多个相同的 `EventListener`，那么重复的实例会被抛弃。所以这么做不会使得 `EventListener` 被调用两次，也不需要用 `removeEventListener` 手动清除多余的 `EventListener`，因为重复的都被自动抛弃了，前提是 `options` 中的 `capture` 的参数值一致，如果 `capture` 的值不一致，此时就算重复定义，也不会被抛弃掉。

    - 内存问题

      ```javascript
      var i;
      var els = document.getElementsByTagName('*');

      // 不要这样写匿名函数，不然无法调用 Element.removeEventListener
      for(i=0 ; i<els.length ; i++){
        els[i].addEventListener("click", function(e){/*do something*/}, false});
      }

      // 建议这样写
      function processEvent(e){
        /*do something*/
      }

      for(i=0 ; i<els.length ; i++){
        els[i].addEventListener("click", processEvent, false});
      }
      ```

1. ★`src` 和 `href` 的区别

    `src` 和 `href` 都是用来引用外部的资源
    - 常见例子：
      - 引用 `css` 文件时：`href="cssfile.css"`
      - 引用 `js` 文件时：`src="myscript.js"`
      - 引用图片：`src="mypic.jpg"`
      - 网站链接：`href="http://www.webpage.com"`

    - 区别（`src` 用于替代这个元素； `href` 用于建立这个标签与外部资源之间的关系）
      - `src`：表示对资源的引用，它指向的内容会嵌入到当前标签所在的位置。`src` 会将其指向的资源下载并应⽤到⽂档内，如请求 `js` 脚本。当浏览器解析到该元素时，会暂停其他资源的下载和处理，直到将该资源加载、编译、执⾏完毕，所以⼀般 `js` 脚本会放在页面底部。
      例如当我们写：
      `<script src="script.js"></script>`
      当浏览器解析到这句代码时，页面的加载和解析都会暂停直到浏览器拿到并执行完这个 `js` 文件。这就像是把 `js` 文件里的内容全部注入到这个 `script` 标签中，类似于 `img`，`img` 标签是一个空标签，它的内容就是由 `src` 这个属性定义，浏览器会暂停加载直到这个图片加载完成。这也是为什么要将 `js` 文件的加载放在 `body` 最后的原因（在 `</body>` 前面）。

      - `href`：表示超文本引用，它指向一些网络资源，建立和当前元素或本文档的链接关系。当浏览器识别到它他指向的⽂件时，就会并⾏下载资源，不会停⽌对当前⽂档的处理。常用在 `a、link` 等标签上。

      例如当我们写：
      `<link href="style.css" rel="stylesheet" />`
      浏览器知道这是个样式表文件，`html` 的解析和渲染不会暂停，`css` 文件的加载是同时进行的，这不同于在 `style` 标签里面的内置样式，用 `@import` 添加的样式是在页面载入之后再加载，这可能会导致页面因重新渲染而闪烁。所以我们建议使用 `link` 而不是 `@import`。

1. 常⽤的 `meta` 标签有哪些？

1. s

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
