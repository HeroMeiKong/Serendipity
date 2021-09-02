# 前端实现原理汇总

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

## `Javascript`

1. ### `Object.is` 的实现原理

    ```javascript
    Object.is = function (x, y) {
      if (x === y) {
        // +0 !== -0
        // 1/+0 = +Infinity; 1/-0 = -Infinity
        // +Infinity !== -Infinity
        // Infinity：正无穷大的数值；-Infinity：负无穷大的数值
        return x !== 0 || 1 / x === 1 / y;
      } else {
        // NaN == NaN
        // 一个变量不等于自身变量，那么它一定是 NaN
        // 两个都是 NaN 的时候返回 true
        return x !== x && y !== y;
      }
    };
    ```

## `Vue`

1. ### `Vue2.0` 响应式实现原理

1. ### `Vue3.0` 响应式实现原理

1. ### `computed` 的实现原理

1. ### `watch` 的实现原理

1. ### `$nextTick` 的实现原理

## `React`

## `Webpack`

1. ### 说说 `Webpack` 的实现原理

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
