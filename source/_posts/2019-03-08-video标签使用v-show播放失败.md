---
title: video标签使用v-show播放失败
tags: [html, video, vue, v-show]
toc: true
mathjax: true
date: 2019-03-08 14:06:52
categories:
- 框架
- Vue
- 问题积累
---

# video source src 异步赋值播放失败

如图所示，v-show控制video标签是否显示，showVideo初始值为false，模拟接口异步返回视频链接，然后改变视频的src，但此时视频没有成功播放，Network中也没有去加载对应的视频资源

<img width="500px" src="/assets/vue/problems/10.png"/>
<img width="500px" src="/assets/vue/problems/11.png"/>

# Why?

大家都知道v-show控制的css的visibility属性，实际上无论v-show的值是true或者false都会去渲染dom，可见，视频播放失败跟v-show并没有多大的关系，于是我们把v-show去了验证一下：

<img width="500px" src="/assets/vue/problems/12.png"/>

同样，视频也播放失败，并且Network中也没有视频资源的加载记录，由此可推断，当video中存在source标签的时候，浏览器渲染之后会自动去加载src对应的资源，后续即便地址改变，浏览器也不会再去获取资源

# 解决方法

1. src属性添加到video标签上

    <img width="500px" src="/assets/vue/problems/15.png"/>

2. 使用v-if替代v-show，触发dom重排

    <img width="500px" src="/assets/vue/problems/13.png"/>

3. 触发video的load方法

    <img width="500px" src="/assets/vue/problems/14.png"/>
