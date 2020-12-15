# JavaScript篇笔记

★★★★★★★★★★
更新于2019-01-10
这是自己平时书写的笔记，有很多漏洞，也有很多错误，还望大家多多指正，感谢🙏
★★★★★★★★★★

1. `<html>`、`<head>`、`<body>`在相应情况下可省略。而`<title>`是必须写的。
2. 除了`<div>`、`<span>`其它标签都有默认样式。
3. ★可以出现在`<head>`元素内的标签有`<meta>`、`<link>`、`<title>`、`<style>`、`<script>`、`<noscript>`、`<base>`。
4. ★`<noscript>`如果用户浏览器不支持`<script>`则会显示`<noscript>`的内容来替代。
5. ★CSS 可替换元素的展现不是由 CSS 来控制的。这些元素是一类外观渲染独立于 CSS 的外部对象。典型的可替换元素有 `<img>`、 `<object>`、 `<video>` 和 表单元素，如`<textarea>`、 `<input>` 。某些元素只在一些特殊情况下表现为可替换元素，例如 `<audio>` 和 `<canvas>` 。 通过 CSS content 属性来插入的对象被称作**匿名可替换元素（anonymous replaced elements）**。
6. ★HTML 中的空元素：`<area>`、`<base>`、`<br>`、`<col>`、`<colgroup>(when span is parent)`、`<command>`、`<embed>`、`<hr>`、`<img>`、`<input>`、`<keygen>`、`<link>`、`<meta>`、`<param>`、`<source>`、`<track>`、`<wbr>`，在极少数的情况下，空元素被错误地称为“无效元素”(void element)
7. body 标签的默认 `margin` 是8像素。
8. `button` 和 `input` 设置按钮时的区别：`input` 为空元素，里面不能加东西，`button` 里面可以添加东西！
9. 当你在加载图片的时候会把加载的图片放在原来 `img` 的地方替换它。
10. ★可替换元素自带宽高，可以设置属性覆盖，还可以设置 CSS 覆盖属性。
11. 全局属性 `contenteditable` 是一个枚举属性，表示元素是否可被用户编辑。如果可以，浏览器会修元素的部件(widget)以允许编辑。该属性必须为下面的值之一：

    ----- true或空字符串，表示元素是可编辑的
  
    ----- false，表示元素是不可编辑的

    如果没有设置该属性，其默认值继承自父元素

    该属性是一个枚举属性，而非布尔属性，这意味着必须显式设置其值为 `true`、`false` 或空字符串中的一个，并且不允许简写为`<label contenteditable>Ex</label>`(注：这在大部分浏览器中是有效的)，正确的用法是`<label contenteditable="true">Ex</label>`

12. `a` 标签里面的 `target` 属性：
    - `_black`：在空白页打开，相当于打开新页面
    - `_self`：在本窗口打开
    - `_parent`：在父窗口打开
    - `_top`：在最上层窗口打开，例如爷孙三代就在爷爷页面打开
13. `a` 标签的 `download` 属性：文件没有 `download` 也能下载，那是为什么？由 http 响应决定，如果你将 http 的 `content-type` 设置为 `content-type:application/octec-stream` ，则表示任意二进制保存;若没有那个，而是 `text/html` ，则可以用 `download` 属性。
14. ★`http-server -c -1` 不要缓存
15. `a` 标签的 `href` ：
    - "#"：表示锚点会在页面内的跳转，不发请求
    - "?name=ex"：会在页面发起 GET 请求并在路径后面加上参数 ?name=ex
    - JS伪协议："javascript: ;"是可点击，但什么也不做的链接
    - ""：会刷新页面
    - 无协议//……会根据本页面协议，相对路径
16. `a` 标签跳转页面是发 GET 请求，`form` 标签跳转页面是发 POST 请求，`form` 默认GET，`form` 表单里必须有提交按钮
17. file 协议不支持 POST
18. GET 会默认让数据出现在查询参数中，会在地址栏中看到，可通过给 `action` 传值让 POST 也有查询参数
19. `form` 标签的 `target` 与 `a` 标签一样
20. `form` 中的 `button` 和 `input`
    1. 一个 `form` 中如果 `button` 标签的 `type` 属性未设置 `submit` 值，且为空，则它会自动升级为 `submit` 值可提交，若 `type` 设置为 `button` 则不会改变值类型
    2. `input` 则不会有以上问题， `type` 设置什么就是什么
    3. `input` 标签

        - `checkbox` 中若希望点击文字也可以勾选 `checkbox` 选项则：`<input type="checkbox" id="yourId"><label for="yourId">选择</label>`,老司机写法：不用 `id` 和 `for` ，直接用 `label` 把 `input` 包起来

        - `checkbox` 默认未选中不显示，选中值为 `on` ，可通过 `value` 进行更改

        - ：`radio` 同名时只能有一个被选中。
21. `select` 下拉菜单：

    ```javascript
    <select name="nameA">
      <option value="分组"></option>
      <option value="1" disabled>不能选</option>
      <option value="2" selected>默认选中</option>
    </select>
    <select name="group" multiple>可多选</select>
    ```

22. `textarea` 标签：用 css 设置其表现样式比较好
23. `table` 标签：
    - 允许的子标签：(1可选) `<thead>` (表头)、(0-more) `<tbody>` (表体)、(1可选) `<tfoot>` (表脚)、(1可选) `<caption>` 、(0-more) `<colgroup>` 、(1-more) `<tr>`
    - `<tr>` (表行 table row)、 `<td>` (表数据 table data)、 `<th>` 表标题 table header)
    - `<colgroup><col width=100>控制每列宽度</colgroup>`
    - 若 `<thead><tbody><tfoot>` 顺序错乱，但浏览器会自动纠正显示顺序，所以最后成像与四个标签顺序无关，若没有头、身体、尾，则会全部放在自动生成但 `<tbody>` 里，然后按照代码顺序一次排列
    - `table` 的 `border` 默认有空间；可以使用 `border-collapse: collapse;` 去掉空隙
24. `@import url(./b.css);` 在 css 页面引用 css
25. 四种修改样式方法：
    1. 内联 style 属性
    2. style 标签
    3. 外部文件 css link
    4. `@import url(./b.css)`
26. 如何去除无序列表中小圆点的样式：在 ul 中使用 `list-style: none;`
27. 使元素并列可用 `float`，用 `float` 就会有 bug，就直接用 `.clearfix::after{}` 类来解决 bug。

    ```css
    .clearfix::after{
        content: '';
        display: block;
        clear: both;
    }/*注：将此类加在所有浮动元素的爸爸身上*/
    ```

28. `.className>nav>ul {}`：ul 必须是 nav 的儿子，nav 必须是 .className 的儿子
29. 默认字体大小 16px (计算出的样式，浏览器不同可能不同)
30. 颜色最终由浏览器渲染得出，不一定是你代码的颜色
31. `text-decoration: none;` 无下划线 || `underline wavy red;` 红色波浪下划线
32. 为避免为之后元素加边框会影响元素变动，可在之前元素上加上相同大小的透明边框
33. 如何解决儿子高度比父亲大？在子元素上加上 `display: block;`
34. `color: inherit;` 继承父元素颜色
35. 最后准确的指定标签的 class
36. `line-height` 的值是多少像素，那么 `span` 所占的高度就是多少像素。`line-height` 是可以确定内联元素高度的
37. `div` 的高度是由其内部文档流元素的高度总和决定
38. 文档流：文档内元素的流动方向。内联元素从左到右，遇到宽度不够就换行；块级元素，每个块各占一行，从上往下。

    现象：
    1. 一个 `span` 不会被分成两块 `span` ，而是两部分
    2. 但英文很长且连贯时，不会换行，可用 `word-break` 属性强制换行
39. 内联元素但高由什么决定？
    - `font-size: 100px;` 是指文中文字最高但一点到最低但一点但高度，例如 haaag，中但 h 的最上面和 g 的最下面，没有 hg 也是这样。
    - `span` 含有文字时，会自动适应高度，此高度为建议行高比 `font-size` 更高，各字体不一样。
    - 水平对齐：其实是基线对齐，而不是真的中线对齐
40. `position: fixed;` 脱离文档流，相对于 屏幕固定，原来有空隙的宽度会被省略掉，缩减到刚好包含有宽度的元素，可用 `width: 100%;` 恢复原状(但有 bug)，再在外面加一个 `div` 影响样式最好
41. `max-width`: 有自适应
42. 如果你有一个 `div` 还有宽度，用 `margin-left: auto; margin-right: auto;` 就会自动居中
43. 内联元素不接受宽高，可用 `display: inline-block` (少用)
44. 绝对定位脱离文档流：

    1. 在子元素上写 `position: absolute;`
    2. 在父元素上写 `position: relative;`
45. 有时出现偏上方没有居中，可用 `vertical-align: top;` 一般是加了 `display: inline-block` 的 bug
46. `position` 的取值：

    1. sticky
    2. absolute
    3. fixed
    4. relative
    5. static
47. `line-height` 的默认值为 `normal`，大家常理解 `normal` 相当于 `line-height` 的值是1或者1.2，当 `line-height` 的值为数字时(而非像素)，表示是 `font-size` 的倍数。chrome 的默认 `line-height` 值是 `font-size` 的1.3倍
48. css 中 `width` 的默认值是 `auto`
49. 两种盒子模型尺寸基准：

    1. `content-box` (默认)
    2. `border-box`

    区别：`content-box` 的 `width` 不包括 `padding` 和 `border` 的宽(高)度；而 `border-box` 的 `width` 包括
50. ★HTML 结构为 `div.parent > div.child` ，如果 parent 为 `{width: 1000px; padding: 100px;}` 那么 div.child 所占宽度为 1000px
51. 默认 `div` 中有 `::before` 和 `::after` ，`div` 中输入的元素一般在其两者之间
52. 伪元素不是元素，无法选中，复制
53. 伪元素 `::before` 和 `::after` 默认不出现，可以加入内容 `{content: "";}` 就会显示效果
54. 绝对定位就会自动变为 `display: block;`
55. 元素只能有一级伪元素，伪元素不能有伪元素之类
56. 内联元素想居中，需要在其父元素上设置居中
57. 可用 `box-sizing` 来改变盒模型的测量方案，来实现重新计算宽高
58. 一个 `:` 是伪类，两个 `::` 是伪元素
59. 最好只使用 `padding + border-box`，`margin` 在 IE 下有 bug
60. 结构化编程：

    1. 一行一行地执行
    2. 有条件控制语句 `if…else`
    3. 有循环语句 `while…do`
61. 在 `input` ， `button` 等可点击中使用 `outline: none;` 去掉点击出现等外边框
62. css 将所有小写字母变成大写字母：`text-transform: uppercase`;
63. `prompt`：显示一个对话框，对话框中包含一条文字信息，用来提示用户输入文字。
`result = window.prompt(text,value);` result 用来存储用户输入文字的字符串或 null；text 用来提示用户输入文字的字符串，如果没有任何提示内容，该参数可以省略不写，value 文本输入框中的默认值，该参数也可以省略不写。不过在 Internet Explorer 7 和 8 中，省略该参数会导致输入框中显示默认值"undefined"。
64. `box-shadow`：可扩展阴影效果
65. `background-size: cover;` 保证图片覆盖全屏
66. 网页图标位置：根目录/favicon.ico
67. `display: flex` 居中定位：`justify-content: center; align-items: center;`
68. `canvas` 有宽高，不会因为修改 `inline-block` 而变
69. `canvas` 要先填充再画图，不然先画图再设置属性无作用
70. ★`canvas` 初始宽高不要用 css 控制，图会等比例变化
71. ★使 `canvas` 全屏：

    ```javascript
    window.onresize = function () {
      var pageWidth = document.documentElement.clientWidth;
      var pageHeight = document.documentElement.clientHeight;
      canvasId.width = pageWidth;
      canvasId.height = pageHeight;
    }
    ```

72. `parseInt('1000',2)`：2表示二进制，不填默认是十进制
73. ★`parseFloat('3.14more non-digit')`：值是3.14
74. "`\`"这个 JS 字符串的 `length` 是1，转移符不算
75. "`\u007A`"这个 JS 字符串的 `length` 是1，转移符为一个字符
76. JS 七种数据类型：`number, string, boolean, symbol, null, undefined, object`
77. `0b` 开头为二进制，`0` 开头为八进制，`0x` 开头为十六进制，`e` 表示10，`e2` 表示10^2
78. 多行字符串表达方式：

    ```javascript
    var a = "123 \
           456"//多行字符串，不加"\"会报错
    ```

79. ES6新特性：

    ```javascript
    var a = `123
            456`//多行字符串包含回车
    ```

80. 有一对象，不给值=> `null` => `var obj = null`；有一非对象=> `undefined` => `var obj`
81. 对象访问属性：`person['name']`，访问 `person` 里面 `name` 对应的值，若写成 `person[name]`，则访问 `person` 里面 `name` 的值所对应的值
82. 对象可设置空字符串属性，用 `person['']` 调用，属性也遵循标示符原则(不用引号时)
83. 对象属性加引号功能强大，也可'a b' => `person['a b']` ,不加引号限制多
84. 对象的点属性必须满足标示符原则，否则用 `[]` 访问
85. `person` 有属性 `name` ，无值：`person.name => undefined, 'name' in person => true`
  
    `person` 删除 `name` ，无值：`person.name => undefined, 'name' in person => false`

86. for in

    ```javascript
    for (var key in person) {
      console.log(key, person[key]) // name HeroMeiKong && age 25
      //console.log(key,person.key)不能打印，因为key为字符串
      // name undefined && age undefined
    }
    ```

87. `typeof null` => 'object'；`typeof function` (某个函数) => 'object'
88. JavaScript 引擎不能自动识别编号大于 `0xFFFF` 的 Unicode 字符
89. Base64
    - 全局函数 `atob()` 能将 Base64 编码转为原来的编码
    - 全局函数 `btoa()` 能将字符串转为 Base64 编码
    - 要将非 Ascll 码字符转为 Base64 编码，必须在中间插入一个转码环节
90. 查看所有属性，可用 `object.keys` 方法
91. ★快速转换其他类型为字符串，直接在前或者后面添加 `''` (空字符串)，功能相当于在变量后面加 `toString()` ，而 `null` ， `undefined` 无该属性，而前一种方法也可以将其转化为字符串
92. `Boolean(NaN)` 、 `Boolean(0)` 、 `Boolean('')` 、 `Boolean(null)` 、 `Boolean(undefined)` 都为 `false` ，对象及其它均为 `true`
93. ★老司机用 `!!` 来取布尔值，而不用 `Boolean()`
94. 转 Number？
    - `Number('1') === 1`
    - `parseInt('1',10) === 1` (全局函数，10可省略，默认是10，表示10进制)
    - `parseFloat('1.23') === 1.23`
    - `'1' - 0 === 1` (老司机用)
    - `+ '1' === 1`、`+ '-1' === -1`、`+ '.1' === 0.1`、`- '1' === -1`、`- (- '-1') === -1`
    例如`parseInt('S') === NaN`，`parseInt('1sb') === 1` (能转多少是多少)
95. JS 规定数字存64位，字符存16位(ES5)
96. 当对简单值(如： `var n = 1` ),使用 `toString()` 时，(它本身无此方法)它会临时申请变量 temp( `temp = new Number(n); return temp.toString();` )，此后被回收，用时又创建新的
97. ★`1.toString()` 语法错误 |  `1..toString() === '1'`

    ```javascript
    1toString()                   // error, 语法错误
    1.toString()                  // error, 不确定是点运算还是浮点数
    1..toString()                 // "1", 第一个视为浮点数，第二个视为点运算
    1.0.toString()                // "1", 同上
    1 .toString()                 // "1", 用空格和后面的.toString()隔开, 把前面的当成运算式处理
    1 + 2.toString()                                  // error, 不确定是点运算还是浮点数
    1 + 2 .toString()                                 // "12"
    (1 + 2).toString()                                // "3"
    (1) + (2).toString()                              // "12", 1 + "2"
    (1) + (2) + 0 .toString()                         // "30"
    (1) + (2) + 0 + (11) .toString()                  // "311"
    (1) + (2) + 0 + 12 .toString()                    // "312"
    [1] + [2] + [3]                                   // "123", 数组起拼接作用
    ([1] + [2] + [3]) + 12 .toString()                // "12312"
    ((1) + (2) + [3]) + 12 + 43 .toString()           // "331243"
    (1) + (2) + 13 + "(15)" + 1 + 0 + (1) .toString() // "16(15)101"
    ```

98. 滚动事件： `onscroll`

    `window.scrollY` ：返回文档在垂直方向已滚动对像素值

99. 有时 `x.target` (不可靠)与 `x.currentTarget` (不可靠)有可能不是同一个(在点击事件时)
100. `a.href` 是带 http 协议对浏览器加的； `a.getAttribute('href')` 是用户写入的
101. `elementList = parentNode.querySelectorAll(selectors);` 返回一个静态 `NodeList`，包含至少一个指定选择器匹配的元素的 `Element` 对象，或者没有匹配的空的 `NodeList`

      注：如果 `selectors` 参数中包含 [CSS伪元素](https://developer.mozilla.org/en-US/docs/Web/CSS/Pseudo-elements)，则返回的列表始终为空。

102. `setTimeout()` 只执行一次，而 `setInterval()` 能一直循环执行，给 `setInterval()` 取个ID名在其内部通过 `window.clearInterval(id)` 去掉它
103. Object 对象有无 new 都一样，都生成对象，根据给定的值返回不同类型对象：

      例：`Object(1) => Number { [[PrimitiveValue]]: 1 }`

      `Object('sss') => String { [[PrimitiveValue]]: 'sss' }`

      以下不一样：`String(1) => '1'` (常量，字符串)

      `new String(1) => String { [[PrimitiveValue]]: '1' }` (对象)

104. `var a = Array(3)` ：会生成长度为3，三个数值都是 `undefined` 的数组

      `var a = Array(3, 3)` ：会生成长度为2，两个数值是3，3的数组

      加不加 `new` 都一样

105. `var f = new Function('a', 'b', 'return a + b')`

      `'a','b'` 以字符串形式传入参数， `'return a + b'` 运行函数体

      如果是复杂***对象***，加不加 `new` 返回值一样

      如果是基本***类型***，加不加 `new` 返回不同值

      `function () {}` 与 `Function()`：`function` 是关键字，`Function` 是全局对象

106. 数组是对象，故可以向数组中添加属性
107. `arguments` 构成伪数组
108. `forEach()` 接受一个函数 => `forEach(function (x, y) {})` ：x 为 `"value"`，y 为 `"key"`

     `forEach()` 可以通过 `this` 获取到第一个参数，即 `"."` 前面的数，例：`a.forEach ; this => a`
109. `sort()` 排序是从小到大，可添加函数改变方向，函数必须接受两个参数，且有返回值
`a.sort(function (x, y) { return x - y })` (内置快排)
110. `a = [1, 2, 3]; a.join('yk') => "1yk2yk3"` (数组变字符串)；`join` 不传参，默认 `','`
111. ★`a = [1, 2, 3]; b = [4, 5, 6]; a + b => "1, 2, 34, 5, 6"`

      `a.concat(b) => "1, 2, 3, 4, 5, 6"`(原来的 a 和 b 不变) // `concat` 返回一个新数组，故可用来复制数组
      - `var b = a.concat([]) => a === b 为 false`
      - `var b = a => a === b 为 true`
112. `map` (映射)与 `forEach` 差不多，`forEach` 无返回值，`map` 有返回值

      `a.map(value => value * 2) <=> a.map(function (value, key) { return value * 2 }) => [2,4,6]`(原来的 a 不变)
113. 所有数组的方法中只有 `sort()` 会改变原数据的值(位置)
114. `a.filter` 与 `map` 差不多，过滤

      `a = [1, 2, 7, 8]; a.filter(function (value, key) { return value >= 5 }) => [7, 8]` // (原来 a 不变)
115. `a.reduce(function (sum, n) { return sum + n }, 0) <=> a.reduce((sum, n) => sum + n, 0)` // sum：之前的结果；n：当前的数字；0：初始值
116. `map`、`filter`、`forEach`都可用 `reduce` 实现

      ```javascript
      // 用reduce表示map
      a.reduce(function (arr, n) { 
        arr.push(n * 2);
        return arr;
      }, []) // [2, 4, 6]
      //用reduce表示filter
      a = [1, 2, 7, 8];
      a.reduce(function (arr, n) { 
        if (n >= 5) {
          arr.push(n);
          return arr;
        }
      }, []) // [7, 8]
      ```

117. `reverse()` 可以使数组倒过来
118. 函数的五种生命方式：
      - `function () {}` (浏览器会自动加上“ `return undefined` ”)匿名函数(必须赋值)
      - `function x() {}` 具名函数，可用 `console.log()` 打印出x，作用域是全局
      - `var x = function y() {}` 不可用 `console.log()` 打印出y，报错，作用域只在y内部
      - `f = new Function ('x', 'y', 'return x + y')`
      - `f = (x, y) => {}` 箭头函数——若只有一句话，且不返回对象 (可变形成)=>  `f = (x, y) => x + y` (return 与 {} 一起省略)；若只有一个参数 => `f = n => n * n` // 箭头函数不能有名字
119. 函数有个属性name：

      ```javascript
      function f() {} | f.name => 'f'
      var f2 = function () {} | f2.name => 'f2'
      var f3 = function f4() {} | f3.name => 'f4'
      var f5 = new Function ('x', 'y', 'return x + y') | f5.name => 'anonymous'
      ```

120. `f(1, 2) <=> f.call(undefined, 1, 2)`
121. `call` 的第一个参数可以用 `this` 得到；`call` 后面的参数可以用 `arguments` 得到
122. 面试题：

      ```javascript
      var a = 1;
      function f1() {
        f2.call();
        console.log(a); // undefined => 变量提升
        var a = 2;
      }
      var a = 1;
      function f2() {
        console.log(a);
        var a = 2;
        f3.call(); // 看此函数的原作用域及其父作用域，寻找a，最后是全局作用域的a=1
      }
      function f3() {
        console.log(a); // 1
      }
      f1.call(); // undefined 1 undefined
      console.log(a); // 1
      ```

123. 如果一个函数，使用了它范围外的变量，那么(这个函数+这个变量)就叫做闭包
124. `var a = console.log(1); // a 的值是 undefined`
125. `var a = (1, console.log(2)); // a 的值是 undefined`

      `var a = (1, 2); // a 的值是2`
126. 面试题

      ```javascript
      function f1() {
        console.log(this) // { name: 'obj' }
        function f2() {
          console.log(this) // 打印出Window对象
        }
        f2.call() // 未带值，默认 window
      }
      var obj = { name: 'obj' }
      f1.call(obj)
      ```

127. `isEqualNode()` 是相同的，`isSameNode()` 是同一个
128. Dom 通过 `querySelector/querySelectorAll` 查找的都是伪数组
129. 常见节点的类型有7种：
     - `ELEMENT_NODE`: 1
     - `TEXT_NODE`: 3
     - `PROCESSING_INSTRUCTION_NODE`: 7
     - `COMMENT_NODE`: 8
     - `DOCUMENT_NODE`: 9
     - `DOCUMENT_TYPE_NODE`: 10
     - `DOCUMENT_FRAGMENT_NODE`: 11
130. `Dom` 树的根节点是 `html`
131. `HTMLCollection` 实例对象可以用 `id` 属性或 `name` `属性引用节点元素，NodeList` 只能使用数字索引引用
132. `childNode` 接口的方向有：`after(), before(), remove(), replaceWith()`
133. 最好在运用 `jQuery` 的变量前加上 `$`，好识别
134. `<div id=parent></div>` ：如果有父窗口，就是父窗口；如果没有，就是当前窗口；`window` 有 `parent` 的默认属性
135. 立即执行函数，申请匿名函数，立即执行

      ```javascript
      (function () {}())
      (function () {}.call())

      (function () {})()
      (function () {}).call()

      -function () {}()
      -function () {}.call()

      ...+/!/~(二进制取反)
      ```

136. `{ var parent = 1 }` // 若用 `var`，`parent` 会成为全局变量，因为没有 `function` 阻止其变量提升

      `{ let parent = 1 }` // 这样能实现局部变量 `parent`，即立即执行函数，`let` 不会让变量提升
137. `jQuery` 的 `one` 方法只监听一次，最近一次动画才有效
138. 常用 Dom Level2 事件：
     - Dom L1 `onclick` = "要执行的代码"

        `x.onclick = print` // 不要执行，也不要结果

        HTML：`onclick = "print()"` || JS：`x.onclick = print`
     - Dom L2 `onclick` 与 `addEventListener('click', function () {})` 的区别：
       - `onclick` 是属性，只执行一次，易重复被覆盖(别人或自己)，唯一
       - `addEventListener` 是队列模式，先绑定就先触发，非唯一
     - ★**addEventListener('', function () {}, ?)：?处不填或者 false 表示冒泡事件，从小到大，若？处 true，则表示捕获事件，由大到小**
     - 事件一定是先捕获后冒泡吗？**不一定**
![事件模型示例图.jpeg](https://upload-images.jianshu.io/upload_images/5999132-0cdbd89fb90f1274.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
若在自己身上同时存在捕获冒泡事件，则按照事件代码书写顺序执行；若在父类或祖先类身上同时有捕获冒泡事件，则按照事件模型顺序：先捕获，后冒泡

139. `e.stopPropagation()` 阻止事件传播； `e.preventDefault()` 阻止默认事件
140. `jQuery: $(wrapper).on('click', false) === $(wrapper).on('click', function (e) { e.preventDefault() } )`
141. 在点击事件中只用 `one` 事件来实现浮层的显隐，减少内存消耗
142. `setTimeout(function () {}, 0)` 会在原本事件完成那一瞬间执行
143. `jQuery` 中 `clone(false)` 不克隆子元素，`clone(true)` 克隆该元素及其所有子元素
144. 在实现无缝轮播时，有时切换会出现其它画面，可通过 `offset()` 清除掉

      例：`$slide.hide().offset();$slide.css(…).show()`
145. 用 `img` 传值只能使用 `get` 方法
146. 用 `script` 传值，必须将其放入 `body` 内才能传值，结束时用事件移除 `script——SRJ(server rendered javascript)` 方案
147. `JSON + (with) padding = JSONP` (填充式 JSON，参数式 JSON)

      请求方：`rose.com` 的前端程序员(浏览器)

      响应方：`jack.com` 的后端程序员(服务器)
      1. 请求方创建 `script`，`src` 指向响应方，同时传一个查询参数 `?callbackName = xxx`
      2. 响应方根据查询参数 `callbackName`，构造形如：`xxx.call (undefined, '你要的数据')` 或 `xxx.('你要的数据')` 这样的数据
      3. 浏览器接收到响应，就会执行 `xxx.call(undefined,'你要的数据')`
      4. 那么请求方就知道了他要的数据

     约定：`1.callbackName` => `callback; 2.xxx` => 随机数(避免重复，用完即弃)
148. 请求 `JSONP` 为什么不支持 `POST` 请求？

      a. 因为JSONP是通过动态创建script实现的

      b. 动态创建script时，只能用GET，没法用POST
149. 手写AJAX：

      ```javascript
      myButton.addEventListener('click', () => {
        let request = new XMLHttpRequest()
        request.open('get', 'http://baidu.com:8080/xxx')
        request.send()
        request.onreadystatechange = () => {
          if (request.readyStatus === 4) {
            if (request.status >= 200 && request.status < 300) {
              let str = request.responseText
              let obj = window.JSON.parse(str)
            }
          }
        }
      })
      ```

150. 跨域资源共享([CORS](https://developer.mozilla.org/en-US/docs/Glossary/CORS "CORS: CORS (Cross-Origin Resource Sharing) is a system, consisting of transmitting HTTP headers, that determines whether browsers block frontend JavaScript code from accessing responses for cross-origin requests.")—Cross-Origin Resource Sharing)
151. `Promise` 语法：`return new Promise(function (resolve , reject)`
152. 监听 `Form` 的 `submit` 事件最好处理表单提交
153. ★JavaScript 的或(||)与且(&&)基本不可能是 true / false，一般都是 truly / falsy (现取)

      `1|| 2 => 1`; `1 && 2 => 2`

      `1 && 0 && console.log(3) // 0` => 到0就 判断完了，不会执行 console.log(3) 语句，找到第一个假值，如果没有找到就是最后一个真值

      `0 || undefined || null || 1 // 1` => 找到第一个真值，找到之后不再找
154. `<pre>` 标签是预览标签，可以看到任何细节
155. `setInterval()` 只看第一次传的时间间隔，以后都以此为准，`setTimeout()` 每次都以 time 为准
156. 刷新页面 `session` 失效
157. ★`Cache-Control`：选择资源被缓存，例：`max-age: 30` => 30s内不要请求重新加载该文件(不会发送请求，直到30s之后)

      首页不要设置 `Cache-Control`，否则用户永远无法加载更新最新页面

      尽量将一个版本缓存久一点，若想要更新，则修改以下 url，它会自动加载新文件也可以在文件名中加入随机数更改如：main.ac45jbdc5.css
158. `Expires` => 指定时间过期(本地时间难以控制) ('Expires', 'sun, 04 Feb 2018 14:40:05 GMT')
159. `Etag` 会请求，不加载第四部分，返回304(无修改) || 将原文件 MD5 给 Etag，然后比对判断是否加载，下一次的头中带有 `"If-None-Match"` 字段比对之前的 Etag 携带的 MD5
160. `Last-Modified`：在第一次访问服务器200之后，得到请求内容，同时带有 `Last-Modified` 标记服务器最后一次被修改时间，在第二次访问服务器时会传送 `"If-Modified-Since"` 字段，询问最后更改时间，若一致，则返回304，若修改则重新加载。若 `"If-Modified-Since"` 时间比服务器当前时间还晚，则被认为是非法请求
161. `_.join(['Hello', 'webpack'], ''); // Lodash是一个JS实用工具库，该方法由它提供`
`=> Hello webpack`
`_.join(['a', 'b', 'c'], '~'); // "a~b~c"`
162. `axios` 是专注于 `AJAX` 的一个基于 `Promise` 的 `HTTP` 库(只有 `AJAX` 功能)
163. ES6 解构语法：

      ```javascript
      let config = response.config
      let { method, url, data } = config
      // 前两句相当于
      let { config : { method, url, data } } = response
      ```

164. `Object.assign` 批量修改

      ```javascript
      var book = { name: 'a', name: 1, id: 1 }
      Object.assign(book , { name: 'b' }, { num: 2 }) => book: { name: 'b', num: 2, id: 1 }
      Object.assign(book , { id: 2 }, { id: 3 }) => book: { name: 'b', num: 2, id: 3 }
      ```

165. 绑定 `this` ，使用 `bind()`
166. 发布/订阅模式：解耦合
167. ES6 新语法：`render(data = {})`：若 `data` 为空则为{}，否则为原值
168. 对象赋值

      ```javascript
      Object.assign (this, data, {
        id: id,
        name: attributes.name,
        singer: attributes.singer,
        url: attributes.url
      }) // 将第二个的值赋给第一个，属性、方法一起
      ```

169. ES6 新语法：如果 `key` 和 `value` 一样，则只用写一个，如：`{ id: id } === { id }`
170. `…Attributes` => 将 `Attributes` 里的所有东西拷贝
171. 传值时最好用新地址，即使用深拷贝
172. `array = search.split('&').filter((v => v))` // 通过 v 字符串判断及过滤掉空字符串(`true` `保留，false` 舍弃)
173. `dom.getBoundingClientRect()` // 可获取与屏幕的距离
174. `ParentNode.prepend()` // 在第一个子节点之前插入一组 `Node` 对象或 `DomString` 对象(`text` 文本)
175. `jQuery` 的 `.html(htmlstring)` 代替指定元素内容
176. `jQuery` 的 `$("").empty()` // 删除匹配的所有子节点
177. 匿名函数的执行环境具有全局性，故它的 `this` 对象指向 `windows`
178. `slice(begin, end)`：都可选，提取从 `begin` 开始(包含 `begin`)到 `end` 结束(不包含 `end`)，正数就从头开数，负数就从尾开数
179. 箭头函数：当只有一个参数时，()可选 => `(e) => {} => e => {}`
180. `undefined + 1 = NaN`
181. ★当一个函数被声明两次的时候，后一次声明覆盖前一次声明，且函数也有变量提升，故和 `var` 一样，会被提到开头
182. `setTimeout()` 不管加不加时间都会做异步处理，会被放在末尾加入队列，当文件执行完成后会去执行此段代码
183. `destroy()` 并不能在页面删掉自己，需用 `remove()`
184. 代码简化

      ```javascript
      if (name === this.name) {
        this.active = true
      } else this.active = false

      this.active = name === this.name // 简化
      ```

185. `() => {} <=> function () {}.bind(this)`
186. ★`x` 与 `x.bind()` 是两个函数，若在删除监听器时使用，则无法正确删除监听器，故用 `let name = () => {}` 函数来解决
      *`x.bind()` 会返回一个新函数*
187. 继承

      ```javascript
      function Man (name) {
        Human.call(this.name)
        this.gender = '男'
      }

      Man.prototype.__proto__ = Human.prototype // IE不支持

      // 不想继承Fn自带属性，只要prototype，那就相当于空对象

      var f = function () {}
      f.prototype = Human.prototype
      Man.prototype = new f() // IE支持
      ```

      `var obj = new Fn()` 中 `new` 的过程：
      - 产生一个空对象
      - this = 空对象
      - `this.__proto__ = Fn.prototype`
      - 执行 `Fn.call(this, x, y, …)`
      - `return` 结果(this)
188. `prototype` 属性，只有一个功能，存放共有属性对象的地址

      `var yk = new Man()` => ![prototype属性](https://upload-images.jianshu.io/upload_images/5999132-7c17c57cbbe6da86.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
      ![对象](https://upload-images.jianshu.io/upload_images/5999132-c4b7c80dbc1b93d2.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
189. ★Promise的一些小测验：

      ```javascript
      let promise = new Promise(function(resolve, reject) {
        console.log('promise1') // 会立即执行，无异步，同步
        resolve()               // 异步
      })
      console.log('promise2')
      setTimeout(function () {
        promise.then(function () {
          console.log('promise3')
        })
        console.log('promise4')
      })
      console.log('promise5')
      setTimeout(function () {
        console.log('promise6')
      })
      promise.then(function () {
        console.log('promise7')
      })
      setTimeout(function () {
        console.log('promise8')
      })
      promise.then(function () {
        console.log('promise9')
      })
      console.log('promise10')

      输出结果：
      //无异步
      promise1
      promise2
      promise5
      promise10
      //promise
      promise7
      promise9
      //setTimeout
      promise4
      promise3
      promise6
      promise8
      ```

      ★*故：输出时间长度：`setTimeout` > `Promise` > 同步(处理顺序主要与“宏任务与微任务”有关)*
190. `class` 的 `extends` 就是为了实现 `Man.prototype.__proto__ = Human.prototype` ,像这样直接修改会很消耗性能(浏览器会检查一遍)
191. 常见术语：
      - `mixin` — 混入(把一个值的属性一个一个拷贝到另一个值上)，就是现在的 `Object.assign()`

        ```javascript
        var mixin = function (a, b) {
          for (let key in b) {
            a[key] = b[key]
          }
        }
        ```

      - `currying` — 柯里化(把 n 个自变量的函数变成(n - 1)个自变量的偏函数的过程)

        ```javascript
        var cache = []
        var add = function (n) {
          if (n === undefined) {
            return cache.reduce((p, n) => p + n, 0)
          } else {
            cache.push(n)
            return add
          }
        }
        add(1)(2)(3)(4)() // 10
        ```

      - `HOC` — 高阶函数(接收一个或多个函数作为输入式或输出一个函数)

        ```javascript
        function add(x, y) { return x + y }
        var f = Function.prototype.bind.call(add, undefined, 1)
        f(2) // 3
        ```

192. 并行下载，串行解析
193. `Web` 性能优化：
      - 减少 `DNS` 查询
      - `TCP` 连接复用：`keep-alive`
      - 减小 `Cookie`，缓存文件，浏览器会同时请求多个文件
      - `Gzip` 压缩文件(会耗 `CPU`，权衡一下)
      - `DOCTYPE` 不能不写且不能写错
      - `cdn` (内容分布网络)加速
      - `css` 放 `head` 里，`js` 放 `body` 最后(尽早下载样式)
194. ★`MacroTask` & `MicroTask` (宏任务与微任务)
      - `setTimeout()`：属于宏任务&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 精确度不高，可能有延迟执行的情况发生，且因动用红黑树，所以消耗资源大
      - `setImmediate()`：属于宏任务&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| 消耗资源小，不会造成阻塞，效率最低
      - `process.nextTick()`：属于微任务 | 效率最高，消耗资源小，会阻塞 `CPU` 的后续调用

      调用顺序：`process.nextTick() > setImmediate() > setTimeout()`
      ![宏任务与微任务.jpeg](https://upload-images.jianshu.io/upload_images/5999132-187587b31f131f5b.jpeg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

      MacroTask优先级：`script (主代码块) > setImmediate > MessageChannel (未实验过) > setTimeout / setInterval > I / O > UI rendering`

      MicroTask优先级：`rocess.nextTick > Promise > Object.observe (已废弃) > MutationObserver (不兼容，H5特性)`
195. `str.charAt(index)` —从一个字符串中返回指定到单个字符
196. `str.substr(start[, length])` —返回一个字符串中从指定位置开始有指定长度个字符
197. `str.substring(start[, end])` —返回一个字符串中从开始位置到结束位置(不包括)到字符
198. 获取当前页面url：`window.location.href`
199. 事件中的：
      - `clientX`：点击位置距离当前body可是区域X坐标
      - `layerX`：FireFox特有,不考虑
      - `movementX`：是只读属性，它提供了当前事件和上一个 `mousemove` 事件之间鼠标在水平方向上的移动值
      - `offsetX`：相对于带有定位的父盒子X
      - `pageX`：对整个页面来说，包括被卷去的body长度
      - `screenX`：当前电脑屏幕
      - `x`：于screen一样
200. 模版字符串—反引号(``)来替代普通字符串中的双引号和单引号。模版字符串可以包含特定语法(${expression})的占位符。在模版字符串中使用反引号(`)时需在前面加转义符(\)
