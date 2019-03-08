---
title: VueRouter之打开新的标签页
tags: [vue, vue-router, window.open]
toc: true
mathjax: true
date: 2019-03-08 14:20:37
categories:
- 框架
- Vue
- 问题积累
---

1. router-link添加target="_blank"属性
2. window.open

````````````````````````javascript
const { href } = this.$router.resolve(location, current ?, append ?) // 解析目标位置
window.open(href, '_blank')
````````````````````````
