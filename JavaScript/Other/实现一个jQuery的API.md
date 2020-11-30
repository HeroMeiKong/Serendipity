# 实现一个 jQuery 的 API

  ```javascript
  window.jquery = function (nodeOrSelector) {
    let nodes = {};
    if (typeof nodeOrSelector === 'string') {
      //nodes = document.querySelectorAll(nodeOrSelector);//包含node的原型链
      let temp = document.querySelectorAll(nodeOrSelector);
      for (let i = 0, length = temp.length; i < length; i++) {
        nodes[i] = temp[i];
      }
      nodes.length = temp.length;
    } else if (nodeOrSelector instanceof Node) {
      nodes = { 0:nodeOrSelector, length: 1 }
    }

    nodes.addClass = function (classes) {
      for (let key in classes) {
        var value = classes[key];
        var methodName = value ? 'add' : 'remove';
        for (let i = 0, length = nodes.length; i < length; i++) {
          nodes[i].classList[methodName](key);
        }
      }
    }

    /*nodes.getText = function () {
      var texts = [];
      for (let i = 0, length = nodes.length; i < length; i++) {
        texts.push(nodes[i].textContent)
      }
      return texts;
    }
    nodes.setText = function (text) {
      for (var i = nodes.length - 1; i >= 0; i--) {
        nodes[i].textContent = text;
      }
    }*/

    nodes.text = function (text) {
      if (text === undefined) {
        var texts = [];
        for (let i = nodes.length - 1; i >= 0; i--) {
          texts.push(nodes[i].textContent);
        }
        return texts;
      } else {
        for (let i = nodes.length - 1; i >= 0; i--) {
          nodes[i].textContent = text;
        }
      }
    }
    return nodes;
  }

  var node2 = jquery('ul > li');
  node2.addClass({ a:true, b:false, c:true });
  node2.text('hi');
  ```
