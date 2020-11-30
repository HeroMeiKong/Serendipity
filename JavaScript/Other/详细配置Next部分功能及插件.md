# 详细配置Next部分功能及插件

本篇文章只介绍配置Next主题，如若不是这个主题便可不用浏览！！！

***[站点配置文件]()为博客目录下的[_config.yml]()文件，[主题配置文件]()为主题目录下的[_config.yml]()文件！！！***

1. 首先进入Next主页：[https://github.com/iissnan/hexo-theme-next](https://github.com/iissnan/hexo-theme-next "null")
2. 复制它的 SSH 地址或 HTTPS 地址，假设地址为 `git@github.com:iissnan/hexo-theme-next.git`
3. `cd themes`
4. `git clone git@github.com:iissnan/hexo-theme-next.git`
5. `cd ..`
6. 将 `_config.yml` 的第 75 行改为 `theme: hexo-theme-next，`保存
7. `hexo generate`
8. `hexo deploy`
9. 先修改Schenme,我选择的是：
![Schenme.png](http://upload-images.jianshu.io/upload_images/5999132-2b4c383413feb7a5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
10. 设置菜单，编辑主题配置文件：（可任意修改）
![menu.png](http://upload-images.jianshu.io/upload_images/5999132-d08862a413ce7b35.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
11. 设置菜单项的图标：默认就好，图标也可修改为其他，严格区分大小写！！！
12. 修改侧栏sidebar的位置：`position: left`
13. 设置侧栏显示的时机：`display: always`
14. 设置头像，将头像图片放在 `next\source\images` 目录下：`avatar: /images/1.jpg`
15. 设置RSS：`npm install hexo-generator-feed --save`
16. 设置代码高亮主题：`highlight_theme: night eighties`
17. 设置侧边栏社交连接：
![social.png](http://upload-images.jianshu.io/upload_images/5999132-84fff2600ee27adb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
设置连接的图标：
![social_icons.png](http://upload-images.jianshu.io/upload_images/5999132-6b9a8c90709da481.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
18. 开启打赏功能：
![reward_comment.png](http://upload-images.jianshu.io/upload_images/5999132-cd5f53ae3be5e41d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
19. 设置站点建立时间：`since: 2018`
20. 设置动画效果：`motion.enable: true`
21. 开启动画：`canvas_nest: true, three_waves: true`

## 配置搜索服务

### LocalSearch

安装：
>npm install hexo-generator-searchdb --save

在站点配置文件新增如下：
![search.png](http://upload-images.jianshu.io/upload_images/5999132-f43772ad75014e30.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
在主题配置文件新增：
![local_search.png](http://upload-images.jianshu.io/upload_images/5999132-48c8b433ffda2c76.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

#### Algolia搜索

1. 登录[Algolia官网](https://www.algolia.com/)，注册账号，在indices中新添加一个index，名字可以随意取，但是后面需要用到例如personal_blog.
2. 在ApiKey中找到Search-Only API Key，再点击ALL API KEYS找到对应的Key并将personal_blog添加进去，在ACL中勾选如下，点击更新：
![ACL.png](http://upload-images.jianshu.io/upload_images/5999132-33c0996fd2b3e81d.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 修改站点配置文件，添加内容如下，在红线处填入你的Algolia中的Key，apikey即为Search-Only API Key：
![Algolia.png](http://upload-images.jianshu.io/upload_images/5999132-252dfadd66ed1de6.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
修改主题配置文件： `algolia_search: enable: true`
4. 安装插件：`npm install --save hexo-algolia`
5. 配置环境变量： `export HEXO_ALGOLIA_INDEXING_KEY=你的API Key`
注意：运行此命令时，切记是在git bash中运行代码，若你不是用到git bash运行，请另行参考。此处的API Key=Search-Only API Key=站点配置重点apiKey。
6. 更新index： `hexo algolia`
7. 若出现以下画面即为成功：
![success.png](http://upload-images.jianshu.io/upload_images/5999132-3870537e6625fc42.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
8. 若出现Algolia搜索栏但是无法跳转到搜索结果，请运行以下代码： `npm install hexo-algolia@0.2.0`

然后在站点配置中找到 `package.json` ，把里面的hexo-algolia， 換成 `"hexo-algolia": "^0.2.0"`，如图：
![hexo-algolia.png](http://upload-images.jianshu.io/upload_images/5999132-a343e8a0df3b0df5.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由此，配置完成。

#### 来必力评论

1. 登录[来必力官网](https://livere.com/),注册，选择安装版本，选择个人版
2. 获取data-uid：
![data-uid.png](http://upload-images.jianshu.io/upload_images/5999132-9767b8c90a4f4d67.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
3. 修改主题配置文件：
![livere_uid.png](http://upload-images.jianshu.io/upload_images/5999132-135db711ef840545.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
由此配置完成。

#### 其他配置将以后更新！谢谢浏览！

### 参考文章

[NexT使用文档](http://theme-next.iissnan.com/getting-started.html)

[邻家枫扬在知乎的回答](https://www.zhihu.com/question/46822587/answer/109403261)

[大都小酒馆](https://www.cnblogs.com/doublezcc/p/6140788.html)

[5.1.0使用algolia搜索问题](https://github.com/iissnan/hexo-theme-next/issues/1084)

[1022_zhang's Blog](https://1022-zhang.github.io/personal-blog-advanced.html)

### 纯属个人简单操作，望大佬些细心指出错误或不足之处！！！