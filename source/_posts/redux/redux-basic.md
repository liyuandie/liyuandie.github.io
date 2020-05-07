---
title: 状态管理工具：Redux
draft: false
tags: [react, redux, store, action, reducer]
category: Redux
date: "2017-11-03T23:32:23Z"
math: false
---
本篇介绍状态管理工具Redux的基础知识和基本用法。开始之前让我们来看一看什么是Redux。

# 简介

随着JavaScript单页应用开发日趋复杂，JavaScript需要管理比任何时候都要多的```state```（状态）。 这些```state```可能包括服务器响应、缓存数据、本地生成尚未持久化到服务器的数据，也包括UI状态，如激活的路由，被选中的标签，是否显示加载动效或者分页器等等。

管理不断变化的```state```非常困难。如果一个model的变化会引起另一个model变化，那么当view变化时，就可能引起对应model以及另一个 model 的变化，依次地，可能会引起另一个view的变化。直至你搞不清楚到底发生了什么。```state```在什么时候，由于什么原因，如何变化已然不受控制。当系统变得错综复杂的时候，想重现问题或者添加新功能就会变得举步维艰。

这里的复杂性很大程度上来自于：我们总是将两个难以理清的概念混淆在一起：变化和异步。如果把二者分开，能做的很好，但混到一起，就变得一团糟。为了解决这个问题React试图在视图层禁止异步和直接操作DOM来解决这个问题。但是，美中不足的是，React依旧把处理```state```中数据的问题留给了我们。而Redux就是为了帮助我们解决这个问题而诞生的状态管理工具。

简而言之，Redux让```state```的变化变得可预测了，从而让我们更好的管理好应用中的状态，试想当你的应用每一个```state```的变化你都非常清晰，它何时变化？它为何变化？它的变化会引起谁的变化？当你对这些都了如指掌的时候，也就不再为管理这些```state```而烦恼了。

但是我们学习Redux的原因，不是因为我们学了React，所以我们要学Redux，因为，Redux并不是为React量身定做的，事实上，Redux可以作为一个状态管理工具与其他任何库搭配使用，所以，在阅读后续内容之前，你可能要问自己一句：我真的需要Redux吗？可能你现在还没有答案，没关系，这里分享一篇Redux作者Dan Abramov自己写的blog，相信看完你就会有答案了：[You Might Not Need Redux](https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367)

如果看完你觉得完全没有必要用Redux，那么你已经可以关掉这篇文章了。

如果看完觉得，这正是我想要的东西，那么就跟随我的脚步一起来探索吧！不过开始之前，建议先看一套视频，也是作者自己录制的，一步一步讲解了如何使用Redux，真是个良心作者，让我想起了被求锤得锤的良心歌手薛之谦，咳咳，题外话，视频地址：https://egghead.io/lessons/react-redux-the-single-immutable-state-tree

# 安装 
直接使用npm安装：
```bash
npm install --save redux
```
大多数情况下，我们还需要用到React绑定库和一些开发工具：
```bash
npm install --save react-redux
npm install --save-dev redux-devtools
npm install --save redux-logger
npm install --save redux-persist
npm install --save redux-thunk
```
# 核心概念
简单来说，Redux核心的东西其实只有三个：store、action、reducer。

我们用一句话将他们串联就是：整个应用的```state```被储存在一棵```object tree```中，并且这个```object tree```只存在于唯一一个```store```中，唯一改变```state```的方法就是触发```action```，```action```是一个用于描述已发生事件的普通对象，而为了描述```action```如何改变```state tree```，你需要编写```reducer```函数。

就是这么简单，并没有什么复杂的神奇魔法，这就是Redux如何工作的整个流程，接下来我们来一一介绍这三个东西。

## action

action是把数据从应用传到store的荷载，是store中数据的唯一来源，我们可以通过```store.dispatch()```方法将action传给store。

### 基本用法

简单来说，action其实就是一个简单的Javascript对象。这个对象中必须有一个```type```字段来表示要执行的动作，你还可以增加其他任意字段来描述这个动作或传递参数，像这样：
```js
{
    type: ADD_TODO,
    text: 'Build my first Redux app'
}
//传递参数
{
    type: 'CREATED_TEAM',
      team: {
        id: r.id,
        name: teamName,
      },
}
```
当应用规模越来越大时，建议使用单独的模块或文件来存放 action。
```js
import { ADD_TODO, REMOVE_TODO } from '../actionTypes'
```
### Action 创建函数

Action 创建函数就是生成action的方法，也就是简单返回一个action的函数，像这样：
```js
const addTodo = (text) =>{
  return {
    type: ADD_TODO,
    text
  }
}
```
这样做将使action创建函数更容易被移植和测试。

## reducer

reducer是一个纯函数，接收旧的state和一个action，返回一个新的state：
```js
(previousState, action) => newState
```
这里有没有想起什么？对了，还记得我们之前介绍的reduce方法吗？我们说reduce方法接收一个初始值和一个function，返回一个值。是不是很类似啊？所以我们叫reducer啊，它的原理就跟reduce是一样的。

注意这里我们说reducer是一个纯函数，这一点很重要，我们一定要保证它的纯净，也就是说千万不要在reducer函数做这些操作：

       修改传入参数；
       执行有副作用的操作，如 API 请求和路由跳转；
       调用非纯函数，如 Date.now() 或 Math.random()。

这样是为了保证，只要传入的参数确定，那么就一定会返回一个确定的状态，这样才能保证我们对state的变化做到了如指掌，也就是说，你可以把reducer看作一个单纯的计算工具，只要我们传入的值相同，那么返回的值一定永远是相同的。

所以，在编写reducer之前，一定要先想好store的结构，然后我们来看一下怎么写reducer函数：
```js
const team = (state = {}, action) => {
  switch (action.type) {
    case 'CREATED_TEAM':
      return {
        ...action.team,
      };
    case 'TEAM_INFO':
      return {
        ...state,
        queryTeam: action.teamInfo,
      };
    default:
      return state;
  }
};
```
其实核心就是一堆switch语句，告诉应用，当遇到type是什么的时候，该执行什么，注意，我们并没有修改state，而是返回一个新的对象，千万不要修改state！千万不要修改state！千万不要修改state！重要的事情说三遍！然后注意，最后一定要在default情况下返回旧的state，这样可以保证在遇到未知的action时，返回原来的state。

另外，一个reducer只负责应用中所有state中它管理的那部分，所以一个应用中我们可能需要编写多个reducer，每个reducer传入的参数都不同，随着应用的膨胀，我们还可以将拆分后的 reducer 放到不同的文件中, 以保持其独立性并用于专门处理不同的数据域。

最后，Redux提供了```combineReducers()```工具类来将多个reducer合并为一个，这样就能消灭一些样板代码了。
整个应用就有一个总的reducer：
```js
import { combineReducers } from 'redux'

const todoApp = combineReducers({
  visibilityFilter,
  todos
})

export default todoApp
```
```combineReducers()```所做的只是生成一个函数，这个函数来调用你的一系列reducer，每个reducer根据它们的key来筛选出state中的一部分数据并处理，然后这个生成的函数再将所有 reducer的结果合并成一个大的对象。

## store
前面我们学会了使用action来描述“发生了什么”，和使reducers来根据action更新state的用法。store就是把它们联系在一起的对象，简单来说store有以下功能：
* 维持应用的 state；
* 提供 getState() 方法获取state；
* 提供 dispatch(action) 方法更新state；
* 通过 subscribe(listener) 注册监听器;
* 通过 subscribe(listener) 返回的函数注销监听器。

根据已有的reducer来创建store是非常容易的。在前面，我们使用```combineReducers()```将多个reducer合并成为一个。现在我们将其导入，并传递createStore()。
```js
import { createStore } from 'redux'
import todoApp from './reducers'

let store = createStore(todoApp)
```
现在我们已经创建好了store，让我们来验证一下！
```js
import {
  addTodo,
  toggleTodo,
  setVisibilityFilter,
  VisibilityFilters
} from './actions'

// 打印初始状态
console.log(store.getState())

// 每次 state 更新时，打印日志
// 注意 subscribe() 返回一个函数用来注销监听器
const unsubscribe = store.subscribe(() =>
  console.log(store.getState())
)

// 发起一系列 action
store.dispatch(addTodo('Learn about actions'))
store.dispatch(addTodo('Learn about reducers'))
store.dispatch(addTodo('Learn about store'))
store.dispatch(toggleTodo(0))
store.dispatch(toggleTodo(1))
store.dispatch(setVisibilityFilter(VisibilityFilters.SHOW_COMPLETED))

// 停止监听 state 更新
unsubscribe();
```
然后来看一下看到 store 里的 state 是如何变化的：

![store](/blogimgs/store.png)

# 总结

基础概念我们就先介绍这么多吧，想了解更多可以参考：[官方文档](https://redux.js.org/)。

总之，两句话：
1. Redux 应用只有一个单一的 store
2. 整个应用的```state```被储存在一棵```object tree```中，并且这个```object tree```只存在于唯一一个```store```中，唯一改变```state```的方法就是触发```action```，```action```是一个用于描述已发生事件的普通对象，而为了描述```action```如何改变```state tree```，你需要编写```reducer```函数。

我相信，这就已经足够理解Redux的核心概念了。

下一篇可能会介绍一下Redux如何与React搭配使用。

# Refers
* https://redux.js.org/
* https://egghead.io/lessons/react-redux-the-single-immutable-state-tree
* https://cn.redux.js.org/
* https://medium.com/@dan_abramov/you-might-not-need-redux-be46360cf367



