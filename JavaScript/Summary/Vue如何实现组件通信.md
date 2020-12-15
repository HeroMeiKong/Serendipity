# Vue如何实现组件通信

Vue组件通信的三种情况：

- 父子通信
- 爷孙通信
- 兄弟通信

1. 父子通信：父组件使用Prop向子组件传递数据，子组件通过事件向父组件发送消息，使用 v-on 绑定自定义事件）

    ```jsx
    <div id="app">
      <p>{{message}}</p>
      <button @click="visible=true">打开</button>
      <child v-show=visible @close="visible=false"></child>
    </div>

    Vue.component('child',{
      template: `
        <div>我是儿子
          <button @click="$emit('close')">关闭</button>
        </div>`
    })
    new Vue({
      el: '#app',
      data: {
        message: 'hello',
        visible: false
      }
    })
    ```

2. 爷孙通信（通过两对父子通信，爷爸之间父子通信，爸儿之间父子通信）

    ```jsx
    <div id="app">
      <p>{{message}}</p>
      <button @click="seen=true">打开</button>
      <child v-show=seen  @close="log(seen)"></child>
    </div>

    Vue.component('child',{
      props: ['seen','seensunzi'],
      template: `
        <div>
          我是爸爸<button  @click="seensunzi=true">打开</button>
          <sunzi v-show=seensunzi @close="seensunzi=false;$emit('close')"></sunzi>
        </div>`
    })
    Vue.component('sunzi',{
      props: ['seensunzi'],
      template: `
        <div>
          我是儿子<button @click="$emit('close')">关闭</button>
        </div>`
    })
    var vue = new Vue({
      el: '#app',
      data: {
        message: '我是爷爷',
        seen: false,
        seensunzi: false
      },
      methods:{
        log(seen){
          console.log('爷爷收到消息：爷爷先关掉儿子，再关掉我爸爸')
          setTimeout(()=>{
            this.seen = false
          },2000)
        }
      }
    })
    ```

3. 兄弟通信（new Vue() 作为 eventBus）

    ```jsx
    <div id="app">
      <component-a></component-a>
      <component-b></component-b>
    </div>

    let eventHub = new Vue()
    Vue.prototype.$eventHub = eventHub

    Vue.component('component-a',{
      template:`
        <div>a<button @click=notify>click</button></div>
      `,
      methods:{
        notify(){
          this.__proto__ === Vue.prototype
          this.$eventHub.$emit('xxx','hi')
        }
      }
    })
    Vue.component('component-b',{
      template:`
        <div>b<div ref=output></div></div>
      `,
      created(){
        this.$eventHub.$on('xxx',(data)=>{
          this.$refs.output.textContent = data
        })
      }
    })
    let app = new Vue({
      el: "#app"
    })
    ```
