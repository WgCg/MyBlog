---
title: ant-design问题记录
tags: []
toc: true
mathjax: true
date: 2019-12-23 21:14:45
categories:
  - 框架
  - ant-design
  - 问题积累
---

## Select

1. `select` `dropdownMatchSelectWidth=false` 导致设置的 `width` 没用 会被设置为 `min-width`
2. `select` 增加 `getPopupContainer` 无效，给弹出层的父级增加 `postion:relative`

## PageHeader

1. `page-header` `.ant-page-header-content` 的 `overflow:hidden` 导致输入框的 `focus` 的 `box-shadow` 不显示

## DatePicker

1. `DatePicker` 组件没有 `getPopupContainer` 属性，而是 `getCalendarContainer` 属性，且不能通过 `ConfigProvider` 统一添加
