# 为什么我推荐使用 pnpm 作为包管理工具？

## 目录

- 包管理器的发展历史简要
- 什么是 `pnpm`？
- 推荐原因
  - 功能对比
  - 与其它包管理工具的比较
  - `pnpm vs npm`
  - 什么是 `Monorepo`？
  - `Monorepo` 支持
  - 别名
  - 命令行 `tab` 自动补全
- `node_modules` 结构
- 存储&依赖管理
- 部分命令
  - `pnpm import`
  - `pnpm prune`
  - `pnpm dlx`
  - `pnpm env <cmd>`
  - `pnpm store`
  - `pnpm doctor`
- 局限
- 安全
- 一些常见的问题

- ## 包管理器的发展历史简要
  
  - 先来看两张图：
    ![node_modules](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/TechnologySharing/node_modules.webp)
    ![node_modules](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/TechnologySharing/node_modules1.webp)

    *中子星：一立方厘米的物质便可重达十亿吨*
    *黑洞：强大引入，光都难以逃脱*
    *`node_modules`: 略略略*
    **DDDD(懂的都懂)**

  - 发展历史简要
  `npm` 是最早出现的，有很多问题
  `yarn` 出现了，解决了很多问题，并不是全部
  `pnpm` 出现了，比 `npm` 和 `yarn` 都好
  `yarn` 出现后，`npm` 也一直在进步
  `pnpm` 出现后，`yarn` 也一直在进步
  *`cnpm` 是 `for china` 的 `npm`，只是这样而已*

- ## 什么是 `pnpm`？

  简介：速度快、节省磁盘空间的**软件包管理器**
  含义：`pnpm = performant npm`
  初衷：
  - 节省磁盘空间
    使用 `npm` 时，依赖每次被不同的项目使用，都会重复安装一次。而在使用 `pnpm` 时，依赖会被存储在内容可寻址的存储中，所以：

    - 如果你用到了某依赖项的不同版本，只会将不同版本间有差异的文件添加到仓库
      例如，如果某个包有 `100` 个文件，而它的新版本只改变了其中 `1` 个文件。那么 `pnpm update` 时只会向存储中心额外添加 `1` 个新文件，而不会因为仅仅一个文件的改变复制整新版本包的内容。
    - 所有文件都会存储在硬盘上的某一位置
      当软件包被被安装时，包里的文件会硬链接到这一位置，而不会占用额外的磁盘空间。这允许你跨项目地共享同一版本的依赖。
      因此，您在磁盘上节省了大量空间，这与项目和依赖项的数量成正比，并且安装速度要快得多！
  - 提高安装速度
    - 依赖解析
      识别所有必需的依赖项并将其提取到存储库
    - 目录结构计算
      `node_modules` 目录结构是基于依赖项计算的
    - 链接依赖项
      将所有剩余的依赖项从存储库中获取并 `hard link` 到 `node_modules` 文件夹中
    ![pnpm install](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/TechnologySharing/pnpm-install.svg)
    这种方法比传统的解析、获取和写入所有依赖项的三阶段安装过程要快得多
    ![npm install](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/TechnologySharing/npm-install.svg)
  - 创建一个非扁平的 `node_modules` 目录
    使用 `npm` 或 `Yarn Classic` 安装依赖项时，所有的包都被提升到模块目录的根目录。导致可以直接访问和修改依赖，而不是作为只读的项目依赖

- ## 推荐原因

  1. 好
    特点：
      1. 快速：`pnpm` 比其他包管理器快 `2` 倍
      2. 高效：`node_modules` 中的所有文件均克隆或硬链接自单一存储位置
      3. 支持 `Monorepos：pnpm` 内置了对单个源码仓库中包含多个软件包的支持
      4. 权限严格：`pnpm` 默认创建了一个非平铺的 `node_modules`，因此代码无法访问任意包
  2. `Vue` 生态项目的迁移
  3. `Vite` 支持 `pnpm`
  4. 大势所趋：所接触的大部分主要库都开始使用
  5. 更智能的包管理器
     - 更好的提示
     - 更智能的命令

- ### 功能对比

  ![功能对比](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/TechnologySharing/pnpm-function.png)

- ### 与其它包管理工具的比较

  *`Yarn PnP`：直接去掉 `node_modules`，将依赖包内容写在磁盘，节省了 `node` 文件 `I/O` 的开销，这样也能提升安装速度*
  ![s](https://pnpm.io/img/benchmarks/alotta-files.svg)

- ### `pnpm vs npm`

  - `npm` 的平铺目录结构
    从 `npm v3` 开始，`npm` 使用平铺的依赖结构。这可以减少磁盘空间占用，但却导致 `node_modules` 目录的混乱。
    另一方面，`pnpm` 通过使用硬链接和软链接到全局磁盘内容可寻址存储来管理 `node_modules`。这将为您带来减少磁盘空间使用的好处，同时还保持您的 `node_modules` 是干净的。

    `pnpm` 正确的 `node_modules` 结构的好处在于，它"有助于避免愚蠢的错误"，因为它让你无法使用不是 `package.json` 中指定的模块。

  - 安装
    `pnpm` 不允许安装 `package.json` 中没有包含的包。如果没有参数传递给 `pnpm add`，包将保存为常规依赖项。与 `npm` 一样，`--save-dev` 和 `--save-optional` 可以是用于安装包作为开发或可选的依赖。

    由于此限制，项目在使用 `pnpm` 时不会有任何无关的包，除非它们删除依赖项并将其保留为孤立的。这就是为什么 `pnpm` 的实现的 `prune command` 不允许你指定包来修剪 - 它总是去除所有多余的和孤儿包。

  - 目录依赖
    目录依赖以 `file:` 前缀开始，指向文件系统的目录。与 `npm` 一样，`pnpm` 软链接这些依赖项。与 `npm` 不同的是，`pnpm` 不执行这些文件依赖项的安装。

    这意味着如果您有一个名为 `foo (<root>/foo)` 的包，它有 `bar@file:../bar` 作为依赖项，则当你在 `foo` 上执行 `pnpm install` 时， `pnpm` 将不会为 `<root>/bar` 安装。

- ### 什么是 `Monorepo`？

  - `Monorepo` 是一个单一的存储库，包含多个不同的项目，和明确的关系
  - 与之对应的是传统的是 `Polyrepo` 模式，每个项目对应一个单独的仓库来分散管理

  为什么使用 `Monorepo`？`Polyrepo` 的弊端：
  1. 代码重复
    不同仓库之间的割裂感、导致复用代码的成本很高，开发体验差。或者需要在多个仓库中重复编写相同的代码，从而导致代码的不一致性和冗余性
  1. 版本管理
    有合适的版本管理方法，保证版本和代码的一致性
  1. 工具混乱
    每个项目都使用自己的一组命令来运行测试、构建、服务、检查、部署等
  1. 代码共享
    要跨存储库共享代码，可能会为共享代码创建一个存储库。那么必须设置工具和 `CI` 环境，将提交者添加到存储库，并设置包发布以便其他存储库可以依赖它
  1. 代码管理
    基于 `Polyrepo` 管理，每个开发人员的 `commit` 都会提交到各个仓库，不利于代码的 `CR`，也不利于管理代码合入机制，导致容易出现逃逸现象

  `Monorepo` 能解决什么问题？
  1. 代码复用：因为多个项目共享一个代码库，所以避免了在不同项目中重复编写相同功能代码的问题，提高了开发效率
  1. 提升协作效率：多个项目在同一个代码库中进行开发，可以方便地共享代码和文档，避免不同项目之间的沟通和协调成本
  1. 集中管理：不同的应用程序都在同一个代码库中，方便管理和监控。**这一点非常重要，特别是在需要同时对多个版本进行修改和维护的情况下**
  1. 统一构建：可以共用一套构建系统和工具链进行构建和部署，提升了构建的效率
  1. 可以快速定位问题：由于所有的代码都在同一个代码库中进行开发，`debugger` 可以很快找出问题所在的代码文件和行数，便于开发人员调试问题
  1. 一个版本：无需担心因为项目依赖于第三方库的冲突版本而导致的不兼容问题

- ### `Monorepo` 支持

  **[workspace 支持](https://pnpm.io/zh/workspaces)**
  Monorepo 的缺点：**Phantom dependencies（幽灵依赖）** 和 **NPM doppelgangers（NPM 分身）**
  ![Monorepo 的缺点](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/TechnologySharing/monorepo-defect.png)

  - **Phantom dependencies**
    即某个包没有被安装（`package.json` 中并没有，但是用户却能够引用到这个包）

    引发这个现象的原因一般是因为 `node_modules` 结构所导致的，例如使用 `yarn` 对项目安装依赖，依赖里面有个依赖叫做 `foo`，`foo` 这个依赖同时依赖了 `bar`，`yarn` 会对安装的 `node_modules` 做一个扁平化结构的处理（`npm v3` 之后也是这么做的），会把依赖在 `node_modules` 下打平，这样相当于 `foo` 和 `bar` 出现在同一层级下面。那么根据 `nodejs` 的寻径原理，用户能 `require` 到 `foo`，同样也能 `require` 到 `bar`。

    ```bash
    package.json -> foo(bar 为 foo 依赖)
    node_modules
      /foo
      /bar -> 幽灵依赖
    ```

    那么这里这个 `bar` 就成了一个幽灵依赖，如果某天某个版本的 `foo` 依赖不再依赖 `bar` 或者 `foo` 的版本发生了变化，那么 `require bar` 的模块部分就会抛错。

    这其实是个比较常见的现象，甚至有些包会直接去利用这种残缺的引入方式去减轻包体积。

    还有一种场景就是在 `lerna + yarn workspace` 的项目里面，因为 `yarn` 中提供了 `hoist` 机制（即一些底层子项目的依赖会被提升到顶层的 `node_modules` 中），这种 `phantom dependencies` 会更多，一些底层的子项目经常会去 `require` 一些在自己里面没有引入的依赖，而直接去找顶层 `node_modules` 的依赖（`nodejs` 这里的寻径是个递归上下的过程）并使用。

    而根据前面提到的 `pnpm` 的 `node_modules` 依赖结构，这种现象是显然不会发生的，因为被打平的依赖会被放到 `.pnpm` 这个虚拟磁盘目录下面去，用户通过 `require` 是根本找不到的。

    >值得一提的是，`pnpm` 本身其实也提供了将依赖提升并且按照 `yarn` 那种形式组织的 `node_modules` 结构的 `Option`，作者将其命名为 `--shamefully-hoist`，即 "羞耻的 `hoist`".....

  - **NPM doppelgangers**
    会导致有大量的依赖的被重复安装，举个例子:

    例如有个 `package`，下面依赖有 `lib_a、lib_b、lib_c、lib_d`，其中 `a` 和 `b` 依赖 `util_e@1.0.0`，而 `c` 和 `d` 依赖 `util_e@2.0.0`。

    那么早期 `npm` 的依赖结构应该是这样的:

    ```bash
    - package
    - package.json
    - node_modules
    - lib_a
      - node_modules <- util_e@1.0.0
    - lib_b
      - node_modules <- util_e@1.0.0
    _ lib_c
      - node_modules <- util_e@2.0.0
    - lib_d
      - node_modules <- util_e@2.0.0
    ```

    这样必然会导致很多依赖被重复安装，于是就有了 `hoist` 和打平依赖的操作:

    ```bash
    - package
    - package.json
    - node_modules
    - util_e@1.0.0
    - lib_a
    - lib_b
    _ lib_c
      - node_modules <- util_e@2.0.0
    - lib_d
      - node_modules <- util_e@2.0.0
    ```

    但是这样也只能提升一个依赖，如果两个依赖都提升了会导致冲突，这样同样会导致一些不同版本的依赖被重复安装多次，这里就会导致使用 `npm` 和 `yarn` 的性能损失。

    如果是 `pnpm` 的话，这里因为依赖始终都是存在 `store` 目录下的 `hard links`，一份不同的依赖始终都只会被安装一次，因此这个是能够被彻彻底底的消除的。

- ### 别名

  ```bash
  pnpm add lodash1@npm:lodash@1
  pnpm add lodash2@npm:lodash@2
  ```

- ### 命令行 `tab` 自动补全

  ```bash
  pnpm install-completion
  ```

- ## `node_modules` 结构

  ![pnpm node_modules 结构](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/TechnologySharing/pnpm-principle.webp)

  `node_modules` 结构的发展史
  - `npm2`：嵌套

    ```bash
    project
    └─ node_modules/express
       └─ lib
          └─ node_modules
             └─ accepts
                └─ node_modules
                   └─ mime-types
                      └─ node_modules/mime-db
                         └─ index.js
    ```

    问题：
    - 嵌套结构，依赖会被复制多次，占据更大的磁盘空间
    - 层级嵌套太深会超过 `windows` 的文件最长路径 `260` 多个字符

  - `yarn/npm3`：铺平

    ```bash
    project
    └─ node_modules
       └─ .bin
          accepts
          cookie
          ...
    ```

    问题：
    - 一个包是可能有多个版本的，提升只能提升一个，所以后面再遇到相同包的不同版本，依然还是用嵌套的方式
    - 幽灵依赖

  - `pnpm`
    例如在项目中使用 `pnpm` 安装了一个叫做 `express` 的依赖，那么最后会在 `node_modules` 中形成这样两个目录结构:

    ```bash
    project
    └─ node_modules
       └─ .pnpm
          └─ accepts@1.3.8
             bytes@3.1.2
             express@4.18.1/node_modules
             └─ accepts
                ...
             ...
          express
          ...
    ```

    优势：
    - 解决幽灵依赖
    - 安装快
    - 节省磁盘

- ## 存储&依赖管理
  
  - 硬链接（`hard link`）机制
    是一种在文件系统中创建一个指向现有文件的链接的方式。在这种情况下，将依赖项硬链接到 `node_modules` 文件夹中意味着这些依赖项可以被轻松地访问和使用，而不需要每次都从存储库中重新获取它们。这有助于提高项目的性能和可靠性。

  - 符合链接/软链接（`symlink/symbolic links/soft links`）机制
    是一种特殊的文件，它包含指向另一个文件或目录的路径。当访问符号链接时，系统会自动将其解析为实际的文件或目录路径。这是一种在文件系统中创建指向其他文件或目录的快捷方式的方法。符号链接经常用于简化文件路径，共享文件和目录，以及在不同的文件系统之间移动文件。

  - 使用符号链接的好处：
    - 它可以减少磁盘空间的使用，因为它允许多个文件共享相同的数据
    - 可以提高性能，因为它们允许快速访问文件，而不必复制整个文件。在这种情况下，使用符号链接来添加项目的依赖项可以减少模块目录的大小，并提高应用程序的启动时间。
  
  - 为什么要使用 `hard link`：
    - 允许包自己导入自己
    - 避免循环 `symlinks`

  - `pnpm` 是通过 `hard link` 在全局里面搞个 `store` 目录来存储 `node_modules` 依赖里面的 `hard link` 地址，然后在引用依赖的时候则是通过 `symlink` 去找到对应虚拟磁盘目录下（`.pnpm` 目录）的依赖地址

    这两者结合在一起工作之后，假如有一个项目依赖了 `bar@1.0.0` 和 `foo@1.0.0`，那么最后的 `node_modules` 结构呈现出来的依赖结构可能会是这样的:

    ```bash
    node_modules
    └── bar // symlink to .pnpm/bar@1.0.0/node_modules/bar
    └── foo // symlink to .pnpm/foo@1.0.0/node_modules/foo
    └── .pnpm
        ├── bar@1.0.0
        │   └── node_modules
        │       └── bar -> <store>/bar
        │           ├── index.js
        │           └── package.json
        └── foo@1.0.0
            └── node_modules
                └── foo -> <store>/foo
                    ├── index.js
                    └── package.json
    ```

    `node_modules` 中的 `bar` 和 `foo` 两个目录会软连接到 `.pnpm` 这个目录下的真实依赖中，而这些真实依赖则是通过 `hard link` 存储到全局的 `store` 目录中

  - 举个例子，例如项目里面有个 `1MB` 的依赖 `a`，在 `pnpm` 中，看上去这个 `a` 依赖同时占用了 `1MB` 的 `node_modules` 目录以及全局 `store` 目录 `1MB` 的空间（加起来是 `2MB`），但因为 `hard link` 的机制使得两个目录下相同的 `1MB` 空间能从两个不同位置进行寻址，因此实际上这个 `a` 依赖只用占用 `1MB` 的空间，而不是 `2MB`。
    - 因为这个机制，可以每次安装依赖的时候，如果是个相同的依赖，有好多项目都用到这个依赖，那么这个依赖实际上最优情况（即版本相同）只用安装一次。
    - 如果是 `npm` 或 `yarn`，那么这个依赖在多个项目中使用，在每次安装的时候都会被重新下载一次。
    - 提供了一个选项，使用方法为 `pnpm store prune`，它提供了一种用于删除一些不被全局项目所引用到的 `packages` 的功能。不被引用的包会被删除掉。
  
- ## 部分命令（特有，或少有）

  - `pnpm import`：从另一个软件包管理器的 `lock` 文件生成 `pnpm-lock.yaml`。支持的源文件：
    - `package-lock.json`
    - `npm-shrinkwrap.json`
    - `yarn.lock`
  - `pnpm prune`：移除不需要的 `packages`
  - `pnpm dlx`：从源中获取包而不将其安装为依赖项，热加载，并运行它公开的任何默认命令二进制文件
    `eg`：`pnpm dlx create-react-app ./my-app`
  - `pnpm env <cmd>`：管理 `Node.js` 环境
  - `pnpm store`：管理包存储
  - `pnpm doctor`：检查 `pnpm` 配置的已知常见问题

- ## 局限

  1. `npm-shrinkwrap.json` 和 `package-lock.json` 被忽略
    与 `pnpm` 不同，`npm` 可以多次安装相同的 `name@version`，并且具有不同的依赖项组合。`npm` 的锁文件旨在反映平铺的 `node_modules` 布局，但是，由于 `pnpm` 默认创建隔离布局，它无法由 `npm` 的锁文件格式反映出来。但是，如果您希望将锁定文件转换为 `pnpm` 的格式，请使用 `pnpm import`
  2. `Binstubs`（在 `node_modules/.bin` 中的文件）总是 `shell` 文件，而不是指向 `JS` 文件的符号链接
    创建 `shell` 文件是为了帮助支持插件的 `CLI` 的程序在特殊的 `node_modules` 结构中能够正确地找到它们的插件。这是很少有的问题，如果您希望文件是 `JS` 文件，请直接引用原始文件

- ## 安全
  
  `pnpm` 这种依赖管理的方式也很巧妙地规避了非法访问依赖的问题，也就是只要一个包未在 `package.json` 中声明依赖，那么在项目中是无法访问的

- ## 为什么 `Vite` 要从 `yarn` 迁移到 `pnpm`？

  因为底层使用 `esbuild` 打包，而在 `v0.13` 之后使用了 `optionalDependencies` 来安装某些不同平台的依赖，而 `yarn 1/2` 并不会根据对应的 `optional` 规则去下载对应平台的包而是会去选择下载所有的包

- ## [一些常见的问题](https://pnpm.io/zh/faq)

*参考资料:*
[1] [pnpm 官方文档](https://pnpm.io/)
[2] [JavaScript package managers compared: npm, Yarn, or pnpm?](https://blog.logrocket.com/javascript-package-managers-compared/)
[3] [关于现代包管理器的深度思考——为什么现在我更推荐 pnpm 而不是 npm/yarn?](https://juejin.cn/post/6932046455733485575)