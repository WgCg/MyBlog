---
title: 深入理解CSS：字体度量、line-height和vertical-align
tags: [CSS, 字体度量, line-height, vertical-align]
toc: true
mathjax: true
date: 2019-03-08 14:42:32
categories:
- 编程语言
- CSS
---

# 参考文档

原文：

[Deep dive CSS: font metrics, line-height and vertical-align - Vincent De Oliveira](http://link.zhihu.com/?target=http%3A//iamvdo.me/en/blog/css-font-metrics-line-height-and-vertical-align)

译文：

[深入理解 CSS：字体度量、line-height 和 vertical-align](https://zhuanlan.zhihu.com/p/25808995)

[深入研究CSS字体度量及CSS 盒子](https://juejin.im/entry/5acdc54c51882555867fc7fd)

## 总结

### 概述

line-height, vertical-align涉及到IFC(内联格式化上下文)

line-height取值可以为数字或px，数字表示相对于font-size的倍数，默认值为normal，normal取值为1-1.2之间

### font-size

不同字体，设置相同的font-size，其在页面上所占据的高度是不一样的，原因在于字体本身

字体的定义规则：

* 字母的高度被称为“em”，在数字化字体中 em 是空间的数字化定义总量。em的大小（以下均写为: EM size）通常是 1000 单位，在 TrueType 字体中，EM size 约定是2的幂，通常是1024或2048。
* 根据其实际使用的单位，字体的度量可以根据一些设置来决定。注意，有些值是em-square之外的值。
* 在浏览器中，相对单位是用于缩放用来适应所需的 font-size

字体的设置：

* 一款字体会定义一个[em-square](http://designwithfontforge.com/zh-CN/The_EM_Square.html)：在字体设计中一个字符所在的空间容器（也被称作“EM size”或者“UPM”）），一般被设定为宽高均为1000的相对单位，也可是1024、2048相对单位
* 字体度量：

  <img src="/assets/css/1.png" width="800"/>

  * baseline (基线): 分隔 ascent 和 descent ，默认字符底端沿 baseline 排列，如图中的P，x，Ё(为俄文字符)
  * ascent (上升): 基线的上部分，字符最高处与 ascent 顶端可能有空白，由 font-family 决定
  * descent (下降): 基线的下部分，字符最低处与 descent 底端可能有空白，由 font-family 决定
  * xHeight (X 字高): 小写字符 x 的高度，由 font-family 决定
  * capHeight (顶面高度): 大些字符 P 的高度，由 font-family 决定
  * lineSpacing (行间距): 在浏览器中一般 lineSpacing = ascent + descent
  * lineHeight （行高）: 默认等于 lineSpacing，受 line-height 设置影响，如果设置 line-height，lineHeight 等于 line-height。
  * half-leading (半行距): 如果lineHeight > lineSpacing，则lineHeight 与 lineSpacing 之间会产生上下相等的空隙 (lineHeight - lineSpacing)    /2 称为半行距（half-leading或 half lead strips）。