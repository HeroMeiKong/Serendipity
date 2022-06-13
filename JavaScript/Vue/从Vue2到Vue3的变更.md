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

    /*
     *****
    */

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


    /*
     *****
    */

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

- 优势：
  - 少的样板内容，更简洁的代码。
  - 能够使用纯 `Typescript` 声明 `props` 和抛出事件。
  - 更好的运行时性能 (其模板会被编译成与其同一作用域的渲染函数，没有任何的中间代理)。
  - 更好的 `IDE` 类型推断性能 (减少语言服务器从代码中抽离类型的工作)。
  <br>

- #### 基本语法

  ```js
  <script setup>
  console.log('hello script setup')
  </script>
  ```

  里面的代码会被编译成组件 `setup()` 函数的内容。这意味着与普通的 `<script>` 只在组件被首次引入的时候执行一次不同，`<script setup>` 中的代码会在每次组件实例被创建的时候执行。
  <br>

  **顶层的绑定会被暴露给模板**
  任何在 `<script setup>` 声明的顶层的绑定 (包括变量，函数声明，以及 `import` 引入的内容) 都能在模板中直接使用：

  ```js
  <script setup>
  // import 引入
  import { capitalize } from './helpers'
  // 变量
  const msg = 'Hello!'

  // 函数
  function log() {
    console.log(msg)
  }
  </script>

  <template>
    <div>{{ capitalize('hello') }}</div>
    <div @click="log">{{ msg }}</div>
  </template>
  ```

  <br>

- #### 响应式

  响应式状态需要明确使用响应式 `APIs` 来创建。和从 `setup()` 函数中返回值一样，`ref` 值在模板中使用的时候会自动解包：

  ```js
  <script setup>
  import { ref } from 'vue'

  const count = ref(0)
  </script>

  <template>
    <button @click="count++">{{ count }}</button>
  </template>
  ```

  <br>

- #### 使用组件

  ```js
  <script setup>
  import MyComponent from './MyComponent.vue'
  </script>

  <template>
    <MyComponent />
    // or
    // <my-component />
  </template>
  ```

  <br>

  **动态组件**
  由于组件被引用为变量而不是作为字符串键来注册的，在 `<script setup>` 中要使用动态组件的时候，就应该使用动态的 `:is` 来绑定：

  ```js
  <script setup>
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'
  </script>

  <template>
    <component :is="Foo" />
    <component :is="someCondition ? Foo : Bar" />
  </template>
  ```

  <br>

  **递归组件**
  一个单文件组件可以通过它的文件名被其自己所引用。例如：名为 `FooBar.vue` 的组件可以在其模板中用 `<FooBar/>` 引用它自己。

  请注意这种方式相比于 `import` 导入的组件优先级更低。如果有命名的 `import` 导入和组件的推断名冲突了，可以使用 `import` 别名导入：

  ```js
  import { FooBar as FooBarChild } from './components'
  ```

  <br>

  **命名空间组件**
  可以使用带点的组件标记，例如 `<Foo.Bar>` 来引用嵌套在对象属性中的组件。这在需要从单个文件中导入多个组件的时候非常有用：

  ```js
  <script setup>
  import * as Form from './form-components'
  </script>

  <template>
    <Form.Input>
      <Form.Label>label</Form.Label>
    </Form.Input>
  </template>
  ```

  <br>

- #### 使用自定义指令

  全局注册的自定义指令将以符合预期的方式工作，且本地注册的指令可以直接在模板中使用，就像上文所提及的组件一样。

  但这里有一个需要注意的限制：必须以 `vNameOfDirective` 的形式来命名本地自定义指令，以使得它们可以直接在模板中使用。

  ```js
  <script setup>
  const vMyDirective = {
    beforeMount: (el) => {
      // 在元素上做些操作
    }
  }
  </script>
  <template>
    <h1 v-my-directive>This is a Heading</h1>
  </template>

  /* -------------------- */
  <script setup>
    // 导入的指令同样能够工作，并且能够通过重命名来使其符合命名规范
    import { myDirective as vMyDirective } from './MyDirective.js'
  </script>
  ```

  <br>

- #### `defineProps` 和 `defineEmits`

  在 `<script setup>` 中必须使用 `defineProps` 和 `defineEmits API` 来声明 `props` 和 `emits`，它们具备完整的类型推断并且在 `<script setup>` 中是直接可用的：

  ```js
  <script setup>
  const props = defineProps({
    foo: String
  })

  const emit = defineEmits(['change', 'delete'])
  // setup code
  </script>
  ```

  - `defineProps` 和 `defineEmits` 都是只在 `<script setup>` 中才能使用的编译器宏。他们不需要导入且会随着 `<script setup>` 处理过程一同被编译掉。

  - `defineProps` 接收与 `props` 选项相同的值，`defineEmits` 也接收 `emits` 选项相同的值。

  - `defineProps` 和 `defineEmits` 在选项传入后，会提供恰当的类型推断。

  - 传入到 `defineProps` 和 `defineEmits` 的选项会从 `setup` 中提升到模块的范围。因此，传入的选项不能引用在 `setup` 范围中声明的局部变量。这样做会引起编译错误。但是，它可以引用导入的绑定，因为它们也在模块范围内。
  <br>

- #### `defineExpose`

  使用 `<script setup>` 的组件是默认关闭的，也即通过模板 `ref` 或者 `$parent` 链获取到的组件的公开实例，不会暴露任何在 `<script setup>` 中声明的绑定。

  为了在 `<script setup>` 组件中明确要暴露出去的属性，使用 `defineExpose` 编译器宏：

  ```js
  <script setup>
  import { ref } from 'vue'

  const a = 1
  const b = ref(2)

  defineExpose({
    a,
    b
  })
  </script>
  ```

  当父组件通过模板 `ref` 的方式获取到当前组件的实例，获取到的实例会像这样 `{ a: number, b: number }` (`ref` 会和在普通实例中一样被自动解包)

  <br>

- #### `useSlots` 和 `useAttrs`

  在 `<script setup>` 使用 `slots` 和 `attrs` 的情况应该是很罕见的，因为可以在模板中通过 `$slots` 和 `$attrs` 来访问它们。在你的确需要使用它们的罕见场景中，可以分别用 `useSlots` 和 `useAttrs` 两个辅助函数：

  ```js
  <script setup>
  import { useSlots, useAttrs } from 'vue'

  const slots = useSlots()
  const attrs = useAttrs()
  </script>
  ```

  `useSlots` 和 `useAttrs` 是真实的运行时函数，它会返回与 `setupContext.slots` 和 `setupContext.attrs` 等价的值，同样也能在普通的组合式 `API` 中使用。

  <br>

- #### 与普通 `<script>` 一起使用

  `<script setup>` 可以和普通的 `<script>` 一起使用。普通的 `<script>` 在有这些需要的情况下或许会被使用到：

  无法在 `<script setup>` 声明的选项，例如 `inheritAttrs` 或通过插件启用的自定义的选项。
  声明命名导出。
  运行副作用或者创建只需要执行一次的对象。

  ```js
  <script>
  // 普通 <script>, 在模块范围下执行(只执行一次)
  runSideEffectOnce()

  // 声明额外的选项
  export default {
    inheritAttrs: false,
    customOptions: {}
  }
  </script>

  <script setup>
  // 在 setup() 作用域中执行 (对每个实例皆如此)
  </script>
  ```

  >该场景下不支持使用 `render` 函数。请使用一个普通的 `<script>` 结合 `setup` 选项来代替。

  <br>

- #### 顶层 `await`

  `<script setup>` 中可以使用顶层 `await`。结果代码会被编译成 `async setup()`：

  ```js
  <script setup>
  const post = await fetch(`/api/post/1`).then(r => r.json())
  </script>
  ```

  另外，`await` 的表达式会自动编译成在 `await` 之后保留当前组件实例上下文的格式。
  <br>

  >注意
  `async setup()` 必须与 `Suspense` 组合使用，`Suspense` 目前还是处于实验阶段的特性。我们打算在将来的某个发布版本中开发完成并提供文档 - 如果你现在感兴趣，可以参照 [`tests`](https://github.com/vuejs/core/blob/main/packages/runtime-core/__tests__/components/Suspense.spec.ts) 看它是如何工作的。
  <br>

- #### 仅限 `TypeScript` 的功能

  **仅限类型的 `props/emit` 声明**
  `props` 和 `emits` 都可以使用传递字面量类型的纯类型语法做为参数给 `defineProps` 和 `defineEmits` 来声明：

  ```js
  const props = defineProps<{
    foo: string
    bar?: number
  }>()

  const emit = defineEmits<{
    (e: 'change', id: number): void
    (e: 'update', value: string): void
  }>()
  ```

  - `defineProps` 或 `defineEmits` 只能是要么使用运行时声明，要么使用类型声明。同时使用两种声明方式会导致编译报错。

  - 使用类型声明的时候，静态分析会自动生成等效的运行时声明，以消除双重声明的需要并仍然确保正确的运行时行为。

    - 在开发环境下，编译器会试着从类型来推断对应的运行时验证。例如这里从 `foo: string` 类型中推断出 `foo: String`。如果类型是对导入类型的引用，这里的推断结果会是 `foo: null` (与 `any` 类型相等)，因为编译器没有外部文件的信息。

    - 在生产模式下，编译器会生成数组格式的声明来减少打包体积 (这里的 `props` 会被编译成 `['foo', 'bar']`)。

    - 生成的代码仍然是有着类型的 `Typescript` 代码，它会在后续的流程中被其它工具处理。

  - 截至目前，类型声明参数必须是以下内容之一，以确保正确的静态分析：

    - 类型字面量
    - 在同一文件中的接口或类型字面量的引用

  现在还不支持复杂的类型和从其它文件进行类型导入。理论上来说，将来是可能实现类型导入的。
  <br>

  **使用类型声明时的默认 `props` 值**
  仅限类型的 `defineProps` 声明的不足之处在于，它没有可以给 `props` 提供默认值的方式。为了解决这个问题，提供了 `withDefaults` 编译器宏：

  ```js
  interface Props {
    msg?: string
    labels?: string[]
  }

  const props = withDefaults(defineProps<Props>(), {
    msg: 'hello',
    labels: () => ['one', 'two']
  })
  ```

  上面代码会被编译为等价的运行时 `props` 的 `default` 选项。此外，`withDefaults` 辅助函数提供了对默认值的类型检查，并确保返回的 `props` 的类型删除了已声明默认值的属性的可选标志。
  <br>

- #### 限制：没有 `Src` 导入

  由于模块执行语义的差异，`<script setup>` 中的代码依赖单文件组件的上下文。当将其移动到外部的 `.js` 或者 `.ts` 文件中的时候，对于开发者和工具来说都会感到混乱。因而 `<script setup>` 不能和 `src` attribute 一起使用。

### 7.  单文件组件状态驱动的 `CSS` 变量 (`<style>` 中的 `v-bind`)

- 单文件组件的 `<style>` 标签可以通过 `v-bind` 这一 `CSS` 函数将 `CSS` 的值关联到动态的组件状态上：

  ```js
  <template>
    <div class="text">hello</div>
  </template>

  <script>
  export default {
    data() {
      return {
        color: 'red'
      }
    }
  }
  </script>

  <style>
  .text {
    color: v-bind(color);
  }
  </style>
  ```

  <br>

  这个语法同样也适用于 `<script setup>`，且支持 `JavaScript` 表达式 (需要用引号包裹起来)

  ```js
  <script setup>
  const theme = {
    color: 'red'
  }
  </script>

  <template>
    <p>hello</p>
  </template>

  <style scoped>
  p {
    color: v-bind('theme.color');
  }
  </style>
  ```

  实际的值会被编译成 hash 的 CSS 自定义 property，CSS 本身仍然是静态的。自定义 property 会通过内联样式的方式应用到组件的根元素上，并且在源值变更的时候响应式更新。
  <br>

### 8.  `SFC <style scoped>` 现在可以包含全局规则或只针对插槽内容的规则

- [原文地址](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0023-scoped-styles-changes.md)
  在 `SFC`(单文件组件)中提供更一致的自定义 `CSS` 扩展，例如：

  ```js
  <style scoped>
  /* deep selectors */
  ::v-deep(.foo) {}
  /* shorthand */
  :deep(.foo) {}

  /* targeting slot content */
  ::v-slotted(.foo) {}
  /* shorthand */
  :slotted(.foo) {}

  /* one-off global rule */
  ::v-global(.foo) {}
  /* shorthand */
  :global(.foo) {}
  </style>
  ```

  - `Deep Selectors`(深度选择器)
    有时我们可能想要明确地制定一个针对子组件的规则。

    最初我们支持 `>>>` 组合器使选择器“深”。但是，一些 `CSS` 预处理器（例如 `SASS`）在解析它时存在问题，因为这不是官方的 `CSS` 组合器。

    我们后来切换到 `/deep/`，它曾经是 `CSS` 的实际提议添加（甚至在 `Chrome` 中原生提供），但后来放弃了。这给一些用户造成了困惑，因为他们担心 `/deep/` 在 `Vue SFC` 中使用会使他们的代码在已删除该功能的浏览器中不受支持。然而，就像 `>>>,/deep/` 仅被 `Vue` 的 `SFC` 编译器用作编译时提示来重写选择器，并在最终的 `CSS` 中被删除。

    为了避免被丢弃的 `/deep/` 组合器的混淆，我们引入了另一个自定义组合 `::v-deep` 器，这一次更明确地表明这是一个特定于 `Vue` 的扩展，并使用伪元素语法，以便任何预处理器都应该能够解析它.

    出于兼容性原因，当前的 `Vue 2 SFC` 编译器仍然支持以前版本的深度组合器，这再次让用户感到困惑。在 `v3` 中，我们弃用了对 `>>>` 和 `/deep/` 的支持。

    当我们为 `v3` 开发新的 `SFC` 编译器时，我们注意到 `CSS` 伪元素实际上在语义上不是关系选择器。伪元素接受参数更符合惯用的 `CSS`，因此我们也在以 `::v-deep()` 这种方式进行工作。如果您不关心显式 `v-` 前缀，您也可以使用较短的 `:deep()` 变体，它的工作原理完全相同。

    在当前情况仍然支持 `::v-deep` 用作关系选择器的用法，但它被认为已弃用并会发出警告。
    <br>

  - `Targeting / Avoiding Slot Content` (定位/使插槽内容失效)
    目前，从父级传入的插槽内容受父级范围样式和子级范围样式的影响。无法创建仅明确针对插槽内容或不影响插槽内容的规则。

    在 `v3` 中，我们打算让子范围样式默认不影响插槽内容。要显式定位插槽内容，可以使用 `::v-slotted()`（简写：`:slotted()`）伪元素。
    <br>

  - `One-Off Global Rules`(一次性全局规则)
    目前要添加全局 `CSS` 规则，我们需要使用单独的无范围 `<style>` 块。我们正在为一次性全局规则引入一个新的`::v-global()`（简写：`:global()`）伪元素。
    <br>

- 设计细节

  - `>>>` 和 `/deep/` 支持已被弃用。

  - `::v-deep` 不推荐用作组合器：

    ```css
    /* 不推荐 */
    ::v-deep .bar {}
    ```

    相反，将其用作接受另一个选择器作为参数的伪元素：

    ```css
    ::v-deep(.bar) {}

    /* 编译为：*/
    [v-data-xxxxxxx] .bar {}
    ```

  - 默认情况下，从父级传入的插槽内容不再受子级样式的影响。相反，孩子现在需要使用新的 `::v-slotted()` 伪元素来专门针对插槽内容：

    ```js
    ::v-slotted(.foo) {}

    /* 编译为：*/
    .foo[v-data-xxxxxxx-s] {}
    ```

    请注意 `-s` 使其仅针对插槽内容的后缀。

  - 新的伪元素 `::v-global()` 可用于在 `<style scoped>` 块内应用全局规则：

    ```js
    ::v-global(.foo) {}

    /* 编译为：*/
    .foo {}
    ```

### 9.  `Suspense`(实验性)

- #### 异步组件用例

  ```js
  <template>
    <suspense>
      <template #default>
        <todo-list />
      </template>
      <template #fallback>
        <div>
          Loading...
        </div>
      </template>
    </suspense>
  </template>

  <script>
  export default {
    components: {
      TodoList: defineAsyncComponent(() => import('./TodoList.vue'))
    }
  }
  </script>
  ```

  `<suspense>` 组件有两个插槽。它们都只接收一个直接子节点。`default` 插槽里的节点会尽可能展示出来。如果不能，则展示 `fallback` 插槽里的节点。

  重要的是，异步组件不需要作为 `<suspense>` 的直接子节点。它可以出现在组件树任意深度的位置，且不需要出现在和 `<suspense>` 自身相同的模板中。只有所有的后代组件都准备就绪，该内容才会被认为解析完毕。

  另一个触发 `fallback` 的方式是让后代组件从 `setup` 函数中返回一个 `Promise`。通常这是通过 `async` 实现的，而不是显式地返回一个 `Promise`：

  ```js
  export default {
    async setup() {
      // 在 `setup` 内部使用 `await` 需要非常小心
      // 因为大多数组合式 API 函数只会在
      // 第一个 `await` 之前工作
      const data = await loadData()

      // 它隐性地包裹在一个 Promise 内
      // 因为函数是 `async` 的
      return {
        // ...
      }
    }
  }
  ```

  <br>

- #### 子组件更新

  一旦 `<suspense>` 的 `default` 插槽里的内容被解析，则它只有在 `default` 根结点被替换的时候才能被再次触发。而树里的深层嵌套组件不足以让 `<suspense>` 回到等待状态。

  如果根结点发生了变化，它会触发 `pending` 事件。然而，默认情况下，它不会更新 `DOM` 以展示 `fallback` 内容。取而代之的是，它会继续展示旧的 `DOM`，直到新组件准备就绪。这个行为可以通过 `timeout prop` 进行控制。这个值是一个毫秒数，告诉 `<suspense>` 组件多久之后展示 `fallback`。如果这个值是 0 则表示它在 `<suspense>` 进入等待状态时会立即显示。

- #### 事件

  除了 `pending` 事件以外，`<suspense>` 组件还拥有 `resolve` 和 `fallback` 事件。`resolve` 事件会在 `default` 插槽完成新内容的解析之后被触发。`fallback` 事件会在 `fallback` 插槽的内容展示的时候被触发。

  这些事件可以用在诸如当新组件加载时在旧 DOM 上展示一个加载标识等场景。

- #### 和其它组件结合

  将 `<suspense>` 跟 `<transition>` 和 `<keep-alive>` 组件相结合是常见的情形。这些组件的嵌套顺序对于它们的正确工作很重要。

  额外的，这些组件经常用于衔接 `Vue Router` 的 `<router-view>` 组件。

  以下示例展示了如何嵌套这些组件以让它们的表现符合预期。若要简化这个组合你可以移除不需要的组件：

  ```html
  <router-view v-slot="{ Component }">
    <template v-if="Component">
      <transition mode="out-in">
        <keep-alive>
          <suspense>
            <component :is="Component"></component>
            <template #fallback>
              <div>
                Loading...
              </div>
            </template>
          </suspense>
        </keep-alive>
      </transition>
    </template>
  </router-view>
  ```

  `Vue Router` 有内置的基于动态导入的组件懒加载支持。它和异步组件有所区别，并且当前不会触发 `<suspense>`。不过它们仍然可以包含异步组件作为后代，这样它们还是可以正常触发 `<suspense>`。

## 全局 `API`

### 10. 全局 `Vue API` 已更改为使用应用程序实例

- #### 一个新的全局 API：createApp

  调用 `createApp` 返回一个应用实例，一个 `Vue3` 中的新概念。

  ```js
  import { createApp } from 'vue'

  const app = createApp({})
  ```

  如果你使用的是 `Vue` 的 `CDN` 构建版本，那么 `createApp` 将通过全局的 `Vue` 对象暴露。

  ```js
  const { createApp } = Vue

  const app = createApp({})
  ```

  应用实例暴露了 `Vue2` 全局 `API` 的一个子集，经验法则是，***任何全局改变 Vue 行为的 API 现在都会移动到应用实例上***，以下是 `Vue2` 全局 `API` 及其相应的实例 `API` 列表：

  2.x 全局 API | 3.x 实例 API (app)
  -|-
  Vue.config | app.config
  Vue.config.productionTip | 移除 (见下方)
  Vue.config.ignoredElements | app.config.compilerOptions.isCustomElement (见下方)
  Vue.component | app.component
  Vue.directive | app.directive
  Vue.mixin | app.mixin
  Vue.use | app.use (见下方)
  Vue.prototype | app.config.globalProperties (见下方)
  Vue.extend | 移除 (见下方)

  所有其他不全局改变行为的全局 `API` 现在都是具名导出，文档见全局 `API Treeshaking`。
  <br>

  - **`config.ignoredElements` 替换为 `config.isCustomElement`**
    引入此配置选项的目的是为了支持原生自定义元素，因此重命名可以更好地传达它的意图。同时，新选项接受一个函数，相比旧的字符串或正则表达式来说能提供更高的灵活性：

    ```js
    // 之前
    Vue.config.ignoredElements = ['my-el', /^ion-/]

    // 之后
    const app = createApp({})
    app.config.compilerOptions.isCustomElement = tag => tag.startsWith('ion-')
    ```

    <br>

  - **`Vue.prototype` 替换为 `config.globalProperties`**
    在 `Vue 2` 中，`Vue.prototype` 通常用于添加所有组件都能访问的 `property`。

    在 `Vue 3` 中与之对应的是 `config.globalProperties`。这些 `property` 将被复制到应用中，作为实例化组件的一部分。

    ```js
    // 之前 - Vue 2
    Vue.prototype.$http = () => {}

    // 之后 - Vue 3
    const app = createApp({})
    app.config.globalProperties.$http = () => {}
    ```

    使用 `provide` (稍后会讨论) 时，也应考虑作为 `globalProperties` 的替代品。
    <br>

  - **`Vue.extend` 移除**
    在 `Vue 2.x` 中，`Vue.extend` 曾经被用于创建一个基于 `Vue` 构造函数的“子类”，其参数应为一个包含组件选项的对象。在 `Vue 3.x` 中，我们已经没有组件构造器的概念了。应该始终使用 `createApp` 这个全局 `API` 来挂载组件：

    ```js
    // 之前 - Vue 2

    // 创建构造器
    const Profile = Vue.extend({
      template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
      data() {
        return {
          firstName: 'Walter',
          lastName: 'White',
          alias: 'Heisenberg'
        }
      }
    })
    // 创建一个 Profile 的实例，并将它挂载到一个元素上
    new Profile().$mount('#mount-point')

    // 之后 - Vue 3
    const Profile = {
      template: '<p>{{firstName}} {{lastName}} aka {{alias}}</p>',
      data() {
        return {
          firstName: 'Walter',
          lastName: 'White',
          alias: 'Heisenberg'
        }
      }
    }
    Vue.createApp(Profile).mount('#mount-point')
    ```

    - 类型推断
      在 `Vue 2` 中，`Vue.extend` 也被用来为组件选项提供 `TypeScript` 类型推断。在 `Vue 3` 中，为了达到相同的目的，`defineComponent` 全局 `API` 可以用来作为 `Vue.extend` 的替代方案。

      需要注意的是，虽然 `defineComponent` 的返回类型是一个类似构造器的类型，但是它的目的仅仅是为了 `TSX` 的推断。在运行时 `defineComponent` 里基本没有什么操作，只会原样返回该选项对象。

    - 组件继承
      在 `Vue 3` 中，我们强烈建议使用 组合式 `API` 来替代继承与 `mixin`。如果因为某种原因仍然需要使用组件继承，你可以使用 `extends` 选项 来代替 `Vue.extend`。
      <br>

  - **插件开发者须知**
    在 `UMD` 构建中，插件开发者使用 `Vue.use` 来自动安装插件是一个通用的做法。例如，官方的 `vue-router` 插件是这样在浏览器环境中自行安装的：

    ```js
    // 在 Vue 3 中已无法正常工作，将触发一个警告
    var inBrowser = typeof window !== 'undefined'
    /* … */
    if (inBrowser && window.Vue) {
      window.Vue.use(VueRouter)
    }

    // 必须在应用实例上显式指定使用此插件
    const app = createApp(MyApp)
    app.use(VueRouter)
    ```

    <br>

- #### 挂载 App 实例

  使用 `createApp(/* options */)` 初始化后，应用实例 `app` 可通过 `app.mount(domTarget)` 挂载根组件实例：

  ```js
  import { createApp } from 'vue'
  import MyApp from './MyApp.vue'

  const app = createApp(MyApp)
  app.mount('#app')

  /*
   *****
  */
  
  // 经过所有的这些更改，我们在指南开头编写的组件和指令现在将被改写为如下内容：
  const app = createApp(MyApp)

  app.component('button-counter', {
    data: () => ({
      count: 0
    }),
    template: '<button @click="count++">Clicked {{ count }} times.</button>'
  })

  app.directive('focus', {
    mounted: el => el.focus()
  })

  // 现在，所有通过 app.mount() 挂载的应用实例及其组件树，
  // 将具有相同的 “button-counter” 组件和 “focus” 指令，
  // 而不会污染全局环境
  app.mount('#app')
  ```

  <br>

- #### Provide / Inject

  与在 `2.x` 根实例中使用 `provide` 选项类似，`Vue 3` 应用实例也提供了可被应用内任意组件注入的依赖项：

  ```js
  // 在入口中
  app.provide('guide', 'Vue 3 Guide')

  // 在子组件中
  export default {
    inject: {
      book: {
        from: 'guide'
      }
    },
    template: `<div>{{ book }}</div>`
  }
  ```

  在编写插件时使用 `provide` 将尤其有用，可以替代 `globalProperties`。
  <br>

- #### 在应用之间共享配置

  在应用之间共享配置 (如组件或指令) 的一种方法是创建工厂函数，如下所示：

  ```js
  import { createApp } from 'vue'
  import Foo from './Foo.vue'
  import Bar from './Bar.vue'

  const createMyApp = options => {
    const app = createApp(options)
    app.directive('focus' /* ... */)

    return app
  }

  createMyApp(Foo).mount('#foo')
  createMyApp(Bar).mount('#bar')
  ```

  现在，`Foo` 和 `Bar` 实例及其后代中都可以使用 `focus` 指令。

### 11. 全局和内部 `API` 已经被重构为支持 `tree-shake`

- 2.x 语法

  `Vue.nextTick()` 这样的全局 `API` 是不支持 `tree-shaking` 的，不管它们实际上是否被使用了，都会被包含在最终的打包产物中。
  <br>

- 3.x 语法

  在 `Vue 3` 中，全局和内部 `API` 都经过了重构，并考虑到了 `tree-shaking` 的支持。因此，对于 `ES` 模块构建版本来说，全局 `API` 现在通过具名导出进行访问。例如，我们之前的代码片段现在应该如下所示：

  ```js
  import { nextTick } from 'vue'

  nextTick(() => {
    // 一些和 DOM 有关的东西
  })


  /*
   *****
  */

  // or
  import { shallowMount } from '@vue/test-utils'
  import { MyComponent } from './MyComponent.vue'
  import { nextTick } from 'vue'

  test('an async feature', async () => {
    const wrapper = shallowMount(MyComponent)

    // 执行一些 DOM 相关的任务

    await nextTick()

    // 运行你的断言
  })
  ```

  直接调用 `Vue.nextTick()` 将导致臭名昭著的 `undefined is not a function` 错误。

  通过这一更改，如果模块打包工具支持 `tree-shaking`，则 `Vue` 应用中未使用的全局 `API` 将从最终的打包产物中排除，从而获得最佳的文件大小。
  <br>

- #### 受影响的 `API`

  `Vue 2.x` 中的这些全局 `API` 受此更改的影响：

  - `Vue.nextTick`
  - `Vue.observable` (用 `Vue.reactive` 替换)
  - `Vue.version`
  - `Vue.compile` (仅完整构建版本)
  - `Vue.set` (仅兼容构建版本)
  - `Vue.delete` (仅兼容构建版本)
  <br>

- #### 内部帮助器

  除了公共 `API`，许多内部组件/帮助器现在也以具名的方式导出。这允许编译器只在代码被使用到时才引入并输出它。例如以下模板：

  ```js
  <transition>
    <div v-show="ok">hello</div>
  </transition>


  // 将编译为类似于以下的内容：
  import { h, Transition, withDirectives, vShow } from 'vue'

  export function render() {
    return h(Transition, [withDirectives(h('div', 'hello'), [[vShow, this.ok]])])
  }
  ```

  这实际上意味着只有在应用实际使用了 `Transition` 组件它才会被导入。换句话说，如果应用没有使用任何 `Transition` 组件，那么用于支持此功能的代码将不会出现在最终的打包产物中。

  有了全局 `tree-shaking` 后，用户只需为他们实际使用到的功能“买单”。更棒的是，因为可选特性不会增加未使用它们的应用的打包产物大小，以后在追加核心功能时，即使对框架大小有所顾虑，它也将不再那么重要了。
  <br>

  >重要
  以上仅适用于 `ES` 模块构建版本，用于支持 `tree-shaking` 的打包工具——`UMD` 构建仍然包括所有特性，并暴露 `Vue` 全局变量上的所有内容 (编译器将生成适当的输出以从该全局变量上使用 `API`，而不是导入)。
  <br>

- #### 插件中的用法

  如果你的插件依赖到了受影响的 `Vue 2.x` 全局 `API`，例如：

  ```js
  const plugin = {
    install: Vue => {
      Vue.nextTick(() => {
        // ...
      })
    }
  }

  /*
   *****
  */

  // 在 Vue 3 中，必须显式导入：
  import { nextTick } from 'vue'

  const plugin = {
    install: app => {
      nextTick(() => {
        // ...
      })
    }
  }
  ```

  如果使用了 `webpack` 这样的模块打包工具，这可能会导致 `Vue` 的源代码输出打包到插件中，而通常情况下，这并不是你所期望的。为了防止发生这种情况，一种常见做法是配置模块打包工具以将 `Vue` 从最终的打包产物中排除。对于 `webpack` 来说，你可以使用 `externals` 配置选项：

  ```js
  // webpack.config.js
  module.exports = {
    /*...*/
    externals: {
      vue: 'Vue'
    }
  }
  ```

  这将告诉 `webpack` 将 `Vue` 模块视为一个外部库，而不将它打包进来。

  如果你选择的模块打包工具恰好是 `Rollup`，你基本上可以直接获得相同的效果。因为默认情况下，`Rollup` 会将绝对模块 `id` (在我们的例子中为 `'vue'`) 作为外部依赖项，而不会将它们包含在最终的打包产物中。但是在输出打包期间，它可能会抛出一个“将 `vue` 作为外部依赖”的警告，可使用 `external` 选项阻止该警告：

  ```js
  // rollup.config.js
  export default {
    /*...*/
    external: ['vue']
  }
  ```

## 模板指令

### 12. 组件上 `v-model` 用法已更改，以替换 `v-bind.sync`

- #### 2.x 语法

  在 `2.x` 中，在组件上使用 `v-model` 相当于绑定 `value prop` 并触发 `input` 事件：

  ```js
  <ChildComponent v-model="pageTitle" />

  <!-- 是以下的简写: -->
  <ChildComponent :value="pageTitle" @input="pageTitle = $event" />
  ```

  如果想要更改 `prop` 或事件名称，则需要在 `ChildComponent` 组件中添加 `model` 选项：

  ```js
  <!-- ParentComponent.vue -->

  <ChildComponent v-model="pageTitle" />

  /*
  *****
  */

  // ChildComponent.vue
  export default {
    model: {
      prop: 'title',
      event: 'change'
    },
    props: {
      // 这将允许 `value` 属性用于其他用途
      value: String,
      // 使用 `title` 代替 `value` 作为 model 的 prop
      title: {
        type: String,
        default: 'Default title'
      }
    }
  }

  // 所以，在这个例子中 v-model 是以下的简写：
  <ChildComponent :title="pageTitle" @change="pageTitle = $event" />
  ```

  - 使用 `v-bind.sync`
    在某些情况下，我们可能需要对某一个 `prop` 进行“双向绑定”(除了前面用 `v-model` 绑定 `prop` 的情况)。为此，我们建议使用 `update:myPropName` 抛出事件。例如，对于在上一个示例中带有 `title prop` 的 `ChildComponent`，我们可以通过下面的方式将分配新 `value` 的意图传达给父级：

    ```js
    this.$emit('update:title', newValue)
    ```

    然后父组件可以在需要时监听该事件，并更新本地的 `data property`。例如：

    ```js
    <ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
    ```

    为了方便起见，我们可以使用 `.sync` 修饰符来缩写，如下所示：

    ```js
    <ChildComponent :title.sync="pageTitle" />
    ```

    <br>

- #### 3.x 语法

  在 `3.x` 中，自定义组件上的 `v-model` 相当于传递了 `modelValue prop` 并接收抛出的 `update:modelValue` 事件：

  ```js
  <ChildComponent v-model="pageTitle" />

  <!-- 是以下的简写: -->

  <ChildComponent
    :modelValue="pageTitle"
    @update:modelValue="pageTitle = $event"
  />
  ```

  - `v-model` 参数
  若需要更改 `model` 的名称，现在我们可以为 `v-model` 传递一个参数，以作为组件内 `model` 选项的替代：

  ```js
  <ChildComponent v-model:title="pageTitle" />

  <!-- 是以下的简写: -->

  <ChildComponent :title="pageTitle" @update:title="pageTitle = $event" />
  ```

  ![v-bind anatomy](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/Vue/v-bind-instead-of-sync.png)

  这也可以作为 `.sync` 修饰符的替代，而且允许我们在自定义组件上使用多个 `v-model`。

  ```js
  <ChildComponent v-model:title="pageTitle" v-model:content="pageContent" />

  <!-- 是以下的简写： -->

  <ChildComponent
    :title="pageTitle"
    @update:title="pageTitle = $event"
    :content="pageContent"
    @update:content="pageContent = $event"
  />
  ```

  - `v-model` 修饰符
  除了像 `.trim` 这样的 `2.x` 硬编码的 `v-model` 修饰符外，现在 `3.x` 还支持自定义修饰符：

  ```js
  <ChildComponent v-model.capitalize="pageTitle" />
  ```

  我们可以在 [`Custom Events`](https://v3.cn.vuejs.org/guide/component-custom-events.html#%E5%A4%84%E7%90%86-v-model-%E4%BF%AE%E9%A5%B0%E7%AC%A6) 部分中了解有关自定义 `v-model` 修饰符的更多信息。
  <br>

### 13. `<template v-for>` 和非 `v-for` 节点上的 `key` 用法已更改

- #### 在条件分支中

  在 `Vue 2.x` 中，建议在 `v-if/v-else/v-else-if` 的分支中使用 `key`。

  ```js
  <!-- Vue 2.x -->
  <div v-if="condition" key="yes">Yes</div>
  <div v-else key="no">No</div>
  ```

  这个示例在 `Vue 3.x` 中仍能正常工作。但是我们不再建议在 `v-if/v-else/v-else-if` 的分支中继续使用 `key` attribute，因为没有为条件分支提供 `key` 时，也会自动生成唯一的 `key`。

  ```js
  <!-- Vue 3.x -->
  <div v-if="condition">Yes</div>
  <div v-else>No</div>
  ```

  非兼容变更体现在如果你手动提供了 `key`，那么每个分支都必须使用一个唯一的 `key`。因此大多数情况下都不需要设置这些 `key`。

  ```js
  <!-- Vue 2.x -->
  <div v-if="condition" key="a">Yes</div>
  <div v-else key="a">No</div>

  <!-- Vue 3.x (推荐方案：移除 key) -->
  <div v-if="condition">Yes</div>
  <div v-else>No</div>

  <!-- Vue 3.x (替代方案：确保 key 始终是唯一的) -->
  <div v-if="condition" key="a">Yes</div>
  <div v-else key="b">No</div>
  ```

- #### 结合 `<template v-for>`

  在 `Vue 2.x` 中，`<template>` 标签不能拥有 `key`。不过，你可以为其每个子节点分别设置 `key`。

  ```js
  <!-- Vue 2.x -->
  <template v-for="item in list">
    <div :key="'heading-' + item.id">...</div>
    <span :key="'content-' + item.id">...</span>
  </template>
  ```

  在 `Vue 3.x` 中，`key` 则应该被设置在 `<template>` 标签上。

  ```js
  <!-- Vue 3.x -->
  <template v-for="item in list" :key="item.id">
    <div>...</div>
    <span>...</span>
  </template>
  ```

  类似地，当使用 `<template v-for>` 时如果存在使用 `v-if` 的子节点，则 `key` 应改为设置在 `<template>` 标签上。

  ```js
  <!-- Vue 2.x -->
  <template v-for="item in list">
    <div v-if="item.isVisible" :key="item.id">...</div>
    <span v-else :key="item.id">...</span>
  </template>

  <!-- Vue 3.x -->
  <template v-for="item in list" :key="item.id">
    <div v-if="item.isVisible">...</div>
    <span v-else>...</span>
  </template>
  ```

### 14. 在同一元素上使用的 `v-if` 和 `v-for` 优先级已更改

- #### 2.x 语法

  `2.x` 版本中在一个元素上同时使用 `v-if` 和 `v-for` 时，`v-for` 会优先作用。

- #### 3.x 语法

  `3.x` 版本中 `v-if` 总是优先于 `v-for` 生效。

### 15. `v-bind="object"` 现在排序敏感

- #### 不兼容：`v-bind` 的绑定顺序会影响渲染结果

- #### 2.x 语法

  在 `2.x` 中，如果一个元素同时定义了 `v-bind="object"` 和一个相同的独立 `attribute`，那么这个独立 `attribute` 总是会覆盖 `object` 中的绑定。

  ```js
  <!-- 模板 -->
  <div id="red" v-bind="{ id: 'blue' }"></div>
  <!-- 结果 -->
  <div id="red"></div>
  ```

- #### 3.x 语法

  在 `3.x` 中，如果一个元素同时定义了 `v-bind="object"` 和一个相同的独立 `attribute`，那么绑定的声明顺序将决定它们如何被合并。换句话说，相对于假设开发者总是希望独立 `attribute` 覆盖 `object` 中定义的内容，现在开发者能够对自己所希望的合并行为做更好的控制。

  ```js
  <!-- 模板 -->
  <div id="red" v-bind="{ id: 'blue' }"></div>
  <!-- 结果 -->
  <div id="blue"></div>

  <!-- 模板 -->
  <div v-bind="{ id: 'blue' }" id="red"></div>
  <!-- 结果 -->
  <div id="red"></div>
  ```

### 16. `v-on:event.native` 修饰符已移除

- #### 2.x 语法

  默认情况下，传递给带有 `v-on` 的组件的事件监听器只能通过 `this.$emit` 触发。要将原生 `DOM` 监听器添加到子组件的根元素中，可以使用 `.native` 修饰符：

  ```js
  <my-component
    v-on:close="handleComponentEvent"
    v-on:click.native="handleNativeClickEvent"
  />
  ```

- #### 3.x 语法

  `v-on` 的 `.native` 修饰符已被移除。同时，新增的 [`emits`](https://v3.cn.vuejs.org/guide/migration/emits-option.html) 选项允许子组件定义真正会被触发的事件。

  因此，对于子组件中未被定义为组件触发的所有事件监听器，`Vue` 现在将把它们作为原生事件监听器添加到子组件的根元素中 (除非在子组件的选项中设置了 `inheritAttrs: false`)。

  ```js
  <my-component
    v-on:close="handleComponentEvent"
    v-on:click="handleNativeClickEvent"
  />

  // MyComponent.vue
  <script>
    export default {
      emits: ['close']
    }
  </script>
  ```

- #### 重点
  
  - `const app = createApp(App)`，在 `emits` 中声明的事件会在 `app._context.emitsCache` 与 `app._context.optionsCache` 中存储
  - 放入 `emits` 的事件不会出现在 `this.$attrs` 中，也不会出现在组件根元素的 `Event Listeners` 中；它们被放在 `this.$options.emits` 中
  - 未放入 `emits` 的事件会出现在 `this.$attrs` 中，也会出现在组件根元素的 `Event Listeners` 中；它们不会被放在 `this.$options.emits` 中
  - 未放入 `emits` 的事件，对于向其父组件透传原生事件的组件来说，这会导致有两个事件被触发：一次来自 `$emit()`；一次来自应用在根元素上的原生事件监听器。
  <br>

### 17. `v-for` 中的 `ref` 不再注册 `ref` 数组

- #### 2.x 运用

  在 `v-for` 中使用的 `ref` attribute 会用 `ref` 数组填充相应的 `$refs property`。当存在嵌套的 `v-for` 时，这种行为会变得不明确且效率低下。

- #### 3.x 运用

  此类用法将不再自动创建 `$ref` 数组。要从单个绑定获取多个 `ref`，请将 `ref` 绑定到一个更灵活的函数上 (这是一个新特性)：

  ```html
  <div v-for="item in list" :ref="setItemRef"></div>
  ```

  结合选项式 `API`:

  ```js
  export default {
    data() {
      return {
        itemRefs: []
      }
    },
    methods: {
      setItemRef(el) {
        if (el) {
          this.itemRefs.push(el)
        }
      }
    },
    beforeUpdate() {
      this.itemRefs = []
    },
    updated() {
      console.log(this.itemRefs)
    }
  }
  ```

  结合组合式 `API`:

  ```js
  import { onBeforeUpdate, onUpdated } from 'vue'

  export default {
    setup() {
      let itemRefs = []
      const setItemRef = el => {
        if (el) {
          itemRefs.push(el)
        }
      }
      onBeforeUpdate(() => {
        itemRefs = []
      })
      onUpdated(() => {
        console.log(itemRefs)
      })
      return {
        setItemRef
      }
    }
  }
  ```

  注意：
  - `itemRefs` 不必是数组：它也可以是一个对象，其 `ref` 可以通过迭代的 `key` 被设置。
  - 如有需要，`itemRefs` 也可以是响应式的，且可以被侦听。

## 组件

### 18. 只能使用普通函数创建函数式组件

- #### 2.x 语法

  使用 `<dynamic-heading>` 组件，负责提供适当的标题 (即：`h1、h2、h3` 等等)，在 2.x 中，这可以通过单文件组件编写：

  ```js
  // Vue 2 函数式组件示例
  export default {
    functional: true,
    props: ['level'],
    render(h, { props, data, children }) {
      return h(`h${props.level}`, data, children)
    }
  }
  ```

  或者，对于喜欢在单文件组件中使用 `<template>` 的用户：

  ```js
  <!-- Vue 2 结合 <template> 的函数式组件示例 -->
  <template functional>
    <component
      :is="`h${props.level}`"
      v-bind="attrs"
      v-on="listeners"
    />
  </template>

  <script>
  export default {
    props: ['level']
  }
  </script>
  ```

- #### 3.x 语法

  - **通过函数创建组件**
    现在，在 `Vue 3` 中，所有的函数式组件都是用普通函数创建的。换句话说，不需要定义 `{ functional: true }` 组件选项。

    它们将接收两个参数：`props` 和 `context`。`context` 参数是一个对象，包含组件的 `attrs、slots` 和 `emit property`。

    此外，`h` 现在是全局导入的，而不是在 `render` 函数中隐式提供。

    以前面提到的 `<dynamic-heading>` 组件为例，下面是它现在的样子。

    ```js
    import { h } from 'vue'

    const DynamicHeading = (props, context) => {
      return h(`h${props.level}`, context.attrs, context.slots)
    }

    DynamicHeading.props = ['level']

    export default DynamicHeading
    ```

  - **单文件组件 (`SFC`)**
    在 `3.x` 中，有状态组件和函数式组件之间的性能差异已经大大减少，并且在大多数用例中是微不足道的。因此，在单文件组件上使用 `functional` 的开发者的迁移路径是删除该 `attribute`，并将 `props` 的所有引用重命名为 `$props`，以及将 `attrs` 重命名为 `$attrs`。

    以之前的 `<dynamic-heading>` 为例，下面是它现在的样子。

    ```js
    <template>
      <component
        v-bind:is="`h${$props.level}`"
        v-bind="$attrs"
      />
    </template>

    <script>
    export default {
      props: ['level']
    }
    </script>
    ```

    主要的区别在于：
    - 从 `<template>` 中移除 `functional` attribute
    - `listeners` 现在作为 `$attrs` 的一部分传递，可以将其删除

### 19. `functional` attribute 在单文件组件 (`SFC`) 的 `<template>` 和 `functional` 组件选项中被废弃

- 同上

### 20. 异步组件现在需要使用 `defineAsyncComponent` 方法来创建

- 异步组件（新增）- `defineAsyncComponent`
- #### 2.x 语法

  以前，异步组件是通过将组件定义为返回 `Promise` 的函数来创建的，例如：

  ```js
  const asyncModal = () => import('./Modal.vue')
  ```

  或者，对于带有选项的更高阶的组件语法：

  ```js
  const asyncModal = {
    component: () => import('./Modal.vue'),
    delay: 200,
    timeout: 3000,
    error: ErrorComponent,
    loading: LoadingComponent
  }
  ```

- #### 3.x 语法

  现在，在 `Vue 3` 中，由于函数式组件被定义为纯函数，因此异步组件需要通过将其包裹在新的 `defineAsyncComponent` 助手方法中来显式地定义：

  ```js
  import { defineAsyncComponent } from 'vue'
  import ErrorComponent from './components/ErrorComponent.vue'
  import LoadingComponent from './components/LoadingComponent.vue'

  // 不带选项的异步组件
  const asyncModal = defineAsyncComponent(() => import('./Modal.vue'))

  // 带选项的异步组件
  const asyncModalWithOptions = defineAsyncComponent({
    loader: () => import('./Modal.vue'),
    delay: 200,
    timeout: 3000,
    errorComponent: ErrorComponent,
    loadingComponent: LoadingComponent
  })
  ```

  >注意
  `Vue Router` 支持一个类似的机制来异步加载路由组件，也就是俗称的懒加载。尽管类似，但是这个功能和 `Vue` 所支持的异步组件是不同的。当用 `Vue Router` 配置路由组件时，你不应该使用 `defineAsyncComponent`。你可以在 `Vue Router` 文档的懒加载路由章节阅读更多相关内容。

  对 `2.x` 所做的另一个更改是，`component` 选项现在被重命名为 `loader`，以明确组件定义不能直接被提供。

  ```js
  import { defineAsyncComponent } from 'vue'

  const asyncModalWithOptions = defineAsyncComponent({
    loader: () => import('./Modal.vue'),
    delay: 200,
    timeout: 3000,
    errorComponent: ErrorComponent,
    loadingComponent: LoadingComponent
  })
  ```

  此外，与 `2.x` 不同，`loader` 函数不再接收 `resolve` 和 `reject` 参数，且必须始终返回 `Promise`。

  ```js
  // 2.x 版本
  const oldAsyncComponent = (resolve, reject) => {
    /* ... */
  }

  // 3.x 版本
  const asyncComponent = defineAsyncComponent(
    () =>
      new Promise((resolve, reject) => {
        /* ... */
      })
  )
  ```

### 21. 组件事件现在需要在 `emits` 选项中声明

- 同上 4

## 渲染函数

### 22. 渲染函数 `API` 更改

- 非兼容
  - `h` 现在是全局导入，而不是作为参数传递给渲染函数
  - 更改渲染函数参数，使其在有状态组件和函数组件的表现更加一致
  - `VNode` 现在有一个扁平的 `prop` 结构

- #### 渲染函数参数

  - 2.x 语法
    在 `2.x` 中，`render` 函数会自动接收 `h` 函数 (它是 `createElement` 的惯用别名) 作为参数：

    ```js
    // Vue 2 渲染函数示例
    export default {
      render(h) {
        return h('div')
      }
    }
    ```

  - 3.x 语法
    在 `3.x` 中，`h` 函数现在是全局导入的，而不是作为参数自动传递。

    ```js
    // Vue 3 渲染函数示例
    import { h } from 'vue'

    export default {
      render() {
        return h('div')
      }
    }
    ```

- #### 渲染函数签名更改

  - 2.x 语法
    在 `2.x` 中，`render` 函数自动接收参数，如 `h` 函数。

    ```js
    // Vue 2 渲染函数示例
    export default {
      render(h) {
        return h('div')
      }
    }
    ```

  - 3.x 语法
    在 `3.x` 中，由于 `render` 函数不再接收任何参数，它将主要在 `setup()` 函数内部使用。这还有一个好处：可以访问在作用域中声明的响应式状态和函数，以及传递给 `setup()` 的参数。

    ```js
    import { h, reactive } from 'vue'

    export default {
      setup(props, { slots, attrs, emit }) {
        const state = reactive({
          count: 0
        })

        function increment() {
          state.count++
        }

        // 返回渲染函数
        return () =>
          h(
            'div',
            {
              onClick: increment
            },
            state.count
          )
      }
    }
    ```

- #### `VNode Prop` 格式化

  - 2.x 语法
    在 `2.x` 中，`domProps` 包含 `VNode prop` 中的嵌套列表：

    ```js
    // 2.x
    {
      staticClass: 'button',
      class: { 'is-outlined': isOutlined },
      staticStyle: { color: '#34495E' },
      style: { backgroundColor: buttonColor },
      attrs: { id: 'submit' },
      domProps: { innerHTML: '' },
      on: { click: submitForm },
      key: 'submit-button'
    }
    ```

  - 3.x 语法
    在 `3.x` 中，整个 `VNode prop` 的结构都是扁平的。使用上面的例子，来看看它现在的样子。

    ```js
    // 3.x 语法
    {
      class: ['button', { 'is-outlined': isOutlined }],
      style: [{ color: '#34495E' }, { backgroundColor: buttonColor }],
      id: 'submit',
      innerHTML: '',
      onClick: submitForm,
      key: 'submit-button'
    }
    ```

- #### 注册组件

  - 2.x 语法
    在 `2.x` 中，注册一个组件后，把组件名作为字符串传递给渲染函数的第一个参数，它可以正常地工作：

    ```js
    // 2.x
    Vue.component('button-counter', {
      data() {
        return {
          count: 0
        }
      },
      template: `
        <button @click="count++">
          Clicked {{ count }} times.
        </button>
      `
    })

    export default {
      render(h) {
        return h('button-counter')
      }
    }
    ```

  - 3.x 语法
    在 `3.x` 中，由于 `VNode` 是上下文无关的，不能再用字符串 `ID` 隐式查找已注册组件。取而代之的是，需要使用一个导入的 `resolveComponent` 方法：

    ```js
    // 3.x
    import { h, resolveComponent } from 'vue'

    export default {
      setup() {
        const ButtonCounter = resolveComponent('button-counter')
        return () => h(ButtonCounter)
      }
    }
    ```

### 23. `$scopedSlots property` 已移除，所有插槽都通过 `$slots` 作为函数暴露

- 插槽统一（非兼容）
- #### 2.x 语法

  当使用渲染函数，即 `h` 时，`2.x` 曾经在内容节点上定义 `slot` 数据 `property`。

  ```js
  // 2.x 语法
  h(LayoutComponent, [
    h('div', { slot: 'header' }, this.header),
    h('div', { slot: 'content' }, this.content)
  ])
  ```

  此外，可以使用以下语法引用作用域插槽：

  ```js
  // 2.x 语法
  this.$scopedSlots.header
  ```

- #### 3.x 语法

  在 `3.x` 中，插槽以对象的形式定义为当前节点的子节点：

  ```js
  // 3.x Syntax
  h(LayoutComponent, {}, {
    header: () => h('div', this.header),
    content: () => h('div', this.content)
  })
  ```

  当你需要以编程方式引用作用域插槽时，它们现在被统一到 `$slots` 选项中了。

  ```js
  // 2.x 语法
  this.$scopedSlots.header

  // 3.x 语法
  this.$slots.header()
  ```

### 24. `$listeners` 被移除或整合到 `$attrs`

- 移除 `$listeners`(非兼容)
- #### 2.x 语法

  在 `Vue 2` 中，你可以通过 `this.$attrs` 访问传递给组件的 `attribute`，以及通过 `this.$listeners` 访问传递给组件的事件监听器。结合 `inheritAttrs: false`，开发者可以将这些 `attribute` 和监听器应用到根元素之外的其它元素：

  ```js
  <template>
    <label>
      <input type="text" v-bind="$attrs" v-on="$listeners" />
    </label>
  </template>
  <script>
    export default {
      inheritAttrs: false
    }
  </script>
  ```

- #### 3.x 语法

  在 `Vue 3` 的虚拟 `DOM` 中，事件监听器现在只是以 `on` 为前缀的 `attribute`，这样它就成为了 `$attrs` 对象的一部分，因此 `$listeners` 被移除了。

  ```js
  <template>
    <label>
      <input type="text" v-bind="$attrs" />
    </label>
  </template>
  <script>
  export default {
    inheritAttrs: false
  }
  </script>
  ```

  如果这个组件接收一个 `id` attribute 和一个 `v-on:close` 监听器，那么 `$attrs` 对象现在将如下所示:

  ```js
  {
    id: 'my-input',
    onClose: () => console.log('close 事件被触发')
  }
  ```

### 25. `$attrs` 现在包含 `class` 和 `style` attribute

- `$attrs` 包含 `class & style`(非兼容)
- #### 2.x 行为

  `Vue 2` 的虚拟 `DOM` 实现对 `class` 和 `style` attribute 有一些特殊处理。因此，与其它所有 `attribute` 不一样，它们没有被包含在 `$attrs` 中。

  上述行为在使用 `inheritAttrs: false` 时会产生副作用：

  - `$attrs` 中的 `attribute` 将不再被自动添加到根元素中，而是由开发者决定在哪添加。
  - 但是 `class` 和 `style` 不属于 `$attrs`，它们仍然会被应用到组件的根元素中：

  ```js
  <template>
    <label>
      <input type="text" v-bind="$attrs" />
    </label>
  </template>
  <script>
  export default {
    inheritAttrs: false
  }
  </script>
  ```

  像这样使用时：

  ```js
  <my-component id="my-id" class="my-class"></my-component>
  ```

  将生成以下 `HTML`：

  ```html
  <label class="my-class">
    <input type="text" id="my-id" />
  </label>
  ```

- #### 3.x 行为

  `$attrs` 包含了所有的 `attribute`，这使得把它们全部应用到另一个元素上变得更加容易了。现在上面的示例将生成以下 `HTML`：

  ```js
  <label>
    <input type="text" id="my-id" class="my-class" />
  </label>
  ```

## 自定义元素

### 26. 自定义元素检测现在在模板编译时执行

- 与自定义元素的互操作性(非兼容)
- #### 自主定制元素

  如果我们想要在 `Vue` 外部定义添加自定义元素 (例如使用 `Web Components API`)，则需要“指示” `Vue` 将其视为自定义元素。让我们以下面的模板为例。

  `<plastic-button></plastic-button>`
  - 2.x 语法
    在 `Vue 2.x` 中，通过 `Vue.config.ignoredElements` 将标签配置为自定义元素：

    ```js
    // 这将使 Vue 忽略在其外部定义的自定义元素
    // (例如：使用 Web Components API)

    Vue.config.ignoredElements = ['plastic-button']
    ```

  - 3.x 语法
    **在 `Vue 3.0` 中，此检查在模板编译期间执行**。要指示编译器将 `<plastic-button>` 视为自定义元素：

    - 如果使用构建步骤：给 `Vue` 模板编译器传入 `isCustomElement` 选项。如果使用了 `vue-loader`，则应通过 `vue-loader` 的 `compilerOptions` 选项传递：

      ```js
      // webpack 中的配置
      rules: [
        {
          test: /\.vue$/,
          use: 'vue-loader',
          options: {
            compilerOptions: {
              isCustomElement: tag => tag === 'plastic-button'
            }
          }
        }
        // ...
      ]
      ```

    - 如果使用动态模板编译，请通过 `app.config.compilerOptions.isCustomElement` 传递：

      ```js
      const app = Vue.createApp({})
      app.config.compilerOptions.isCustomElement = tag => tag === 'plastic-button'
      ```

      需要注意的是，运行时配置只会影响运行时的模板编译——它不会影响预编译的模板。

- #### 定制内置元素

  自定义元素规范提供了一种将自定义元素作为[自定义内置元素](https://html.spec.whatwg.org/multipage/custom-elements.html#custom-elements-customized-builtin-example)的方法，方法是向内置元素添加 `is` attribute：

  ```html
  <button is="plastic-button">点击我!</button>
  ```

  在原生 `attribute` 于浏览器中普遍可用之前，`Vue` 对 `is` 这个特殊 `attribute` 的使用就已经在模拟其行为。但是，在 `2.x` 中，它将被解释为渲染一个名为 `plastic-button` 的 `Vue` 组件，这将阻碍上面所提到的自定义内置元素的原生用法。

  在 `3.0` 中，我们将 `Vue` 对 `is` attribute 的特殊处理限制在了 `<component>` 标签中。

  - 在保留的 `<component>` 标签上使用时，它的行为将与 `2.x` 中完全相同；
  - 在普通组件上使用时，它的行为将类似于普通 `attribute`：

    ```html
    <foo is="bar" />
    ```

    - `2.x` 的行为：渲染 `bar` 组件。
    - `3.x` 的行为：渲染 `foo` 组件，并将 `is` attribute 传递给它。
  - 在普通元素上使用时，它将作为 `is` attribute 传递给 `createElement` 调用，并作为原生 `attribute` 渲染。这支持了自定义内置元素的用法。

    ```html
    <button is="plastic-button">点击我！</button>
    ```

    - `2.x` 的行为：渲染 `plastic-button` 组件。
    - `3.x` 的行为：通过调用以下函数渲染原生的 `button`

      ```js
      document.createElement('button', { is: 'plastic-button' })
      ```

- #### 使用 `vue:` 前缀来解决 `DOM` 内模版解析问题

  >提示：本节仅影响直接在页面的 `HTML` 中写入 `Vue` 模板的情况。 在 `DOM` 模板中使用时，模板受原生 `HTML` 解析规则的约束。一些 `HTML` 元素，例如 `<ul>、<ol>、<table>` 和 `<select>` 对它们内部可以出现的元素有限制，以及一些像 `<li>、<tr>` 和 `<option>` 只能出现在特定的其他元素中。

  - 2.x 语法
    在 `Vue 2` 中，我们建议在原生标签上使用 `is` attribute 来绕过这些限制：

    ```html
    <table>
      <tr is="blog-post-row"></tr>
    </table>
    ```

  - 3.x 语法
    随着 `is` 的行为发生变化，现在将元素解析为 `Vue` 组件需要添加一个 `vue:` 前缀：

    ```html
    <table>
      <tr is="vue:blog-post-row"></tr>
    </table>
    ```

### 27. 特殊的 `is attribute` 的使用被严格限制在被保留的 `<component>` 标签中

- 同上

## 其他小改变

### 28. `destroyed` 生命周期选项被重命名为 `unmounted`

### 29. `beforeDestroy` 生命周期选项被重命名为 `beforeUnmount`

### 30. `default prop` 工厂函数不再可以访问 `this` 上下文

- 在 `prop` 的默认函数中访问 `this`(非兼容)
  生成 `prop` 默认值的工厂函数不再能访问 `this`。

  取而代之的是：

  - 组件接收到的原始 `prop` 将作为参数传递给默认函数；

  - `inject API` 可以在默认函数中使用。

    ```js
    import { inject } from 'vue'

    export default {
      props: {
        theme: {
          default (props) {
            // `props` 是传递给组件的、
            // 在任何类型/默认强制转换之前的原始值，
            // 也可以使用 `inject` 来访问注入的 property
            return inject('theme', 'default-theme')
          }
        }
      }
    }
    ```

### 31. 自定义指令的 `API` 已更改为与组件生命周期一致，且 `binding.expression` 已移除

- 自定义指令(非兼容)
  指令的钩子函数已经被重命名，以更好地与组件的生命周期保持一致。
  额外地，`expression` 字符串不再作为 `binding` 对象的一部分被传入。

- #### 2.x 语法

  在 `Vue 2` 中，自定义指令通过使用下列钩子来创建，以对齐元素的生命周期，它们都是可选的：

  - `bind` - 指令绑定到元素后调用。只调用一次。
  - `inserted` - 元素插入父 `DOM` 后调用。
  - `update` - 当元素更新，但子元素尚未更新时，将调用此钩子。
  - `componentUpdated` - 一旦组件和子级被更新，就会调用这个钩子。
  - `unbind` - 一旦指令被移除，就会调用这个钩子。也只调用一次。
  
  下面是一个例子：

  ```js
  <p v-highlight="'yellow'">以亮黄色高亮显示此文本</p>


  Vue.directive('highlight', {
    bind(el, binding, vnode) {
      el.style.background = binding.value
    }
  })
  ```

  此处，在这个元素的初始设置中，通过给指令传递一个值来绑定样式，该值可以在应用中任意更改。

- #### 3.x 语法

  在 `Vue 3` 中，我们为自定义指令创建了一个更具凝聚力的 `API`。正如你所看到的，它们与我们的组件生命周期方法有很大的不同，即使钩子的目标事件十分相似。我们现在把它们统一起来了：

  - **`created`** - 新增！在元素的 `attribute` 或事件监听器被应用之前调用。
  - `bind` → **`beforeMount`** - 指令绑定到元素后调用。只调用一次。
  - `inserted` → **`mounted`** - 元素插入父 `DOM` 后调用。
  - **`beforeUpdate`**：新增！在元素本身被更新之前调用，与组件的生命周期钩子十分相似。
  - `update` → 移除！该钩子与 `updated` 有太多相似之处，因此它是多余的。请改用 `updated`。
  - `componentUpdated` → **`updated`** - 一旦组件和子级被更新，就会调用这个钩子。
  - **`beforeUnmount`**：新增！与组件的生命周期钩子类似，它将在元素被卸载之前调用。
  - `unbind` -> **`unmounted`** - 一旦指令被移除，就会调用这个钩子。也只调用一次。
  
  最终的 `API` 如下：

  ```js
  const MyDirective = {
    created(el, binding, vnode, prevVnode) {}, // 新增
    beforeMount() {},
    mounted() {},
    beforeUpdate() {}, // 新增
    updated() {},
    beforeUnmount() {}, // 新增
    unmounted() {}
  }
  ```

  因此，`API` 可以这样使用，与前面的示例相同：

  ```js
  <p v-highlight="'yellow'">以亮黄色高亮显示此文本</p>



  const app = Vue.createApp({})

  app.directive('highlight', {
    beforeMount(el, binding, vnode) {
      el.style.background = binding.value
    }
  })
  ```

  既然现在自定义指令的生命周期钩子与组件本身保持一致，那么它们就更容易被推理和记住了！

  - 边界情况：访问组件实例
    通常来说，建议在组件实例中保持所使用的指令的独立性。从自定义指令中访问组件实例，通常意味着该指令本身应该是一个组件。然而，在某些情况下这种用法是有意义的。

    在 `Vue 2` 中，必须通过 `vnode` 参数访问组件实例：

    ```js
    bind(el, binding, vnode) {
      const vm = vnode.context
    }
    ```

    在 `Vue 3` 中，实例现在是 `binding` 参数的一部分：

    ```js
    mounted(el, binding, vnode) {
      const vm = binding.instance
    }
    ```

    >WARNING
    有了[片段](https://v3.cn.vuejs.org/guide/migration/fragments.html#%E6%A6%82%E8%A7%88)的支持，组件可能会有多个根节点。当被应用于多根组件时，自定义指令将被忽略，并将抛出警告。

### 32. `data` 选项应始终被声明为一个函数

- `Data` 选项(非兼容)
  - 非兼容：组件选项 `data` 的声明不再接收纯 `JavaScript object`，而是接收一个 `function`。
  - 非兼容：当合并来自 `mixin` 或 `extend` 的多个 `data` 返回值时，合并操作现在是浅层次的而非深层次的 (只合并根级属性)。
- #### 2.x 语法

  在 `2.x` 中，开发者可以通过 `object` 或者是 `function` 定义 `data` 选项。

  例如：

  ```js
  <!-- Object 声明 -->
  <script>
    const app = new Vue({
      data: {
        apiKey: 'a1b2c3'
      }
    })
  </script>

  <!-- Function 声明 -->
  <script>
    const app = new Vue({
      data() {
        return {
          apiKey: 'a1b2c3'
        }
      }
    })
  </script>
  ```

  虽然这种做法对于具有共享状态的根实例提供了一些便利，但是由于其只可能存在于根实例上，因此变得混乱。

- #### 3.x 语法

  在 `3.x` 中，`data` 选项已标准化为只接受返回 `object` 的 `function`。

  使用上面的示例，代码只可能有一种实现：

  ```js
  <script>
    import { createApp } from 'vue'

    createApp({
      data() {
        return {
          apiKey: 'a1b2c3'
        }
      }
    }).mount('#app')
  </script>
  ```

- #### `Mixin` 合并行为变更

  此外，当来自组件的 `data()` 及其 `mixin` 或 `extends` 基类被合并时，合并操作现在将被浅层次地执行：

  ```js
  const Mixin = {
    data() {
      return {
        user: {
          name: 'Jack',
          id: 1
        }
      }
    }
  }

  const CompA = {
    mixins: [Mixin],
    data() {
      return {
        user: {
          id: 2
        }
      }
    }
  }
  ```

  在 `Vue 2.x` 中，生成的 `$data` 是：

  ```js
  {
    "user": {
      "id": 2,
      "name": "Jack"
    }
  }
  ```

  在 `3.0` 中，其结果将会是：

  ```js
  {
    "user": {
      "id": 2
    }
  }
  ```

### 33. 来自 `mixin` 的 `data` 选项现在为浅合并

- 同上

### 34. `Attribute` 强制策略已更改

- `attribute` 强制行为(非兼容)
  - 移除枚举 `attribute` 的内部概念，并将这些 `attribute` 视为普通的非布尔 `attribute`
  - 非兼容：如果值为布尔值 `false`，则不再移除 `attribute`。取而代之的是，它将被设置为 `attr="false"`。若要移除 `attribute`，应该使用 `null` 或者 `undefined`。
- #### 2.x 语法

  在 `2.x`，我们有以下策略来强制 `v-bind` 的值：

  - 对于某些 `attribute`/元素对，`Vue` 始终使用相应的 `IDL attribute (property)`：比如 `<input>，<select>，<progress>` 中的 `value`，等等。
  - 对于“布尔 `attribute`”和 `xlinks`，如果它们是 `falsy` (`undefined`，`null` 或 `false`) 的，`Vue` 会移除它们，否则会加上。(见这里和这里)。
  - 对于“枚举 `attribute`” (目前来说包括 `contenteditable、draggable` 和 `spellcheck`)，`Vue` 会尝试将它们强制转换为字符串 (目前对 `contenteditable` 做了特殊处理，以修复 vuejs/vue#9397)。
  - 对于其他 `attribute`，我们将移除 `falsy` 的值 (`undefined，null`，或 `false`)，其他值按原样设置 (见这里)。

  下表描述了 `Vue` 如何强制转换“枚举 `attribute`”，以及与普通非布尔 `attribute` 的不同之处：

  绑定表达式 | `foo` 正常 | `draggable` 枚举
  -|-|-
  `:attr="null"` | - | `draggable="false"`
  `:attr="undefined"` | - | -
  `:attr="true"` | `foo="true"` | `draggable="true"`
  `:attr="false"` | - | `draggable="false"`
  `:attr="0"` | `foo="0"` | `draggable="true"`
  `attr=""` | `foo=""` | `draggable="true"`
  `attr="foo"` | `foo="foo"` | `draggable="true"`
  `attr` | `foo=""` | `draggable="true"`
  从上表可以看出，当前实现将 true 强制转换为了 'true'，但如果 attribute 为 false，则将其移除。这也导致了不一致性，并要求用户在非常常见的用例中手动将布尔值强制转换为字符串，例如 aria-* attribute，例如 aria-selected、aria-hidden 等等。

- #### 3.x 语法

  我们打算放弃“枚举型 `attribute`”的内部概念，并将它们视为普通的非布尔型 `HTML attribute`。

  - 这就解决了普通非布尔型 `attribute` 和“枚举型 `attribute`”之间的不一致性问题
  - 这个改动还使得对于诸如 `contenteditable` 这样的 `attribute`，我们可以使用 `'true'` 和 `'false'` 以外的值，甚至是未来新增的关键字。
  - 对于非布尔型 `attribute`，如果其值为 `false，Vue` 将不再移除它们，而是将其强制转换为 `'false'`。这就解决了 `true` 和 `false` 之间的不一致性，并更易于输出 `aria-* attribute`
  
  下表描述了新的行为：

  绑定表达式 | `foo` 正常 | `draggable` 枚举
  -|-|-
  `:attr="null"` | - | - *
  `:attr="undefined"` | - | -
  `:attr="true"` | `foo="true"` | `draggable="true"`
  `:attr="false"` | `foo="false"` * | `draggable="false"`
  `:attr="0"` | `foo="0"` | `draggable="0"` *
  `attr=""` | `foo=""` | `draggable=""` *
  `attr="foo"` | `foo="foo"` | `draggable="foo"` *
  `attr` | `foo=""` | `draggable=""` *
  *：已改变

  布尔型 `attribute` 的强制转换保持不变。

### 35. 一些过渡的 `class` 被重命名

- 过渡的 `class` 名更改(非兼容)
  - 过渡类名 `v-enter` 修改为 `v-enter-from`、过渡类名 `v-leave` 修改为 `v-leave-from`。
- #### 2.x 语法

  在 `v2.1.8` 版本之前，每个过渡方向都有两个过渡类：初始状态与激活状态。

  在 `v2.1.8` 版本中，引入了 `v-enter-to` 来定义 `enter` 或 `leave` 变换之间的过渡动画插帧。然而，为了向下兼容，并没有变动 `v-enter` 类名：

  ```css
  .v-enter,
  .v-leave-to {
    opacity: 0;
  }

  .v-leave,
  .v-enter-to {
    opacity: 1;
  }
  ```

  这样做会带来很多困惑，类似 `enter` 和 `leave` 含义过于宽泛，并且没有遵循类名钩子的命名约定。

- #### 3.x 语法

  为了更加明确易读，我们现在将这些初始状态重命名为：

  ```css
  .v-enter-from,
  .v-leave-to {
    opacity: 0;
  }

  .v-leave-from,
  .v-enter-to {
    opacity: 1;
  }
  ```

  现在，这些状态之间的区别就清晰多了。

  ![Transition Diagram](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/Vue/transitions.svg)

  `<transition>` 组件的相关 `prop` 名称也发生了变化：

  - `leave-class` 已经被重命名为 `leave-from-class` (在渲染函数或 `JSX` 中可以写为：`leaveFromClass`)
  - `enter-class` 已经被重命名为 `enter-from-class` (在渲染函数或 `JSX` 中可以写为：`enterFromClass`)

### 36. `<TransitionGroup>` 不再默认渲染包裹元素

- `Transition Group` 根元素(非兼容)
  - `<transition-group>` 不再默认渲染根元素，但仍然可以用 `tag` attribute 创建根元素。
- #### 2.x 语法

在 `Vue 2` 中，`<transition-group>` 像其它自定义组件一样，需要一个根元素。默认的根元素是一个 `<span>`，但可以通过 `tag` attribute 定制。

```html
<transition-group tag="ul">
  <li v-for="item in items" :key="item">
    {{ item }}
  </li>
</transition-group>
```

- #### 3.x 语法

  在 `Vue 3` 中，我们有了[片段](https://v3.cn.vuejs.org/guide/migration/fragments.html)的支持，因此组件不再需要根节点。所以，`<transition-group>` 不再默认渲染根节点。

  - 如果像上面的示例一样，已经在 `Vue 2` 代码中定义了 `tag` attribute，那么一切都会和之前一样
  - 如果没有定义 `tag` attribute，而且样式或其它行为依赖于 `<span>` 根元素的存在才能正常工作，那么只需将 `tag="span"` 添加到 `<transition-group>`：

  ```html
  <transition-group tag="span">
    <!-- -->
  </transition-group>
  ```

### 37. 当侦听一个数组时，只有当数组被替换时，回调才会触发，如果需要在变更时触发，则必须指定 `deep` 选项

- 侦听数组(非兼容)
  - 当侦听一个数组时，只有当数组被替换时才会触发回调。如果你需要在数组被改变时触发回调，必须指定 `deep` 选项。
- #### 3.x 语法

  当使用 `watch` 选项侦听数组时，只有在数组被替换时才会触发回调。换句话说，在数组被改变时侦听回调将不再被触发。要想在数组被改变时触发侦听回调，必须指定 `deep` 选项。

  ```js
  watch: {
    bookList: {
      handler(val, oldVal) {
        console.log('book list changed')
      },
      deep: true
    },
  }
  ```

### 38. 没有特殊指令的标记 (`v-if/else-if/else、v-for` 或 `v-slot`) 的 `<template>` 现在被视为普通元素，并将渲染为原生的 `<template>` 元素，而不是渲染其内部内容

### 39. 已挂载的应用不会取代它所挂载的元素

- 被挂载的应用不会替换元素(非兼容)
  - 在 `Vue 2.x` 中，当挂载一个具有 `template` 的应用时，被渲染的内容会替换我们要挂载的目标元素。在 `Vue 3.x` 中，被渲染的应用会作为子元素插入，从而替换目标元素的 `innerHTML`。
- #### 2.x 语法

  在 `Vue 2.x` 中，我们为 `new Vue()` 或 `$mount` 传入一个 `HTML` 元素选择器：

  ```js
  new Vue({
    el: '#app',
    data() {
      return {
        message: 'Hello Vue!'
      }
    },
    template: `
      <div id="rendered">{{ message }}</div>
    `
  })

  // 或
  const app = new Vue({
    data() {
      return {
        message: 'Hello Vue!'
      }
    },
    template: `
      <div id="rendered">{{ message }}</div>
    `
  })

  app.$mount('#app')
  ```

  当我们把应用挂载到拥有匹配被传入选择器 (在这个例子中是 `id="app"`) 的 `div` 的页面时：

  ```html
  <body>
    <div id="app">
      Some app content
    </div>
  </body>
  ```

  在渲染结果中，上面提及的 div 将会被应用所渲染的内容替换：

  ```html
  <body>
    <div id="rendered">Hello Vue!</div>
  </body>
  ```

- #### 3.x 语法

  在 `Vue 3.x` 中，当我们挂载一个应用时，其渲染内容会替换我们传递给 `mount` 的元素的 `innerHTML`：

  ```js
  const app = Vue.createApp({
    data() {
      return {
        message: 'Hello Vue!'
      }
    },
    template: `
      <div id="rendered">{{ message }}</div>
    `
  })

  app.mount('#app')
  ```

  当这个应用挂载到拥有匹配 `id="app"` 的 `div` 的页面时，结果会是：

  ```html
  <body>
    <div id="app" data-v-app="">
      <div id="rendered">Hello Vue!</div>
    </div>
  </body>
  ```

### 40. 生命周期的 `hook:` 事件前缀改为 `vnode-`

- `VNode` 生命周期事件(非兼容)
  在 Vue 2 中，我们可以通过事件来监听组件生命周期中的关键阶段。这些事件名都是以 hook: 前缀开头，并跟随相应的生命周期钩子的名字。

  在 Vue 3 中，这个前缀已被更改为 vnode-。额外地，这些事件现在也可用于 HTML 元素，和在组件上的用法一样。

- #### 2.x 语法

  在 `Vue 2` 中，这些事件名和相应的生命周期钩子一致，并带有 `hook:` 前缀：

  ```html
  <template>
    <child-component @hook:updated="onUpdated">
  </template>
  ```

- #### 3.x 语法

  在 `Vue 3` 中，事件名附带的是 `vnode-` 前缀：

  ```html
  <template>
    <child-component @vnode-updated="onUpdated">
  </template>
  ```

  或者在驼峰命名法的情况下附带前缀 `vnode`：

  ```html
  <template>
    <child-component @vnodeUpdated="onUpdated">
  </template>
  ```

## 被移除的 API

### 41. `keyCode` 作为 `v-on` 修饰符的支持

- 按键修饰符(非兼容)
  - 不再支持使用数字 (即键码) 作为 `v-on` 修饰符
  - 不再支持 `config.keyCodes`

- #### 2.x 语法

  在 `Vue 2` 中，`keyCodes` 可以作为修改 `v-on` 方法的一种方式。

  ```html
  <!-- 键码版本 -->
  <input v-on:keyup.13="submit" />

  <!-- 别名版本 -->
  <input v-on:keyup.enter="submit" />
  ```

  此外，也可以通过全局的 `config.keyCodes` 选项定义自己的别名。

  ```js
  Vue.config.keyCodes = {
    f1: 112
  }
  <!-- 键码版本 -->
  <input v-on:keyup.112="showHelpText" />

  <!-- 自定义别名版本 -->
  <input v-on:keyup.f1="showHelpText" />
  ```

- #### 3.x 语法

  从 `KeyboardEvent.keyCode` 已被废弃开始，`Vue 3` 继续支持这一点就不再有意义了。因此，现在建议对任何要用作修饰符的键使用 `kebab-cased` (短横线) 名称。

  ```html
  <!-- Vue 3 在 v-on 上使用按键修饰符 -->
  <input v-on:keyup.page-down="nextPage">

  <!-- 同时匹配 q 和 Q -->
  <input v-on:keypress.q="quit">
  ```

  因此，这意味着 `config.keyCodes` 现在也已弃用，不再受支持。
  对于某些标点符号键，可以直接把它们包含进去，以 , 键为例：

  ```html
  <input v-on:keypress.,="commaPress">
  ```
  
  语法的限制导致某些特定字符无法被匹配，比如 `"`、`'`、`/`、`=`、`>` 和 `.`。对于这些字符，你应该在监听器内使用 `event.key` 代替。

### 42. `$on、$off` 和 `$once` 实例方法

- 事件 `API`(非兼容)
  - `$on`，`$off` 和 `$once` 实例方法已被移除，组件实例不再实现事件触发接口。

- #### 2.x 语法

  在 `2.x` 中，`Vue` 实例可用于触发由事件触发器 `API` 通过指令式方式添加的处理函数 (`$on，$off` 和 `$once`)。这可以用于创建一个事件总线，以创建在整个应用中可用的全局事件监听器：

  ```js
  // eventBus.js

  const eventBus = new Vue()

  export default eventBus

  /*
  *****
  */

  // ChildComponent.vue
  import eventBus from './eventBus'

  export default {
    mounted() {
      // 添加 eventBus 监听器
      eventBus.$on('custom-event', () => {
        console.log('Custom event triggered!')
      })
    },
    beforeDestroy() {
      // 移除 eventBus 监听器
      eventBus.$off('custom-event')
    }
  }

  /*
  *****
  */

  // ParentComponent.vue
  import eventBus from './eventBus'

  export default {
    methods: {
      callGlobalCustomEvent() {
        eventBus.$emit('custom-event') // 当 ChildComponent 已被挂载时，控制台中将显示一条消息
      }
    }
  }
  ```

- #### 3.x 更新

  我们从实例中完全移除了 `$on、$off` 和 `$once` 方法。`$emit` 仍然包含于现有的 `API` 中，因为它用于触发由父组件声明式添加的事件处理函数。

### 43. 过滤器 (`filter`)

- 过滤器(移除)
  - 从 Vue 3.0 开始，过滤器已移除，且不再支持。

- #### 2.x 语法

  在 `2.x` 中，开发者可以使用过滤器来处理通用文本格式。

  例如：

  ```js
  <template>
    <h1>Bank Account Balance</h1>
    <p>{{ accountBalance | currencyUSD }}</p>
  </template>

  <script>
    export default {
      props: {
        accountBalance: {
          type: Number,
          required: true
        }
      },
      filters: {
        currencyUSD(value) {
          return '$' + value
        }
      }
    }
  </script>
  ```

  虽然这看起来很方便，但它需要一个自定义语法，打破了大括号内的表达式“只是 `JavaScript`”的假设，这不仅有学习成本，而且有实现成本。

- #### 3.x 更新

  在 `3.x` 中，过滤器已移除，且不再支持。取而代之的是，我们建议用方法调用或计算属性来替换它们。

  以上面的案例为例，以下是一种实现方式。

  ```js
  <template>
    <h1>Bank Account Balance</h1>
    <p>{{ accountInUSD }}</p>
  </template>

  <script>
    export default {
      props: {
        accountBalance: {
          type: Number,
          required: true
        }
      },
      computed: {
        accountInUSD() {
          return '$' + this.accountBalance
        }
      }
    }
  </script>
  ```

### 44. 内联模板 `attribute`

- 内联模板 `Attribute`(非兼容)
- #### 2.x 语法

  在 `2.x` 中，`Vue` 为子组件提供了 `inline-template` attribute，以便将其内部内容作为模板使用，而不是作为分发内容。

  ```html
  <my-component inline-template>
    <div>
      <p>它们将被编译为组件自己的模板，</p>
      <p>而不是父级所包含的内容。</p>
    </div>
  </my-component>
  ```

- #### 3.x 语法

  将不再支持此功能。

### 45. `$children` 实例 `property`

- `$children`(移除)
  - `$children` 实例 `property` 已从 `Vue 3.0` 中移除，不再支持。

- #### 2.x 语法

  在 `2.x` 中，开发者可以使用 `this.$children` 访问当前实例的直接子组件：

  ```js
  <template>
    <div>
      <img alt="Vue logo" src="./assets/logo.png">
      <my-button>Change logo</my-button>
    </div>
  </template>

  <script>
  import MyButton from './MyButton'

  export default {
    components: {
      MyButton
    },
    mounted() {
      console.log(this.$children) // [VueComponent]
    }
  }
  </script>
  ```

- #### 3.x 更新

  在 `3.x` 中，`$children property` 已被移除，且不再支持。如果你需要访问子组件实例，我们建议使用 [`$refs`](https://v3.cn.vuejs.org/guide/component-template-refs.html#%E6%A8%A1%E6%9D%BF%E5%BC%95%E7%94%A8)。

### 46. `propsData` 选项

- `propsData`(移除)
- #### 2.x 语法

  在 `2.x` 中，我们可以在创建 `Vue` 实例的时候传入 `prop`：

  ```js
  const Comp = Vue.extend({
    props: ['username'],
    template: '<div>{{ username }}</div>'
  })

  new Comp({
    propsData: {
      username: 'Evan'
    }
  })
  ```

- #### 3.x 更新

  `propsData` 选项已经被移除。如果你需要在实例创建时向根组件传入 `prop`，你应该使用 `createApp` 的第二个参数：

  ```js
  const app = createApp(
    {
      props: ['username'],
      template: '<div>{{ username }}</div>'
    },
    { username: 'Evan' }
  )
  ```

### 47. `$destroy` 实例方法。用户不应再手动管理单个 `Vue` 组件的生命周期。

### 48. 全局函数 `set` 和 `delete` 以及实例方法 `$set` 和 `$delete`。基于代理的变化检测已经不再需要它们了
