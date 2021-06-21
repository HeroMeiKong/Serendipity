
# ★ h5bp Front-end Developer Interview Answer

## 目录

- 常见问题
- `HTML` 相关问题
- `CSS` 相关问题
- `JS` 相关问题
- 测试相关问题
- 效能相关问题
- 网络相关问题
- 代码相关问题
- 趣味问题

### 常见问题

- 你在昨天/本周学到了什么？
- 编写代码的哪些方面能够使你兴奋或感兴趣？
- 你最近遇到过什么技术挑战？你是如何解决的？
- 在制作一个网页应用或网站的过程中，你是如何考虑其 `UI`、安全性、高性能、`SEO`、可维护性-以及技术因素的？
- 请谈谈你喜欢的开发环境。
- 你最熟悉哪一套版本控制系统？
- 你能描述当你制作一个网页的工作流程吗？
- 假若你有 5 个不同的样式文件 (`stylesheets`), 整合进网站的最好方式是?
- 你能描述渐进增强 (`progressive enhancement`) 和优雅降级 (`graceful degradation`) 之间的不同吗?
- 你如何对网站的文件和资源进行优化？
- 浏览器同一时间可以从一个域名下载多少资源？有什么例外吗？
- 请说出三种减少页面加载时间的方法。(加载时间指感知的时间或者实际加载时间)
- 如果你参与到一个项目中，发现他们使用 `Tab` 来缩进代码，但是你喜欢空格，你会怎么做？
- 请写一个简单的幻灯效果页面。
- 如果今年你打算熟练掌握一项新技术，那会是什么？
- 请谈谈你对网页标准和标准制定机构重要性的理解。
- 什么是 `FOUC` (无样式内容闪烁)？你如何来避免 `FOUC`？
- 请解释什么是 `ARIA` 和屏幕阅读器 (`screenreaders`)，以及如何使网站实现无障碍访问 (`accessible`)。
- 请解释 `CSS` 动画和 `JavaScript` 动画的优缺点。
- 什么是跨域资源共享 (`CORS`)？它用于解决什么问题？

### HTML 相关问题

1. #### `DOCTYPE`(文档类型) 的作用是什么？

    DOCTYPE 是 `Document Type Declaration (文档类型声明)` 的缩写。它是 `HTML` 中用来区分标准模式和怪异模式的声明，用来告知浏览器使用标准模式渲染页面。

    需在页面开始处添加 `<!DOCTYPE html>`

1. #### 浏览器标准模式 (`standards mode`) 、几乎标准模式（`almost standards mode`）和怪异模式 (`quirks mode`) 之间的区别是什么？

1. #### `HTML` 和 `XHTML` 有什么区别？

1. #### 如果页面使用 `application/xhtml+xml` 会有什么问题吗？

1. #### 如果网页内容需要支持多语言，你会怎么做？

    **语言切换谁来做？**

    - 通过 `URL` 切换（后端控制，生成多版本 `HTML`）
    - 通过本地设置切换（前端控制）

    **语言切换内容？**

    - 文案长短
    - 图片
    - 接口数据
    - 特定格式（时间、SDK等）

    **实现方式：**

    1. 当客户端向服务器发送 `HTTP` 请求时，通常会发送有关语言首选项的信息，比如使用 `Accept-Language` 请求头。如果替换语言存在，服务器可以利用该信息返回与之相匹配的 `HTML` 文档。返回的 `HTML` 文档还应在 `<html>` 标签中声明 `lang` 属性，比如 `<html lang="en">...</html>`

        在后台中，`HTML` 将包含 `i18n` 占位符和待以替换的内容，这些按照不同语言，以 `YML` 或 `JSON` 格式存储。然后，服务器将动态生成指定语言内容的 `HTML` 页面。整个过程通常需要借助后台框架实现。

        `accept-language: zh-CN,zh;q=0.9`: 表示语言为中文，权重最大，首选

    2. 根据不同 `URL` 返回不同的静态 `HTML` 文件
    3. 将语言的切换交由前端控制，将语言设置项存储在本地 `LocalStorage`，根据不同 `JSON` 渲染页面

1. #### 在设计和开发多语言网站时，有哪些问题你必须要考虑？

    - 在 `HTML` 中使用 `lang` 属性。
    - 引导用户切换到自己的母语——让用户能够轻松地切换到自己的国家或语言。
    - 尽量减少有特殊文案的图片
    - 限制词语或句子的长度
    - 注意颜色的使用——颜色在不同的语言和文化中，意义和感受是不同的。设计时应该使用恰当的颜色。
    - 日期和货币的格式化
    - 不要使用连接的翻译字符串——不要做类似这样的事情，比如“今天的日期是”+具体日期。这样做可能会打乱其他语言的语序。替代方案是，为每种语言编写带变量替换的模版字符串。请看下面两个分别用英语和中文表示的句子：I will travel on {% date %}和{% date %} 我会出发。可以看到，语言的语法规则不同，变量的位置是不同的。
    - 注意语言阅读的方向——在英语中，文字是从左向右阅读的；而在传统日语中，文字是从右向左阅读的。

1. #### 使用 `data-` 属性的好处是什么？

    `data-*` 全局属性，是一类被称为自定义数据属性的属性，它赋予我们在所有 `HTML` 元素上嵌入自定义数据属性的能力，并可以通过脚本在 `HTML` 与 `DOM` 表现之间进行专有数据的交换。

    1. 状态记录
    2. 容易访问（`HTMLElement.dataset`）
    3. 容易修改
    4. 方便 `CSS` 过滤

1. #### 如果把 `HTML5` 看作做一个开放平台，那它的构建模块有哪些？

    - **语义**：能够让你更恰当地描述你的内容是什么。
    - **连通性**：能够让你和服务器之间通过创新的新技术方法进行通信。
    - **离线 & 存储**：能够让网页在客户端本地存储数据以及更高效地离线运行。
    - **多媒体**：使 `video` 和 `audio` 成为了在所有 `Web` 中的一等公民。
    - **`2D/3D` 绘图 & 效果**：提供了一个更加分化范围的呈现选择。
    - **性能 & 集成**：提供了非常显著的性能优化和更有效的计算机硬件使用。
    - **设备访问 `Device Access`**：能够处理各种输入和输出设备。
    - **样式设计**: 让作者们来创作更加复杂的主题吧！

1. #### 请描述 `cookies、sessionStorage` 和 `localStorage` 的区别

1. #### 请解释 `<script>`、`<script async>` 和 `<script defer>` 的区别

    - `<script>` - `HTML` 解析中断，脚本被提取并立即执行。执行结束后，`HTML` 解析继续 -- **阻塞（同步），立即加载，立即执行**
    - `<script async>` - 脚本的提取、执行的过程与 `HTML` 解析过程并行，并尽快解析和执行。当脚本与页面上其他脚本独立时，可以使用 `async`，比如用作页面统计分析 -- **并行（异步）加载，立即执行**
    - `<script defer>` - 脚本仅提取过程与 `HTML` 解析过程并行，脚本的执行将在 `HTML` 解析完毕后进行。如果有多个含 `defer` 的脚本，脚本的执行顺序将按照在 `document` 中出现的位置，从上到下顺序执行 -- **并行（异步）加载，延迟执行**

    注意：
    - 没有 `src` 属性的脚本，`async` 和 `defer` 属性会被忽略。
    - `async & defer` 属性能够消除解析阻塞的 `Javascript`。解析阻塞的 `Javascript` 会导致浏览器必须加载并且执行脚本，之后才能继续解析
    - 布尔属性的存在意味着 `true` 值，布尔属性的缺失意味着 `false` 值
    - `defer` 在触发 `DOMContentLoaded (en-US)` 事件前执行。有 `defer` 属性的脚本会阻止 `DOMContentLoaded` 事件，直到脚本被加载并且解析完成
    - **如果缺少 `src` 属性（即内嵌脚本），该属性不应被使用，因为这种情况下它不起作用。`defer` 属性对模块脚本没有作用 —— 他们默认 `defer`**

1. #### 为什么通常推荐将 `CSS <link>` 放置在 `<head></head>` 之间，而将 `JS <script>` 放置在 `</body>` 之前？你知道有哪些例外吗？

    把 `<link>` 标签放在 `<head></head>` 之间是规范要求的内容。主要是这种做法可以让页面逐步呈现，提高了用户体验。如果将样式表放在文档底部附近，会使许多浏览器（包括 `Internet Explorer`）不能逐步呈现页面，一些浏览器会阻止渲染，以避免在页面样式发生变化时，重新绘制页面中的元素。这种做法可以防止呈现给用户空白的页面或没有样式的内容。

    把 `<script>` 标签恰好放在 `</body>` 之前

    脚本在下载和执行期间会阻止 `HTML` 解析。把 `<script>` 标签放在底部，保证 `HTML` 首先完成解析，将页面尽早呈现给用户。

    例外情况是当你的脚本里包含 `document.write()` （不推荐使用）。现在推荐的做法是将 `<script defer> | <script async>` 放在 `<head></head>` 中，这样就能**并发加载** `JS & HTML`，缺点是不兼容旧版浏览器

1. #### 什么是渐进式渲染（`progressive rendering`）？

    渐进式渲染是用于提高网页性能（尤其是提高用户感知的加载速度），以尽快呈现页面的技术。

    - 图片懒加载 —— 页面上的图片不会一次性全部加载。当用户滚动页面到图片部分时， `JavaScript` 将加载并显示图像。
    - 确定显示内容的优先级（分层次渲染）—— 为了尽快将页面呈现给用户，页面只包含基本的最少量的 `CSS`、脚本和内容，然后可以使用延迟加载脚本或监听 `DOMContentLoaded/load` 事件加载其他资源和内容。
    - 异步加载 `HTML` 片段 —— 当页面通过后台渲染时，把 `HTML` 拆分，通过异步请求，分块发送给浏览器。

1. #### 为什么在 `<img>` 标签中使用 `srcset` 属性？请描述浏览器遇到该属性后的处理过程

    分辨率切换(图片内容一样，分辨率不一样) - 用法：

    ```html
    <img srcset="elva-fairy-320w.jpg 320w,
             elva-fairy-480w.jpg 480w,
             elva-fairy-800w.jpg 800w"
         sizes="(max-width: 320px) 280px,
                  (max-width: 480px) 440px,
                  800px"
         src="elva-fairy-800w.jpg" alt="Elva dressed as a fairy">

    <!-- 
    srcset: 文件名 + 空格 + 图片真实大小
    sizes: 媒体条件 + 空格 + 图片将填充的槽的宽度(px, em, rem, vw 可用，不能使用百分比，最后一个表示默认，最后匹配)
    -->
    ```

    因为需要设计响应式图片。我们可以使用两个新的属性—— `srcset` 和 `sizes` ——来提供更多额外的资源图像和提示，帮助浏览器选择正确的一个资源。

    `srcset` 定义了我们允许浏览器选择的图像集，以及每个图像的大小。

    `sizes` 定义了一组媒体条件（例如屏幕宽度）并且指明当某些媒体条件为真时，什么样的图片尺寸是最佳选择。

    所以，有了这些属性，浏览器会：

    1. 查看设备宽度
    2. 检查 `sizes` 列表中哪个媒体条件是第一个为真
    3. 查看给予该媒体查询的槽大小
    4. 加载 `srcset` 列表中引用的最接近所选的槽大小的图像

    此外，也可使用 `<picture>` , `<picture>` 中的 `<source>` 相当于 `<img>`

    美术设计(图片内容不一样) - 用法：

    ```html
    <picture>
      <source type="image/svg+xml" media="(max-width: 799px)" srcset="elva-480w-close-portrait.jpg">
      <source type="image/webp" media="(min-width: 800px)" srcset="elva-800w.jpg">
      <img src="elva-800w.jpg" alt="Chris standing up holding his daughter Elva">
    </picture>
    ```

    - `<source>` 元素包含一个 `media` 属性，这一属性包含一个媒体条件——就像第一个 `srcset` 例子，这些条件来决定哪张图片会显示——第一个条件返回真，那么就会显示这张图片。在这种情况下，如果视窗的宽度为 799px 或更少，第一个 `<source>` 元素的图片就会显示。如果视窗的宽度是 800px 或更大，就显示第二张图片。
    - `srcset` 属性包含要显示图片的路径。请注意，正如我们在 `<img>` 上面看到的那样，`<source>` 可以使用引用多个图像的 `srcset` 属性，还有 `sizes` 属性。所以你可以通过一个 `<picture>` 元素提供多个图片，不过也可以给每个图片提供多分辨率的图片。实际上，你可能不想经常做这样的事情。
    - 在任何情况下，你都必须在 `</picture>` 之前正确提供一个 `<img>` 元素以及它的 `src` 和 `alt` 属性，否则不会有图片显示。当媒体条件都不返回真的时候（你可以在这个例子中删除第二个 `<source>` 元素），它会提供图片；如果浏览器不支持 `<picture>` 元素时，它可以作为后备方案。
    - 在 `<source>` 元素中，你只可以引用在 `type` 中声明的文件类型。

    ⚠️注意：你应该仅仅当在美术设计场景下使用 `media` 属性；当你使用 `media` 时，不要在 `sizes` 属性中也提供媒体条件。

    ***为什么我们不能使用 JavaScript 来做到这一效果?***

    - 当浏览器开始加载一个页面, 它会在主解析器开始加载和解析页面的 `CSS` 和 `JavaScript` 之前先下载 (预加载) 任意的图片。这是一个非常有用的技巧，平均下来减少了页面加载时间的20%。但是, 这对响应式图片一点帮助都没有, 所以需要类似  `srcset` 的实现方法。因为你不能先加载好 `<img>` 元素后, 再用 `JavaScript` 检测可视窗口的宽度，如果觉得大小不合适，再动态地加载小的图片替换已经加载好的图片，这样的话, 原始的图像已经被加载了, 然后你又加载了小的图像, 这样的做法对于响应式图像的理念来说，是很糟糕的。

    ***还有没有其它办法？***

    - 使用 `CSS` 的 `image-set()` 方法
      - `srcset` 是在 `image-set()` 之后建模的。
      - 像 `srcset` 一样，`image-set` 的值包含逗号分隔的图像URI列表以及显示密度描述符。
      - `image-set()` 还不支持宽度描述符。该计划是完善 `image-set()` 以提供与 `srcset` 的功能奇偶校验。
      - `image-set()` 上看到的大多数示例都将其应用于背景图像，但可以将其应用于任何接受图像的 `CSS` 属性。
    - `background-image: image-set( "foo.png" 1x, "foo-2x.png" 2x);`
      - `dpi`：表示每英寸的点数。屏幕通常每英寸包含 72 或 96 个点，但打印文档的 `dpi` 通常要大得多。 1 英寸是 2.54 厘米，`1dpi ≈ 0.39 dpcm`。
      - `dpcm`：每厘米上的点数。1 英寸是 2.54 厘米, `1dpcm ≈ 2.54dpi`。
      - `dppx`：表示每个 `px` 的点数。 由于 `CSS px` 的固定比率为 `1:96`，因此 `1dppx` 相当于 `96dpi`。
      - `x`：`dppx` 的别名

1. #### 你用过哪些不同的 `HTML` 模板语言？

    没有，比如 Pug 等等。提供了用于展示数据的内容替换和过滤器的功能。大部分模版引擎都支持自定义过滤器，以展示自定义格式的内容。

1. #### HTML5 有哪些新特性？语义化标签用过哪些？

    - **语义**：能够让你更恰当地描述你的内容是什么。
    - **连通性**：能够让你和服务器之间通过创新的新技术方法进行通信。
    - **离线 & 存储**：能够让网页在客户端本地存储数据以及更高效地离线运行。
    - **多媒体**：使 `video` 和 `audio` 成为了在所有 `Web` 中的一等公民。
    - **`2D/3D` 绘图 & 效果**：提供了一个更加分化范围的呈现选择。
    - **性能 & 集成**：提供了非常显著的性能优化和更有效的计算机硬件使用。
    - **设备访问 `Device Access`**：能够处理各种输入和输出设备。
    - **样式设计**: 让作者们来创作更加复杂的主题吧！
  
    语义：
    1. HTML5 中新的区块和段落元素一览: `<section>, <article>, <nav>, <header>, <footer>, <aside>` 和 `<hgroup>`
    2. 多媒体：`<audio>` 和 `<video>`
    3. `<mark>，<figure>，<figcaption>，<data>，<time>，<output>，<progress>，<meter>，<main>`
    4. `<form>` 改进
        - 强制校验 `API` 及属性
        - `required`：指定在提交表单之前是否需要填写表单字段。
        - `minlength` 和 `maxlength` ：指定文本数据（字符串）的最小和最大长度
        - `min` 和 `max`：指定数字输入类型的最小值和最大值
        - `type`：`<input>` 元素 `type` 属性值,指定数据是数字，电子邮件地址还是其他特定的预设类型。
        - `pattern`：指定正则表达式，该正则表达式定义输入的数据需要遵循的模式。
        - `<output>` 元素
    5. `<iframe>` 改进
        - `sandbox`：该属性对呈现在 iframe 框架中的内容启用一些额外的限制条件。属性值可以为空字符串（这种情况下会启用所有限制），也可以是用空格分隔的一系列指定的字符串
        - `srcdoc`：该属性是一段 `HTML` 代码，这些代码会被渲染到 iframe 中。如果浏览器不支持 srcdoc 属性，则会渲染 src 属性表示的内容
    6. `MathML`：允许直接嵌入数学公式

### `CSS` 相关问题

1. #### `CSS` 选择器的优先级是如何计算的？

    - 浏览器通过优先级规则，判断元素展示哪些样式。优先级通过 4 个维度指标确定，我们假定以 `a、b、c、d、e` 命名，分别代表以下含义：

      - `a` 表示是否使用内联样式（`inline style`）。如果使用，`a` 为 1，否则为 0。
      - `b` 表示 `ID` 选择器的数量。
      - `c` 表示类选择器、属性选择器和伪类选择器数量之和。
      - `d` 表示标签（元素/类型）选择器和伪元素选择器之和。
      - `e` 表示通用选择器（*）、子选择器（>）和相邻同胞选择器（+），不占权重，不计算

    - 优先级从 `a` 到 `e` 依次减小，上一级权重完全影响下一级权重。比如 0，1，0，0 的优先级高于 0，0，10，10。
    - 选择器越具体，优先级越高。 #xxx 大于 .yyy
    - 同样优先级，写在后面的覆盖前面的。
    - `color: red !important;` 优先级最高。

1. #### 请问 `resetting` 和 `normalizing` `CSS` 之间的区别？你会如何选择，为什么？
  
    - 重置（`Resetting`）： 重置意味着除去所有的浏览器默认样式。对于页面所有的元素，像 `margin、padding、font-size` 这些样式全部置成一样。你将必须重新定义各种元素的样式。
    - 标准化（`Normalizing`）： 标准化没有去掉所有的默认样式，而是保留了有用的一部分，同时还纠正了一些常见错误。让所有浏览器的标签都跟标准规定的默认样式一致，各浏览器上的标签默认样式基本统一。
    - 标准化（`Normalizing`）的优势：
      - 保护有用的浏览器默认样式而不是完全去掉它们
      - 为大部分 `HTML` 元素提供一般化的样式
      - 修复浏览器自身的 `bug` 并保证各浏览器的一致性
      - 用一些小技巧优化 `CSS` 可用性
      - 用注释和详细的文档来解释代码

    当需要实现非常个性化的网页设计时，我会选择重置的方式，因为我要写很多自定义的样式以满足设计需求，这时候就不再需要标准化的默认样式了。

1. #### 请解释浮动 (`Float`) 及其工作原理

    浮动（`float`）是 `CSS` 定位属性。浮动元素从网页的正常流动中移出，但是保持了部分的流动性，会影响其他元素的定位（比如文字会围绕着浮动元素）。这一点与绝对定位不同，绝对定位的元素完全从文档流中脱离。

    `CSS` 的 `clear` 属性通过使用 `left、right、both`，让该元素向下移动（清除浮动）到浮动元素下面。

    如果父元素只包含浮动元素，那么该父元素的高度将塌缩为 0。我们可以通过清除（`clear`）从浮动元素后到父元素关闭前之间的浮动来修复这个问题。

    有一种 `hack` 的方法，是自定义一个 `.clearfix` 类，利用伪元素选择器 `::after` 清除浮动。另外还有一些方法，比如添加空的 `<div></div>` 和设置浮动元素父元素的 `overflow` 属性。与这些方法不同的是，`clearfix` 方法，只需要给父元素添加一个类，定义如下：

    ```css
    .clearfix::after {
      content: '';
      display: block;
      clear: both;
    }
    ```

    值得一提的是，把父元素属性设置为 `overflow: auto` 或 `overflow: hidden`，会使其内部的子元素形成块格式化上下文（`Block Formatting Context`），并且父元素会扩张自己，使其能够包围它的子元素。

1. #### 请阐述 `z-index` 属性，并说明如何形成层叠上下文（`stacking context`）

    `CSS` 中的 `z-index` 属性控制重叠元素的垂直叠加顺序。`z-index` 只能影响 `position` 值不是 `static` 的元素。

    没有定义 `z-index` 的值时，元素按照它们出现在 `DOM` 中的顺序堆叠（层级越低，出现位置越靠上）。非静态定位的元素（及其子元素）将始终覆盖静态定位（`static`）的元素，而不管 `HTML` 层次结构如何。

    层叠上下文是包含一组图层的元素。 在一组层叠上下文中，其子元素的 `z-index` 值是相对于该父元素而不是 `document root` 设置的。每个层叠上下文完全独立于它的兄弟元素。如果元素 `B` 位于元素 `A` 之上，则即使元素 `A` 的子元素 `C` 具有比元素 `B` 更高的 `z-index` 值，元素 `C` 也永远不会在元素 `B` 之上.

    每个层叠上下文是自包含的：当元素的内容发生层叠后，整个该元素将会在父层叠上下文中按顺序进行层叠。少数 `CSS` 属性会触发一个新的层叠上下文，例如 `opacity` 小于 1，`filter` 不是 `none，transform` 不是 `none`。

1. #### 请阐述块格式化上下文（`Block Formatting Context`）及其工作原理

    块格式上下文（`BFC`）是 `Web` 页面的可视化 `CSS` 渲染的部分，是块级盒布局发生的区域，也是浮动元素与其他元素交互的区域。

    浮动定位和清除浮动时只会应用于同一个 `BFC` 内的元素。浮动不会影响其它 `BFC` 中元素的布局，而清除浮动只能清除同一 `BFC` 中在它前面的元素的浮动。外边距折叠（`Margin collapsing`）也只会发生在属于同一 `BFC` 的块级元素之间。
    `BFC` 的规则：
    - 在 `BFC` 中,盒子从顶端开始垂直地一个接一个排列，两个盒子之间的垂直间隙是由他们的 `margin` 值决定
    - 在同一个 `BFC` 中，两个相邻块级盒子的垂直外边距会产生重叠
    - 在 `BFC` 中，每一个盒子的左边缘都会触碰到容器到左边缘
    - 计算 `BFC` 元素高度时，浮动元素也参与计算块级格式化上下文包括了创建该上下文的元素的所有子元素，但不包括创建了新的块格式化上下文的子元素。

    一句话概括：`BFC` 是为其内容而生的。

    **`BFC` 不包括产生 `BFC` 的元素，也就是不包括生产者！**

    一个 `HTML` 盒（`Box`）满足以下任意一条，会创建块格式化上下文：

    - `float` 的值不是 `none`.
    - `position` 的值不是 `static` 或 `relative`.
    - `display` 的值是 `table-cell、table-caption、inline-block、flex、或inline-flex`。
    - `overflow` 的值不是 `visible`。
    - 在 `BFC` 中，每个盒的左外边缘都与其包含的块的左边缘相接。

    两个相邻的块级盒在垂直方向上的边距会发生合并（`collapse`）。

    举例：
    `overflow:hidden` 清除浮动（方方总是用 `.clearfix` 清除浮动，坚决不用 `overflow:hidden` 清除浮动）
    `overflow:hidden` 取消父子 `margin` 合并（方方用 `padding-top: 1px;`）

    ***什么是外边距合并（`margin collapsing`）***

    当两个元素的垂直边距接触时，将仅保留具有最大边距值的元素的边距，而具有较小边距值的元素的边距将折叠为零。如果一个元素的边距为负，则将边距值相加以确定最终值。如果两者均为负，则使用较大的负值。此定义适用于相邻元素和嵌套元素。

    **可使用 `border、padding、overflow:hidden` 清除合并**

    在其他情况下，元素的边距不会合并：

      - 浮动元素
      - 绝对定位的元素
      - 内联块元素
      - 除 `overflow:visible` 以外的任何元素（它们不会与子元素一起折叠边距。）
      - 已清除的元素（它们不会用其父块的底部边距折叠顶部边距。）
      - 根元素

1. #### 有哪些清除浮动的技术，都适用哪些情况？

     - 空 `div` 方法：使用这种方式清除浮动优点是简单，兼容性也好 ；缺点是不利于代码语义化，后期维护成本大。
     `<div style="clear:both;"></div>`
     - `clearfix` 方法：

        ```css
        /* Bootstrap */
        .clearfix::after {
          content: '';
          display: block;
          clear: both;
        }

        /* taobao */
        .clearfix:after {
          height: 0;
          line-height: 0;
          visibility: hidden;
          clear: both;
        }
        .clearfix:after, .clearfix:before {
          content: " ";
          display: table;
        }

        /* CSS-Tricks */
        .group:after {
          content: "";
          display: table;
          clear: both;
        }


        .clearfix{
          zoom: 1; /* IE 兼容 */
        }
        ```

     - `overflow: auto` 或 `overflow: hidden` 方法：子元素的高度小于等于父元素的时候。
     - 在大型项目中，我会使用 `clearfix` 方法，在需要的地方使用 `.clearfix`。设置 `overflow: hidden` 的方法可能使其子元素显示不完整，当子元素的高度大于父元素时。

1. #### 请解释什么是精灵/雪碧图（`css sprites`），以及如何实现？

    精灵图是把多张图片整合到一张上的图片。它被运用在众多使用了很多小图标的网站上
    实现方法：

    - 使用生成器将多张图片打包成一张精灵图，并为其生成合适的 `CSS`。
    - 每张图片都有相应的 `CSS` 类，该类定义了 `background-image、background-position` 和 `background-size` 属性。
    - 使用图片时，将相应的类添加到你的元素中。

    好处：

    - 减少加载多张图片的 `HTTP` 请求数（一张精灵图只需要一个请求）。但是对于 `HTTP2` 而言，加载多张图片不再是问题。
    - 提前加载资源，防止在需要时才在开始下载引发的问题，比如只出现在 `:hover` 伪类中的图片，不会出现闪烁。

1. #### 你最喜欢的图片替换方法是什么，你如何选择使用

1. #### 你会如何解决特定浏览器的样式问题？

    - 在确定问题原因和有问题的浏览器后，使用单独的样式表，仅供出现问题的浏览器加载。这种方法需要使用服务器端渲染。
    - 使用已经处理好此类问题的库，比如 `Bootstrap`。
    - 使用 [autoprefixer](https://github.com/postcss/autoprefixer) 自动生成 `CSS` 属性前缀。
    - 使用 `Reset CSS` 或 [Normalize.css](https://github.com/necolas/normalize.css)。

1. #### 如何为有功能限制的浏览器提供网页？你会使用哪些技术和处理方法？

    - 优雅的降级：为现代浏览器构建应用，同时确保它在旧版浏览器中正常运行。
    - 渐进式增强：构建基于用户体验的应用，但在浏览器支持时添加新增功能。
    - 利用 [caniuse.com](caniuse.com) 检查特性支持。
    - 使用 [autoprefixer](https://github.com/postcss/autoprefixer) 自动生成 `CSS` 属性前缀。
    - 使用 [Modernizr](https://modernizr.com/) 进行特性检测。

1. #### 有什么不同的方式可以隐藏内容（使其仅适用于屏幕阅读器）？

    这些方法与可访问性（[A11Y](https://developer.mozilla.org/zh-CN/docs/Glossary/Accessibility)）有关。
      - `width: 0; height: 0`：使元素不占用屏幕上的任何空间，导致不显示它。
      - `position: absolute; left: -99999px`： 将它置于屏幕之外。
      - `text-indent: -9999px`：这只适用于 `block` 元素中的文本。
      - `Metadata`： 例如通过使用 Schema.org，RDF 和 JSON-LD。
      - `WAI-ARIA`：如何增加网页可访问性的 `W3C` 技术规范。

    即使 `WAI-ARIA` 是理想的解决方案，我也会采用绝对定位方法，因为它具有最少的注意事项，适用于大多数元素，而且使用起来非常简单。

1. #### 你用过栅格系统 (`grid system`) 吗？如果使用过，你最喜欢哪种？

1. #### 你用过媒体查询，或针对移动端的布局/`CSS` 吗？

1. #### 你熟悉 `SVG` 样式的书写吗？

    可缩放矢量图形（`Scalable Vector Graphics，SVG`），是一种用于描述二维的矢量图形，基于 `XML` 的标记语言。
    你可以使用内联 `CSS`、嵌入式 `CSS` 部分或外部 `CSS` 文件对形状进行着色（包括指定对象上的属性）。在网上大部分 `SVG` 使用的是内联 `CSS`。

    - 常见元素：
      - `<a>`：定义一个超链接
      `<a href="" target="_blank"></a>`
      - `<circle>`：创建一个基于圆心+半径的圆
      `<circle fill="#FDFDFD" cx="73.704" cy="145.516" r="5.421" p-id="338"></circle>`
      - `<g>`：用来组合对象的容器。添加到 `g` 元素上的变换会应用到其所有的子元素上。添加到g元素的属性会被其所有的子元素继承。此外，`g` 元素也可以用来定义复杂的对象，之后可以通过 `<use>` 元素来引用它们。

        ```svg
        <g stroke="green" fill="white" stroke-width="5">
          <circle cx="25" cy="25" r="15" />
          <circle cx="40" cy="25" r="15" />
          <circle cx="55" cy="25" r="15" />
          <circle cx="70" cy="25" r="15" />
        </g>
        ```

      - `<linearGradient>`：用来定义线性渐变，用于图形元素的填充或描边

        ```svg
        <linearGradient id="MyGradient">
          <stop offset="5%"  stop-color="green"/>
          <stop offset="95%" stop-color="gold"/>
        </linearGradient>
        ```

      - `<path>`：用来定义形状的通用元素。所有的基本形状都可以用 `path` 元素来创建。
      `<path d="M 100 100 L 300 100 L 200 300 z" fill="orange" stroke="black" stroke-width="3" />`
      - `<rect>`：是 `SVG` 的一个基本形状，用来创建矩形，基于一个角位置以及它的宽和高。它还可以用来创建圆角矩形。
      `<rect x="10" y="10" width="100" height="100"/>`
      - `<text>`：定义了一个由文字组成的图形。注意：我们可以将渐变、图案、剪切路径、遮罩或者滤镜应用到 `text` 上。
      `<text x="250" y="150" font-family="Verdana" font-size="55">Hello</text>`
      - `<use>`：在 `SVG` 文档内取得目标节点，并在别的地方复制它们。它的效果等同于这些节点被深克隆到一个不可见的 `DOM` 中，然后将其粘贴到 `use` 元素的位置

        ```svg
        <svg width="100%" height="100%" xmlns="http://..." xmlns:xlink="http://...">
          <style>
            .classA { fill:red }
          </style>
          <defs>
            <g id="Port">
              <circle style="fill:inherit" r="10"/>
            </g>
          </defs>

          <text y="15">black</text>
          <use x="50" y="10" href="#Port" />
          <text y="35">red</text>
          <use x="50" y="30" href="#Port" class="classA"/>
          <text y="55">blue</text>
          <use x="50" y="50" href="#Port" style="fill:blue"/>
        </svg>
        ```

    - 常见属性
      - `color`：用来为 `fill`、`stroke`、`stop-color`、`flood-color` 和 `lighting-color` 属性提供一个潜在的间接值(`currentColor`)
      - `d`：绘制路径
      - `fill`：填充色
      - `fill-opacity`：填充色透明度
      - `stroke`：绘边色
      - `stroke-opacity`：绘边色透明度

1. #### 除了 `screen`，你还能说出一个 `@media` 属性的例子吗？

    - `all` 适用于所有设备
    - `print` 适用于在打印预览模式下在屏幕上查看的分页材料和文档
    - `screen` 主要适用于屏幕
    - `speech` 主要用于语音合成器

1. #### `CSS` 的 `@` 规则([at-rule](https://developer.mozilla.org/zh-CN/docs/Web/CSS/At-rule))

    以 `@` 符号开头, `'@' (U+0040 COMMERCIAL AT)`, 后跟一个标识符，并包括直到下一个分号的所有内容, `';' (U+003B SEMICOLON)`, 或下一个 `CSS` 块，以先到者为准。
    - `@charset`, 定义样式表使用的字符集.
    `@charset "UTF-8";`
    - `@import`, 告诉 `CSS` 引擎引入一个外部样式表.

      ```css
      @import url("fineprint.css") print;
      @import url("bluish.css") projection, tv;
      @import 'custom.css';
      @import url("chrome://communicator/skin/");
      /* @import "common.css" screen, projection; 可以使用，只是本地vscode会引起错误 */
      @import url('landscape.css') screen and (orientation:landscape);
      ```

    - `@namespace`, 告诉 `CSS` 引擎必须考虑 `XML` 命名空间。

      ```css
      /* 默认命名空间 */
      @namespace url(XML-namespace-URL);
      @namespace "XML-namespace-URL";

      /* 命名空间前缀 */
      @namespace prefix url(XML-namespace-URL);
      @namespace prefix "XML-namespace-URL";
      ```

    - 嵌套 `@` 规则, 是嵌套语句的子集,不仅可以作为样式表里的一个语句，也可以用在条件规则组里：
      - `@media`, 如果满足媒介查询的条件则条件规则组里的规则生效。

        ```css
        /* At the top level of your code */
        @media screen and (min-width: 900px) {
          article {
            padding: 1rem 3rem;
          }
        }

        /* Nested within another conditional at-rule */
        @supports (display: flex) {
          @media screen and (min-width: 900px) {
            article {
              display: flex;
            }
          }
        }
        ```

      - `@page`, 描述打印文档时布局的变化

        ```css
        @page {
          margin: 1cm;
        }

        @page :first {
          margin: 2cm;
        }
        ```

      - `@font-face`, 描述将下载的外部的字体

        ```css
        @font-face {
          font-family: "Open Sans";
          src: url("/fonts/OpenSans-Regular-webfont.woff2") format("woff2"),
              url("/fonts/OpenSans-Regular-webfont.woff") format("woff");
        }
        ```

      - `@keyframes`, 描述 `CSS` 动画的中间步骤

        ```css
        @keyframes slidein {
          from { transform: translateX(0%); }
          to { transform: translateX(100%); }
        }
        ```

      - `@supports`, 如果满足给定条件则条件规则组里的规则生效

        ```css
        @supports (display: grid) {
          div {
            display: grid;
          }
        }
        @supports not (display: grid) {
          div {
            float: right;
          }
        }
        @supports (transform-origin: 5% 5%) { ... }
        ```

      - `@document`, 如果文档样式表满足给定条件则条件规则组里的规则生效。 (推延至 CSS Level 4 规范)

        ```css
        @document url("https://www.example.com/") {
          h1 {
            color: green;
          }
        }
        ```

1. #### 编写高效的 `CSS` 应该注意什么？

    首先，浏览器从最右边的选择器，即关键选择器（`key selector`），向左依次匹配。根据关键选择器，浏览器从 `DOM` 中筛选出元素，然后向上遍历被选元素的父元素，判断是否匹配。**选择器匹配语句链越短，浏览器的匹配速度越快**。***避免使用标签和通用选择器作为关键选择器，因为它们会匹配大量的元素，浏览器必须要进行大量的工作，去判断这些元素的父元素们是否匹配。***

    [BEM (Block Element Modifier)](https://www.bemcss.com/) 原则上建议为独立的 `CSS` 类命名，并且在需要层级关系时，将关系也体现在命名中，这自然会使选择器高效且易于覆盖。
    `BEM = 模块名 + 元素名 + 修饰器名`

    ```css
    /* Instagram 团队使用的驼峰式: */
    .blockName-elementName--modifierName { /* ... */ }
    /* 还有单下划线：*/
    .block-name_element-name--modifierName { /* ... */ }
    /* 还有修饰器名用单横线连接：*/
    .blockName__elementName-modifierName { /* ... */ }
    /* BEM 推荐的写法*/
    .block__element { display: none; } .block__element--active { display: block; }
    /*
     统一JS控制状态的类名格式，如is-active、js-active等等
     只用作标识
     不允许有默认的公共样式
    */
    .block__element { display: none; } .block__element.is-active { display: block; }
    ```

    搞清楚哪些 `CSS` 属性会触发重新布局（`reflow`）、重绘（`repaint`）和合成（`compositing`）。在写样式时，避免触发重新布局的可能。

1. #### 使用 `CSS` 预处理器的优缺点有哪些？请描述你曾经使用过的 `CSS` 预处理器的优缺点

    - 优点：
      - 提高 `CSS` 可维护性。
      - 易于编写嵌套选择器。
      - 引入变量，增添主题功能。可以在不同的项目中共享主题文件。
      - 通过混合（`Mixins`）生成重复的 `CSS`。
      - 将代码分割成多个文件。不进行预处理的 `CSS`，虽然也可以分割成多个文件，但需要建立多个 `HTTP` 请求加载这些文件。
    - 缺点：
      - 需要预处理工具。
      - 重新编译的时间可能会很慢。
    - 区别：
      - `Less`
      - `Sass (Sass3 = Scss)`
      - `Stylus`
      - `PostCSS`

1. #### 如果设计中使用了非标准的字体，你该如何去实现？

    使用 `@font-face` 并为不同的 `font-weight` 定义 `font-family`。

1. #### 解释浏览器如何确定哪些元素与 `CSS` 选择器匹配？

    浏览器从最右边的选择器（关键选择器）根据关键选择器，浏览器从 `DOM` 中筛选出元素，然后向上遍历被选元素的父元素，判断是否匹配。选择器匹配语句链越短，浏览器的匹配速度越快。

    例如：`body.ready #wrapper > .app`

    先把所有 `class` 中有 `app` 的元素拿出来组成一个集合，然后上一层，对每一个集合中的元素，如果元素的 `parent id` 不为 `#wrapper` 则把元素从集合中删去。 再向上，从这个元素的父元素开始向上找，没有找到一个 `tagName` 为 `body` 且 `class` 中有 `ready` 的元素，就把原来的元素从集合中删去。

    为什么从后往前匹配？
    - 效率：找元素的父亲和之前的兄弟比遍历所有儿子快而且方便。从右往左的集合中获取的是最大集合，故往左走会越来越小；而从左往右的集合中获取的不一定是最大集合，每往下走一步就会遍历子元素（需要过滤不匹配的，耗时）
    - 文档流的解析方向：一个元素只要出现在文档流中，就能确定他的匹配情况。

    注意⚠️：
    1. `ID` 的效率最高，`Universal` 的效率最低

        ```css
        /* 关键选择器有四种：ID > 类 > 标签 > 通用键(效率排序) */
        #main-navigation {   }      /* ID (Fastest) */
        body.home #page-wrap {   }  /* ID */
        .main-navigation {   }      /* Class */
        ul li a.current {   }       /* Class *
        ul {   }                    /* Tag */
        ul li a {  }                /* Tag */
        * {   }                     /* Universal (Slowest) */
        #content [title='home']     /* Universal */

        /*
          看似很快 => 先找 ID，再找 li ❌
          实则很慢 => 先找 li，再找 ID ✅
        */
        #main-nav > li {   }
        ```

    2. 不要修饰标签(`tag-qualify`)
    `ul#main-navigation {  } /* 永远不要这么做 */`
    `li.first { } /* 减少类似操作 */`

    3. 后代选择器是效率最糟糕的
    后代选择器是 `CSS` 中最昂贵的选择器。这是非常昂贵的-**特别是如果选择器在标签或通用类别中。**
    `html body ul li a {  } /* 效率的灾难 */`
    4. 一个失败的选择器比相同的选择器匹配更有效率
    在选择器的从右到左解释中，一旦匹配失败，它就会停止尝试，从而比保持解释所需的精力更少。
    5. `CSS3` 与效率
    `CSS3` 选择器（例如：`nth-​​child`）在确定所需元素的同时保持标记的清晰及语义化。但是，更耗费浏览器资源。

1. #### `CSS` 性能优化

    - 阻塞渲染优化

    ```css
    /* 为了创建非阻塞 CSS 链接，将不会立即使用的样式（例如打印样式）移动到单独的文件中，将 <link> 添加到 HTML 中，并添加媒体查询，在这种情况下说明它是打印样式表。 */
    <link rel="stylesheet" href="styles.css"> <!-- blocking -->
    <link rel="stylesheet" href="print.css" media="print"> <!-- not blocking -->
    <link rel="stylesheet" href="mobile.css" media="screen and (max-width: 480px)"> <!-- not blocking on large screens -->
    ```

    - 在 `GPU` 上呈现动画
    `3D transforms (transform: translateZ(), rotate3d()，etc.)`，`animating transform`， `opacity, position: fixed，will-change`，`filter`。一些元素，例如 `<video>`, `<canvas>` 和 `<iframe>`

    - `will-change` 属性(少用)
    `will-change: opacity, transform;`
    告诉浏览器元素的哪些属性需要修改，使浏览器能够在元素实际更改之前设置优化，通过在实际更改前执行耗时的工作以提升性能。
    - `font-display` 属性
    根据 `@font-face` 规则，`font-display` 属性定义了浏览器如何加载和显示字体文件，允许文本在字体加载或加载失败时显示回退字体。可以通过依靠折中无样式文本闪现使文本可见替代白屏来提高性能。

        ```css
        @font-face {
          font-family: someFont;
          src: url(/path/to/fonts/someFont.woff) format('woff');
          font-weight: 400;
          font-style: normal;
          font-display: fallback;
        }
        ```

    - `contain` 属性
    `CSS` 的 `contain` 属性允许作者指示元素及其内容尽可能独立于文档树的其余部分。 这允许浏览器针对 `DOM` 的有限区域而不是整个页面重新计算布局，样式，绘画，大小或它们的任意组合。

    - 避免使用 `!important` 覆盖级联
    - 避免在 `HTML` 中使用内联样式。
    - 采用 `BEM` 命名方法
    - 减少使用以下属性：
      - `border-radius`
      - `box-shadow`
      - `opacity`
      - `transform`
      - `filter`
      - `position: fixed`
    - 减少动画的使用

1. #### 请描述伪元素 (`pseudo-elements`) 及其用途

    `CSS` 伪元素是添加到选择器末的关键字，去选择元素的特定部分，对特定部分样式修改。

    注意⚠️：与伪元素比较，`pseudo-classes(伪类)` 能够根据状态改变元素样式。

    **一个选择器中只能使用一个伪元素。伪元素必须紧跟在语句中的简单选择器/基础选择器之后。**

    - `::first-line(:first-line)`：选中块级元素的第一行，不能匹配任何真实存在的 `html` 元素，且只能在 `display` 值为 `block, inline-block, table-cell` 或者 `table-caption` 中有用才有效。
    - `::first-letter(:first-letter)`：选中块级元素第一行第一个字母，并且文字所处的行之前没有其他内容（如图片和内联的表格）。
    - `::before(:before)`：创建一个伪元素，作为已选中元素的第一个子元素，默认行内元素。
    - `::after(:after)`：创建一个伪元素，作为已选中元素的最后一个子元素，默认行内元素。

1. #### 请解释你对盒模型的理解，以及如何在 `CSS` 中告诉浏览器使用不同的盒模型来渲染你的布局

    盒模型就是将所有元素表示为一个矩形盒子，每个盒子包括了四个部分，从内到外依次是：`content`(内容区)、`padding`(内边距)、`border`(边框)以及 `margin`(外边距)。
    盒模型分为两个类型：`content-box` 和 `border-box`
    默认是 `content-box`，推荐用 `border-box`

    ```css
    box-sizing:border-box;
    /* content-box */
    width == content-width(内容区宽度)
    /* border-box */
    width == content-width(内容区宽度) + padding + border
    /* 大多数情况下这使得我们更容易的去设定一个元素的宽高 */
    ```

    盒模型有以下规则：

    - 块级元素的大小由 `width、height、padding、border、margin` 决定。
    - 如果没有指定 `height`，则块级元素的高度等于其包含子元素的内容高度加上 `padding` (除非有浮动元素)。
    - 如果没有指定 `width`，则非浮动块级元素的宽度等于其父元素的宽度减去父元素的 `padding`。
    - 元素的 `height` 是由内容的 `height` 来计算的。
    - 元素的 `width` 是由内容的 `width` 来计算的。
    - 默认情况下，`padding` 和 `border` 不是元素 `width` 和 `height` 的组成部分。

1. #### 请罗列出你所知道的 `display` 属性的全部值

    `display` 属性可以设置元素的内部和外部显示类型。元素的外部显示类型将决定该元素在流式布局中的表现(块级或内联元素)；元素的内部显示类型可以控制其子元素的布局（例如：`flow layout，grid` 或 `flex`）。

    - `<display-outside>`
      - `block`：将该元素变成块级元素。该元素之前和之后打断（换行）
      - `inline`：将该元素变成行内元素。该元素之前和之后不会打断（换行）
    - `<display-inside>`
      - `flow-root`：该元素生成一个块元素框，该框建立一个新的块格式化上下文，定义格式化根所在的位置。
      - `table`：元素的行为类似于 `<table>` 元素。它定义了一个块级框。
      - `flex`：元素的行为类似于块元素，并根据 `flexbox` 模型布置其内容。
      - `grid`：元素的行为类似于块元素，并根据网格模型布置其内容。
    - `<display-listitem>`
      - `list-item`：元素的行为类似于列表项。类似于 `<li>` 元素
    - `<display-internal>`
      - `table-row-group`：元素的行为类似于 `<tbody>` 元素。
      - `table-header-group`：元素的行为类似于 `<thead>` 元素。
      - `table-footer-group`：元素的行为类似于 `<tfoot>` 元素。
      - `table-row`：元素的行为类似于 `<tr>` 元素。
      - `table-cell`：元素的行为类似于 `<td>` 元素。
      - `table-column-group`：元素的行为类似于 `<colgroup>` 元素。
      - `table-column`：元素的行为类似于 `<col>` 元素。
      - `table-caption`：元素的行为类似于 `<caption>` 元素。
    - `<display-box>`
      - `contents`：这些元素自己不显示。它们被它们的伪盒（`pseudo-box`）和子项盒（`child boxes`）取代。**调试器也看不到其整体大小**
      - `none`：关闭元素的显示，不影响布局（文件中没有该元素）。所有子项的显示也被关闭。**该盒子和内容不会渲染。**
    - `<display-legacy>`
      - `inline-block`：元素会产生一个块元素盒子，并且像内联盒子一样（表现得更像一个被替换的元素），可以融入到周围内容中。等同于 `inline flow-root`。
      - `inline-table`：没有直接对应关系。它表现为一个 `<table>` 元素， 但是又表现为一个不同于块级盒子的内联盒子。表盒子内部是一个块级上下文。等同于 `inline table`。
      - `inline-flex`：元素表现为一个内联元素，并对内容采用弹性盒子模型进行布局。等同于 `inline flex`。
      - `inline-grid`：元素表现为一个内联元素，并对内容采用网格模型进行布局。等同于 `inline grid`。

1. #### 请解释 `inline` 和 `inline-block` 的区别？

    || `inline` | `inline-block` | `block` |
    | - | - | - | - |
    |大小 | 取决于内容 | 取决于内容 | 填充其父容器的宽度 |
    |定位 | 与其他内容一起流动，并允许旁边有其他元素 | 与其他内容一起流动，并允许旁边有其他元素 | 从新的一行开始，并且不允许旁边有 HTML 元素（除非是float） |
    |设置 `width` 和 `height` | 不能，设置会被忽略 | 能 | 能 |
    |使用 `vertical-align` 对齐 | 可以 | 可以 | 不可以 |
    |边距(`margin`)和填充(`padding`) | 只有水平方向存在。垂直方向会被忽略。 尽管 `border` 和 `padding` 在 `content` 周围，但垂直方向上的空间取决于 `line-height` | 各个方向都存在 | 各个方向都存在 |
    |浮动(`float`) | 就像一个 `block` 元素，可以设置垂直边距和填充。 | - | - |

1. #### 请例举 `position` 所有属性值，并说明其区别

    `position` 属性用于指定一个元素在文档中的定位方式。`top，right，bottom` 和 `left` 属性则决定了该元素的最终位置。
    - **定位类型：**
      - 定位元素：除 `static` 以外的元素
      - 相对定位元素： `relative`
      - 绝对定位元素： `absolute | fixed`
      - 粘性定位元素： `sticky`
    - **取值：**
      - `static`：默认定位属性值。该元素使用正常的布局行为，即元素在文档常规流中当前的布局位置。此时 `top, right, bottom, left` 和 `z-index` 属性无效。
      - `relative`：元素先放置在未添加定位时的位置，再在不改变页面布局的前提下调整元素位置（因此会在此元素未添加定位时所在位置留下空白）；`table-*-group, table-row, table-column, table-cell, table-caption` 元素无效。
      - `absolute`：元素会被移出正常文档流，不为元素预留空间，通过指定元素相对于最近的非 `static` 定位祖先元素的偏移，来确定元素位置。绝对定位的元素可以设置外边距（`margins`），且不会与其他边距合并。
      - `fixed`：元素会被移出正常文档流，不为元素预留空间，而是通过指定元素相对于屏幕视口（`viewport`）的位置来指定元素位置。元素的位置在屏幕滚动时不会改变。**打印时，元素会出现在的每页的固定位置。`fixed` 属性会创建新的层叠上下文。当元素祖先的 `transform` 属性非 `none` 时，容器由视口改为该祖先**。
      - `sticky`：盒位置根据正常流计算(这称为正常流动中的位置)，然后相对于该元素在流中的 `flow root(BFC)` 和 `containing block`（最近的块级祖先元素）定位。在所有情况下（即便被定位元素为 `table` 时），该元素定位均不对后续元素造成影响。当元素 `B` 被粘性定位时，后续元素的位置仍按照 `B` 未定位时的位置来确定。`position: sticky` 对 `table` 元素的效果与 `position: relative` 相同。

1. #### `CSS` 中字母 `C` 的意思是叠层 (`Cascading`)。请问在确定样式的过程中优先级是如何决定的 (请举例)？如何有效使用此系统？

1. #### 你在开发或生产环境中使用过哪些 `CSS` 框架？你觉得应该如何改善他们？

1. #### 请问你有尝试过 `CSS` `Flexbox` 或者 `Grid` 标准规格吗？

    了解。`Flexbox` 主要用于一维布局，而 `Grid` 则用于二维布局。

    `Flexbox` 解决了 `CSS` 中的许多常见问题，例如容器中元素的垂直居中，粘性定位（`sticky`）的页脚等。`Bootstrap` 基于 `Flexbox`，这是创建布局的推荐方式。我之前曾使用过 `Flexbox`，但在使用 `flex-grow` 时遇到了一些浏览器不兼容问题（`Safari`），我必须使用 `inline-blocks` 和手动计算百分比宽度，来重写我的代码，这种体验不是很好。

    `Grid` 创建基于栅格的布局，是迄今为止最直观的方法，但目前浏览器支持并不广泛。

1. #### 请解释在编写网站时，响应式与移动优先的区别

    两种方法不是互斥的。

    使一个网站响应意味着网站会根据设备屏幕的尺寸自行调整一些元素的尺寸和功能，通常是通过CSS媒体查询的视口宽度。
    移动优先策略同样也指的是响应式，但是它建议我们应该默认定义移动设备的所有样式，仅仅添加特定的规则用来适配其他设备。

    移动优先策略有 2 大优势：

    - 在移动设备上有更好的性能，因为应用于它们的规则无需针对任何媒体查询的验证。
    - 它让你强制编写与响应CSS规则相关的更干净的代码。

1. #### 为什么响应式设计 (`responsive design`) 和自适应设计 (`adaptive design`) 不同？

    响应式设计和自适应设计都以提高不同设备间的用户体验为目标，根据视窗大小、分辨率、使用环境和控制方式等参数进行优化调整。

    响应式设计的适应性原则：网站应该凭借一份代码，在各种设备上都有良好的显示和使用效果。响应式网站通过使用媒体查询，自适应栅格和响应式图片，基于多种因素进行变化，创造出优良的用户体验。就像一个球通过膨胀和收缩，来适应不同大小的篮圈。

    自适应设计更像是渐进式增强的现代解释。与响应式设计单一地去适配不同，自适应设计通过检测设备和其他特征，从早已定义好的一系列视窗大小和其他特性中，选出最恰当的功能和布局。与使用一个球去穿过各种的篮筐不同，自适应设计允许使用多个球，然后根据不同的篮筐大小，去选择最合适的一个。

1. #### 你有兼容 `retina` 屏幕的经历吗？如果有，在什么地方使用了何种技术？

    我倾向于使用更高分辨率的图形（显示尺寸的两倍）来处理视网膜显示。更好的方法是使用媒体查询，像 `@media only screen and (min-device-pixel-ratio: 2) { ... }`，然后改变 `background-image`。

    对于图标类的图形，我会尽可能使用 `svg` 和图标字体，因为它们在任何分辨率下，都能被渲染得十分清晰。

    还有一种方法是，在检查了 `window.devicePixelRatio` 的值后，利用 `JavaScript` 将 `<img>` 的 `src` 属性修改，用更高分辨率的版本进行替换。

1. #### 什么情况下，用 `translate()` 而不用绝对定位？什么时候，情况相反?

    `translate()` 是 `transform` 的一个值。改变 `transform` 或 `opacity` 不会触发浏览器重新布局（`reflow`）或重绘（`repaint`），只会触发复合（`compositions`）。而改变绝对定位会触发重新布局，进而触发重绘和复合。`transform` 使浏览器为元素创建一个 `GPU` 图层，但改变绝对定位会使用到 `CPU`。 因此 `translate()` 更高效，可以缩短平滑动画的绘制时间。

    当使用 `translate()` 时，元素仍然占据其原始空间（有点像 `position：relative`），这与改变绝对定位不同。

1. #### CSS3 新特性

### JS 相关问题

1. #### 请解释事件代理 (`event delegation`)，有什么好处？

    时间代理又称事件委托：
    - 对“事件处理程序过多”问题的解决方案就是事件委托。
    - 事件委托利用了事件冒泡，只指定一个事件处理程序，就可以管理某一类型的所有事件。
    - 事件委托是将事件监听器添加到父元素，而不是每个子元素单独设置事件监听器。当触发子元素时，事件会冒泡到父元素，监听器就会触发。

    事件委托的好处：
    - `document` 对象很快就可以访问，而且可以在页面生命周期的任何时点上为它添加事件处理程序（无需等待 `DOMContentLoaded` 或
     `load` 事件）。简言之，只要可点击的元素呈现在页面上，就可以立即具备适当的功能。即可以监听还没有出生的儿子（动态生成的
    元素）。
    - 无需从已删除的元素中解绑处理程序，也无需将处理程序绑定到新元素上。
    - 在页面中设置事件处理程序所需的时间更少。即省监听器。
    - 整个页面占用的内存空间更少，能够提升整体性能。

      ```js
      function delegate(element, eventType, selector, fn) {
        element.addEventListener(eventType, e => {
          let el = e.target
          while (!el.matches(selector)) {//判断是不是监听的元素
            if (element === el) {//判断是不是事件委托的元素即父（祖先）元素
              el = null //没有点到监听的元素上。不执行
              break
            }
            el = el.parentNode //查找父元素是否为监听对象
          }
          el && fn.call(el, e, el)
          //如果el为真，那么执行fn.call(el, e, el)；为假就不执行。
        })
        return element
      }
      delegate(ul, 'click', 'li', ()=>{})
      ul>li*5>span


      function delegate(el, selector, type, fn) {
        function callback(e) {
          e = e || window.event;
          const target = e.target || e.srcElement;
          let selectors = el.querySelectorAll(selector);
          selectors = [].slice.call(selectors); // 浅拷贝
          if (selectors.includes(target)) {
            fn.call(target, e);
          }
        }
        el.addEventListener(type, callback, false);
      }

      delegate(document.querySelector('body'), 'button', 'click', function () {
        console.log('bingo');
      });
      ```

1. #### 请解释 `JavaScript` 中 `this` 是如何工作的

    `this` 取值符合以下规则：
    1. 在调用函数时使用 `new` 关键字，函数内的 `this` 是一个全新的对象。
    2. 如果 `apply、call` 或 `bind` 方法用于调用、创建一个函数，函数内的 `this` 就是作为参数传入这些方法的对象。
    3. 当函数作为对象里的方法被调用时，函数内的 `this` 是调用该函数的对象。比如当 `obj.method()` 被调用时，函数内的 `this` 将绑定到 `obj` 对象。
    4. 如果调用函数不符合上述规则，那么 `this` 的值指向全局对象（`global object`）。浏览器环境下 `this` 的值指向 `window` 对象，但是在严格模式下(`'use strict'`)，`this` 的值为 `undefined`。
    5. 如果符合上述多个规则，则较高的规则（1 号最高，4 号最低）将决定 `this` 的值。
    6. 如果该函数是 `ES2015` `中的箭头函数，将忽略上面的所有规则，this` 被设置为它被创建时的上下文。

    ```javascript
    // 面试题
    var obj = {
      foo: function () {
        console.log(this)
      }
    }
    var bar = obj.foo
    obj.foo() // obj.foo.call(obj) 打印出的 this 是 obj
    bar() // bar.call(undefined) 打印出的 this 是 window
    ```

    **如果你传入的 `context` 是 `null` 或者 `undefined`，那么 `window` 对象就是默认的 `context`（严格模式下默认 `context` 是 `undefined`）**
    - `fn()` 里面的 `this` 就是 `window`
    - `fn()` 是 `strict mode，this` 就是 `undefined`
    - `a.b.c.fn()` 里面的 `this` 就是 `a.b.c`
    - `new F()` 里面的 `this` 就是新生成的实例
    - `() => console.log(this)` 里面 `this` 跟外面的 `this` 的值一模一样
    - 匿名函数的执行环境具有全局性，因此它的 `this` 对象通常指向 `windows`

1. #### 请解释原型继承 (`prototypal inheritance`) 的原理

    所有 `JS` 对象都有一个 `__proto__` 属性，指向它的原型对象。当试图访问一个对象的属性时，如果没有在该对象上找到，它还会搜寻该对象的原型，以及该对象的原型的原型，依次层层向上搜索，直到找到一个名字匹配的属性或到达原型链的末尾。这种行为是在模拟经典的继承，但是与其说是继承，不如说是委托（`delegation`）。

    - `__proto__`:
      每个JS对象一定对应一个原型对象，并从原型对象继承属性和方法。

      ```javascript
      var one = {x: 1};
      var two = new Object();
      one.__proto__ === Object.prototype // true
      two.__proto__ === Object.prototype // true
      one.toString === one.__proto__.toString // true
      ```

    - `prototype`
      只有函数才有`prototype`
      - 当你创建函数时，JS会为这个函数自动添加`prototype`属性，值是一个有`constructor`属性的对象，不是空对象。而一旦你把这个函数当作构造函数(`constructor`)调用(即通过`new`关键字调用)，那么JS就会帮你创建该构造函数的实例，实例继承构造函数`prototype`的所有属性和方法(实例通过设置自己的`__proto__`指向构造函数的`prototype`来实现这种继承)。
      - 构造函数，通过`prototype`来存储要共享的属性和方法，也可以设置`prototype`指向现存的对象来继承该对象
      - 对象的`__proto__`指向自己构造函数的`prototype`。由此产生原型链`obj.__proto__.__proto__...`，`instanceof`就是依靠原型链来判断类型的
      - JS是单继承的，`Object.prototype`是原型链的顶端，所有对象从它继承了包括`toString`等方法和属性。
      - 注意：

      ```javascript
      Object instanceof Function // true
      Function instanceof Object // true
      ```

    鸡和蛋的问题，为什么？
    - `Function`本身就是函数，`Function.__proto__`是标准的内置对象`Function.prototype`
      `Function.prototype.__proto__`是标准的内置对象`Object.prototype`

    总结：
    - `Function.prototype`和`Function.__proto__`都指向`Function.prototype`，可是`Function.prototype.__proto__ === Object.prototype //true`，而`Function.prototype.__proto__ === Function.prototype //false`
    - `Object.prototype.__proto__ === null`，说明原型链到`Object.prototype`终止。

    结论：
    - 原型链的尽头(root)是`Object.prototype`。**所有对象均从`Object.prototype`继承属性**
    - `Function.prototype`和`Function.__proto__`为**同一对象**。
    *`Object/Array/String`等等构造函数本质上和`Function`一样，均继承于`Function.prototype`*
    - `Function.prototype`直接继承root(Object.prototype)。
    *继承的原型链：`Object.prototype(root)<--Function.prototype<--Function|Object|Array...`*
    - 实例的 `__proto__` 属性（原型）等于其构造函数的 `prototype` 属性。
    此处实例的方法只能找到构造函数为止，不能再向上。

1. #### 拓展原型链的方法

    - `New-initialization`
      - 例子：

      ```javascript
      function foo() {}

      foo.prototype = {
        foo_prop: "foo val"
      };

      function bar() {}

      var proto = new foo;
      proto.bar_prop = "bar val";
      bar.prototype = proto;
      var inst = new bar;
      console.log(inst.foo_prop); // foo val
      console.log(inst.bar_prop); // bar val
      ```

      - 优势：支持目前以及所有可想象到的浏览器( `IE5.5` 都可以使用)。 这种方法非常快，非常符合标准，并且充分利用 `JIT` 优化。
      - 缺陷：为使用此方法，必须对相关函数初始化。 在初始化过程中，构造函数可以存储每个对象必须生成的唯一信息。但是，这种唯一信息只生成一次，可能会带来潜在的问题。此外，构造函数的初始化，可能会将不需要的方法放在对象上。然而，如果你只在自己的代码中使用，你也清楚（或有通过注释等写明）各段代码在做什么，这些在大体上都不是问题（事实上，通常是有益处的）。

    - `Object.create`
      - 例子：

      ```javascript
      function foo() {}

      foo.prototype = {
        foo_prop: "foo val"
      };
      
      function bar() {}

      var proto = Object.create(
        foo.prototype
      );
      proto.bar_prop = "bar val";
      bar.prototype = proto;
      var inst = new bar;
      console.log(inst.foo_prop); // foo val
      console.log(inst.bar_prop); // bar val

      --------------------------------------

      function foo() {}

      foo.prototype = {
        foo_prop: "foo val"
      };

      function bar() {}

      var proto = Object.create(
        foo.prototype,
        {
          bar_prop: {
            value: "bar val"
          }
        }
      );
      bar.prototype = proto;
      var inst = new bar;
      console.log(inst.foo_prop); // foo val
      console.log(inst.bar_prop); // bar val
      ```

      - 优势：支持当前所有非微软版本或者 `IE9` 以上版本的浏览器。允许一次性地直接设置 `__proto__` 属性，以便浏览器能更好地优化对象。同时允许通过 `Object.create(null)` 来创建一个没有原型的对象。
      - 缺陷：不支持 `IE8` 以下的版本。然而，随着微软不再对系统中运行的旧版本浏览器提供支持，这将不是在大多数应用中的主要问题。另外，这个慢对象初始化在使用第二个参数的时候有可能成为一个性能黑洞，因为每个对象的描述符属性都有自己的描述对象。当以对象的格式处理成百上千的对象描述的时候，可能会造成严重的性能问题。
    - `Object.setPrototypeOf`
      - 例子：

      ```javascript
      function foo() {}

      foo.prototype = {
        foo_prop: "foo val"
      };

      function bar() {}

      var proto = {
        bar_prop: "bar val"
      };

      Object.setPrototypeOf(
        proto, foo.prototype
      );
      bar.prototype = proto;
      var inst = new bar;
      console.log(inst.foo_prop); // foo val
      console.log(inst.bar_prop); // bar val

      --------------------------------

      function foo() {}

      foo.prototype = {
        foo_prop: "foo val"
      };

      function bar() {}

      var proto;
      proto = Object.setPrototypeOf(
        { bar_prop: "bar val" },
        foo.prototype
      );
      bar.prototype = proto;
      var inst = new bar;
      console.log(inst.foo_prop); // foo val
      console.log(inst.bar_prop); // bar val
      ```

      - 优势：支持所有现代浏览器和微软 `IE9+` 浏览器。允许动态操作对象的原型，甚至能强制给通过 `Object.create(null)` 创建出来的没有原型的对象添加一个原型。
      - 缺陷：这个方式表现并不好，应该被弃用。如果你在生产环境中使用这个方法，那么快速运行 `Javascript` 就是不可能的，因为许多浏览器优化了原型，尝试在调用实例之前猜测方法在内存中的位置，但是动态设置原型干扰了所有的优化，甚至可能使浏览器为了运行成功，使用完全未经优化的代码进行重编译。 不支持 `IE8` 及以下的浏览器版本。
    - `__proto__`
      - 例子：

      ```javascript
      function foo() {}

      foo.prototype = {
        foo_prop: "foo val"
      };

      function bar() {}

      var proto = {
        bar_prop: "bar val",
        __proto__: foo.prototype
      };
      bar.prototype = proto;
      var inst = new bar;
      console.log(inst.foo_prop); // foo val
      console.log(inst.bar_prop); // bar val

      --------------------------------

      var inst = {
        __proto__: {
          bar_prop: "bar val",
          __proto__: {
            foo_prop: "foo val",
            __proto__: Object.prototype
          }
        }
      };
      console.log(inst.foo_prop); // foo val
      console.log(inst.bar_prop); // bar val
      ```

      - 优势：支持所有现代非微软版本以及 `IE11` 以上版本的浏览器。将 `__proto__` 设置为非对象的值会静默失败，并不会抛出错误。
      - 缺陷：应该完全将其抛弃因为这个行为完全不具备性能可言。 如果你在生产环境中使用这个方法，那么快速运行 `Javascript` 就是不可能的，因为许多浏览器优化了原型，尝试在调用实例之前猜测方法在内存中的位置，但是动态设置原型干扰了所有的优化，甚至可能使浏览器为了运行成功，使用完全未经优化的代码进行重编译。不支持 `IE10` 及以下的浏览器版本。

1. #### `prototype` 和 `Object.getPrototypeOf` 的区别

    `var a1 = new A(); var a2 = new A();`
    那么 `a1.doSomething` 事实上会指向 `Object.getPrototypeOf(a1).doSomething`，它就是你在 `A.prototype.doSomething` 中定义的内容。也就是说：`Object.getPrototypeOf(a1).doSomething == Object.getPrototypeOf(a2).doSomething == A.prototype.doSomething`（补充：实际上，执行 `a1.doSomething()` 相当于执行 `Object.getPrototypeOf(a1).doSomething.call(a1)==A.prototype.doSomething.call(a1)`）

    简而言之，`prototype` 是用于类的，而 `Object.getPrototypeOf()` 是用于实例的（instances），两者功能一致。

    当执行 `var o = new Foo();`，实际执行：

    ```javascript
    var o = new Object();
    o.__proto__ = Foo.prototype;
    Foo.call(o);
    ```

1. #### 你怎么看 `AMD vs CommonJS`？

    `CommonJS, AMD, CMD` 都是 `Javascript` 模块化的规范。
    `CommonJS` 是服务器端 `Javascript` 模块化的规范，`NodeJS` 是这种规范的实现。
    `AMD`(异步模块定义)和 `CMD`(通用模块定义)都是浏览器端 `Javascript` 模块化的规范。`RequireJS` 遵循的是 `AMD，SeaJS` 遵循的是 `CMD`。

    - CommonJS
    一个单独的文件就是一个模块。加载模块使用 `require` 方法，该方法读取一个文件并执行，最后返回文件内部的 `exports` 对象。定义模块就是写新的 `Javascript` 文件，再通过 `exports` 导出。
    `CommonJS` 加载模块是同步的，所以只有加载完成才能执行后面的操作。像 `Node.js` 主要用于服务器的编程，加载的模块文件一般都已经存在本地硬盘，所以加载起来比较快，不用考虑异步加载的方式，所以 `CommonJS` 规范比较适用。**`CommonJS` 模块的设计考虑了服务器开发**。但如果是浏览器环境，要从服务器加载模块，这是就必须采用异步模式。所以就有了 `AMD、CMD` 解决方案。

      ```javascript
      // module.js
      var A = function () {
        console.log('我是定义的模块');
      }
      // 导出这个模块
      // 1.第一种返回方式 module.exports = A; 
      // 2.第二种返回方式 module.exports.test = A
      // 3.第三种返回方式 exports.test = A;
      exports.test = A;

      // test.js
      var module = require("./module"); // 加载这个模块

      // 调用这个模块，不同的返回方式用不同的方式调用
      // 1.第一种调用方式 module();
      // 2.第二种调用方式 module.test();
      // 3.第三种调用方式 module.test();
      module.test();
      ```

      优点：
      - 简单：开发人员无需查看文档即可掌握概念。
      - 集成了依赖管理：模块需要其他模块并按所需顺序加载。
      - `require` 可以在任何地方调用：可以以编程方式加载模块。
      - 支持循环依赖。

      缺点：
      - 同步 `API` 使其不适合某些用途（客户端）。
      - 每个模块一个文件。
      - 浏览器需要加载程序库或转译。
      - 没有模块的构造函数（尽管 `Node` 支持这个）。
      - 静态代码分析器很难分析。

    - AMD（异步模块定义）
    `AMD` 规范通过 `define` 方法去定义模块，通过 `require` 方法去加载模块。`RequireJS` 实现了这种规范。**`AMD` 和 `CommonJS` 之间的主要区别在于它支持异步模块加载**。
    `AMD` 只有一个接口：`define(id?,dependencies?,factory);` 它要在声明模块的时候制定所有的依赖(`dep`)，并且还要当做形参传到 `factory` 中。要是没什么依赖，就定义简单的模块（或者叫独立的模块）

      ```javascript
      // 编写一个 module1.js 文件
      // 定义独立的模块
      define({
        methodA: function () {
          console.log('我是module1的methodA');
        },
        methodB: function () {
          console.log('我是module1的methodB');
        }
      });

      // 编写一个 module2.js 文件
      // 另一种定义独立模块的方式
      define(function () {
        return {
          methodA: function () {
            console.log('我是module2的methodA');
          },
          methodB: function () {
            console.log('我是module2的methodB');
          }
        };
      });

      // 编写一个 module3.js 文件
      // 定义非独立的模块（这个模块依赖其他模块）
      define(['module1', 'module2'], function (m1, m2) {
        return {
          methodC: function () {
            m1.methodA();
            m2.methodB();
          }
        };
      });

      // 再定义一个 main.js，去加载这些个模块
      require(['module3'], function (m3) {
        m3.methodC();
      });

      // 我们在一个 html 文件中去通过 RequireJS 加载这个 main.js
      // 等号右边的 main 指的 main.js
      <script data-main="main" src="require.js"></script>

      //浏览器控制台输出结果
      我是module1的methodA
      我是module2的methodB
      ```

      优点
      - 异步加载（更好的启动时间）。
      - 支持循环依赖。
      - 兼容 `exports` 和 `require`。
      - 依赖管理完全集成。
      - 如有必要，模块可以拆分为多个文件。
      - 支持构造函数。
      - 插件支持（自定义加载步骤）。

      缺点
      - 语法上稍微复杂一些。
      - 除非转译，否则需要加载程序库。
      - 静态代码分析器很难分析。

    - CMD（通用模块定义）
      `CMD` 是 `SeaJS` 在推广过程中对模块定义的规范化产出。

      `AMD` 和 `CMD` 的区别：

      1. 对于依赖的模块，`AMD` 是提前执行，`CMD` 是延迟执行。不过 `RequireJS` 从 2.0 开始，也改成可以延迟执行（根据写法不同，处理方式不同）。`CMD` 推崇 `as lazy as possible`（尽可能的懒加载，也称为延迟加载，即在需要的时候才加载）。

      2. `CMD` 推崇依赖就近，`AMD` 推崇依赖前置。虽然 `AMD` 也支持 `CMD` 的写法，同时还支持将 `require` 作为依赖项传递
      3. `AMD` 的 `API` 默认是一个当多个用，`CMD` 的 `API` 严格区分，推崇职责单一。比如 `AMD` 里，`require` 分全局 `require` 和局部 `require`，都叫 `require`。`CMD` 里，没有全局 `require`，而是根据模块系统的完备性，提供 `seajs.use` 来实现模块系统的加载启动。`CMD` 里，每个 `API` 都简单纯粹。

      ```javascript
      // CMD
      define(function (require, exports, module) {
        var a = require('./a');
        a.doSomething();
        // 此处略去 100 行
        var b = require('./b');   // 依赖可以就近书写
        b.doSomething();
        // ... 
      })

      // AMD 默认推荐的是
      define(['./a', './b'], function (a, b) { // 依赖必须一开始就写好
        a.doSomething();
        // 此处略去 100 行
        b.doSomething();
        //...
      })
      ```

    - ES2015 模块

      ```javascript
      //------ lib.js ------
      export const sqrt = Math.sqrt;
      export function square(x) {
        return x * x;
      }
      export function diag(x, y) {
        return sqrt(square(x) + square(y));
      }

      //------ main.js ------
      import { square, diag } from 'lib';
      console.log(square(11)); // 121
      console.log(diag(4, 3)); // 5
      ```

      优点
      - 支持同步和异步加载。
      - 语法简单。
      - 支持静态分析工具。
      - 集成在语言中（最终到处都支持，不需要库）。
      - 支持循环依赖。

      缺点
      - 仍然不支持所有地方。

1. #### 请解释为什么接下来这段代码不是 `IIFE` (立即调用的函数表达式)：`function foo(){ }();`，要做哪些改动使它变成 `IIFE`?

    `IIFE（Immediately Invoked Function Expressions）`代表立即执行函数。它是一个匿名函数，在被创建然后立即调用。它不是从其他任何地方调用的（因此它是匿名的），而是在创建后立即运行。 `JavaScript` 解析器将 `function foo(){ }();` 解析成 `function foo(){ }和();`。其中，前者是函数声明；后者 `()` 是试图调用一个函数，却没有指定名称，因此它会抛出 `Uncaught SyntaxError: Unexpected token )` 的错误。

    **目的：造出一个函数作用域，申请局部变量，防止污染全局变量。**

    修改方法是：再添加一对括号，形式上有两种：`(function foo(){ })()` 和 `(function foo(){ }())`。以上函数不会暴露到全局作用域，如果不需要在函数内部引用自身，可以省略函数的名称。

    你可能会用到 `void` 操作符：`void function foo(){ }();`。但是，这种做法是有问题的。表达式的值是 `undefined`，所以如果你的 `IIFE` 有返回值，不要用这种做法。例如：

    ```javascript
    // void 运算符 对给定的表达式进行求值，然后返回 undefined。
    // void expression

    const foo = void (function bar() {
      return 'foo';
    })();

    console.log(foo); // undefined
    ```

1. #### 描述以下变量的区别：`null，undefined` 或 `undeclared`？该如何检测它们？

    当你没有提前使用 `var、let` 或 `const` 声明变量，就为一个变量赋值时，该变量是未声明变量（`undeclared variables`）。未声明变量会脱离当前作用域，成为全局作用域下定义的变量。在严格模式下，给未声明的变量赋值，会抛出 `ReferenceError` 错误。和使用全局变量一样，使用未声明变量也是非常不好的做法，应当尽可能避免。要检查判断它们，需要将用到它们的代码放在 `try/catch` 语句中。

    ```javascript
    function foo() {
      x = 1; // 在严格模式下，抛出 ReferenceError 错误
    }

    foo();
    console.log(x); // 1
    ```

    当一个变量已经声明，但没有赋值时，该变量的值是 `undefined`。如果一个函数的执行结果被赋值给一个变量，但是这个函数却没有返回任何值，那么该变量的值是 `undefined`。要检查它，需要使用严格相等（`===`）；或者使用 `typeof`，它会返回 `'undefined'` 字符串。请注意，不能使用非严格相等（`==`）来检查，因为如果变量值为 `null`，使用非严格相等也会返回 `true`。

    ```javascript
    var foo;
    console.log(foo); // undefined
    console.log(foo === undefined); // true
    console.log(typeof foo === 'undefined'); // true

    console.log(foo == null); // true. 错误，不要使用非严格相等！

    function bar() {}
    var baz = bar();
    console.log(baz); // undefined
    ```

    `null` 只能被显式赋值给变量。它表示空值，与被显式赋值 `undefined` 的意义不同。要检查判断 `null` 值，需要使用严格相等运算符。请注意，和前面一样，不能使用非严格相等（`==`）来检查，因为如果变量值为 `undefined`，使用非严格相等也会返回 `true`。

    ```javascript
    var foo = null;
    console.log(foo === null); // true

    console.log(foo == undefined); // true. 错误，不要使用非严格相等！
    ```

    作为一种个人习惯，我从不使用未声明变量。如果定义了暂时没有用到的变量，我会在声明后明确地给它们赋值为 `null`。

    [**声明与未声明的全局变量**](https://stackoverflow.com/questions/15985875/effect-of-declared-and-undeclared-variables)
    它们的存储和访问机制是相同的，但是在某些情况下 `JavaScript` 会根据 `configurable` 的值来处理。在正常情况下，它们的行为应该是一样的。
    - 都存在全局对象

      ```javascript
      var declared = 1;  // 显式全局变量（新变量）
      undeclared   = 1;  // 隐式全局变量（默认全局对象的属性）

      window.hasOwnProperty('declared')    // true
      window.hasOwnProperty('undeclared')  // true

      window.propertyIsEnumerable('declared')    // true
      window.propertyIsEnumerable('undeclared')  // true

      window.declared     // 1
      window.undeclared   // 1

      window.declared   = 2;
      window.undeclared = 2;

      declared     // 2
      undeclared   // 2

      delete declared     // false
      delete undeclared   // true
      delete undeclared   // true (same result if delete it again)

      delete window.declared     // false
      delete window.undeclared   // true (same result if delete it yet again)
      delete window.undeclared   // true (still true)
      ```

      已声明和未声明的全局变量都是窗口对象(默认全局对象)的属性。两者都不是通过原型链从不同的对象继承的。它们都直接存在于 `window` 对象中(`window.hasOwnProperty === true`)
    - `The configurable attribute`
      对于声明的全局变量，`configurable` 的值为 `false`。对于未声明的全局变量，`configurable` 的值为 `true`。可以通过 `getOwnPropertyDescriptor` 方法检索可配置属性的值，如下所示:

      ```javascript
      var declared = 1;
      undeclared = 1;

      (Object.getOwnPropertyDescriptor(window, 'declared')).configurable // false
      (Object.getOwnPropertyDescriptor(window, 'undeclared')).configurable // true
      ```

      如果属性的基本属性（`attribute`）`configurable` 为 `true`，则可以使用 `defineProperty` 方法更改该属性的基础属性，并且可以使用 `delete` 操作符删除该属性。否则，将无法更改属性，也无法通过这种方式删除属性。
      在非严格模式下，如果属性是可配置的，则 `delete` 操作符返回 `true`，如果属性是不可配置的，则返回 `false`。

    - 总结
      - 声明全局变量
        - 默认全局对象（窗口）的属性
        - 属性的基础属性（`The property attributes`）不可更改
        - 不能使用 `delete` 删除
      - 未声明全局变量
        - 默认的全局对象（窗口）的属性
        - 属性的基础属性（`The property attributes`）可更改
        - 可以使用 `delete` 删除

1. #### 什么是闭包 (`closure`)，如何使用它，为什么要使用它？

    闭包是指有权访问另一个函数作用域中的变量的函数。

    ```javascript
    function F1() {
      var n = 0;
      return function () {
        n += 1;
      }
    }
    let adder = F1();
    adder(); // n === 1
    adder(); // n === 2
    console.log(n); // n is not defined
    ```

    可概括为：「函数」和「函数内部能访问到的变量」（也叫环境）的总和，就是一个闭包。
    闭包的作用：
    - 闭包常常用来「间接访问一个变量」。换句话说，「隐藏一个变量」。
    - 一个是可以读取函数内部的变量，另一个就是让这些变量的值始终保持在内存中。
    - 利用闭包实现数据私有化或模拟私有方法。这个方式也称为[模块模式（`module pattern`）](https://addyosmani.com/resources/essentialjsdesignpatterns/book/#modulepatternjavascript)
    - 部分参数函数（partial applications）柯里化（currying）.

1. #### 请举出一个匿名函数的典型用例？

1. #### 你是如何组织自己的代码？是使用模块模式，还是使用经典继承的方法？

1. #### 请指出 `JavaScript` 宿主对象 (`host objects`) 和原生对象 (`native objects`) 的区别？

1. #### 请指出以下代码的区别：`function Person(){}`、`var person = Person()`、`var person = new Person()`？

1. #### `.call` 和 `.apply` 的区别是什么？

1. #### 请解释 `Function.prototype.bind`？

1. #### 在什么时候你会使用 `document.write()`？

1. #### 请指出浏览器特性检测，特性推断和浏览器 `UA` 字符串嗅探的区别？

1. #### 请尽可能详尽的解释 `Ajax` 的工作原理

1. #### 使用 `Ajax` 都有哪些优劣？

1. #### 请解释 `JSONP` 的工作原理，以及它为什么不是真正的 `Ajax`

1. #### 你使用过 `JavaScript` 模板系统吗？如有使用过，请谈谈你都使用过哪些库？

1. #### 请解释变量声明提升 (`hoisting`)

1. #### 请描述事件冒泡机制 (`event bubbling`)

1. #### `attribute` 和 `property` 的区别是什么？

1. #### 为什么扩展 `JavaScript` 内置对象不是好的做法？

1. #### 请指出 `document load` 和 `document DOMContentLoaded` 两个事件的区别

1. #### `==` 和 `===` 有什么不同？

1. #### 请解释 `JavaScript` 的同源策略 (`same-origin policy`)

1. #### 如何实现下列代码：`[1,2,3,4,5].duplicator(); // [1,2,3,4,5,1,2,3,4,5]`

1. #### 什么是三元表达式 (`Ternary expression`)？“三元 (`Ternary`)” 表示什么意思？

1. #### 什么是 `"use strict";` ? 使用它的好处和坏处分别是什么？

1. #### 请实现一个遍历至 100 的 `for loop` 循环，在能被 3 整除时输出 `"fizz"`，在能被 5 整除时输出 `"buzz"`，在能同时被 3 和 5 整除时输出 `"fizzbuzz"`

1. #### 为何通常会认为保留网站现有的全局作用域 (`global scope`) 不去改变它，是较好的选择？

1. #### 为何你会使用 `load` 之类的事件 (`event`)？此事件有缺点吗？你是否知道其他替代品，以及为何使用它们？

1. #### 请解释什么是单页应用 (`single page app`), 以及如何使其对搜索引擎友好 (`SEO-friendly`)

1. #### 你使用过 `Promises` 及其 `polyfills` 吗? 请写出 `Promise` 的基本用法（`ES6`）

1. #### 使用 `Promises` 而非回调 (`callbacks`) 优缺点是什么？

1. #### 使用一种可以编译成 `JavaScript` 的语言来写 `JavaScript` 代码有哪些优缺点？

1. #### 你使用哪些工具和技术来调试 `JavaScript` 代码？

1. #### 你会使用怎样的语言结构来遍历对象属性 (`object properties`) 和数组内容？

1. #### 请解释可变 (`mutable`) 和不变 (`immutable`) 对象的区别。请举出 `JavaScript` 中一个不变性对象 (`immutable object`) 的例子？不变性 (`immutability`) 有哪些优缺点？如何用你自己的代码来实现不变性 (`immutability`)？

1. #### 请解释同步 (`synchronous`) 和异步 (`asynchronous`) 函数的区别

1. #### 什么是事件循环 (`event loop`)？请问调用栈 (`call stack`) 和任务队列 (`task queue`) 的区别是什么？

1. #### 解释 `function foo() {}` 与 `var foo = function() {}` 用法的区别

1. #### ES6-至今 新特性

### 测试相关问题

1. #### 对代码进行测试的有什么优缺点？

1. #### 你会用什么工具测试你的代码功能？

1. #### 单元测试与功能/集成测试的区别是什么？

1. #### 代码风格 `linting` 工具的作用是什么？

### 效能相关问题

1. #### 你会用什么工具来查找代码中的性能问题？

1. #### 你会用什么方式来增强网站的页面滚动效能？

1. #### 请解释 `layout、painting` 和 `compositing` 的区别

### 网络相关问题

1. #### 为什么传统上利用多个域名来提供网站资源会更有效？

1. #### 请尽可能完整得描述从输入 `URL` 到整个网页加载完毕及显示在屏幕上的整个流程

1. #### `Long-Polling、Websockets` 和 `Server-Sent Event` 之间有什么区别？

1. #### 请描述以下 `request` 和 `response headers`

     - Diff. between `Expires, Date, Age and If-Modified-...`
     - `Do Not Track`
     - `Cache-Control`
     - `Transfer-Encoding`
     - `ETag`
     - `X-Frame-Options`

1. #### 什么是 `HTTP method`？请罗列出你所知道的所有 `HTTP method`，并给出解释

1. #### 请解释 `HTTP status 301` 与 `302` 的区别？

### 代码相关的问题

- #### 问题：`foo` 的值是什么？

  `var foo = 10 + '20';`

- #### 问题：如何实现以下函数？

  ```javascript
  add(2, 5); // 7
  add(2)(5); // 7
  ```

- #### 问题：下面的语句的返回值是什么？

  `"i'm a lasagna hog".split("").reverse().join("");`

- #### 问题：`window.foo` 的值是什么？

  `( window.foo || ( window.foo = "bar" ) );`

- #### 问题：下面两个 `alert` 的结果是什么？

  ```javascript
  var foo = "Hello";
  (function() {
    var bar = " World";
    alert(foo + bar);
  })();
  alert(foo + bar);
  ```

- #### 问题：`foo.length` 的值是什么？

  ```javascript
  var foo = [];
  foo.push(1);
  foo.push(2);
  ```

- #### 问题：`foo.x` 的值是什么？

  ```javascript
  var foo = {n: 1};
  var bar = foo;
  foo.x = foo = {n: 2};
  ```

- #### 问题：下面代码的输出是什么？

  ```javascript
  console.log('one');
  setTimeout(function() {
    console.log('two');
  }, 0);
  console.log('three');
  ```

### 趣味问题

- #### 你最近写过什么的很酷的项目吗？

- #### 在你使用的开发工具中，最喜欢哪些方面？

- #### 谁使你踏足了前端开发领域？

- #### 你有什么业余项目吗？是哪种类型的？

- #### 你最爱的 `IE` 特性是什么？

- #### 你对咖啡有没有什么喜好？
