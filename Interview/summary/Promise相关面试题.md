# Promise 相关面试题

1. ## `Promise` 输出顺序

    ```js
    Promise.resolve().then(() => {
      console.log(0)
      return Promise.resolve(4)
    }).then(res => {
      console.log(res)
    })

    Promise.resolve().then(() => {
      console.log(1)
    }).then(() => {
      console.log(2)
    }).then(() => {
      console.log(3)
    }).then(() => {
      console.log(5)
    }).then(() =>{
      console.log(6)
    })

    // 输出：0 1 2 3 4 5 6

    // 主要问题在 return Promise.resolve(4)，传入的是 Promise
    // 处理程序里返回 thenable 对象就会导致增加两个任务入列。
    // 简单说，就是创建 NewPromiseResolveThenableJob，多了一个 microtask；
    // 运行 NewPromiseResolveThenableJob 又多了一个 microtask，
    // 这两个 microtask 不执行 JS 代码。

    Promise.resolve().then(() => {
      console.log(0)
      return new Promise(resolve => resolve(4))
      .then(res => {
        console.log("新增then")
        return res
      })
    }).then(res => {
      console.log(res)
    })

    Promise.resolve().then(() => {
      console.log(1)
    }).then(() => {
      console.log(2)
    }).then(() => {
      console.log(3)
    }).then(() => {
      console.log(5)
    }).then(() =>{
      console.log(6)
    })

    // 输出：0 1 新增then 2 3 4 5 6

    Promise.resolve().then(() => {
      console.log(0)
      return new Promise(resolve => resolve(4))
      .then(res => {
        console.log("新增then1")
        return res
      })
      .then(res => {
        console.log("新增then2")
        return res
      })
    }).then(res => {
      console.log(res)
    })

    Promise.resolve().then(() => {
      console.log(1)
    }).then(() => {
      console.log(2)
    }).then(() => {
      console.log(3)
    }).then(() => {
      console.log(5)
    }).then(() =>{
      console.log(6)
    })

    // 输出：0 1 新增then1 2 新增then2 3 5 4 6
    ```
