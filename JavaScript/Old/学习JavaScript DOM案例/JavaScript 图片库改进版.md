# JavaScript 图片库改进版

实现图片图的结构、样式和行为彻底分离！！！

## gallery.html

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
          ![](images/short4.jpg)
        </a>
      </li>
      <li>
        <a href="images/coffee.jpg"  title="fire2">
          ![](images/short3.jpg)
        </a>
      </li>
      <li>
        <a href="images/rose.jpg"  title="fire3">
          ![](images/short1.jpg)
        </a>
      </li>
      <li>
        <a href="images/bigben.jpg"  title="fire4">
          ![](images/short2.jpg)
        </a>
      </li>
    </ul>
    <script type="text/javascript" src="scripts/showPic.js"></script>
  </body>
  </html>
  ```

## showPic.js

  ```javascript
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

  function insertAfter(newElement, targetElement) {
    var parent = targetElement.parentNode;
    if (parent.lastChild === targetElement) {
      parent.appendChild(newElement);
    } else {
      parent.insertBefore(newElement, targetElement.nextSibling);
    }
  }
  function showPic(whichpic) {
    if (!document.getElementById("placeholder")) return false;
    var source = whichpic.getAttribute("href");
    var placeholder = document.getElementById("placeholder");
    placeholder.setAttribute("src", source);
    if (!document.getElementById("description")) return false;
    if (whichpic.getAttribute("title")) {
      var text = whichpic.getAttribute("title");
    } else {
      var text = "";
    }
    var description = document.getElementById("description");
    if (description.firstChild.nodeType === 3) {
      description.firstChild.nodeValue = text;
    }
    return false;
  }

  function prepareGallery() {
    if (!document.getElementsByTagName) return false;
    if (!document.getElementById) return false;
    if (!document.getElementById("imagegallery")) return false;
    var gallery = document.getElementById("imagegallery");
    var links = gallery.getElementsByTagName("a");
    for (var i = 0; i < links.length; i++) {
      links[i].onclick = function () {
        return showPic(this);
      }
      links[i].onkeypress = links[i].onclick;
    }
  }

  function preparePlaceholder() {
    if (!document.createElement) return false;
    if (!document.createTextNode) return false;
    if (!document.getElementById) return false;
    if (!document.getElementById("imagegallery")) return false;
    var placeholder = document.createElement("img");
    placeholder.setAttribute("id", "placeholder");
    placeholder.setAttribute("src", "images/placeholder.jpg");
    placeholder.setAttribute("alt", "my image gallery");
    var description = document.createElement("p");
    description.setAttribute("id", "description");
    var desctext = document.createTextNode("Choose an image.");
    description.appendChild(desctext);
    var gallery = document.getElementById("imagegallery");
    insertAfter(placeholder, gallery);
    insertAfter(description, placeholder);
  }
  addLoadEvent(preparePlaceholder);
  addLoadEvent(prepareGallery);
  ```

## layout.css

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
