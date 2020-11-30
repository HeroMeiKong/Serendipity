# JavaScript 图片库优化版

在之前的版本上进行修改和优化。

## 需要考虑的几点

1. 引入了检测和测试吗？代码能平稳退化吗？
2. 向后兼容
3. 分离JavaScript
4. 优化、简洁代码

## 代码

*gallery.html代码：*

  ```html
  <!DOCTYPE html>
  <html lang="en">
  <head>
    <meta charset="utf-8"/>
    <title>Image Gallery</title>
    <link rel="stylesheet" type="text/css" href="styles/layout.css" media="screen"/>
  </head>
  <body>
    <h1>Snapshots</h1>
    <ul id="imagegallery">
      <li>
        <a href="images/fire.jpg"  title="fire1">
          ![](images/fire.jpg)
        </a>
      </li>
      <li>
        <a href="images/coffee.jpg"  title="fire2">
          ![](images/coffee.jpg)
        </a>
      </li>
      <li>
        <a href="images/rose.jpg"  title="fire3">
          ![](images/rose.jpg)
        </a>
      </li>
      <li>
        <a href="images/bigben.jpg"  title="fire4">
          ![](images/bigben.jpg)
        </a>
      </li>
    </ul>
    ![](images/placeholder.jpg)
    <p id="description">Choose an image.</p>
    <script type="text/javascript" src="scripts/showPic.js"></script>
  </body>
  </html>
  ```

*showPic.js代码：*

  ```javascript
  //调用addLoadEvent()函数，同时加载多个函数，适用于函数数量过多的情况
  function addLoadEvent(func) {
    var oldonload = window.onload;
    if (typeof window.onload !== 'function') {
      window.onload = func;
    } else {
      window.onload = function () {
        oldonload();
        func();
      }
    }
  }
  addLoadEvent(prepareGallery);
  /*由于需要调用的函数较少，可以使用以下简单的方法：
  window.onload = function() {
    countBodyChildren();
    prepareGallery();
  }
  */
  function showPic(whichpic) {
    if (!document.getElementById("placeholder")) return false;//检测是否存在id为placeholder的元素
    var source = whichpic.getAttribute("href");
    var placeholder = document.getElementById("placeholder");
    if (placeholder.nodeName !== "IMG") return false;//判断元素placeholder是否是图片
    placeholder.setAttribute("src", source);
    if (document.getElementById("description")) {//检查元素description是否存在
      var text = whichpic.getAttribute("title") ? whichpic.getAttribute("title") : "";//如果getAttribute("title")返回的值不是null，text将被赋予第一个值；如果返回值是null，text将被赋予第二个值。
      var description = document.getElementById("description");
      if (description.firstChild.nodeType == 3) {//检查description元素的第一个子元素是不是文本节点
        description.firstChild.nodeValue = text;
      }
    }
    return true;
  }

  function prepareGallery() {
    //method1
    // if (!document.getElementsByTagName || !document.getElementById || !document.getElementById("imagegallery")) return false;
    /*method2
    if (!document.getElementsByTagName) {
      return false;
    }
    if (!document.getElementById) {
      return false;
    }
    if (!document.getElementById("imagegallery")) {
      return false;
    }*/
    if (!document.getElementsByTagName) return false;
    if (!document.getElementById) return false;
    if (!document.getElementById("imagegallery")) return false;
    var gallery = document.getElementById("imagegallery");
    var links = gallery.getElementsByTagName("a");
    for (var i = 0; i < links.length; i++) {
      links[i].onclick = function () {
        return showPic(this) ? false : true;
      }
    }
  }
  ```

*layout.css代码：*

  ```css
  body {
    font-family: "Helvetica","Arial",serif;
    color: #333;
    background-color: #ccc;
    margin: 1em 10%;
  }
  h1 {
    color: #333;
    background-color: transparent;
  }
  a {
    color: #c60;
    background-color: transparent;
    font-weight: bold;
    text-decoration: none;
  }
  ul {
    padding: 0;
  }
  li {
    float: left;
    padding: 1em;
    list-style: none;
  }
  #imagegallery {
    list-style: none;
  }
  #imagegallery li {
    display: inline;
  }
  #imagegallery li a img {
    border: 0;
  }
  ```

### 学习笔记

1. 如果想要在将在时同时执行两个函数：

    - `window.onload = firstFunction;`
    - `window.onload = secondFunction;`
    - 如果把它们逐一绑定到 `onload` 事件上，它们当中将只有最后那个才会被实际执行，`secondFunction` 将取代 `firstFunction` 。想要实现可以参照上面的方法。
2. 三元操作符：`?`，是 `if / else` 语句的一种变体形式。这个问号的后面是变量的两种取值。
　　`variable = condition ? if true : if false;`
3. `nodeName` 属性总是返回一个大写字母的值，即使元素在HTML文档里是小写字母。
4. 在考虑了并非所有用户都是使用鼠标的情况下，此处还是并未使用 `onkeypress` 事件函数，因为用户每按下一个键都会触发它。即使是Tab键！而 `onclick` 事件处理函数比我们想象的更聪明：在几乎所有的浏览器里，用Tab键移动到某个链接后按下回车键的动作也会触发 `onclick` 事件。
