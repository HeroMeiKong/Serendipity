# JavaScript - Bizarre 题目汇总★

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****

1. `true + false`

    ```javascript
    Number(true); // -> 1
    Number(false); // -> 0
    1 + 0; // -> 1
    ```

    💡 说明：布尔值被转换为它们的数字表示

1. `true` 是 `false`

    ```javascript
    !!"false" == !!"true"; // -> true
    !!"false" === !!"true"; // -> true
    ```

1. ★★★`![] + []`

    ```javascript
    ![] + []; // "false"
    true + []; // "true"
    NaN + []; // "NaN"
    1 + []; // "1"
    "hello" + []; // "hello"
    [] + []; // ""

    {} + []; // 0
    [] + {}; // "[object Object]"
    var a = {} + []; // a === "[object Object]"
    var b = [] + {}; // b === "[object Object]"
    {} + {}; // "[object Object][object Object]"

    [+[]]; // [0]
    [!+[]]; // [true]
    [+!+[]]; // [1]
    [![]] + [][[]]; // [false] + undefined === "falseundefined"
    +!+[] + [+[]]; // 1 + [0] === "10"
    ```

1. `baNaNa`

    `"b" + "a" + +"a" + "a"; // baNaNa`
    用 `JavaScript` 写的老派笑话：

    `"foo" + +"bar"; // -> 'fooNaN'`
    💡 说明：这个表达式可以转化成 `'foo' + (+'bar')`，但无法将 `'bar'` 强制转化成数值。

1. ★★★`NaN` 不是一个 `NaN`

    `NaN === NaN; // -> false`
    💡 说明：规范严格定义了这种行为背后的逻辑：

    - 如果 `Type(x)` 不同于 `Type(y)`, `return false`.
    - 如果 `Type(x)` 数值, 然后
    - 如果 `x` 是 `NaN`, `return false`.
    - 如果 `y` 是 `NaN`, `return false`.
    … … …
    遵循 `IEEE` 的 `NaN` 的定义：**有四种可能的相互排斥的关系：小于，等于，大于和无序。当至少一个操作数是 `NaN` 时，便是最后一种情况。每个 `NaN` 都要比较无穷无尽的一切，包括自己。**

1. ★★★它是 `fail`

    ```javascript
    (![] + [])[+[]] +
      (![] + [])[+!+[]] +
      ([![]] + [][[]])[+!+[] + [+[]]] +
      (![] + [])[!+[] + !+[]];
    // -> 'fail'
    ```

    💡 说明：将大量的符号分解成片段，我们注意到，以下表达式经常出现：

    ```javascript
    ![] + []; // -> 'false'
    ![]; // -> false
    ```

    所以我们尝试将 `[]` 和 `false` 加起来。 但是通过一些内部函数调用（`binary + Operator - >ToPrimitive - >[[DefaultValue]]`），我们最终将右边的操作数转换为一个字符串：

    `![] + [].toString(); // 'false'`
    将字符串作为数组，我们可以通过 `[0]` 来访问它的第一个字符：

    `"false"[0]; // -> 'f'`
    现在，其余的是明显的，可以自己弄清楚！

1. `[]` 是 `true`, 但它不等于 `true`

    ```javascript
    !![]       // -> true
    [] == true // -> false
    ```

    💡 说明：数组是一个 `true`，但是它不等于 `true`。

1. `null` 是 `false`, 但又不等于 `false`

    尽管 `null` 是 `false`，但它不等于 `false`。

    ```javascript
    !!null; // -> false
    null == false; // -> false
    ```

    同时，其他的一些等于 `false` 的值，如 `0` 或 `''` 等于 `false`。

    ```javascript
    0 == false; // -> true
    "" == false; // -> true
    ```

1. `document.all` 是一个 `object`，但又同时是 `undefined`

    ⚠️ 这是浏览器 `API` 的一部分，对于 `Node.js` 环境无效 ⚠️

    尽管 `document.all` 是一个 `array-like object` 并且通过它可以访问页面中的 `DOM` 节点，但在通过 `typeof` 的检测结果是 `undefined`。

    ```javascript
    document.all instanceof Object; // -> true
    typeof document.all; // -> 'undefined'
    ```

    同时，`document.all` 不等于 `undefined`。

    ```javascript
    document.all === undefined; // -> false
    document.all === null; // -> false
    ```

    但是同时：
    `document.all == null; // -> true`
    💡 说明：`document.all` 曾经是访问页面 `DOM` 节点的一种方式，特别是在早期版本的 `IE` 浏览器中。它从未成为标准，但被广泛使用在早期的 `JS` 代码中。当标准演变出新的 `API` 时（例如 `document.getElementById`）这个 `API` 调用就被废弃了，标准委员会必须决定如何处理它。由于它被广泛使用，所以决定保留这个 `API` 但引入一个有意的对 `JavaScript` 的标准的违反。 其与 `undefined` 使用严格相等比较得出 `false` 而使用抽象相等比较得出 `true` 是因为这个有意的对标准的违反明确地允许了这一点。

1. 最小值大于零

    `Number.MIN_VALUE` 是最小的数字，大于零：

    `Number.MIN_VALUE > 0; // -> true`
    💡 说明：`Number.MIN_VALUE` 是 `5e-324`，即可以在浮点精度内表示的最小正数，即可以达到零。它定义了浮点数的最高精度。

    现在，整体最小的值是 `Number.NEGATIVE_INFINITY`，尽管这在严格意义上并不是真正的数字。

1. 函数不是函数

    ⚠️ `V8 v5.5` 或更低版本中出现的 `Bug（Node.js <= 7）` ⚠️

    你们所有人都知道的关于讨厌的 `undefined` 不是 `function`，但是这个呢？

    ```javascript
    // Declare a class which extends null
    class Foo extends null {}
    // -> [Function: Foo]

    new Foo() instanceof null;
    // > TypeError: function is not a function
    // >     at … … …
    ```

    💡 说明：这不是规范的一部分。这只是一个错误，现在它已被修复，所以将来不会有这个问题。

1. 数组相加

    如果您尝试两个数组相加呢？

    `[1, 2, 3] + [4, 5, 6]; // -> '1,2,34,5,6'`
    💡 说明：会发生合并。一步一步地，它是这样的:

    ```javacript
    [1, 2, 3] +
      [4, 5, 6][
        // joining
        (1, 2, 3)
      ].join() +
      [4, 5, 6].join();
    // concatenation
    "1,2,3" + "4,5,6";
    // ->
    ("1,2,34,5,6");
    ```

1. ★★★数组中的逗号

    您已经创建了一个包含 `4` 个空元素的数组。尽管如此，你还是会得到一个有三个元素的，因为后面的逗号：

    ```javascript
    let a = [, , ,];
    a.length; // -> 3
    a.toString(); // -> ',,'
    ```

    💡 说明：尾逗号 (有时也称为“最后逗号”) 在向 `JavaScript` 代码中添加新元素、参数或属性时有用。如果您想添加一个新属性，您可以简单地添加一个新行，而不用修改以前的最后一行，如果该行已经使用了后面的逗号。这使得版本控制比较清洁和编辑代码可能不太麻烦。

1. 数组相等是一个怪物

    数组进行相等比较是一个怪物，看下面的例子：

    ```javascript
    [] == ''   // -> true
    [] == 0    // -> true
    [''] == '' // -> true
    [0] == 0   // -> true
    [0] == ''  // -> false
    [''] == 0  // -> true

    [null] == ''      // true
    [null] == 0       // true
    [undefined] == '' // true
    [undefined] == 0  // true

    [[]] == 0  // true
    [[]] == '' // true

    [[[[[[]]]]]] == '' // true
    [[[[[[]]]]]] == 0  // true

    [[[[[[ null ]]]]]] == 0  // true
    [[[[[[ null ]]]]]] == '' // true

    [[[[[[ undefined ]]]]]] == 0  // true
    [[[[[[ undefined ]]]]]] == '' // true
    ```

    💡 说明：
    你应该非常小心留意上面的例子！ `7.2.13 Abstract Equality Comparison` 规范描述了这些行为。

1. `undefined` 和 `Number`

    如果我们不把任何参数传递到 `Number` 构造函数中，我们将得到 `0`。`undefined` 是一个赋值形参，没有实际的参数，所以您可能期望 `NaN` 将 `undefined` 作为参数的值。然而，当我们通过 `undefined`，我们将得到 `NaN`。

    ```javascript
    Number(); // -> 0
    Number(undefined); // -> NaN
    ```

    💡 说明：
    根据规范：
    1. 如果没有参数传递给这个函数，让 `n` 为 `+0`;
    2. 否则，让 `n` 调用 `ToNumber(value)`
    3. 如果值为 `undefined`,那么 `ToNumber(undefined)` 应该返回 `NaN`.

1. `parseInt` 是一个坏蛋

    `parseInt` 它以的怪异而出名。

    ```javascript
    parseInt("f*ck"); // -> NaN
    parseInt("f*ck", 16); // -> 15
    ```

    💡 说明：

    这是因为 `parseInt` 会持续通过解析直到它解析到一个不识别的字符，`'f*ck'` 中的 `f` 是 `16` 进制下的 `15`。

    解析 `Infinity` 到整数也很有意思…

    ```javascript
    //
    parseInt("Infinity", 10); // -> NaN
    // ...
    parseInt("Infinity", 18); // -> NaN...
    parseInt("Infinity", 19); // -> 18
    // ...
    parseInt("Infinity", 23); // -> 18...
    parseInt("Infinity", 24); // -> 151176378
    // ...
    parseInt("Infinity", 29); // -> 385849803
    parseInt("Infinity", 30); // -> 13693557269
    // ...
    parseInt("Infinity", 34); // -> 28872273981
    parseInt("Infinity", 35); // -> 1201203301724
    parseInt("Infinity", 36); // -> 1461559270678...
    parseInt("Infinity", 37); // -> NaN
    ```

    也要小心解析 `null`：

    `parseInt(null, 24); // -> 23`
    💡 说明：它将 `null` 转换成字符串 `'null'`，并尝试转换它。对于基数 `0` 到 `23`，没有可以转换的数字，因此返回 `NaN`。 在 `24` 时，`"n"`，第 `14` 个字母被添加到数字系统。 在 `31` 时，`"u"`，添加第 `21` 个字母，可以解码整个字符串。 在 `37` 处，不再有可以生成的有效数字集，并返回 `NaN`。

    不要忘记八进制：

    ```javascript
    parseInt("06"); // 6
    parseInt("08"); // 8 如果支持 ECMAScript 5
    parseInt("08"); // 0 如果不支持 ECMAScript 5
    ```

    💡 说明：这是因为 `parseInt` 能够接受两个参数，如果没有提供第二个参数，并且第一个参数以 `0` 开始，它将把第一个参数当做八进制数解析。

    `parseInt` 总是把输入转为字符串：

    ```javascript
    parseInt({ toString: () => 2, valueOf: () => 1 }); // -> 2
    Number({ toString: () => 2, valueOf: () => 1 }); // -> 1
    ```

    解析浮点数的时候要注意

    ```javascript
    parseInt(0.000001); // -> 0
    parseInt(0.0000001); // -> 1
    parseInt(1 / 1999999); // -> 5
    ```

    💡 说明： `parseInt` 接受字符串参数并返回一个指定基数下的证书。`parseInt` 也去除第一个字符串中非数字字符（字符集由基数决定）后的内容。`0.000001` 被转换为 `"0.000001"` 而 `parseInt` 返回 `0`。当 `0.0000001` 被转换为字符串时它被处理为 `"1e-7"` 因此 `parseInt` 返回 `1`。`1/1999999` 被转换为 `5`.`00000250000125e-7` 而 `parseInt` 返回 `5`。

1. `true` 和 `false` 数学运算
    我们做一些数学计算：

    ```javascript
    true + (true + true) * (true + true) - true; // -> 4
      ```

    💡 说明：我们可以用 `Number` 构造函数强制转化成数值。很明显，`true` 将被强制转换为 `1`：
    `Number(true); // -> 1`
    一元加运算符尝试将其值转换成数字。 它可以转换整数和浮点的字符串表示，以及非字符串值 `true`，`false` 和 `null`。 如果它不能解析特定的值，它将转化为 `NaN` 。 这意味着我们可以更容易地强制将 `true` 换成 `1`

    `+true; // -> 1`
    当你执行加法或乘法时，`ToNumber` 方法调用。根据规范，该方法返回：

    如果参数 `is true`, 返回 `1`。如果参数是 `false` 返回 `+0`。

    这就是为什么我们可以进行进行布尔值相加并得到正确的结果

1. `HTML` 注释在 `JavaScript` 中有效

    你会留下深刻的印象，`<!--` (这是 HTML 注释）是一个有效的 `JavaScript` 注释。

    ```javascript
    // 有效注释
    <!-- 也是有效的注释
    ```

    💡 说明：
    感动吗? 类似 `HTML` 的注释旨在允许不理解标签的浏览器优雅地降级。这些浏览器，例如 `Netscape 1.x` 已经不再流行。因此，在脚本标记中添加 `HTML` 注释是没有意义的。

    由于 `Node.js` 基于 `V8` 引擎，`Node.js` 运行时也支持类似 `HTML` 的注释。

1. `NaN` 不是一个数值

    尽管 `NaN` 类型是 `'number'`，但是 `NaN` 不是数字的实例：

    ```javascript
    typeof NaN; // -> 'number'
    NaN instanceof Number; // -> false
    ```

1. `[]` 和 `null` 是对象

    ```javascript
    typeof []; // -> 'object'
    typeof null; // -> 'object'

    // 然而
    null instanceof Object; // false
    ```

    💡 说明：`typeof` 运算符的行为在本节的规范中定义：

    根据规范，`typeof` 操作符返回一个字符串。对于没有 `[[Call]]` 实现的 `null`、普通对象、标准特异对象和非标准特异对象，它返回字符串 `"object"`。

    但是，您可以使用 `toString` 方法检查对象的类型。

    ```javascript
    Object.prototype.toString.call([]);
    // -> '[object Array]'

    Object.prototype.toString.call(new Date());
    // -> '[object Date]'

    Object.prototype.toString.call(null);
    // -> '[object Null]'
    ```

1. 神奇的数字增长

    ```javascript
    999999999999999; // -> 999999999999999
    9999999999999999; // -> 10000000000000000
    ```

    💡 说明：这是由 `IEEE 754-2008` 二进制浮点运算标准引起的。

1. `0.1 + 0.2` 精度计算

    来自 `JavaScript` 的知名笑话。`0.1` 和 `0.2` 相加是存在精度错误的

    ```javascript
    0.1 + 0.2 === 0.3; // -> false
    0.1 + 0.2 === 0.30000000000000004; // true
    ```

    💡 说明：浮点计算坏了:
    程序中的常量 `0.2` 和 `0.3` 也将近似为真实值。最接近 `0.2` 的 `double` 大于有理数 `0.2`，但最接近 `0.3` 的 `double` 小于有理数 `0.3`。`0.1` 和 `0.2` 的总和大于有理数 `0.3`，因此不符合您的代码中的常数判断。

1. 扩展数字的方法

    您可以添加自己的方法来包装对象，如 `Number` 或 `String`。

    ```javascript
    Number.prototype.isOne = function () {
      return Number(this) === 1;
    };

    (1.0).isOne(); // -> true
    (1).isOne(); // -> true
    (2.0).isOne()(7).isOne(); // -> false
    ```

    💡 说明：显然，您可以像 `JavaScript` 中的任何其他对象一样扩展 `Number` 对象。但是，不建议扩展不属于规范的行为定义。

1. ★★★三个数字的比较

    ```javascript
    1 < 2 < 3; // -> true
    3 > 2 > 1; // -> false
    ```

    💡 说明：为什么会这样呢？其实问题在于表达式的第一部分。以下是它的工作原理：

    ```javascript
    1 < 2 < 3; // 1 < 2 -> true
    true < 3; // true -> 1
    1 < 3; // -> true

    3 > 2 > 1; // 3 > 2 -> true
    true > 1; // true -> 1
    1 > 1; // -> false
    ```

    我们可以用 大于或等于运算符（`>=`）：

    `3 > 2 >= 1; // true`

1. 有趣的数学

    通常 `JavaScript` 中的算术运算的结果可能是非常难以预料的。 考虑这些例子：

    ```javascript
    3  - 1  // -> 2
    3  + 1  // -> 4
    '3' - 1  // -> 2
    '3' + 1  // -> '31'

    '' + '' // -> ''
    [] + [] // -> ''
    {} + [] // -> 0
    [] + {} // -> '[object Object]'
    {} + {} // -> '[object Object][object Object]'

    '222' - -'111' // -> 333

    [4] * [4]       // -> 16
    [] * []         // -> 0
    [4, 4] * [4, 4] // NaN
    ```

    💡 说明：前四个例子发生了什么？这是一个小表，以了解 `JavaScript` 中的添加：

    ```javascript
    Number  + Number  -> addition
    Boolean + Number  -> addition
    Boolean + Boolean -> addition
    Number  + String  -> concatenation
    String  + Boolean -> concatenation
    String  + String  -> concatenation
    ```

    剩下的例子呢？在相加之前，`[]` 和 `{}` 隐式调用 `ToPrimitive` 和 `ToString` 方法。

1. 字符串不是 `String` 的实例

    ```javascript
    "str"; // -> 'str'
    typeof "str"; // -> 'string'
    "str" instanceof String; // -> false
    ```

    💡 说明：`String` 构造函数返回一个字符串：

    ```javascript
    typeof String("str"); // -> 'string'
    String("str"); // -> 'str'
    String("str") == "str"; // -> true
    ```

    我们来试试一个 `new`：

    ```javascript
    new String("str") == "str"; // -> true
    typeof new String("str"); // -> 'object'
    ```

    对象？那是什么？

    `new String("str"); // -> [String: 'str']`

1. s

****
ℹ️：未完成
★：常考
★★：重点掌握
★★★：高频，必背
****
