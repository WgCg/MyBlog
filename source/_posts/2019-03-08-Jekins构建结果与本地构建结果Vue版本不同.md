---
title: Jekins构建结果与本地构建结果Vue版本不同
tags: [webpack, vue]
toc: true
mathjax: true
date: 2019-03-08 15:00:30
categories:
- 打包工具
- Webpack
- 问题积累
---

# 背景

项目迁移到jenkins，通过jenkins进行构建，部署后发现页面报错，但是本地构建出来代码部署后是正常的，报错如下：

<img width="800" src="/assets/webpack/problems/9.png"/>

# 问题排查过程

1. 注释webpack插件UglifyJsPlugin插件，不进行代码压缩

    <img width="500" src="/assets/webpack/problems/13.png"/>

2. 对比本地代码与jenkins构建出来的代码，构建输出目录结构如下：
    <img width="500" src="/assets/webpack/problems/14.png"/>

3. 对比结果
    * app.js中只存在一些变量名和Path的不一致，不会产生错误
    * manifest.js完全一致
    * vendor.js中Vue的版本不一致，jenkins构建出来的Vue版本是2.5.20，本地构建出来的Vue版本是2.5.21，推测是由此产生的错误
4. 用本地构建出的vendor.js替换jenkins构建出的vendor.js，部署到ftp上，发现问题解决了，确认推测是正确的
5. 对比两个Vue版本代码的不同，发现是以下代码影响的：

    <img width="800" src="/assets/webpack/problems/10.png"/>

    <img width="500" src="/assets/webpack/problems/8.png"/>

    <img width="500" src="/assets/webpack/problems/12.png"/>

    分析：rendList是v-for的实现方法，在Vue2.5.20中，当入参val属于类型: Array, String, Number, Object（非null）时初始化变量ret为数组并进行相应的处理，然后判断ret变量不为undefined和null时，给ret变量添加属性_isVList为true，然后将ret变量返回，ret的类型可能为undefined, Array，当返回undefined时，对undefined进行一些数组的操作就会发生错误，而控制台所报的错误就是因为取了undefined.length导致的，在Vue2.5.21版本修复了这个Bug，如果ret变量为undefined或null时，初始化ret为[]，这样确保rendList的返回值类型始终为Array

# 解决方法

由上可知，产生改问题的原因如下：

* 直接原因： Vue2.5.20版本中存在代码漏洞
* 根本原因：jenkins服务器安装的Vue版本不正确，而为什么jenkins服务器安装的vue版本和本地安装的不一致，需要进一步排查，已经排除是node版本导致的原因

因此，只要把package.json中vue依赖的版本号固定为2.5.21（注意：vue-template-compiler版本需要跟vue版本保持一致），问题就可以解决了，如下图：

<img width="500" src="/assets/webpack/problems/15.png"/>

<img width="500" src="/assets/webpack/problems/11.png"/>
