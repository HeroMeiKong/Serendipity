# 从 Vue2 到 Vue3 的变更

## Vue3 中一些需要关注的新功能包括

### 1. 组合式 `API`

- #### 组合式 `API` 基础

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

- #### `Setup`

  - 参数
    `props`：***因为 `props` 是响应式的，你不能使用 `ES6` 解构，它会消除 `prop` 的响应性***
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

    参数 - `context`：`context` 是个普通 `JavaScript` 对象，可以解构：

    ```js
    export default {
      setup(props, context) {
        // Attribute (非响应式对象，等同于 $attrs)
        console.log(context.attrs)

        // 插槽 (非响应式对象，等同于 $slots)
        console.log(context.slots)

        // 触发事件 (方法，等同于 $emit)
        console.log(context.emit)

        // 暴露公共 property (函数)
        console.log(context.expose)
      }
      // or
      setup(props, { attrs, slots, emit, expose }) {
        ...
      }
    }
    ```

    `attrs` 和 `slots` 是有状态的对象，它们总是会随组件本身的更新而更新。这意味着你应该避免对它们进行解构，并始终**以 `attrs.x` 或 `slots.x` 的方式引用 `property`**。请注意，与 `props` 不同，`attrs` 和 `slots` 的 `property` 是非响应式的。**如果你打算根据 `attrs` 或 `slots` 的更改应用副作用，那么应该在 `onBeforeUpdate` 生命周期钩子中执行此操作。**
    <br>

  - 访问组件的 `property`
    执行 `setup` 时，你只能访问以下 property：`props、attrs、slots、emit`
    换句话说，你将无法访问以下组件选项：`data、computed、methods、refs` (模板 `ref`)
    <br>

  - 结合模板使用
    如果 `setup` 返回一个对象，那么该对象的 `property` 以及传递给 `setup` 的 `props` 参数中的 `property` 就都可以在模板中访问到：

    ```js
    <template>
      <div>{{ collectionName }}: {{ readersNumber }} {{ book.title }}</div>
    </template>

    <script>
      import { ref, reactive } from 'vue'

      export default {
        props: {
          collectionName: String
        },
        setup(props) {
          const readersNumber = ref(0)
          const book = reactive({ title: 'Vue 3 Guide' })

          // 暴露给 template
          return {
            readersNumber,
            book
          }
        }
      }
    </script>
    ```

    注意，从 `setup` 返回的 `refs` 在模板中访问时是被自动浅解包的，因此不应在模板中使用 `.value`。
    <br>

  - 使用渲染函数
    `setup` 还可以返回一个渲染函数，该函数可以直接使用在同一作用域中声明的响应式状态：

    ```js
    import { h, ref, reactive } from 'vue'

    export default {
      setup() {
        const readersNumber = ref(0)
        const book = reactive({ title: 'Vue 3 Guide' })
        // 请注意这里我们需要显式使用 ref 的 value
        return () => h('div', [readersNumber.value, book.title])
      }
    }
    ```

    返回一个渲染函数将阻止我们返回任何其它的东西。

    **可以通过调用 `expose` 来将这个组件的方法通过模板 `ref` 暴露给父组件，给它传递一个对象，其中定义的 `property` 将可以被外部组件实例访问**：

    ```js
    import { h, ref } from 'vue'
    export default {
      setup(props, { expose }) {
        const count = ref(0)
        const increment = () => ++count.value

        expose({
          increment
        })

        return () => h('div', count.value)
      }
    }
    ```

    这个 `increment` 方法现在将可以**通过父组件的模板 `ref` 访问。**

  - 使用 `this`
    **在 `setup()` 内部，`this` 不是该活跃实例的引用**，因为 `setup()` 是在解析其它组件选项之前被调用的，所以 `setup()` 内部的 `this` 的行为与其它选项中的 `this` 完全不同。这使得 `setup()` 在和其它选项式 `API` 一起使用时可能会导致混淆。

- #### 生命周期钩子

  选项式 API | Hook inside setup
  -|-
  beforeCreate | Not needed*
  created | Not needed*
  beforeMount | onBeforeMount
  mounted | onMounted
  beforeUpdate | onBeforeUpdate
  updated | onUpdated
  beforeUnmount | onBeforeUnmount
  unmounted | onUnmounted
  errorCaptured | onErrorCaptured
  renderTracked | onRenderTracked
  renderTriggered | onRenderTriggered
  activated | onActivated
  deactivated | onDeactivated

  **因为 `setup` 是围绕 `beforeCreate` 和 `created` 生命周期钩子运行的，所以不需要显式地定义它们。换句话说，在这些钩子中编写的任何代码都应该直接在 `setup` 函数中编写。**

  ```js
  export default {
    setup() {
      // mounted
      onMounted(() => {
        console.log('Component is mounted!')
      })
    }
  }
  ```

- #### Provide / Inject

  - eg:

    ```js
    <!-- src/components/MyMap.vue -->
    <template>
      <MyMarker />
    </template>

    <script>
    import MyMarker from './MyMarker.vue'

    export default {
      components: {
        MyMarker
      },
      provide: {
        location: 'North Pole',
        geolocation: {
          longitude: 90,
          latitude: 135
        }
      }
    }
    </script>

    <!-- src/components/MyMarker.vue -->
    <script>
    export default {
      inject: ['location', 'geolocation']
    }
    </script>
    ```

    <br>

  - 使用 `provide`：`provide(name<String>, value)`

    ```js
    <!-- src/components/MyMap.vue -->
    <template>
      <MyMarker />
    </template>

    <script>
    import { provide } from 'vue'
    import MyMarker from './MyMarker.vue'

    export default {
      components: {
        MyMarker
      },
      setup() {
        provide('location', 'North Pole')
        provide('geolocation', {
          longitude: 90,
          latitude: 135
        })
      }
    }
    </script>
    ```

    <br>

  - 使用 `inject`：`inject(name[, default_value])`

    ```js
    <!-- src/components/MyMarker.vue -->
    <script>
    import { inject } from 'vue'

    export default {
      setup() {
        const userLocation = inject('location', 'The Universe')
        const userGeolocation = inject('geolocation')

        return {
          userLocation,
          userGeolocation
        }
      }
    }
    </script>
    ```

    <br>

  - 响应性
  **当使用响应式 `provide / inject` 值时，建议尽可能将对响应式 `property` 的所有修改限制在定义 `provide` 的组件内部。** 当这两个 `property` 中有任何更改，`MyMarker` 组件也将自动更新！

    ```js
    <!-- src/components/MyMap.vue -->
    <template>
      <MyMarker />
    </template>

    <script>
    import { provide, reactive, ref } from 'vue'
    import MyMarker from './MyMarker.vue'

    export default {
      components: {
        MyMarker
      },
      setup() {
        const location = ref('North Pole')
        const geolocation = reactive({
          longitude: 90,
          latitude: 135
        })

        provide('location', location)
        provide('geolocation', geolocation)

        return {
          location
        }
      },
      methods: {
        updateLocation() {
          this.location = 'South Pole'
        }
      }
    }
    </script>
    ```

    <br>

    有时我们需要在注入数据的组件内部更新 `inject` 的数据。在这种情况下，我们建议 `provide` 一个方法来负责改变响应式 `property`

    ```js
    <!-- src/components/MyMap.vue -->
    <template>
      <MyMarker />
    </template>

    <script>
    import { provide, reactive, ref } from 'vue'
    import MyMarker from './MyMarker.vue'

    export default {
      components: {
        MyMarker
      },
      setup() {
        const location = ref('North Pole')
        const geolocation = reactive({
          longitude: 90,
          latitude: 135
        })

        const updateLocation = () => {
          location.value = 'South Pole'
        }

        provide('location', location)
        provide('geolocation', geolocation)
        provide('updateLocation', updateLocation)
      }
    }
    </script>


    <!-- src/components/MyMarker.vue -->
    <script>
    import { inject } from 'vue'

    export default {
      setup() {
        const userLocation = inject('location', 'The Universe')
        const userGeolocation = inject('geolocation')
        const updateUserLocation = inject('updateLocation')

        return {
          userLocation,
          userGeolocation,
          updateUserLocation
        }
      }
    }
    </script>
    ```

    <br>

    如果要确保通过 `provide` 传递的数据不会被 `inject` 的组件更改，我们建议对提供者的 `property` 使用 `readonly`。

    ```js
    <!-- src/components/MyMap.vue -->
    <template>
      <MyMarker />
    </template>

    <script>
    import { provide, reactive, readonly, ref } from 'vue'
    import MyMarker from './MyMarker.vue'

    export default {
      components: {
        MyMarker
      },
      setup() {
        const location = ref('North Pole')
        const geolocation = reactive({
          longitude: 90,
          latitude: 135
        })

        const updateLocation = () => {
          location.value = 'South Pole'
        }

        provide('location', readonly(location))
        provide('geolocation', readonly(geolocation))
        provide('updateLocation', updateLocation)
      }
    }
    </script>
    ```

- #### 模板引用

  这里我们在渲染上下文中暴露 `root`，并通过 `ref="root"`，将其绑定到 `div` 作为其 `ref`。在虚拟 `DOM` 补丁算法中，如果 `VNode` 的 `ref` 键对应于渲染上下文中的 `ref`，则 `VNode` 的相应元素或组件实例将被分配给该 `ref` 的值。这是在虚拟 `DOM` 挂载/打补丁过程中执行的，因此模板引用只会在初始渲染之后获得赋值。

  ```js
  <template> 
    <div ref="root">This is a root element</div>
  </template>

  <script>
    import { ref, onMounted } from 'vue'

    export default {
      setup() {
        const root = ref(null)

        onMounted(() => {
          // DOM 元素将在初始渲染后分配给 ref
          console.log(root.value) // <div>This is a root element</div>
        })

        return {
          root
        }
      }
    }
  </script>
  ```

  <br>

  - `JSX` 中的用法

    ```js
    export default {
      setup() {
        const root = ref(null)

        return () =>
          h('div', {
            ref: root
          })

        // with JSX
        return () => <div ref={root} />
      }
    }
    ```

  - `v-for` 中的用法

    ```js
    <template>
      <div v-for="(item, i) in list" :ref="el => { if (el) divs[i] = el }">
        {{ item }}
      </div>
    </template>

    <script>
      import { ref, reactive, onBeforeUpdate } from 'vue'

      export default {
        setup() {
          const list = reactive([1, 2, 3])
          const divs = ref([])

          // 确保在每次更新之前重置ref
          onBeforeUpdate(() => {
            divs.value = []
          })

          return {
            list,
            divs
          }
        }
      }
    </script>
    ```

  - 侦听模板引用

    但与生命周期钩子的一个关键区别是，`watch()` 和 `watchEffect()` 在 `DOM` 挂载或更新之前运行副作用，所以当侦听器运行时，模板引用还未被更新。

    ```js
    <template>
      <div ref="root">This is a root element</div>
    </template>

    <script>
      import { ref, watchEffect } from 'vue'

      export default {
        setup() {
          const root = ref(null)

          watchEffect(() => {
            // 这个副作用在 DOM 更新之前运行，因此，模板引用还没有持有对元素的引用。
            console.log(root.value) // => null
          })

          return {
            root
          }
        }
      }
    </script>
    ```

    因此，使用模板引用的侦听器应该用 `flush: 'post'` 选项来定义，这将在 `DOM` 更新后运行副作用，确保模板引用与 `DOM` 保持同步，并引用正确的元素。

    ```js
    <template>
      <div ref="root">This is a root element</div>
    </template>

    <script>
      import { ref, watchEffect } from 'vue'

      export default {
        setup() {
          const root = ref(null)

          watchEffect(() => {
            console.log(root.value) // => <div>This is a root element</div>
          }, 
          {
            flush: 'post'
          })

          return {
            root
          }
        }
      }
    </script>
    ```

### 2.  `Teleport`

  `Teleport` 提供了一种干净的方法，允许我们控制在 `DOM` 中哪个父节点下渲染了 `HTML`，而不必求助于全局状态或将其拆分为两个组件。
  
- #### 与 `Vue components` 一起使用

    如果 `<teleport>` 包含 `Vue` 组件，则它仍将是 `<teleport>` 父组件的逻辑子组件：

    ```js
    const app = Vue.createApp({
      template: `
        <h1>Root instance</h1>
        <parent-component />
      `
    })

    app.component('parent-component', {
      template: `
        <h2>This is a parent component</h2>
        <teleport to="#endofbody">
          <child-component name="John" />
        </teleport>
      `
    })

    app.component('child-component', {
      props: ['name'],
      template: `
        <div>Hello, {{ name }}</div>
      `
    })
    ```

- #### 在同一目标上使用多个 `teleport`

    多个 `<teleport>` 组件可以将其内容挂载到同一个目标元素。顺序将是一个简单的追加——稍后挂载将位于目标元素中较早的挂载之后。

    ```js
    <teleport to="#modals">
      <div>A</div>
    </teleport>
    <teleport to="#modals">
      <div>B</div>
    </teleport>

    <!-- result-->
    <div id="modals">
      <div>A</div>
      <div>B</div>
    </div>
    ```

### 3. 片段

- #### 3.x 语法

    在 `3.x` 中，组件可以包含多个根节点！但是，这要求开发者显式定义 `attribute` 应该分布在哪里。

    ```js
    <template>
      <header>...</header>
      <main v-bind="$attrs">...</main>
      <footer>...</footer>
    </template>
    ```

### 4.  触发组件选项

- #### 事件名

    与组件和 `prop` 一样，事件名提供了自动的大小写转换。如果在子组件中触发一个以 `camelCase` (驼峰式命名) 命名的事件，你将可以在父组件中添加一个 `kebab-case` (短横线分隔命名) 的监听器。

    `this.$emit('myEvent')`
    `<my-component @my-event="doSomething"></my-component>`
    与 `props` 的命名一样，当你使用 `DOM` 模板时，我们建议使用 `kebab-case` 事件监听器。如果你使用的是字符串模板，这个限制就不适用。

- #### 定义自定义事件

    可以通过 `emits` 选项在组件上定义发出的事件。

    ```js
    app.component('custom-form', {
      emits: ['inFocus', 'submit']
    })
    ```

    当在 `emits` 选项中定义了原生事件 (如 `click`) 时，将使用组件中的事件替代原生事件侦听器。
    <br>

    **要添加验证，请为事件分配一个函数，该函数接收传递给 `$emit` 调用的参数，并返回一个布尔值以指示事件是否有效。**

    ```js
    app.component('custom-form', {
      emits: {
        // 没有验证
        click: null,

        // 验证 submit 事件
        submit: ({ email, password }) => {
          if (email && password) {
            return true
          } else {
            console.warn('Invalid submit event payload!')
            return false
          }
        }
      },
      methods: {
        submitForm(email, password) {
          this.$emit('submit', { email, password })
        }
      }
    })
    ```

- #### `v-model` 参数

    默认情况下，组件上的 `v-model` 使用 `modelValue` 作为 `prop` 和 `update:modelValue` 作为事件。我们可以通过向 `v-model` 传递参数来修改这些名称：

    `<my-component v-model:title="bookTitle"></my-component>`
    在本例中，子组件将需要一个 `title prop` 并发出 `update:title` 事件来进行同步：

    ```js
    app.component('my-component', {
      props: {
        title: String
      },
      emits: ['update:title'],
      template: `
        <input
          type="text"
          :value="title"
          @input="$emit('update:title', $event.target.value)">
      `
    })
    ```

- #### 多个 `v-model` 绑定

    ```js
    <user-name
      v-model:first-name="firstName"
      v-model:last-name="lastName">
    </user-name>

    app.component('user-name', {
      props: {
        firstName: String,
        lastName: String
      },
      emits: ['update:firstName', 'update:lastName'],
      template: `
        <input 
          type="text"
          :value="firstName"
          @input="$emit('update:firstName', $event.target.value)">

        <input
          type="text"
          :value="lastName"
          @input="$emit('update:lastName', $event.target.value)">
      `
    })
    ```

- #### 处理 `v-model` 修饰符

    `v-model` 有内置修饰符——`.trim`、`.number` 和 `.lazy`，还可自定义，eg：`capitalize`。

    ***当组件的 `created` 生命周期钩子触发时，`modelModifiers prop` 会包含 `capitalize`，且其值为 `true`——因为 `capitalize` 被设置在了写为 `v-model.capitalize="myText"` 的 `v-model` 绑定上。***

    我们可以检查 `modelModifiers` 对象键并编写一个处理器来更改发出的值。在下面的代码中，每当 `<input/>` 元素触发 `input` 事件时，我们都将字符串大写。

    ```js
    <div id="app">
      <my-component v-model.capitalize="myText"></my-component>
      {{ myText }}
    </div>

    const app = Vue.createApp({
      data() {
        return {
          myText: ''
        }
      }
    })

    app.component('my-component', {
      props: {
        modelValue: String,
        modelModifiers: {
          default: () => ({})
        }
      },
      emits: ['update:modelValue'],
      methods: {
        emitValue(e) {
          let value = e.target.value
          if (this.modelModifiers.capitalize) {
            value = value.charAt(0).toUpperCase() + value.slice(1)
          }
          this.$emit('update:modelValue', value)
        }
      },
      template: `<input
        type="text"
        :value="modelValue"
        @input="emitValue">`
    })

    app.mount('#app')
    ```

    对于带参数的 `v-model` 绑定，生成的 `prop` 名称将为 `arg + "Modifiers"`：

    ```js
    <my-component v-model:description.capitalize="myText"></my-component>

    app.component('my-component', {
      props: ['description', 'descriptionModifiers'],
      emits: ['update:description'],
      template: `
        <input type="text"
          :value="description"
          @input="$emit('update:description', $event.target.value)">
      `,
      created() {
        console.log(this.descriptionModifiers) // { capitalize: true }
      }
    })
    ```

### 5.  来自 `@vue/runtime-core` 的 `createRenderer API`，用于创建自定义渲染器

- #### 创建自定义渲染器

  ```js
  import { createRenderer } from '@vue/runtime-core'

  const { render, createApp } = createRenderer({
    patchProp,
    insert,
    remove,
    createElement,
    // ...
  })

  // `render` is the low-level API
  // `createApp` returns an app instance with configurable context shared
  // by the entire app tree.
  export { render, createApp }

  export * from '@vue/runtime-core'
  ```

### 6.  单文件组件组合式 `API` 语法糖 (`<script setup>`)

### 7.  单文件组件状态驱动的 `CSS` 变量 (`<style>` 中的 `v-bind`)

### 8.  `SFC <style scoped>` 现在可以包含全局规则或只针对插槽内容的规则

### 9.  `Suspense`(实验性)

## 全局 API

### 10. 全局 `Vue API` 已更改为使用应用程序实例

### 11. 全局和内部 `API` 已经被重构为支持 `tree-shake`

## 模板指令

### 12. 组件上 `v-model` 用法已更改，以替换 `v-bind.sync`

### 13. `<template v-for>` 和非 `v-for` 节点上的 `key` 用法已更改

### 14. 在同一元素上使用的 `v-if` 和 `v-for` 优先级已更改

### 15. `v-bind="object"` 现在排序敏感

### 16. `v-on:event.native` 修饰符已移除

### 17. `v-for` 中的 `ref` 不再注册 `ref` 数组

## 组件

### 18. 只能使用普通函数创建函数式组件

### 19. `functional attribute` 在单文件组件 (`SFC`) 的 `<template>` 和 `functional` 组件选项中被废弃

### 20. 异步组件现在需要使用 `defineAsyncComponent` 方法来创建

### 21. 组件事件现在需要在 `emits` 选项中声明

## 渲染函数

### 22. 渲染函数 `API` 更改

### 23. `$scopedSlots property` 已移除，所有插槽都通过 `$slots` 作为函数暴露

### 24. `$listeners` 被移除或整合到 `$attrs`

### 25. `$attrs` 现在包含 `class` 和 `style attribute`

## 自定义元素

### 26. 自定义元素检测现在在模板编译时执行

### 27. 特殊的 `is attribute` 的使用被严格限制在被保留的 `<component>` 标签中

## 其他小改变

### 28. `destroyed` 生命周期选项被重命名为 `unmounted`

### 29. `beforeDestroy` 生命周期选项被重命名为 `beforeUnmount`

### 30. `default prop` 工厂函数不再可以访问 `this` 上下文

### 31. 自定义指令的 `API` 已更改为与组件生命周期一致，且 `binding.expression` 已移除

### 32. `data` 选项应始终被声明为一个函数

### 33. 来自 `mixin` 的 `data` 选项现在为浅合并

### 34. `Attribute` 强制策略已更改

### 35. 一些过渡的 `class` 被重命名

### 36. `<TransitionGroup>` 不再默认渲染包裹元素

### 37. 当侦听一个数组时，只有当数组被替换时，回调才会触发，如果需要在变更时触发，则必须指定 `deep` 选项

### 38. 没有特殊指令的标记 (`v-if/else-if/else、v-for` 或 `v-slot`) 的 `<template>` 现在被视为普通元素，并将渲染为原生的 `<template>` 元素，而不是渲染其内部内容。

### 39. 已挂载的应用不会取代它所挂载的元素

### 40. 生命周期的 `hook:` 事件前缀改为 `vnode-`

## 被移除的 API

### 41. `keyCode` 作为 `v-on` 修饰符的支持

### 42. `$on、$off` 和 `$once` 实例方法

### 43. 过滤器 (`filter`)

### 44. 内联模板 `attribute`

### 45. `$children` 实例 `property`

### 46. `propsData` 选项

### 47. `$destroy` 实例方法。用户不应再手动管理单个 `Vue` 组件的生命周期。

### 48. 全局函数 `set` 和 `delete` 以及实例方法 `$set` 和 `$delete`。基于代理的变化检测已经不再需要它们了
