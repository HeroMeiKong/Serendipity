# 神策数据面试题目汇总★

1. HTML5语义化的意义
   - 方便SEO。
   - 方便无障碍阅读，对于帮助视力障碍者阅读的设备筛选重要数据有帮助。
   - 方便开发者维护代码。
   - 当丢失层叠样式表的时候，页面也会按照语义化布局显式。
2. 做过自适应吗？
   - 可以用媒体查询。
   - 可以使用rem或者vh、vw相对单位
3. rem为什么可以自适应？
   - rem相对于html标签的字体大小，如果html的font-size:16px;那么1rem = 16px
4. em的大小相对于谁？
   - 相对于父级元素。
5. 讲一下flex与grid布局。
   - flex与grid是css3新增的布局属性。
   - 分别为了解决：一维与二维的布局缺陷。
6. flex能给详细讲一下吗?如何竖向排列？
   - flex存在两条轴：水平主轴、竖直交叉轴。
   - 默认项目不换行在主轴上水平排列，可以使用flex-direction: column;使其竖直排列。
7. 能给讲一下原型与原型链吗？
8. 手撕代码！
   - 第一道，实现一个原生表格的点击表头排列。
   - 第二道，因数分解，用了递归与非递归两种方法。
9. v-for使用过吗？要不要加key属性？为什么要加？
   - 用过
   - 要加
   - 为了方便diff方法的执行，diff算法的简单原理说了一下。
10. 单页面(SPA)应用的优缺点
    - 优点：
      - 方便开发。
      - 用户体验更好。
    - 缺点
      - 第一次加载页面时，速度较慢。
      - SEO需要服务端渲染。
      - 添加书签需要程序支持。
11. let const定义变量
    - 拥有自己块级作用域
    - 不会出现在window属性中，而是在script作用域。
12. call与apply的区别。
    - 性能有差距，apply性能稍快一点。
    - 传参不一样。
13. fetch库用过吗？
    - 正在用fetch封装自己的个人博客的请求操作。
14. 能给讲一下服务端渲染吗？
    - 服务端渲染，就是在服务端请求数据，让后将渲染成一个页面返回到客户端，利于SEO。
15. 实现vue双向绑定。
    - 两种方法，Object.defineProperty()
    - ES6新特性proxy，解决了第一种方法无法监听Array内部的问题，vue3.0拥抱了这种方式。
16. 看过著名框架的源码吗？
17. 框架与库的理解
    - 我认为库就是一个非常完善的大型组件，库与框架的区别是，库是你去调用它的方法，而框架则是你在它的生命周期中写代码，让它调用你的代码。
18. react算不算框架
    - 我认为算。。
    - 但是面试官认为不算。。
19. 写过webpack的loader或者插件吗？
20. h5新特性？localStroage/sessonStroage？有哪些语义化标签？语义化的好处？
21. css3新特性？
22. es6箭头函数
23. call、apply、bind的区别
24. position的五个值，sticky特性怎么模拟？
25. promise、async/await的相关
26. 异步获取数据
27. flex:1的含义
28. 垂直居中的实现
29. 跨域的方案
30. react生命周期
31. react的数据管理
32. 给图片，大致实现一个jsx的组件
33. 虚拟dom和diff算法了解到多少？
34. 对react框架的理解
35. 对于前端工程化的理解
36. 跨域的方案
37. `React` 生命周期函数及作用

38. `React` 开发中可做的优化

39. `React` 函数组件和类组件的区别

40. `React Hooks`
41. 排序两次后什么样，这个可能是什么排序算法
42. 同源策略可能不通过的情况
43. 优化页面加载速度的方法
44. 跨域方法
45. 浏览器缓存
46. position属性
47. 导致界面卡顿的方法
48. defer/async区别
49. css选择器
50. 哪些标签可能被搜索引擎抓取收录
51. 字符串去重

    ```javascript
    return Array.form(new Set(array));
    ```

52. 去除字符串左右空格

    ```javascript
    return str.trim();
    // return str.trimStart(); // 也可使用别名 trimLeft() 去除字符串开头空白字
    // return str.trimEnd(); // 也可使用别名 trimRight() 去除字符串末端空白字符
    ```

53. 字符串大小字符取反

    ```javascript
    str = str.replace(/[a-zA-Z]/g, content => {
      return content.toUpperCase() === content ? content.toLowerCase() : content.toUpperCase();
    }
    return str;
    ```

54. 数字千位数

    ```javascript
    return num.toLocaleString("en-US");
    ```

55. 布局方式
    - 双栏布局
    - 三栏布局：双飞翼，圣杯

56. 布局实现方式
    - 静态布局：固定 px，overflow: hidden；一般 PC，展示一样的结构
    - 弹性布局：flex，可伸缩，布局一致
      - 作用于父元素：
        - flex-wrap：是否运行换行
          - nowrap：不换行（default value）
          - wrap：换行
          - wrap-reverse：换行（前后对调）
        - flex-direction：定义主轴方向
          - row：容器的主轴被定义为与文本方向相同。 主轴起点和主轴终点与内容方向相同 - 从左往右
          - row-reverse：从右往左
          - column：容器的主轴和块轴相同。主轴起点与主轴终点和书写模式的前后点相同 - 从上到下
          - column-reverse：从下到上
        - justify-content：如何分配沿着弹性容器主轴的元素之间及其周围的空间。
          - flex-start：对弹性元素，从行首起始位置开始排列
          - flex-end：对弹性元素，从行尾位置开始排列
          - space-between：均匀排列每个元素，首个元素放置于起点，末尾元素放置于终点
          - space-around：均匀排列每个元素，每个元素周围分配相同的空间
          - space-evenly：均匀排列每个元素，每个元素之间的间隔相等
          - stretch：均匀排列每个元素，'auto'-sized 的元素会被拉伸以适应容器的大小
        - align-items：可以使所有子容器以默认方式沿交叉轴线对齐到每个盒子。
        - justify-items：可以使所有子容器以默认方式沿主轴线对齐到每个盒子。
        - align-content：如何分配沿着弹性容器交叉轴的元素之间及其周围的空间。
      - 作用于子元素：
        - order：弹性容器中的可伸缩项目在布局时的顺序（增序）
        - flex-grow：flex 增长系数，剩余空间，占比
        - flex-shrink：指定了 flex 元素的收缩规则。flex 元素仅在默认宽度之和大于容器的时候才会发生收缩，flex-shrink 值越大，收缩时越小
        - flex-basis：指定 flex 元素在主轴方向上的初始大小
        - justify-self：设置单个盒子在其布局容器主轴中的对其方式
        - align-self：设置单个盒子在其布局容器交叉轴中的对其方式
      - 部分语法：

        ```css
        /* 一个值, 无单位数字: flex-grow */
        flex: 2;

        /* 一个值, width/height: flex-basis */
        flex: 10em;
        flex: 30px;
        flex: min-content;

        /* 两个值: flex-grow | flex-basis */
        flex: 1 30px;

        /* 两个值: flex-grow | flex-shrink */
        flex: 2 2;

        /* 三个值: flex-grow | flex-shrink | flex-basis */
        flex: 2 2 10%;
        ```

    - 自适应布局：根据设备的不同而进行适应，通过检测视口分辨率，来判断当前访问的设备是pc端、平板还是手机，从而请求服务层，返回不同的页面一般需写多个 CSS
      - 技术原理：分别为不同的屏幕分辨率定义布局，即创建多个静态布局，每个静态布局对应一个屏幕分辨率范围。改变屏幕分辨率可以切换不同的静态局部（页面元素位置发生改变），但在每个静态布局中，页面元素不随窗口大小的调整发生变化。
      - 布局特点：屏幕分辨率变化时，页面里面元素的位置会变化，而大小不会变化。
      - 设计方法：使用媒体查询给不同尺寸和介质的设备切换不同的样式，在同一个设备下实际还是固定的布局。
    - 流式布局：元素的宽高用百分比做单位，元素宽高按屏幕分辨率调整，布局不发生变化
    - 响应式布局：响应式布局是网页的布局针对屏幕大小的尺寸而进行响应；通过检测视口分辨率，针对不同客户端在客户端做代码处理，来展现不同的布局和内容
      - 技术原理：糅合了流式布局+弹性布局，再搭配媒体查询技术使用
      - 布局特点：每个屏幕分辨率下面会有一个布局样式，即元素位置和大小都会变
      - 设计方法：媒体查询+流式布局
    - 网格布局：grid

57. HTML新特性
58. CSS3动画优化
59. 本地存储
60. ES6新特性 | 异步方式
61. 路由 hash | histroy
62. viewport
63. canvas
64. 神策前端笔试题
1. 数值格式化,以 「,」 分割
例如：
console.log(formatNumber(10000)) // 10,000
console.log(formatNumber(10000.1234)) // 10,000.1234
console.log(formatNumber(1000)) // 1000

2. 实现如下方法
/**
 * @desc 函数 calc可将数值较大的数字（乘积）拆解为数值较小的数字（乘数）
 * @param {number}   n，数值较大的整数（只考虑数值大于 1 的自然数）
 * @return {array<number>}   由 n 被拆解出来的数字组成
 */
function calc(n) {
 // todo
}

// 例如：
console.log(calc(7));
// [7]
console.log(calc(8));
// [2, 2, 2]
console.log(calc(9));
// [3，3]
console.log(calc(12));
// [2, 2, 3]
console.log(calc(30));
// [2, 3, 5]




