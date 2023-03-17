# Vite 问题总结及解决方案
1. ## 关于 Vite3.x 中 globEager 已弃用以及 TS 报错问题
    `const modules = import.meta.globEager('./zh-CN/**/*.ts');`
    遇到问题：globEager 已弃用问题
    ![importGlob](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/Vite/globEager.png)
    找到原因：
    ![importGlob](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/Vite/importGlob.png)
    修复：`const modules = import.meta.glob('./zh-CN/**/*.ts');`
    如果还有错误提示：
    ![importGlob-import](https://raw.githubusercontent.com/HeroMeiKong/Images/main/Serendipity/JavaScript/Vite/importGlob-import.png)
    `const modules = import.meta.glob('./zh-CN/**/*.ts', { eager: true, import: 'default'});`
