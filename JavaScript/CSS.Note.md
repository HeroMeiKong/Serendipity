# CSS篇笔记

★★★★★★★★★★

更新于2019-01-10
这是自己平时书写的笔记，有很多漏洞，也有很多错误，还望大家多多指正，感谢🙏

★★★★★★★★★★

1. `:after` 与 `::after` 的区别？
    - 它们都是伪元素，与 `:hover/:active` 等伪类不一样
    - `:before/:after` 伪元素是在 CSS2 中提出来的，可兼容到IE8
    - `::before/::after` 是 CSS3 中到写法，为将伪类与伪元素区分开来，为了兼容性最好使用一个冒号的
2. `cubic-bezier(n, n, n, n)` 贝塞尔曲线，决定变化的样式
3. `justify-content: space-between;` // 均匀排列每个元素，首个元素放置于起点，末尾元素放置在终点
4. `skewX(35deg);` // 如正方形变平行四边形
5. `border-radius: 10% 30% 50% 70%;`//圆角度数：左上 右上 右下 左下
6. `filter:blur(5px);` // 过滤器，污点，像加滤镜(模糊)
7. sass(scss)可嵌套：sass 不加中括号，分号；scss 兼容 css
8. Babel 语法：`babel src -d dist` // 将 src 中的 JS 文件转义到 dist 中
9. `box-shadow` 用法：可多层 shadow，以下是取值个数不同所代表的不同值
    `offset-x` | `offset-y` | `blur-radius`(阴影模糊半径) | `spread-radius`(阴影扩展半径) | `color`
    :-: | :-: | :-: | :-: | :-:
    60px | -16px | | | teal
    10px | 5px | 5px | | black
    2px | 2px | 2px | 1px | rgba(0,0,0,.2)
    例：`box-shadow: inset 5em 1em gold;`
10. `transition` 用法：
    property name | duration | timing function | delay
    :-: | :-: | :-: | :-:
    margin-right | 4s | ease-in-out | 1s
    margin-right | 4s | | |
11. `!important` 用法：`color: blue !important;` // 优先级最高（覆盖其它声明，少用）
12. `letter-spacing`：明确文字的间距行为
13. `filter``—过滤的用法：`filter: blur(3px) brightness(.7) ...(更高级的属性)` / /brightness - 遮光（变黑或更亮）
14. 想隐藏元素可用：`display: none;`
15. 取消 `button` 被点击后的外边框蓝色效果：`outline: none;`
16. 添加下划线：`text-decoration: underline;` // none 去掉下划线
17. `flex` 布局：
    - `justify-content(平行方向): space-around(平均分配)；`
    - `align-items(垂直方向): space-between(两端对齐再平均)`
18. 文字两端对齐：`text-align: justify;`
19. `flex-direction: colum(横排);` // row(竖排)
20. `background-image: url('…');` // 注意路径
21. `flex-grow: 1(负值无效);` // 成长因子
22. `input[type = text] {}`
23. `pointer-events: none;` 表示鼠标事件“穿透”该元素，并且指定该元素“下面”的任何东西
24. `filter: blur(1px);` 少加在原元素上，加在子元素或者伪元素上
25. `background-size: cover;` 保证填满整个容器不拉扯图片
26. `text-indent: 2em;` 首行缩进两字符
27. `:root` 选择器—根组件，若不行则改为html
28. 若发现三个相同元素，没有水平对齐，加一个 `vertical-align: middle/top/bottom都行，只要不是baseline;`
29. 为什么都是 `width: 50%;` 都三个元素，却能构成一行平均三等份？

    **由于没有设置 `flex-wrap: wrap;` 故这三个被挤在中间不换行**
30. `flex-shrink: 0;` 不缩，不换行
31. 如何适配不同设备？由范围大到小：`phone(0-oo)>ipad(a-oo)>pc(b-oo) // a<b`
32. `outerHTML` 输出 HTML
33. 如果父元素高度变成 `min-height`，那么子元素都百分比（%）高度就失效了，因为此时父元素没有 `height`，即 `height=0`
34. 使用 `ref` 去让 JS 添加属性
35. 元素的 `style` 只获取内联元素，不获取CSS元素，故用 `getBoundingClientRect()`
36. 有时 `margin-left: auto;` 会自动跑右边去

更新于2019-01-15-19:10，以上皆为日常笔记，如有问题还望大家多多指正！
