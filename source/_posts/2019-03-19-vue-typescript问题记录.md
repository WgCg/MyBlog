---
title: vue+typescript问题记录
tags: [vue, vue-cli, typescript]
toc: true
mathjax: true
date: 2019-03-19 21:14:42
categories:
- 编程语言
- typescript
- 问题积累
---

# 文件包含import或export即为模块

## 问题描述

<img width="500" src="/assets/typescript/problems/1.jpg" title="shims-vue.d.ts" alt="shims-vue.d.ts"/>

因为需要在Vue构造函数下追加一些属性而又想把所有Vue相关的类型声明放在一个文件里，所以就直接在shims-vue.d.ts文件中加入了额外的声明，结果发现引入.vue文件时，提示无法找到该模块。

<img width="500" src="/assets/typescript/problems/2.jpg" title="route/index.ts" alt="route/index.ts"/>

## 原因

产生这个问题的原因是因为在ts中，只要最顶层中含有import或者export语法，该文件即为一个模块，如果没有，则声明默认定义在全局作用域下。正因为我们在shims-vue.d.ts中用了import的语法，导致该文件变成了一个模块，所以就导致*.vue的模块声明语句没有在全局作用域中生效。

## 解决方案

需要声明在全局作用域下的模块单独放在一个文件，在本案例中，把module '*.vue'的声明单独放在shims-vue.d.ts中，其它不需要声明在全局作用域下的，例如本案例中扩展Vue构造函数的声明放在另一个文件即可，如下所示：

<img width="500" src="/assets/typescript/problems/3.jpg" title="shims-vue.d.ts" alt="shims-vue.d.ts"/>

<img width="500" src="/assets/typescript/problems/4.png" title="vue-extend.d.ts" alt="vue-extend.d.ts"/>