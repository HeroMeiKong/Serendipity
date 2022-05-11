# 从 Vue2 到 Vue3 的变更

## Vue3 中一些需要关注的新功能包括

### 1. 组合式 `API`

1. #### 组合式 `API` 基础

    - **`setup` 组件选项**

      新的 `setup` 选项在组件被创建之前执行，一旦 `props` 被解析完成，它就将被作为组合式 `API` 的入口。
      <br>

      ***在 `setup` 中你应该避免使用 `this`，因为它不会找到组件实例。`setup` 的调用发生在 `data property、computed property` 或 `methods` 被解析之前，所以它们无法在 `setup` 中被获取。***
      <br>

      `setup` 选项是一个接收 `props` 和 `context` 的函数。我们将 `setup` 返回的所有内容都暴露给组件的其余部分 (计算属性、方法、生命周期钩子等等) 以及组件的模板。

      ```js
      // src/components/UserRepositories.vue

      export default {
        components: { ... },
        props: {
          user: { 
            type: String,
            required: true
          }
        },
        data () {
          return {
            repositories: [], // 1
          }
        },
        computed: {},
        watch: {
          user: 'getUserRepositories' // 1
        },
        methods: {
          getUserRepositories () {
            // 使用 `this.user` 获取用户仓库
          } // 1
        },
        mounted () {
          this.getUserRepositories() // 1
        }
      }

      /*************************************/
      // 添加 setup 之后
      // src/components/UserRepositories.vue
      import { fetchUserRepositories } from '@/api/repositories'
      import { ref } from 'vue'

      export default {
        components: { ... },
        props: {
          user: {
            type: String,
            required: true
          }
        },
        setup (props) {
          const repositories = ref([])
          const getUserRepositories = async () => {
            repositories.value = await fetchUserRepositories(props.user)
          }

          return {
            repositories,
            getUserRepositories
          }
        },
        data () {
          return {}
        },
        computed: {},
        watch: {
          user: 'getUserRepositories' // 1
        },
        methods: {},
        mounted () {
          this.getUserRepositories() // 1
        }
      }
      ```

    - **带 `ref` 的响应式变量**
    可以通过一个新的 `ref` 函数使任何响应式变量在任何地方起作用

      ```js
      import { ref } from 'vue'

      const counter = ref(0)

      console.log(counter) // { value: 0 }
      console.log(counter.value) // 0

      counter.value++
      console.log(counter.value) // 1
      ```

      将值封装在一个对象中，看似没有必要，但为了保持 `JavaScript` 中不同数据类型的行为统一，这是必须的。这是因为在 `JavaScript` 中，`Number` 或 `String` 等基本类型是通过值而非引用传递的
      <br>

    - **在 `setup` 内注册生命周期钩子**

      ```js
      // src/components/UserRepositories.vue `setup` function
      import { fetchUserRepositories } from '@/api/repositories'
      import { ref, onMounted } from 'vue'

      // 在我们的组件中
      setup (props) {
        const repositories = ref([])
        const getUserRepositories = async () => {
          repositories.value = await fetchUserRepositories(props.user)
        }

        onMounted(getUserRepositories) // 在 `mounted` 时调用 `getUserRepositories`

        return {
          repositories,
          getUserRepositories
        }
      }
      ```

    - **`watch` 响应式更改**
    它接受 3 个参数：
      - 一个想要侦听的响应式引用或 `getter` 函数
      - 一个回调
      - 可选的配置选项

      ```js
      import { ref, watch } from 'vue'

      const counter = ref(0)
      watch(counter, (newValue, oldValue) => {
        console.log('The new counter value is: ' + counter.value)
      })


      /**************/
      // 等效于
      export default {
        data() {
          return {
            counter: 0
          }
        },
        watch: {
          counter(newValue, oldValue) {
            console.log('The new counter value is: ' + this.counter)
          }
        }
      }
      ```

    - **独立的 `computed` 属性**

      ```js
      import { ref, computed } from 'vue'

      const counter = ref(0)
      const twiceTheCounter = computed(() => counter.value * 2)
      ```

    - 最终效果

      ```js
      // src/components/UserRepositories.vue `setup` function
      import { fetchUserRepositories } from '@/api/repositories'
      import { ref, onMounted, watch, toRefs, computed } from 'vue'

      // 在我们的组件中
      setup (props) {
        // 使用 `toRefs` 创建对 props 中的 `user` property 的响应式引用
        const { user } = toRefs(props)

        const repositories = ref([])
        const getUserRepositories = async () => {
          // 更新 `props.user ` 到 `user.value` 访问引用值
          repositories.value = await fetchUserRepositories(user.value)
        }

        onMounted(getUserRepositories)

        // 在 user prop 的响应式引用上设置一个侦听器
        watch(user, getUserRepositories)

        const searchQuery = ref('')
        const repositoriesMatchingSearchQuery = computed(() => {
          return repositories.value.filter(
            repository => repository.name.includes(searchQuery.value)
          )
        })

        return {
          repositories,
          getUserRepositories,
          searchQuery,
          repositoriesMatchingSearchQuery
        }
      }
      ```

1. #### `Setup`

    ***因为 `props` 是响应式的，你不能使用 `ES6` 解构，它会消除 `prop` 的响应性***
    可以在 `setup` 函数中使用 `toRefs` 函数来解构 `prop`：

    ```js
    import { toRefs } from 'vue'

    setup(props) {
      const { title } = toRefs(props)
      // 如果 title 是可选的 prop，则传入的 props 中可能没有 title 。在这种情况下，toRefs 将不会为 title 创建一个 ref 。你需要使用 toRef 替代它：
      const title = toRef(props, 'title')
      console.log(title.value)
    }
    ```

### 1.  `Teleport`

### 1.  片段

### 1.  触发组件选项

### 1.  来自 `@vue/runtime-core` 的 `createRenderer API`，用于创建自定义渲染器

### 1.  单文件组件组合式 `API` 语法糖 (`<script setup>`)

### 1.  单文件组件状态驱动的 `CSS` 变量 (`<style>` 中的 `v-bind`)

### 1.  `SFC <style scoped>` 现在可以包含全局规则或只针对插槽内容的规则

### 1.  `Suspense`(实验性)

## 全局 API

- 全局 `Vue API` 已更改为使用应用程序实例
- 全局和内部 `API` 已经被重构为支持 `tree-shake`

## 模板指令

- 组件上 `v-model` 用法已更改，以替换 `v-bind.sync`
- `<template v-for>` 和非 `v-for` 节点上的 `key` 用法已更改
- 在同一元素上使用的 `v-if` 和 `v-for` 优先级已更改
- `v-bind="object"` 现在排序敏感
- `v-on:event.native` 修饰符已移除
- `v-for` 中的 `ref` 不再注册 `ref` 数组

## 组件

- 只能使用普通函数创建函数式组件
- `functional attribute` 在单文件组件 (`SFC`) 的 `<template>` 和 `functional` 组件选项中被废弃
- 异步组件现在需要使用 `defineAsyncComponent` 方法来创建
- 组件事件现在需要在 `emits` 选项中声明

## 渲染函数

- 渲染函数 `API` 更改
- `$scopedSlots property` 已移除，所有插槽都通过 `$slots` 作为函数暴露
- `$listeners` 被移除或整合到 `$attrs`
- `$attrs` 现在包含 `class` 和 `style attribute`

## 自定义元素

- 自定义元素检测现在在模板编译时执行
- 特殊的 `is attribute` 的使用被严格限制在被保留的 `<component>` 标签中

## 其他小改变

- `destroyed` 生命周期选项被重命名为 `unmounted`
- `beforeDestroy` 生命周期选项被重命名为 `beforeUnmount`
- `default prop` 工厂函数不再可以访问 `this` 上下文
- 自定义指令的 `API` 已更改为与组件生命周期一致，且 `binding.expression` 已移除
- `data` 选项应始终被声明为一个函数
- 来自 `mixin` 的 `data` 选项现在为浅合并
- `Attribute` 强制策略已更改
- 一些过渡的 `class` 被重命名
- `<TransitionGroup>` 不再默认渲染包裹元素
- 当侦听一个数组时，只有当数组被替换时，回调才会触发，如果需要在变更时触发，则必须指定 `deep` 选项
- 没有特殊指令的标记 (`v-if/else-if/else、v-for` 或 `v-slot`) 的 `<template>` 现在被视为普通元素，并将渲染为原生的 `<template>` 元素，而不是渲染其内部内容。
- 已挂载的应用不会取代它所挂载的元素
- 生命周期的 `hook:` 事件前缀改为 `vnode-`

## 被移除的 API

- `keyCode` 作为 `v-on` 修饰符的支持
- `$on、$off` 和 `$once` 实例方法
- 过滤器 (`filter`)
- 内联模板 `attribute`
- `$children` 实例 `property`
- `propsData` 选项
- `$destroy` 实例方法。用户不应再手动管理单个 `Vue` 组件的生命周期。
- 全局函数 `set` 和 `delete` 以及实例方法 `$set` 和 `$delete`。基于代理的变化检测已经不再需要它们了。
