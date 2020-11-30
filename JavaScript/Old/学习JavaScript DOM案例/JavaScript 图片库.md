# JavaScript 图片库

在创建了一个简单的图片浏览网站（点击图片跳转页面显示）之后，需要考虑

1. 当点击某个链接时，我希望能留在这个网页而不是转到能一个窗口。
2. 但点击某个链接时，我希望能在这个网页上同时看到那张图片以及原有的图片清单。

解决办法：

1. 通过增加一个“占位符”图片的办法在这个主页上为图片预留一个浏览区域。
2. 在点击某个链接时，拦截这个网页的默认行为。
3. 在点击某个链接时，把“占位符”图片替换为与那个连接相对应的图片。**

Case1：JavaScript_Image Gallery：

1. images 文件夹：保存图片
2. scripts 文件夹：保存JS文件
3. styles 文件夹：保存CSS文件
4. 代码：
  ***gallery.html代码：***

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
    <ul>
      <li>
        <a href="images/fire.jpg" onclick="showPic(this);return false;" title="fire1">Fireworks</a>
      </li>
      <li>
        <a href="images/coffee.jpg" onclick="showPic(this);return false;" title="fire2">Coffee</a>
      </li>
      <li>
        <a href="images/rose.jpg" onclick="showPic(this);return false;" title="fire1">Rose</a>
      </li>
      <li>
        <a href="images/bigben.jpg" onclick="showPic(this);return false;" title="fire1">Bigben</a>
      </li>
    </ul>
    ![](images/placeholder.jpg)
    <p id="description">Choose an image.</p>
    <script type="text/javascript" src="scripts/showPic.js"></script>
  </body>
  </html>
  ```

  ***showPic.js***

  ```javascript
  function showPic(whichpic) {
    //切换图片
    var source = whichpic.getAttribute("href");
    var placeholder = document.getElementById("placeholder");
    placeholder.setAttribute("src", source);
    //切换文本
    var text = whichpic.getAttribute("title");
    var description = document.getElementById("description");
    description.firstChild.nodeValue = text;
  }
  function countBodyChildren() {
    var body_element = document.getElementsByTagName("body")[0];
    //alert(body_element.childNodes.length);查找body元素一共有多少个子元素
    alert(body_element.nodeType);//获取body元素的元素节点属性
  }
  window.onload = countBodyChildren;
  ```

  ***layout.css***

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
  img {
    display: block;
    clear: both;
  }
  ```

## 学习重点

1. 如何利用DOM所提供的方法去编写图片库脚本。
2. 如何利用事件处理函数把JavaScript代码与网页集成在一起。
3. 需要掌握的几个新属性：
   - childNodes
   - -nodeType
   - nodeValue
   - firstChild
   - lastChild

## 学习笔记

1. `placeholder.setAttribute("src",source); <=>   placeholder.src = source`;
2. 添加事件处理函数语法：event = "JavaScript statement(s)"

    注意：JavaScript 代码包含在一对引号之间。我们可以把任意数量的 JavaScript 语句放在这对引号之间，只要把各语句用分号隔开即可。

3. `this` 关键字，可以表示“这个对象”，在这里指“这个`<a>`元素节点”。
4. 在 `onclick` 事件处理函数所触发的 JavaScript 代码里增加一条 `return false` 语句，就可以防止用户被带到目标连接窗口：
　　`onclick="showPic(this);return false;"`
5. `childNodes` 属性：用来获取任何一个元素的所有子元素，它是一个包含这个元素全部子元素的数组（包含所有类型的节点，不仅仅是元素节点。）
6. `windows.onload = countBodyChildren;//在页面加载时调用countBodyChildren函数。`
7. `nodeType` 属性：每个节点都有的属性，获取节点属性。
　　`nodeType` 共有12种可能值：仅有3种具有实用价值：
　　-元素节点的 `nodeType` 属性值是1。
　　-属性节点的 `nodeType` 属性值是2。
　　-文本节点的 `nodeType` 属性值是3。
8. `nodeValue` 属性：用来检索（得到）和设置一个节点的值。
　　`node.nodeValue`
　例如：`<p id = "de">Choose it</p>`
　　注意：在用nodeValue属性获取de对象的值时，得到的并不是包括在这个段落里的文本。而是 `<p>` 中的值 `null` 。包含在 `<p>` 元素里的文本是另一种节点，它是 `<p>` 元素的第一个子节点。
　　`alert(de.childNodes[0].nodeValue);`
9. 无论何时何地，只要需要访问 `childNodes` 数组的第一个元素，都可以写成 `firstChild` ，同理最后一个元素：
　　`node.firstChild <=> node.childNodes[0];
　　node.lastChild <=> node.childNodes[node.childNodes.length-1];`
