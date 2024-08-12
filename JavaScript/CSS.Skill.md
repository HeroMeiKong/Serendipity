# CSS 小技巧 笔记

★★★★★★★★★★

更新于2024-08-12
这是自己平时书写的笔记，有很多漏洞，也有很多错误，还望大家多多指正，感谢🙏

★★★★★★★★★★

1. 如何使用纯 `CSS` 实现电梯导航？

   需要以下几种属性及方法：
   - `a` 标签的锚定
   - [timeline-scope](https://developer.mozilla.org/en-US/docs/Web/CSS/timeline-scope)
   - [animation-timeline](https://developer.mozilla.org/en-US/docs/Web/CSS/animation-timeline)
   - [view-timeline-name](https://developer.mozilla.org/en-US/docs/Web/CSS/view-timeline-name)
   - [view-timeline-inset](https://developer.mozilla.org/en-US/docs/Web/CSS/view-timeline-inset)

   缺陷：
   - 自由度不高
   - 对每个锚定内容高度有限制，view-timeline-inset 不好控制激活空间

更新于2024-08-12，以上皆为日常笔记，如有问题还望大家多多指正！
