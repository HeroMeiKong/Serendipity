# 为什么我推荐使用 pnpm 作为包管理工具？

## 目录

- [什么是 `pnpm`？]()
- 推荐原因
  - 与其它包管理工具的比较
  - 什么是 `Monorepo`？
  - `Monorepo` 支持
- 存储管理
- 依赖管理
- 安全
- 日常使用指南

- ## 什么是 `pnpm`？

  简介：速度快、节省磁盘空间的**软件包管理器**
  含义：`pnpm = performant npm`
  初衷：
  - 节省磁盘空间
  使用 npm 时，依赖每次被不同的项目使用，都会重复安装一次。  而在使用 pnpm 时，依赖会被存储在内容可寻址的存储中，所以：

  - 如果你用到了某依赖项的不同版本，只会将不同版本间有差异的文件添加到仓库。 例如，如果某个包有100个文件，而它的新版本只改变了其中1个文件。那么 pnpm update 时只会向存储中心额外添加1个新文件，而不会因为仅仅一个文件的改变复制整新版本包的内容。
  所有文件都会存储在硬盘上的某一位置。 当软件包被被安装时，包里的文件会硬链接到这一位置，而不会占用额外的磁盘空间。 这允许你跨项目地共享同一版本的依赖。
  因此，您在磁盘上节省了大量空间，这与项目和依赖项的数量成正比，并且安装速度要快得多！
  - 提高安装速度
  - 创建一个非扁平的 `node_modules` 目录

- ## 推荐原因

  1. 好
    特点：
      1. 快速：`pnpm` 比其他包管理器快 `2` 倍
      2. 高效：`node_modules` 中的所有文件均克隆或硬链接自单一存储位置
      3. 支持 `Monorepos：pnpm` 内置了对单个源码仓库中包含多个软件包的支持
      4. 权限严格：pnpm 默认创建了一个非平铺的 `node_modules`，因此代码无法访问任意包
  2. `Vue` 生态项目的迁移
  3. `Vite` 支持 `pnpm`
  4. 大势所趋：所接触的大部分主要库都开始使用
  ![使用 pnpm 的库]()

- ### 与其它包管理工具的比较

  ![s](https://pnpm.io/img/benchmarks/alotta-files.svg)

- ### 什么是 Monorepo？

  Monorepo 是一个单一的存储库，包含多个不同的项目，和明确的关系。
  与之对应的是传统的是 Polyrepo 模式，每个项目对应一个单独的仓库来分散管理。
  为什么使用 Monorepo？Polyrepo 的弊端：
  1. 代码重复
    不同仓库之间的割裂感、导致复用代码的成本很高，开发体验差。或者需要在多个仓库中重复编写相同的代码，从而导致代码的不一致性和冗余性
  1. 版本管理
    有合适的版本管理方法，保证版本和代码的一致性
  1. 工具混乱
    每个项目都使用自己的一组命令来运行测试、构建、服务、检查、部署等
  1. 代码共享
    要跨存储库共享代码，可能会为共享代码创建一个存储库。那么必须设置工具和 CI 环境，将提交者添加到存储库，并设置包发布以便其他存储库可以依赖它
  1. 代码管理
    基于 Polyrepo 管理，每个开发人员的 commit 都会提交到各个仓库，不利于代码的 CR，也不利于管理代码合入机制，导致容易出现逃逸现象
  Monorepo 能解决什么问题？
  1. 代码复用：因为多个项目共享一个代码库，所以避免了在不同项目中重复编写相同功能代码的问题，提高了开发效率
  1. 提升协作效率：多个项目在同一个代码库中进行开发，可以方便地共享代码和文档，避免不同项目之间的沟通和协调成本
  1. 集中管理：不同的应用程序都在同一个代码库中，方便管理和监控。这一点非常重要，特别是在需要同时对多个版本进行修改和维护的情况下
  1. 统一构建：可以共用一套构建系统和工具链进行构建和部署，提升了构建的效率
  1. 可以快速定位问题：由于所有的代码都在同一个代码库中进行开发，debugger 可以很快找出问题所在的代码文件和行数，便于开发人员调试问题
  1. 一个版本：无需担心因为项目依赖于第三方库的冲突版本而导致的不兼容问题

- ### Monorepo 支持

  **[workspace 支持](https://pnpm.io/zh/workspaces)**
  Monorepo 的缺点：**Phantom dependencies（幽灵依赖）** 和 **NPM doppelgangers（NPM 分身）**
  **Phantom dependencies**
  即某个包没有被安装（package.json 中并没有，但是用户却能够引用到这个包）

  引发这个现象的原因一般是因为 node_modules 结构所导致的，例如使用 yarn 对项目安装依赖，依赖里面有个依赖叫做 foo，foo 这个依赖同时依赖了 bar，yarn 会对安装的 node_modules 做一个扁平化结构的处理(npm v3 之后也是这么做的)，会把依赖在 node_modules 下打平，这样相当于 foo 和 bar 出现在同一层级下面。那么根据 nodejs 的寻径原理，用户能 require 到 foo，同样也能 require 到 bar。

  ```bash
  package.json -> foo(bar 为 foo 依赖)
  node_modules
    /foo
    /bar -> 幽灵依赖
  ```

  那么这里这个 bar 就成了一个幽灵依赖，如果某天某个版本的 foo 依赖不再依赖 bar 或者 foo 的版本发生了变化，那么 require bar 的模块部分就会抛错。

  以上其实只是一个简单的例子，但是根据笔者在字节内部见到的一些 monorepo(主要为 lerna + yarn )项目中，这其实是个比较常见的现象，甚至有些包会直接去利用这种残缺的引入方式去减轻包体积。

  还有一种场景就是在 lerna + yarn workspace 的项目里面，因为 yarn 中提供了 hoist 机制(即一些底层子项目的依赖会被提升到顶层的 node_modules 中)，这种 phantom dependencies 会更多，一些底层的子项目经常会去 require 一些在自己里面没有引入的依赖，而直接去找顶层 node_modules 的依赖(nodejs 这里的寻径是个递归上下的过程)并使用。

  而根据前面提到的 pnpm 的 node_modules 依赖结构，这种现象是显然不会发生的，因为被打平的依赖会被放到 .pnpm 这个虚拟磁盘目录下面去，用户通过 require 是根本找不到的。

  >值得一提的是，pnpm 本身其实也提供了将依赖提升并且按照 yarn 那种形式组织的 node_modules 结构的 Option，作者将其命名为 --shamefully-hoist ，即 "羞耻的 hoist".....

  **NPM doppelgangers**
  会导致有大量的依赖的被重复安装，举个例子:

  例如有个 package，下面依赖有 lib_a、lib_b、lib_c、lib_d，其中 a 和 b 依赖 util_e@1.0.0，而 c 和 d 依赖 util_e@2.0.0。

  那么早期 npm 的依赖结构应该是这样的:

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

  这样必然会导致很多依赖被重复安装，于是就有了 hoist 和打平依赖的操作:

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

  但是这样也只能提升一个依赖，如果两个依赖都提升了会导致冲突，这样同样会导致一些不同版本的依赖被重复安装多次，这里就会导致使用 npm 和 yarn 的性能损失。

  如果是 pnpm 的话，这里因为依赖始终都是存在 store 目录下的 hard links ，一份不同的依赖始终都只会被安装一次，因此这个是能够被彻彻底底的消除的。

- ## 存储管理
  
  - `symlink` 和 `hard link` 机制

  在前面知道了 `pnpm` 是通过 `hardlink` 在全局里面搞个 `store` 目录来存储 `node_modules` 依赖里面的 `hard link` 地址，然后在引用依赖的时候则是通过 `symlink` 去找到对应虚拟磁盘目录下（`.pnpm` 目录）的依赖地址

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

- ## 依赖管理

  安装依赖方面的优化：使用 Hard link 的机制，hard link 使得用户可以通过不同的路径引用方式去找到某个文件。pnpm 会在全局的 store 目录里存储项目 node_modules 文件的 hard links。
  举个例子，例如项目里面有个 1MB 的依赖 a，在 pnpm 中，看上去这个 a 依赖同时占用了 1MB 的 node_modules 目录以及全局 store 目录 1MB 的空间(加起来是 2MB)，但因为 hard link 的机制使得两个目录下相同的 1MB 空间能从两个不同位置进行寻址，因此实际上这个 a 依赖只用占用 1MB 的空间，而不是 2MB。
  因为这个机制，可以每次安装依赖的时候，如果是个相同的依赖，有好多项目都用到这个依赖，那么这个依赖实际上最优情况（即版本相同）只用安装一次。
  如果是 npm 或 yarn，那么这个依赖在多个项目中使用，在每次安装的时候都会被重新下载一次。
  提供了一个选项，使用方法为 pnpm store prune ，它提供了一种用于删除一些不被全局项目所引用到的 packages 的功能。不被引用的包会被删除掉。

- ## node_modules 结构

  node_modules 结构的发展史
  - npm2：嵌套

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
    - 层级嵌套太深会超过 windows 的文件最长路径 260 多个字符

  - yarn/npm3：铺平

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

  - pnpm
    例如在项目中使用 pnpm 安装了一个叫做 express 的依赖，那么最后会在 node_modules 中形成这样两个目录结构:

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

    其中第一个路径是 nodejs 正常寻找路径会去找的一个目录，如果去查看这个目录下的内容，会发现里面连个 node_modules 文件都没有，实际上这个文件只是个软连接，它会形成一个到第二个目录的一个软连接(类似于软件的快捷方式)，这样 node 在找路径的时候，最终会找到 .pnpm 这个目录下的内容。
    其中这个 .pnpm 是个虚拟磁盘目录，然后 express 这个依赖的一些依赖会被平铺到 .pnpm/express@4.17.1/node_modules/ 这个目录下面，这样保证了依赖能够 require 到，同时也不会形成很深的依赖层级。
    在保证了 nodejs 能找到依赖路径的基础上，同时也很大程度上保证了依赖能很好的被放在一起。
    pnpm 对于不同版本的依赖有着极其严格的区分要求，如果项目中某个依赖实际上依赖的 peerDeps 出现了具体版本上的不同，对于这样的依赖会在虚拟磁盘目录 .pnpm 有一个比较严格的区分，具体可以参考: https://pnpm.io/how-peers-are-resolved 这篇文章。
    综合而言，本质上 pnpm 的 node_modules 结构是个网状 + 平铺的目录结构。这种依赖结构主要基于软连接(即 symlink)的方式来完成。

    优势：
    - 解决幽灵依赖
    - 安装快
    - 节省磁盘

- ## 为什么 Vite 要从 yarn 迁移到 pnpm？

  因为底层使用 esbuild 打包，而在 v0.13 之后使用了 optionalDependencies 来安装某些不同平台的依赖，而 yarn 1/2 并不会根据对应的 optional 规则去下载对应平台的包而是会去选择下载所有的包

- ## [一些常见的问题](https://pnpm.io/zh/faq)
