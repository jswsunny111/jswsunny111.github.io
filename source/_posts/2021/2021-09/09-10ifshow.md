---
title: v-if 和 v-show
date: 2021-09-10 22:30:28
tags: Vue
category: Vue
---

### 相同点/不同点

-   相同点：相同使用方式，值为 true 时，满足条件元素显示，反则不显示。
-   不同点：1. v-if 根据值在 DOM 中生成或移除一个元素。v-show 根据值来显示或者隐藏 HTML 元素。当 v-show 赋值为 false 时，元素被隐藏，此时查看代码时，该元素上会多一个内联样式 style=“display:none” 2.渲染元素的开销不同，v-if 有更高的切换消耗，而 v-show 初始渲染消耗较高。频繁切换，则使用 v-show 更好。

![BG图片](/img/1.jpg)
