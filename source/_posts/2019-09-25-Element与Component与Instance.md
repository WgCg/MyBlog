---
title: Element与Component与Instance
tags: []
toc: true
mathjax: true
date: 2019-09-25 16:47:50
categories:
    - 框架
    - React
    - 学习笔记
---

## Element

其实就是`virtual dom`，用js对象来描述一个`dom`节点，它的`type`类型可以是字符串（HTML Tag Name）、Components、HTML Node，一个Element Tree最终渲染时只能包含`HTML Tag Name`

## Components

在React中定义组件的方式有很多中：

1. 创建一个`Class`继承自`React.Component`
2. 单纯的功能组件，是一个`Function`，返回一个`Element`
3. 调用`React.createClass`

## Instance

实例就是我们创建`Class Components`时，`this`的指向，它描述了这个组件相关的一些信息，`Function Components`是没有实例的，对于存储本地状态和对声明周期时间作出反应很有用

## Attention

但我们创建一个Element时，使用`React.createElement`、`JSX`、`element factory helper`，不要直接使用`plain object`

## Reference

1. [https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html](https://reactjs.org/blog/2015/12/18/react-components-elements-and-instances.html)
