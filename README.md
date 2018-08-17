# 🚀 hyperapp 分析

> 1 kB JavaScript micro-framework for building web applications -- [hyperapp](https://github.com/hyperapp/hyperapp)

最吸引人的就是这句介绍了。

# 🔥 hyperapp 文档

原文见[官方文档](https://github.com/hyperapp/hyperapp)

这里我简单的介绍一下：

* Minimal -- 我们积极地将您需要理解的概念最小化，以提高工作效率，同时保持与其他框架相同的功能。
* Pragmatic -- 在管理你的状态的时候可以采用务实的办法，允许副作用、异步操作和 DOM 操作。
* Standalone -- 同样支持牛逼的 virtual DOM 来用更小的代价管理状态、渲染视图，有简单的生命周期和基于 key 标识的节点更新

Hyperapp 只有两个最重要的 API:

- h: 用于处理 view，返回 Virtual DOM 节点
- app: 用于将一个应用程序挂载到特定的 DOM 元素上，也可以不指定 DOM 元素（这将利于测试）

## ❤️ h

文档中使用了 jsx，需要安装插件将 jsx 编译成 h 函数能够处理的样子。

```js
const view = (state, actions) =>
  h("div", {}, [
    h("h1", {}, state.count),
    h("button", { onclick: () => actions.down(1) }, "-"),
    h("button", { onclick: () => actions.up(1) }, "+")
  ])

// h 函数处理之后得到一个 v-DOM
{
  nodeName: "div",
  attributes: {},
  children: [
    {
      nodeName: "h1",
      attributes: {},
      children: [0]
    },
    {
      nodeName: "button",
      attributes: { ... },
      children: ["-"]
    },
    {
      nodeName:   "button",
      attributes: { ... },
      children: ["+"]
    }
  ]
}
```

事实上并不是一定要 jsx 的，你直接写 h 函数结构也能顺利得到一个 v-DOM 结构。也可以用其他任务的语法模版。

## 🚴‍♀️ app

Hyperapp 应用程序主要的三板斧还是 state, actions, view。React 用户似乎对这个很熟悉...

初始化后，您的应用程序将在连续循环中执行，从用户或外部事件接收操作，更新状态，并通过 v-DOM 模型表示视图中的更改。 
将操作视为通知 Hyperapp 更新状态并安排下一个视图重绘的信号。 在处理动作之后，将新状态呈现给用户。

妥妥的单向数据流！

### 🀄️ state

简单的说就是一个 plain object，必须通过 actions 来改变它。

```js
const state = {
  count: 0
}
```

因为在更新 state 的时候执行的是浅合并(shallow merge)，所以顶层必须是 plain object,至于内部的，随便你了。 Map? 👌,Immutablejs? 👌, Sets? 👌。

Question：如果遇到嵌套状态树(state can be a nested tree of objects)如何破❓

```js
const state = {
  counter: {
    count: 0
  }
}

// 你只需要让你的 action 和它保持同样的嵌套结构即可（同样的命名空间下）
const actions = {
  counter: {
    down: value => state => ({ count: state.count - value }),
    up: value => state => ({ count: state.count + value })
  }
}
```

### 👧 actions

同 Redux 很像。

actions 也是一个载体，是一个一元函数（只有一个参数），只能通过 actions 来更新状态，返回一个浅合并的新的状态，然后 v-DOM 一番操作猛如虎之后视图重绘。

当然你也可以返回一个函数，参数是当前的 state 和 actions, 然后再返回一个局部的 state.

```js
const actions = {
  up: (value) => (state, actions) => ({count: state.count + value})
}
```
同 redux 一样，状态的更新应该是 immutable 的，就是不要在 actions 直接改变 state, 而是返回一个新的状态，这对时间旅行调试很有用，
也能避免一些难以追踪的异常。

#### 🐟 异步 actions

用于副作用的操作（写入数据库，向服务器发送请求等）不需要具有返回值。 

你可以从另一个 action 或回调函数中调用操作。 返回 Promise，undefined 或 null 的操作不会触发重绘或更新状态。



### 🐒 view

## 🐍 react

与 React 做一个简单粗暴的对比：

```jsx
import React from 'react'
import ReactDom from 'react-dom'
ReactDOM.render(<App />, document.getElementById('root'))


import { h, app } from 'hyperapp'
app(state, actions, view, document.getElementById('root'))
```

# ✈️ 脚手架

这是一个基于 parcel + typescript + hyperapp + pwa 的脚手架, 正在视图使其支持 antd、数据流、路由等常规配置。

希望有兴趣的可以 PR

[文档](./about.md)