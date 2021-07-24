# 关于官网SPA做SEO的调研

背景：由于 `SPA` (单页应用程序 (`Single-Page Application`)) 返回的是大量 `JavaScript` 文件，且其中存在大量 `Dom` 操作，对于 `SEO` 的解析很不友好

1. `SSR` 服务器渲染

    - 技术：[`Vue`](https://cn.vuejs.org/v2/guide/) + [`Vue-Router(history)`](https://router.vuejs.org/zh/guide/essentials/history-mode.html) + [`NuxtJS`](https://github.com/nuxt/nuxt.js) || [`React`](https://reactjs.org/) + [`React-Router`](https://reactrouter.com/) + [`Next.JS`](https://nextjs.org/)
    - 原理：将服务器渲染后的页面返回给爬虫
    - 优点：
      - 更好的 `SEO`，由于搜索引擎爬虫抓取工具可以直接查看完全渲染的页面；
      - 更快的内容到达时间 (`time-to-content`)，特别是对于缓慢的网络情况或运行缓慢的设备。
    - 缺点：
      - 开发条件所限，浏览器特定的代码，只能在某些生命周期钩子函数 (`lifecycle hook`) 中使用；一些外部扩展库 (`external library`) 可能需要特殊处理，才能在服务器渲染应用程序中运行；
      - 环境和部署要求更高，需要 `Node.js server` 运行环境；
      - 高流量的情况下，请准备相应的服务器负载，并明智地采用缓存策略。
      - 环境问题，代码可能需要大量改动
      - 服务器压力大
    - 适用场景：公司官网，功能、交互简单的产品
    - 相关文章：
      - <https://ssr.vuejs.org/zh/>
      - <https://zhuanlan.zhihu.com/p/95294219?from_voters_page=true>
2. 静态化([`JAMStack`](https://jamstack.wtf/) = `JavaScript + APIs + Markup`)

    - 技术：[`Vue`](https://cn.vuejs.org/v2/guide/) + [`NuxtJS`](https://github.com/nuxt/nuxt.js) || [`React`](https://reactjs.org/) + [`Next.JS`](https://nextjs.org/)
    - 原理：将 `SPA` 拆分成多个静态文件，根据不同请求返回不同静态文件
    - 优点：
      - 纯静态文件，访问速度超快；
      - 对比 `SSR`，不涉及到服务器负载方面问题；
      - 静态网页不宜遭到黑客攻击，安全性更高。
      - 高性能
      - 易部署
      - 易开发
    - 缺点：
      - 不支持 [`Vue-Router`](https://router.vuejs.org/)
      - 需要生成大量静态文件，占据空间
      - 需要 `CDN，Money`
      - 不支持太多动态功能
      - 对内容编辑器不友好
      - 更新 = `coding`
      - 处理数据需要更的对工作
      - 高度依赖第三方系统(生死不由己)
    - 适用场景：公司官网，功能、交互简单的产品，对数据少的页面
    - 相关文章：
      - <https://www.nuxtjs.cn/api/configuration-generate>
3. 预渲染 [`prerender-spa-plugin`](https://github.com/chrisvfritz/prerender-spa-plugin), [`Prerenders(付费)`](https://github.com/chrisvfritz/prerender-spa-plugin), [`react-snap`](https://github.com/stereobooster/react-snap), [`snapshotify`](https://github.com/errorception/snapshotify), [`presite`](https://github.com/egoist/presite), [`prerenderer`](https://github.com/JoshTheDerf/prerenderer)

    - 技术：[`Vue`](https://cn.vuejs.org/v2/guide/) + [`prerender-spa-plugin`](https://github.com/chrisvfritz/prerender-spa-plugin) + [`html5-histroy`](https://developer.mozilla.org/en-US/docs/Web/API/History)
    - 原理：在 [`Webpack`](https://webpack.js.org/) 构建阶段的最后，在本地启动一个 [`Puppeteer`](https://github.com/puppeteer/puppeteer) 的服务，访问配置了预渲染的路由，然后将 [`Puppeteer`](https://github.com/puppeteer/puppeteer) 中渲染的页面输出到 `HTML` 文件中，并建立路由对应的目录
    - 优点：
      - 改动小，只需引入插件配置即可
      - 纯静态文件，访问速度超快；
    - 缺点：
      - 不支持 [`Vue-Router`](https://router.vuejs.org/)
      - 不适用于根据特定用户查看内容或其它动态源的内容
      - 打包耗时，不适用于页面过多的情况
    - 适用场景：公司官网，功能、交互简单的产品，对数据少的页面，路由较短的页面
    - 相关文章：
      - <https://github.com/chrisvfritz/prerender-spa-plugin>
4. 使用 [`Puppeteer`](https://github.com/puppeteer/puppeteer) ([`Phantomjs`](https://github.com/ariya/phantomjs) 问题较多，不选) 针对爬虫做处理

    - 技术：[`Vue`](https://cn.vuejs.org/v2/guide/) + [`NodeJS`](https://nodejs.org/dist/latest-v14.x/docs/api/) + [`Puppeteer`](https://github.com/puppeteer/puppeteer)
    - 原理：经过 [`Nginx`](https://www.nginx.com/) 使用 [`user-agent`](https://developer.mozilla.org/zh-CN/docs/Web/HTTP/Headers/User-Agent) 判断请求是否为爬虫，如果是，使用 [`Puppeteer`](https://github.com/puppeteer/puppeteer) 渲染页面返回给爬虫，如不是，正常返回 `JS`
    - 优点：
      - 无需修改已有 `SPA` 代码
      - 速度快
      - 安全高
      - 稳定好
      - 操作简单
    - 缺点：
      - 捆绑 [`Chromium`](https://www.chromium.org/) (默认最新)
      - 对 [`NodeJS`](https://nodejs.org/dist/latest-v14.x/docs/api/) 版本要求较高
      - 跨浏览器不支持
      - 由于 [`Chromium`](https://www.chromium.org/) 的限制，对音频/视频支持不符合预期，视频播放，屏幕截图很可能会失败
    - 适用场景：除去音频/视频页面
    - 相关文章：
      - <https://zhuanlan.zhihu.com/p/76237595>
