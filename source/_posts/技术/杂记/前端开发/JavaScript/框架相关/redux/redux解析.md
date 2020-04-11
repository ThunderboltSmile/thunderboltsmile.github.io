---
title: redux解析
date: 2017-09-11
categories:
  - JavaScript
  - 框架相关
tags:
  - redux
  - JavaScript
---

如果说react构成了web应用的骨架和血肉，那么redux则是web应用的内循环系统。redux定义了应用中数据流动的方式和细节，支持高度自由的扩展，让应用对数据的控制更加自由多变。

<!-- more -->

#### redux目录结构
redux的源代码很短，仅有的几个文件，实现了所有功能，就像对所有方法论的定义一样，高度抽象，仅仅指明了react应用的开发方式和数据管理规范。

* utils
  * warning.js [辅助]定义各种警告方式
* applyMiddleware.js [核心]将中间件挂载到store上的方法
* bindActionCreators.js [辅助方法，非核心]将action生成器直接绑定给dispatch，在dispatch是自动执行action creator
* combineReducers.js [核心]将多个reducer封装成为一个根reducer
* compose.js [核心]在生成初始store的时候使用的关键函数
* createStore.js [核心]生成整个应用的store
* index.js 将api作为模块向外开放引用

#### 从何说起
初识redux，我也是一脸懵逼的读完了她的文档，并隐约感觉到这并不是最终的答案，单单引入redux是难以实现当前业务需求的。我知道了什么是action、什么事reducer、什么是store然后呢，异步的逻辑写在哪里？看过github上面一些项目，是将异步逻辑写到了action creator里面，但大多数是使用了第三方的中间件比如redux-thunk、saga等，当我知道redux-saga的时候，第一感觉是，天呐，我感觉自己还完全没有理解到redux是什么，就必须要去学习saga了吗？这对于一个严谨的人来说是违反常识的，但这也是react组件化思想的一种提现，各个模块之间的联系会非常密切，发展到了后面甚至会相互影响，以至于后来者会根本不知道从何处开始才能理清思路，在redux的文档中，就直接让读者根据redux-thunk的示例来编写中间件，这让原本就一头雾水的初学者难以理解。

还有react-redux，这对初学者来说又是一个黑盒，当然如果有人要说，管他是不是黑盒呢，知道怎么用就行啦。那我是无法反驳的，毕竟当前前端圈子如此火热，框架层出不穷，开发需求也越来越多样化，哪有什么时间去管什么底层原理，框架内核。。。2333。

理解一个框架，我认为就是与框架开发者进行交流，而且是深入的那种。框架的目的在于解决耦合问题，提供统一的方式来编写组件，不再过多的思考代码的模式，更加专注于业务逻辑。

带着问题对框架进行探索，我认为是解读框架最恰当的方式，那我们现在就要明确一点，redux是解决什么问题的呢？上述内容已经指出了，redux本身并不能解决异步操作的问题，而是提供了接口让开发者自己实现异步的解决方案，而这个方案如何来编写，redux是不会跟你说清楚的，你如果自己理解了中间件的含义，自然就会懂。

redux真正专注解决的是数据与表现分离，那么我们就应该明确，首先解决的是数据与表现分离，然后再考虑异步的问题，这样思路就会清晰很多。

#### 入口文件
就像许多的组件库一样，index.js将所有的api进行打包输出。

```javascript
export {
  createStore,
  combineReducers,
  bindActionCreators,
  applyMiddleware,
  compose
}
```
按照顺序依次是createStore,combineReducers,bindActionCreators,applyMiddleWare,compose,接下来的讲解也将按照如下方式

#### createStore
我遇到一个问题，是否应该贴出源码呢？或者说，源码解读是否必须要源码，我认为是不必要的，源码可以在github上面找到，完全没有必要在这里贴出来，当读者阅读到这里的时候，就应该去github上面下载一份源码并用编辑器打开了。

createStore顾名思义就是创建用于管理应用状态的函数，而创建后的状态怎么拿到呢？你想想可能让你直接拿到原始状态吗，这么重要的东西肯定要放闭包里保护起来塞，因此返回的是一组API，提供了获取和改变状态的接口，这样你想对state做什么事情就都在redux的监控之下了。

#### applyMiddleWare
这应该是理解redux模式最最重要的一部分了，源码我觉得完全没有必要贴出来，因为那只会让人更加困惑，我只消做一个比喻，估计大家就能够明白了。
这个比喻的喻体叫做洋葱，而本体就是被中间件层层包裹住的reducer函数，当我们通过action访问reducer函数的时候，会从外至内地经历applyMiddleWare中加入的中间件函数，因此，我们可以在这些中间件中加入处理异步的逻辑，甚至是结合promise延迟某些action的行为，在理解了这一点后，redux便是一件非常趁手的工具，不然就是一块烫手的山芋。
编写中间时，应该保证将从最外层传入的action向内传递，若发生action的丢失，则中间件会失去连接的作用。