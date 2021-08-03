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
      var els = document.getElementsByTagName("*");

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

    - `meta` 元素定义的元数据的类型包括以下几种：
      - 如果设置了 `name` 属性，`meta` 元素提供的是文档级别（`document-level`）的元数据，应用于整个页面。
      - 如果设置了 `http-equiv` 属性，`meta` 元素则是编译指令，提供的信息与类似命名的 `HTTP` 头部相同。
      - 如果设置了 `charset` 属性，`meta` 元素是一个字符集声明，告诉文档使用哪种字符编码。
      - 如果设置了 `itemprop` 属性，`meta` 元素提供用户定义的元数据。
    - `meta` 属性
      - `name`：`name` 和 `content` 属性可以一起使用，以 `key - value` 对的方式给文档提供元数据，其中 `name` 作为元数据的名称，`content` 作为元数据的值。
      - `charset`：声明文档的字符编码。如果使用，其值必须是`<meta charset="utf-8">`
      - `content`：包含 `http-equiv` 或 `name` 属性的值
      - `http-equiv`：属性定义了一个编译指示指令。这个属性叫做 `http-equiv(alent)` 是因为所有允许的值都是特定 `HTTP` 头部的名称
        - `content-security-policy`：允许定义当前页的内容策略。内容策略主要指定允许的服务器源和脚本端点，这有助于防止跨站点脚本攻击。
        - `content-type`：如果使用，其值必须是 `<meta http-equiv="content-type" content="text/html;charset=utf-8">`。注意：该属性只能用于 `MIME type` 为 `text/html` 的文档，不能用于 `MIME` 类型为 `XML` 的文档。
        - `default-style`：设置默认 `CSS` 样式表组的名称。
        - `x-ua-compatible`：如果指定，则 `content` 属性必须具有值 "`IE=edge`"。用户代理必须忽略此指示。
        - `refresh` 这个属性指定:
          - 如果 `content` 只包含一个正整数，则为重新载入页面的时间间隔(秒)；
          - 如果 `content` 包含一个正整数，并且后面跟着字符串 “`;url=`” 和一个合法的 `URL`，则是重定向到指定链接的时间间隔(秒)

          ```html
          <!-- Redirect page after 3 seconds -->
          <meta http-equiv="refresh" content="3;url=https://www.mozilla.org">
          ```

    - 常见的 `meta` 标签：
      - `<meta charset="UTF-8" >`：描述HTML文档的编码类型
      - `<meta name="keywords" content="关键词" />`：页面关键词
      - `<meta name="description" content="页面描述内容" />`：页面描述
      - `<meta name="theme-color" content="#ffffff">`：表示当前页面的建议颜色，在自定义当前页面从或页面周围的用户界面的显示时，用户代理应当使用此颜色
      - `<meta http-equiv="refresh" content="3;url=https://www.mozilla.org">`：页面重定向和刷新
      - `<meta name="viewport" content="width=device-width, user-scalable=no, initial-scale=1.0, maximum-scale=1.0, minimum-scale=1.0">`：适配移动端，可以控制视口的大小和比例
        - `width`：宽度(数值/`device-width`)
        - `height`：高度(数值/`device-height`)
        - `initial-scale`：初始缩放比例
        - `maximum-scale`：最大缩放比例
        - `minimum-scale`：最小缩放比例
        - `user-scalable`：是否允许用户缩放(`yes/no`）
        - `viewport-fit`：`auto | contain | cover`
      - `<meta name="robots" content="index,follow" />`
        - `all`：文件将被检索，且页面上的链接可以被查询；
        - `none`：文件将不被检索，且页面上的链接不可以被查询；
        - `index`：文件将被检索；
        - `follow`：页面上的链接可以被查询；
        - `noindex`：文件将不被检索；
        - `nofollow`：页面上的链接不可以被查询。

1. ℹ️`HTML5` 有哪些更新

    - 语义化标签
      - `header`：定义文档的页眉（头部）
      - `nav`：定义导航链接的部分
      - `footer`：定义文档或节的页脚（底部）
      - `article`：定义文章内容
      - `section`：定义文档中的节（`section`、区段）
      - `aside`：定义其所处内容之外的内容（侧边）
    - 媒体标签
      - `audio`音频
      `<audio src="" controls autoplay loop="true"></audio>`
      属性：
        - `controls` 控制面板
        - `autoplay` 自动播放
        - `loop="true"` 循环播放

      - `video` 视频
      `<video src="" poster="imgs/aa.jpg" controls></video>`
      属性：
        - `poster`：指定视频还没有完全下载完毕，或者用户还没有点击播放前显示的封面。默认显示当前视频文件的第一针画面，当然通过 `poster` 也可以自己指定。
        - `controls` 控制面板
        - `width`
        - `height`
      - `source` 标签
      因为浏览器对视频格式支持程度不一样，为了能够兼容不同的浏览器，可以通过 `source` 来指定视频源。

        ```html
        <video>
            <source src='aa.flv' type='video/flv'></source>
            <source src='aa.mp4' type='video/mp4'></source>
        </video>
        ```

    - 表单
      - 表单类型：
        - `email`：能够验证当前输入的邮箱地址是否合法
        - `url`：验证 `URL`
        - `number`：只能输入数字，其他输入不了，而且自带上下增大减小箭头，`max` 属性可以设置为最大值，`min` 可以设置为最小值，`value` 为默认值。
        - `search`：输入框后面会给提供一个小叉，可以删除输入的内容，更加人性化。
        - `range`：可以提供给一个范围，其中可以设置 `max` 和 `min` 以及 `value`，其中 `value` 属性可以设置为默认值
        - `color`：提供了一个颜色拾取器
        - `time`：时分秒
        - `data`：日期选择年月日
        - `datatime`：时间和日期(目前只有 `Safari` 支持)
        - `datatime-local`：日期时间控件
        - `week`：周控件
        - `month`：月控件
      - 表单属性：
        - `placeholder`：提示信息
        - `autofocus`：自动获取焦点
        - `autocomplete="on"` 或者 `autocomplete="off"` 使用这个属性需要有两个前提：
          - 表单必须提交过
          - 必须有 `name` 属性。
        - `required`：要求输入框不能为空，必须有值才能够提交。
        - `pattern=""`：里面写入想要的正则模式，例如手机号 `patte="^(+86)?\d{10}$"`
        - `multiple`：可以选择多个文件或者多个邮箱
        - `form="formID"`

      - 表单事件：
        - `oninput`：每当input里的输入框内容发生变化都会触发此事件。
        - `oninvalid`：当验证不通过时触发此事件。

    - 进度条、度量器
      - `<progress>`：用来表示任务的进度（`IE、Safari` 不支持），`max` 用来表示任务的进度，`value` 表示已完成多少
      - `<meter>`：用来显示显示已知范围的标量值或者分数值（`IE、Safari` 不支持）
        - `high/low`：规定被视作高/低的**范围**
        - `max/min`：规定最大/小**值**
        - `value`：规定当前度量值（`min < value < max`）
        - `optimum`：指示最优/最佳取值。它必须在正确的值域内（`min < value < max`）。当使用了low和high属性时，它指明哪一个取值范围是更好的。例如 `min < optimum < low`，那么 `lower` 区间就被认为是更佳的取值范围。
        - `form`：与对应的 `form` 元素关联。
        - 设置规则：`min < low < high < max`
    - `DOM` 查询操作
      - `document.querySelector()`
      - `document.querySelectorAll()`
    - `Web` 存储
      - `localStorage`
      - `sessionStorage`
    - 其它
      - 拖放：拖放是一种常见的特性，即抓取对象以后拖到另一个位置。设置元素可拖放：`<img draggable="true" />`
      - `canvas`：使用 `JavaScript` 在网页上绘制图像。画布是一个矩形区域，可以控制其每一像素。`canvas` 拥有多种绘制路径、矩形、圆形、字符以及添加图像的方法。
      `<canvas id="myCanvas" width="200" height="100"></canvas>`
      - `SVG`：可伸缩矢量图形，用于定义用于网络的基于矢量的图形，使用 `XML` 格式定义图形，图像在放大或改变尺寸的情况下其图形质量不会有损失，它是万维网联盟的标准
      - 地理定位：`Geolocation`（地理定位）用于定位用户的位置。

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
