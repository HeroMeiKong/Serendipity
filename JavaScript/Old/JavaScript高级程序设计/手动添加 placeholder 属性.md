# 手动添加 placeholder 属性

接受一个form对象

  ```javascript
  function resetFields(whichform) {
    if (Modernizr.input.placeholder) return;
    for (var i = 0, length = whichform.elements.length; i < length; i++) {
      var element = whichform.elements[i];
      if (element.type == "submit") continue;
      var check = element.placeholder || element.getAttribute('placeholder');
      if (!check) continue;
      element.onfocus = function () {
        var text = this.placeholder || this.getAttribute('placeholder');
        if (this.value === text) {
          this.className = '';
          this.value = '';
        }
      }
      element.onblur = function () {
        if (this.value === "") {
          this.className = 'placeholder';
          this.value = this.placeholder || this.getAttribute('placeholder');
        }
      }
      element.onblur();
    }
  }
  ```

可通过遍历传入所有form对象

  ```javascript
  function prepareForms() {
    for (var i = 0, length = document.forms.length; i < length; i++) {
      var thisform = document.forms[i];
      resetFields(thisform);
    }
  }
  ```
