---
title: redux简介
date: 2017-08-023
categories:
  - JavaScript
  - 框架相关
tags:
  - redux
  - JavaScript
---
redux是用于管理web应用状态的一套方案，不仅限于react构建的应用，但是最常被应用的地方是react应用，在大多数web应用中，是不太需要redux的，但是一旦应用涉及到的共享状态非常多，组件间需要频繁地通信时，再依靠零散的组件来组合就会造成应用内部的逻辑很容易出现混乱，难以维护，此时，应用redux的好处立马体现，将共有状态交给redux管理，组件不再操心如何与家族成员的沟通，此中涉及到了多种模式的应用，比如策略模式、代理模式、发布订阅模式等等，这些实践是学习构建JavaScript应用非常好的资料，因此对其源码进行分析和学习是大有脾益的。

<!-- more -->
#### compose函数
compose用于将多个storeenhancer进行封装，其内部使用的是
数组的reduce方法。

```javascript
/**
 * Composes single-argument functions from right to left. The rightmost
 * function can take multiple arguments as it provides the signature for
 * the resulting composite function.
 *
 * @param {...Function} funcs The functions to compose.
 * @returns {Function} A function obtained by composing the argument functions
 * from right to left. For example, compose(f, g, h) is identical to doing
 * (...args) => f(g(h(...args))).
 */

export default function compose(...funcs) {
  if (funcs.length === 0) {
    return arg => arg
  }

  if (funcs.length === 1) {
    return funcs[0]
  }

  return funcs.reduce((a, b) => (...args) => a(b(...args)))
}
```

调用实例

```javascript
mport { createStore, combineReducers, applyMiddleware, compose } from 'redux'
import thunk from 'redux-thunk'
import DevTools from './containers/DevTools'
import reducer from '../reducers/index'

const store = createStore(
  reducer,
  compose(
    applyMiddleware(thunk),
    DevTools.instrument()
  )
)
```

这样一来，会在创建store的同时对其进行增强，在dispatch action时会触发middleware中的操作并注册devtool。