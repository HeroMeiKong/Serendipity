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
   - rem相对于body标签的字体大小，如果body的font-size:16px;那么1rem = 16px
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
