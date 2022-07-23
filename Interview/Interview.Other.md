# 其它问题面试题目汇总★

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

## Vite

1. ### 为什么 Vite 会创建 tsconfig 和 tsconfig.node 两个配置文件？

    因为 `Vite` 使用两个不同的环境执行 `TypeScript` 代码：
    - 在浏览器上执行 `App(./src)`
    - 本地环境 `Node`

1. ### Vite 在哪里可以定义全局变量？

    ```js
    import { defineConfig } from 'vite';

    export default defineConfig(({ command, mode }) => {
      // 用 dotenv 支持
      const { VITE_USE_MOCK } = viteEnv;
      const isBuild = command === 'build';

      return {
        // 定义全局变量
        define: {
          isBuild: isBuild,
          __IS_MOCK__: VITE_USE_MOCK
        },
      }
    })
    ```

## 未归类

1. ### `devDependencies` 和 `dependencies` 区别

    - `devDependencies` 安装只在开发环境下依赖的模块，生产环境不被打入包内
    - `dependencies` 安装所有环境的模块，开发环境和生产环境都能使用

1. ### 常见排序算法

1. ### babel原理

1. ### 实现一个幻灯片功能

1. ### 你最近遇到过什么技术挑战？你是如何解决的？

1. ### 浏览器输入网址后做了什么？

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
