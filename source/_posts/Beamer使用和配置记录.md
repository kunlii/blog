---
title: Beamer使用和配置记录
date: 2024-12-09 17:20:06
tags:
  - 教程
categories: 编辑器配置
---
## Beamer的使用
这部分网上教程太多了，因此先略过

## Beamer的配置
在这部分中，我们关注如何自定义Beamer的样式，从而制作出满足个人需求的模板。

每个beamer主题都由以下几部分构成：
```latex
\usefonttheme{}
\usecolortheme{}
\useinnertheme{}
\useoutertheme{}
```

其中，`outertheme`控制普通页面（`frame`）的结构和框架，包括导航栏、侧边栏、标题栏等，`innertheme`用来调整标题页，小节页的组成，也会用来控制普通页面内一些特殊结构的样式（比如脚注、列表、表格等），而` colortheme`和`fonttheme` 就是整体模板的配色与字体。