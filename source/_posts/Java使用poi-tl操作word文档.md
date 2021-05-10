---
title: Java使用poi-tl操作word文档
cover: 'https://static.ivan.fun/blog/javapoi.jpg'
index_img: 'https://static.ivan.fun/blog/javapoi.jpg'
tags:
  - Java
categories: '-Java'
abbrlink: 513676f1
date: 2020-10-09 10:30:00
---
## Java 操作Word、Excel的几种方法

方案|移植性|功能性|易用性
:--|:--|:--|:--
*Poi-tl* |Java跨平台|Word模板引擎|基于Apache POI
Apache POI|Java跨平台|Apache项目，功能丰富|文档不全，这里有一个教程：[Apache POI Word快速入门](http://deepoove.com/poi-tl/apache-poi-guide.html)
Freemarker|XML跨平台|仅支持文本，很大的局限性|复杂，需要维护XML结构，代码不可维护
OpenOffice|部署OpenOffice软件，移植性较差|-|复杂，需要了解OpenOffice的API
HTML浏览器导出|依赖浏览器的实现，移植性较差|HTML不能很好的兼容Word的格式|-
Jacob、winlib|Windows平台|-|复杂，完全不推荐使用

>Apache POI不仅在上层封装了易用的文档API(文本、图片、表格、页眉、页脚、图表等)，也可以在底层直接操作文档XML结构，poi-tl正是一个基于Apache POI的Word模板引擎，并且拥有着让人喜悦的特性。

引擎功能|描述
:--|:--
<i class="fa fa-check-circle"></i>文本|将标签渲染为文本
<i class="fa fa-check-circle"></i>图片|将标签渲染为图片
<i class="fa fa-check-circle"></i>表格|将标签渲染为表格
<i class="fa fa-check-circle"></i>列表|将标签渲染为列表
<i class="fa fa-check-circle"></i>图表|条形图（3D条形图）、柱形图（3D柱形图）、面积图（3D面积图）、折线图（3D折线图）、雷达图、饼图（3D饼图）等图表数据渲染

## [Poi-tl 文档地址](http://deepoove.com/poi-tl/)

<head> 
    <script defer src="https://use.fontawesome.com/releases/v5.0.13/js/all.js"></script> 
    <script defer src="https://use.fontawesome.com/releases/v5.0.13/js/v4-shims.js"></script> 
</head> 
<link rel="stylesheet" href="https://use.fontawesome.com/releases/v5.0.13/css/all.css">