# TypeScript 笔记

★★★★★★★★★★

1. ## 引入非模块类的声明文件

    使用三斜线 `reference` 引入，有常用两种方式 `path` 和 `types`
    区别
    - `types` 一般引入外部依赖的声明
    - `path` 一般引入自己写的声明

    比如外部声明文件位置为 `node_modules/@types/b/index.d.ts`，内容为 `type B = { age: number }`，使用 `path` 和 `types` 引入方式如下：

    ```ts
    /// <reference path="node_modules/@types/b/index.d.ts" />

    /// <reference types="b" />
    ```

    **非模块的声明文件引入后为全局类型，可以直接使用**
    `const b: B = { age: 100 }`
    这儿可以看出，与 `js` 文件引入相比，`ts` 多了一项寻址策略，它中会自动寻找 `node_modules/@types` 下的文件。

★★★★★★★★★★
