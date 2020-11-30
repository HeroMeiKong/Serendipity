# 共享onload事件函数

页面加载时同时加载多个函数：

```javascript
function addLoadEvent(func) {
  var oldonload = window.onload;
  if (typeof window.onload !== 'function') {
    window.onload = func;  
  } else {
    window.onload = function() {
      oldonload();
      func();
    }
  }
}
```
