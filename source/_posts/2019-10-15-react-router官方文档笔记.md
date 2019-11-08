---
title: react-router官方文档笔记
tags: []
toc: true
mathjax: true
date: 2019-10-15 21:42:16
categories:
- 框架
- react-router
- 笔记
---

[官方文档](https://reacttraining.com/react-router/web/guides/quick-start)

## 注意事项

1. 所有路由相关的标签必须嵌入在`routes`类型的标签之内
2. `Route`放在`Switch`中才会进行**单一**匹配，否则只要匹配成功的`Route`，都会进行渲染
3. 如何衡量`<Route component/>`、`<Route render/>`、`<Route children/>`三种方式
   1. `<Route component/>`: 在`Route`内部会根据传递的`component`的值通过`React.createElement`创建组件，这意味你如果传递一个内联方法（通常需要传递额外的非路由相关的`props`时使用），在每次渲染时都会销毁旧组件，创建一个新的组件，而不会更新旧组件，**造成性能上的损失**
   2. `<Route render/>`: 解决了上述问题，但是有关路由的`props`都得自己手动传递一下
   3. `<Route children/>`: 无论`path`是否匹配，都会触发`children`(不在`Switch`组件中)，可以根据`match`进行判断做一些特殊化处理，`props`也得手动传递
   4. `<Route>chidlren</Route>`: 这时候没法把路由参数传递给`children`组件，使用`withRouter`或者`hooks`进行操作
4. `location`是`immutable`的，而`history.location`是`mutable`的
   1. `immutable`: 对象引用不会发生变化
   2. `mutable`: 对象引用会发生变化

## API文档

### react-router-dom

#### Component

1. routers: 以下所有的`high-level`routes都是基于`Router`组件封装的，`Router`组件一般配合状态管理库，例如`redux`、`mbox`进行同步自定义历史记录
   1. `BrowserRouter`: `history`模式
      1. basename: string : 配置所有`路由`的`baseurl`
      2. getUserConfirmation: func : 结合`Prompt`组件使用，可以在离开路由之前进行弹窗提醒
      3. forceRefresh: bool : 路由跳转时是否刷新整个页面
      4. keyLength: number : 设置`location.key`的长度，`location`可以通过`useLocation`或`props`取得
      5. children: node : 要渲染的子元素
   2. `HashRouter`: `hash`模式
      1. basename: string : 配置所有`路由`的`baseurl`
      2. getUserConfirmation: func : 结合`Prompt`组件使用，可以在离开路由之前进行弹窗提醒
      3. hashType : string : 设置`hash`模式的类型
         1. slash: 以`#/`开头，**默认值**
         2. noslash: 以`#`开头
         3. hashbang: 以`#!/`开头，**已被Google废弃**
      4. children: node : 要渲染的子元素
   3. `MemoryRouter`: 在进行`测试`和`非浏览器环境`时使用，暂不介绍
   4. `NativeRouter`: 用在`Native`环境，暂不介绍
   5. `StaticRouter`: 通常用于`服务端渲染`或`测试`，暂不介绍
2. route matchers
   1. `Route`
      1. 有三种渲染组件的方式，无论是哪种渲染方式，都会接受`match`、`location`、`history`三个`props`
         1. `<Route component>`
         2. `<Route render>`
         3. `<Route children>function`
      2. path: string | string[]: 要匹配的路由，匹配成功后会渲染对应的组件
      3. exact: bool: 是否是精确匹配，设置为`true`后，如果`path`设置为`/pages`，`/pages/home`不会匹配成功
      4. strict: bool: 是否是严格匹配，设置为`true`后，如果`path`设置为`/pages/`，`/pages`不会匹配成功
      5. location: object: `Route`匹配默认都是根据当前的`history location`进行匹配的，可以设置该属性更改匹配的`location`
      6. sensitive: bool: 是否大小写敏感
   2. `Switch`: 当`Switch`组件渲染时，它会从上往下依次匹配`children`中的`Route`或者`Redirect`标签，匹配成功之后渲染该组件并停止匹配，如果没有匹配到，什么都不会渲染
      1. location: object: 设置进行匹配的`location`，默认为`current location`
      2. children: node: 该组件的`children`只能包含`Route`和`Redirect`
3. navigation/route changers
   1. `Link`: 跳转到某一路由，最终会渲染成`a`标签
      1. to: 要跳转的路由，值的类型有多种，具体如下
         1. string: 要跳转的路由对应的`url`
         2. object: 路由描述对象，具有以下属性
            1. pathname
            2. search
            3. hash
            4. state: 附加跳转信息
         3. function: 该方法接收当前路由的`location`对象为参数，返回要跳转路由的`string`or`object`描述
      2. replace: bool: 替换当前路由
      3. innerRef
         1. function : 该方法接收`Link`标签最后渲染成的`DOM Node`为参数，目前为`a`标签
         2. RefObject : `react.createRef`创建的`RefObject`
      4. others: 其它想要设置到`a`标签的任意属性，例如`title`、`id`等等
   2. `NavLink`: 与`Link`的作用一致，不同的是可以提供一个`activeClassName`属性，设置与当前路由匹配时的样式
      1. activeClassName: string : 设置当路由匹配时的`className`，默认为`active`
      2. activeStyle: object : 设置当路由匹配时的样式
      3. exact: bool : 当为`true`时，只有路径完全匹配，才会添加设置的`activeClassName`、`activeStyle`
      4. strict: bool : 当设置为`true`时，会对路由进行严格匹配，包括`#`、`#/`
      5. isActive: func : 添加判断当前路由是否匹配的验证方法，参数为`match`，`location`
      6. location: object : 传递给`isActive`方法的`location`
      7. aria-current: string : 设置`active`时的`aria-current`属性，默认为`page`
   3. `Redirect`: 渲染该标签时，会立即将当前的路由转跳到该标签设置的`to`属性的值所对应的路由页面
      1. to
         1. string: 要跳转的`url`
         2. object: `location object`
      2. push: bool: 设置为`true`，将会在浏览器历史记录中增加一条，即为`push`的效果
      3. from: string: 只能当`Redirect`组件被包裹在`Switch`组件中时，才能使用该属性
      4. exact: bool: `from`是否需要精确匹配
      5. strict: bool: `from`是否需要严格匹配，少个`\`都不行
      6. sensitive: bool: `from`匹配时是否大小写敏感
   4. `prompt`: 与`routes`的`getUserConfirmation`属性配合使用

#### Hooks

1. useHistory
2. useLocation
3. useParams
4. useRouteMatch

## 三方库

### 异步组件/code splitting

如果想要实现异步加载组件/代码拆分的功能，需要使用进行安装如下插件以及设置：

1. [@babel/plugin-synntax-dynamic-import](https://babeljs.io/docs/en/babel-plugin-syntax-dynamic-import/)
   1. {"plugins": ["@babel/plugin-syntax-dynamic-import"]}
2. [loadable-components](https://github.com/smooth-code/loadable-components)

### 开发使用

1. [path-to-regexp](将路径转换为正则表达式)

### 底层库

1. [history](https://github.com/ReactTraining/history)