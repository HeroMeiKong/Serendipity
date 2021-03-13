# TypeScript 面试题汇总★

1. 基础类型：
   - boolean：布尔值
   - number：数字
   - string：字符串
   - Array<> || []：数组
   - [string, number]：元组 - 允许表示一个已知元素数量和类型的数组，各元素的类型不必相同
   - enum：枚举 - 重命名
   - any：Any - 编程阶段还不清楚类型的变量指定一个类型
   - void：空 - 与 any 类型相反，它表示没有任何类型
   - null：找不到值 - 取值错误 | 取值丢失
   - undefined：未定义
   - symbol：symbol
   - never：那些永不存在的值的类型 - never 类型是那些总是会抛出异常或根本就不会有返回值的函数表达式或箭头函数表达式的返回值类型；变量也可能是 never 类型，当它们被永不为真的类型保护所约束时。never 类型是任何类型的子类型，也可以赋值给任何类型；然而，没有类型是 never 的子类型或可以赋值给never类型（除了 never 本身之外）。 即使 any 也不可以赋值给 never
   - object：对象 - 非原始类型，也就是除 number，string，boolean，symbol，null 或 undefined 之外的类型
2. s
