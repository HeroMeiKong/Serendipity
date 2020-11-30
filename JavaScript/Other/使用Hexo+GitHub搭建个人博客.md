# 使用Hexo+GitHub搭建个人博客

1. 选择一个目录（最好不要在桌面）
2. 在 GitHub 上新建一个空仓库，仓库名称是「你的用户名.github.io」（请将你的用户名替换成真正的用户名）
3. 安装Hexo: `npm install -g hexo-cli`
4. 创建自己的博客文件夹：`hexo init MyBlog`
5. 切换到自己的博客文件夹：`cd MyBlog`
6. 安装 package.json 中记录的模块：`npm i`
7. 创建一个(测试)博客：`hexo new test`
  ![test.png](http://upload-images.jianshu.io/upload_images/5999132-201c50fcaf5667fb.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

8. 编辑这个文件，可在上图所示的路径中打开文件编辑。即在MyBlog\source\_posts\目录下，若用指令：`start source/_posts/test.md`
注意：路径的\和/区别，Windows下为\，而bash下为/，千万不要弄错了！！！
9. 编辑站点配置：`start _config.yml`
    - 把第 6 行的 title 改成你想要的名字
    - 把第 9 行的 author 改成你的大名
    - 把第 10 行的 language 改为：language: zh-Hans（即为中文简体）
    - 把最后一行的 type 改成 type: git
    - 在最后一行后面新增一行，左边与 type 平齐，加上一行 repo: 仓库地址 （请将仓库地址改为「你的用户名.github.io」对应的仓库地址，仓库地址以 git@github.com: 开头你知道吧？不知道？不知道的话现在你知道了）
    - 第 4 步的 repo: 后面有个空格，不要眼瞎。

10. 安装 git 部署插件: `npm install hexo-deployer-git --save`
11. 部署hexo：`hexo deploy`
12. 打开仓库的 GitHub Pages 功能，即可看到自己的博客页！
13. 创建第二篇博客：`hexo new 第二篇博客`
14. 编辑第二篇博客
15. hexo generate
16. hexo deploy
如果你想换个主题可看下一篇文章：[详细配置Next部分功能及插件](https://www.jianshu.com/p/00f4bc425249)

## 参考文章

[NexT使用文档](http://theme-next.iissnan.com/getting-started.html)

[邻家枫扬在知乎的回答](https://www.zhihu.com/question/46822587/answer/109403261)

[大都小酒馆](https://www.cnblogs.com/doublezcc/p/6140788.html)

[5.1.0使用algolia搜索问题](https://github.com/iissnan/hexo-theme-next/issues/1084)

[1022_zhang's Blog](https://1022-zhang.github.io/personal-blog-advanced.html)

### 纯属个人简单操作，望大佬些细心指出错误或不足之处！！！