---
title: React Hooks(一)：Hook简介与使用State Hook
draft: false
tags: [react hooks, react]
category: React
date: '2019-08-11T17:16:42Z'
math: false
---

最近学习了 React 新特性：Hook，可以说我对它是一见钟情，所以打算用几篇文章来介绍一下它。

## Hook 简介

什么是 Hook？先来看一看官方的介绍：

> Hook 是 React 16.8 的新增特性。它可以让你在不编写 class 的情况下使用 state 以及其他的 React 特性。

看完这段介绍，你可能有跟我第一次看到时一样的疑问：不使用 class 的情况下怎么使用 state 呢？生命周期呢？
不急，我们先来看一个例子：

我们使用 react 实现一个简单的需求：“一个简单的计数器，记录用户点击按钮的次数”。这对我们来说太简单了：

```js
import React, { Componnet } from 'react';

class Example extends Componnet {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
  }

  addCount = () => {
    this.setState({
      count: this.state.count + 1,
    });
  };

  render() {
    const { count } = this.state;
    return (
      <div>
        <p>You Clicked {count} times!</p>
        <button onClick={this.addCount}>Click me</button>
      </div>
    );
  }
}

export default Example;
```

对于 React 使用者来说，这段代码是再熟悉不过了，只有一个简单的 state，但是当我们使用 Hook 后，我们可以这样来写：

```js
import React, { useState } from 'react';

export default function Example() {
  //s声明一个叫count的state变量
  const [count, addCount] = useState(0);

  return (
    <div>
      <p>You Clicked {count} times!</p>
      <button onClick={() => addCount(count + 1)}>Click me</button>
    </div>
  );
}
```

如果你启动你的项目，打开浏览器，你会发现，两种写法在功能上完全一样，每当我们点击按钮，计数器都会增加 1，但是不难发现两种写法在代码行数上有着明显的差别，使用 Hook 使我们的代码更加精简了，因为我们不用使用 class 的方式来定义有状态组件，而我们通过这种“不使用 class”的方法同样可以使用 state 来控制页面渲染，这是最初 Hook 吸引我的第一个点。

### 什么是 Hook？

在上面的示例中`useState`就是一个 Hook：

```js
import React, { useState } from 'react';

export default function Example() {
  // ...
}
```

`Hook`是一个特殊的函数，它可以让你“钩入”React 的特性，例如，`useState`是允许你在 React 组件中添加 state 的 Hook。还有很多其他的 Hook，我们之后再介绍。

### 什么时候要用 Hook？

以前我们使用 React 编写组件时，当组件包含一些 state 状态时，我们采用的方法通常是使用 class 的方法定义组件，我们称之为有状态组件，在 React 发布 v16.8 版本后，增加了新特性 Hook，它允许我们在使用函数定义组件的情况下，向组件添加 state 等 React 特性，而在这之前，我们使用函数定义的组件通常只能是无状态组件，所以，现在我们可以在现有的函数组件中大胆的使用 Hook 来增加 state，不用再定义繁琐的 class 组件。

### 什么情况下不能使用 Hook？

当然我们也不能在任何情况下随心所欲的使用 Kook，react 为我们规定了使用 Hook 时应该遵循的两条规则：

**1.只能在最顶层使用 Hook：**

不要在循环，条件或嵌套函数中使用 Hook，确保总是在你的 React 函数的最顶层调用他们。遵守这条规则，你就能确保 Hook 在每一次渲染中都按照同样的顺序被调用。这让 React 能够在多次的 Hook 调用之间保持 hook 状态的正确。

关于这一点，你首先要了解，当我们使用 Hook 时，React 是怎么知道哪个 state 对应哪个 useState 的？答案是答案是 React 靠的是 Hook 调用的顺序。所以我们必须保证每次 React 调用 Hook 的顺序相同，才能保证每个 state 正常使用。

**2.只在 React 函数中调用 Hook:**

不要在普通的 JavaScript 函数中调用 Hook。你可以：

- 在 React 函数组件中调用 Hook
- 在自定义的 Hook 中调用其他 Hook

## 使用 State Hook

让我们先来回顾一下前面的示例：

```js
import React, { useState } from 'react';

export default function Example() {
  //s声明一个叫count的state变量
  const [count, addCount] = useState(0);

  return (
    <div>
      <p>You Clicked {count} times!</p>
      <button onClick={() => addCount(count + 1)}>Click me</button>
    </div>
  );
}
```

在这个示例中，我们就使用了 State Hook，具体来说是使用了`useState`这个 Hook,下面我们来具体介绍一下是怎么使用的。

### 声明 State 变量

在 class 中，我们往往通过在构造函数中设置`this.state`来声明 state：

```js
import React, { Componnet } from 'react';

class Example extends Componnet {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
  }
}
```

在函数组件中，我们没有 this，所以我们不能直接声明或者读取`this.state`。使用 Hook，我们直接调用`useState`：

```js
import React, { useState } from 'react';

export default function Example() {
  //s声明一个叫count的state变量
  const [count, addCount] = useState(0);
}
```

那么调用 useState 的时候发生了什么呢？调用 useState 的时候，函数执行，返回了两个返回值：state 及更新 state 的函数。所以当我们写`const [count, addCount] = useState(0);`时，分别将这两个返回值赋值给了变量`count`和`addCount`，这是一种数组解构赋值，不了解的小伙伴建议学习一下 ES6 语法，这里我们不做介绍，总之，此时，变量`count`就是我们的 state，而`addCount`是一个更新 state 的函数。

其次 useState 只接收一个参数，就是 state 的初始值，我们这里使用`useState(0)`就表示`count`的初始值为 0。不同于 class 的是，我们可以按照需要使用数字或字符串对其进行赋值，而不一定是对象。在示例中，只需使用数字来记录用户点击次数，所以我们传了 0 作为变量的初始 state。（如果我们想要在 state 中存储两个不同的变量，只需调用 useState() 两次即可。）

现在我们知道了`useState`的作用，那么上面的例子就更容易理解了：我们声明了一个叫 `count` 的 state 变量，然后把它设为 0。React 会在重复渲染时记住它当前的值，并且提供最新的值给我们的函数。我们可以通过调用 `addCount` 来更新当前的 count。

### 读取 State

当我们想在 class 中显示当前的 count，我们读取 `this.state.count`：

```js
render() {
    const { count } = this.state;
    return (
      <div>
        <p>You Clicked {count} times!</p>
        <button onClick={this.addCount}>Click me</button>
      </div>
    );
  }
```

在函数中，我们直接读取变量`count`：

```js
<p>You Clicked {count} times!</p>
```

### 更新 State

在 class 中，我们更新 state 的值需要调用`this.setState()`:

```js
addCount = () => {
  this.setState({
    count: this.state.count + 1,
  });
};

//调用addCount方法
<button onClick={this.addCount}>Click me</button>;
```

### 使用多个 State

将 state 变量声明为一对 `[something, setSomething]` 也很方便，因为如果我们想使用多个 state 变量，它允许我们给不同的 state 变量取不同的名称：

```js
function ExampleWithManyStates() {
  // 声明多个 state 变量
  const [age, setAge] = useState(42);
  const [fruit, setFruit] = useState('banana');
  const [todos, setTodos] = useState([{ text: '学习 Hook' }]);
```

在以上组件中，我们有局部变量 `age`，`fruit` 和 `todos`，并且我们可以单独更新它们：

```js
function handleOrangeClick() {
  // 和 this.setState({ fruit: 'orange' }) 类似
  setFruit('orange');
}
```

你不必使用多个 state 变量。State 变量可以很好地存储对象和数组，因此，你仍然可以将相关数据分为一组。然而，不像 class 中的 `this.setState`，更新 state 变量总是替换它而不是合并它。

# 总结

从上述内容中，我们了解了 React 提供的 useState Hook，有时候我们也叫它 “State Hook”。它让我们在 React 函数组件上添加内部 state —— 这是我们首次尝试。

我们还学到了一些知识比如什么是 Hook。Hook 是能让你在函数组件中“钩入” React 特性的函数。它们名字通常都以 use 开始，还有更多 Hook 等着我们去探索。

现在我们将学习另一个 Hook: `useEffect`。 它能在函数组件中执行副作用，并且它与 class 中的生命周期函数极为类似。
