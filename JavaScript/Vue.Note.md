# JavaScript篇笔记

★★★★★★★★★★

更新于2019-01-10
这是自己平时书写的笔记，有很多漏洞，也有很多错误，还望大家多多指正，感谢🙏

★★★★★★★★★★

1. `app.$mount("#app")` 手动挂载，当 Vue 实例没有 `el` 属性时，则该实例没有挂载到某个 `dom` 中；假如需要延迟挂载，可以在之后手动调用 `vm.$mount` 方法来挂载
2. Vue 的单文件组件 `<template>HTML</template>`、`<script>JavaScript</script>`、`<style>CSS</style>`
3. Vue 的插槽 `<slot></slot>`
4. Vue 中 `':'` 是 `'v-bind:'` 的缩写
5. 在 `props` 里写的 `iconPosition` 到HTML会被 **Vue 自动转换**为 `icon-position` (HTML 规范中不能有大写)
6. Vue 的属性检测器 `validator(xxx) {}` 用来检测用户是否按照要求设置属性
7. 若在属性前不加 `':/v-bind:'` 则被视为字符串，加了以后 `''` 可有可无
8. `mounted() {}` 当该组件出现在页面中时便会执行
9. Vue 中 `this.$children` 只会寻找 Vue 的实例无法区分，可用 `this.$el`
10. 用 `$mount('#app')` 动态加载组件
11. 若不把元素加载到页面，页面便不会渲染它，也不会加载其 CSS 样式，故有时 `$mount()` 需加参数
12. Vue 中使用自定义组件到方法：
    - 全局注册
    - 局部组件( `export default { components: { 'g-icon': Icon } }`)
13. `:class = "{ 'error': error }" => :class = "{error}"`
14. 如果你不想在 `div` 里添加一个 `v-if` (增加一个标签)，那么可使用 `template` 标签
15. 在 `<input>` 中有原生的 `change` 事件，Vue 通过 `$event` 来表示:

    `@change = "emit('change', $event)"`、`emit('change', $event, 'hi', 'How')`
16. `v-model = "message"` <=> `:value = "message", @input = "message = $event.target.value"`

    `value` 绑定 `message` 属性，`input` 发出来一个事件，将 `message` 作为该事件值
17. Vue 的 `dom diff` 检查到双向数据一样，便不会改变
18. Vue 渲染的标签内不能添加 `<style>` 标签
19. `props: { span: [Number, String] }`
20. `Created()` 与 `mounted()` 的区别：`Created()` 是创建对象(标签)，`mounted()` 是将其加载(挂载)到页面 — `appendChild`

    Vue 加载过程：

    ```javascript
    var div = createElement('div');      // 先创建父元素
    var childDiv = createElement('div'); // 再创建子元素
    div.appendChild(childDiv);           // 将子元素挂载到父元素
    document.body.appendChild(div);      // 将父元素挂载到网页上
    ```

21. `data`：是只在一开始的时候读一次，变化之后不改变

    `computed`：响应式，只要数据一变就会随之改变，且有缓存，故每次刷新并不会改变
22. `validator`：控制用户输入，只能使用其中值，其它就提示或保错

    ```javascript
    validator(value) {
      return ['left', 'right', 'center'].includes(value) // 可能不兼容，可换成array的indexOf()
    }
    ```

23. 如果是异步渲染，注意时间问题，用钩子函数便存在异步
24. `<transition>` 标签添加动画
25. `options.name` 就是 `name` 的值，用于回调
26. Vue 添加插件/自定义方法：
    - `Vue.prototype.$toast = function () {}`
    - 插件：

      ```javascript
      MyPlugin.install = function (Vue, options) {
        // 1.添加全局方法或属性
        Vue.myGlobalMethod = function () {}
        // 2.添加全局资源
        Vue.directive('my-directive', {
          bind(el, binding, vnode, oldVnode) {}
        })
        // 3.注入组件
        Vue.mixin({ created: function () {} })
        // 4.添加实例方法
        Vue.prototype.$myMethod = function (methodsOptions) {}
      }
      // 使用插件：
      // 调用 myPlugin.install(Vue)
      Vue.use(MyPlugin)
      
      new Vue({
      // ... options
      })
      // 或者使用
      Vue.use(MyPlugin, { someOption: true })
      // Vue.use 会自动阻止多次注册相同插件，届时只会注册一次该插件。
      ```

27. ★Vue 中如果你的 `default` 需要是一个对象，则写成函数然后 `return` 一个对象(这是考虑到复用性—不这样写的话，如果有两个组件同时使用，而其中一个被更改了，那另一个也会被更改)
例：

    ```javascript
    default: () => {
      return { 
        text: '关闭',
        callback: (toast) => {
          toast.close()
        }
      }
    }
    ```

28. Vue 调试，可在 `created()` 中测试
29. `includes` 兼容不是很好，用 `array` 的 `indexOf` 代替 `return ['top', 'bottom'].indexOf(value) >= 0`
30. Vue 中更新数据：

    ```jsx
    <g-tab selected="selectedTab" @update:selected="selectedTab = $event">
    <g-tab selected.sync="selectedTab">
    <g-tab selected="selectedTab" v-bind:update:selected="selectedTab = $event">
    ```

31. 如果值为变量，则需要加 `':'`，若为常量就不用加
32. `required: true`，值必须要
33. 新增插槽：
    Vue | HTML
    :-: | :-:
    `<slot></slot>` && `<slot name="active"></slot>` | `<template slot="action"></template>`
34. Vue 中有 `class` 和 `style` 会默认添加，其它属性都是覆盖
35. 订阅发布模式：`provide() { return { eventBus … } }` — 所有子孙后代都能访问的属性，跨组件属性
子孙：通过 `inject: ['eventBus']`，去访问 `eventBus`
36. Vue 事件不会冒泡，事件也要看在哪个对象上触发的
37. `data` 与 `props` 的区别？

    如果需要用户传值则用 `props`，不需要用户传值用 `data`

    组件就像一个函数，`props` 就是形参，`data` 就是局部变量
38. Vue 默认插槽，不需要用 `<template>` 包住
39. `@click.stop = "xxx"` 阻止冒泡，自行处理
40. `v-if` 是有没有(是否在 `dom` 中)，`v-show` 是有但看不看得见(改变样式)
41. `<slot>` 不支持引用与类(`ref`、`class`)
42. 可通过 `<slot>` 的 `slot-scope` 取得组件内部的方法，在组件中传值：`yk = "1"`，在网页中取值：`slot-scope = "{yk}"`
43. Vue 的特性如下：
    - 轻量级的框架
    - 双向数据绑定
    - 指令
    - 插件化
