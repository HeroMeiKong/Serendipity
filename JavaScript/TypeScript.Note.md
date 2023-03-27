# TypeScript 笔记

★★★★★★★★★★

1. ## 基础类型

   - `boolean`: 布尔值
   - `number`: 数字
   - `string`: 字符串
   - `Array<> || []`: 数组
   - `[string, number]`: 元组 - 允许表示一个已知元素数量和类型的数组，各元素的类型不必相同
   - `enum`: 枚举 - 重命名
   - `any`: `Any` - 编程阶段还不清楚类型的变量指定一个类型
   - `unknown`: ''
   - `void`: 空 - 与 `any` 类型相反，它表示没有任何类型
   - `null`: 找不到值 - 取值错误 | 取值丢失
   - `undefined`: 未定义
   - `symbol`: `symbol`
   - `never`: 那些永不存在的值的类型 - `never` 类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型；变量也可能是 `never` 类型，当它们被永不为真的类型保护所约束时。`never` 类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是 `never` 的子类型或可以赋值给 `never` 类型（除了 `never` 本身之外）。 即使 `any` 也不可以赋值给 `never`
   - `object`: 对象 - 非原始类型，也就是除 `number，string，boolean，symbol，null` 或 `undefined` 之外的类型

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

1. ## 如何防止 `for in / for of` 对象定义报错

    ```ts
    for (const param in params) {
      console.log("param: ", param)
    }
    
    export interface paramsObj {
      [key: string]: any;
    }
    ```

1. ## 如何在定义 type 类型时，保证其只能取 enum 中的值？

    ```ts
    enum LogLevel {
      ERROR,
      WARN,
      INFO,
      DEBUG,
    }
    
    /**
    * This is equivalent to:
    * type LogLevelStrings = 'ERROR' | 'WARN' | 'INFO' | 'DEBUG';
    */
    type LogLevelStrings = keyof typeof LogLevel;
    ```

1. ## 仅表示类型，但在此处却作为值使用 ts(2693)

    ```ts
    <script setup lang="ts">
    import { User } from "#/users";

    defineProps({
      user: {
        type: User, //ERROR: 'User' only refers to a type, but is being used as a value here. ts(2693)
        required: true,
      },
    });
    </script>
    ```

    ```ts
    <script setup lang="ts">
    import { PropType } from "vue";
    import { User } from "#/users";

    defineProps({
      user: {
        type: String as PropType<User>,
        required: true,
      },
    });
    </script>
    ```

★★★★★★★★★★
