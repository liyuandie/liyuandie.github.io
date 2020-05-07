---
title: React Hooks(二)：使用Effect Hook
draft: false
tags: [react hooks, react]
category: React
date: '2019-08-12T17:16:42Z'
math: false
---

# useEffect

我们接上一篇接着介绍 React Hooks，回到上一篇开头我们的疑问：不使用 class 的情况下怎么使用 state 呢？生命周期呢？

在上一篇我们介绍了 State Hook 的使用，第一个问题已经解决了，现在我们来关注第二个问题，关于生命周期的问题。

同样是上一篇计数器的例子，现在我们增加一个需求：将 document 的 title 设置为包含点击次数的消息。

在 class 中，执行 this.setState()方法后会触发 render 函数重新渲染，但是 render 函数并不会更新页面标题，所以我们需要在 react 组件更新后使用浏览器 API 手动改变页面标题，我们可以这样做：

```js
import React, { Componnet } from 'react';

class Example extends Componnet {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
    };
  }
  //关键代码******start
  componentDidMount() {
    document.title = `You Clicked ${this.state.count} times!`;
  }

  componentDidUpdate() {
    document.title = `You Clicked ${this.state.count} times!`;
  }
  //关键代码******end

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

在上面的 class 中，我们将更新页面标题的操作放到了`componentDidMount`与`componentDidUpdate`，熟悉 react 的小伙伴都知道这是两个生命周期函数，它们在 React 更新 DOM 后立即更新了 document 的 title 属性。但是注意，**在这个 class 中，我们需要在两个生命周期函数中编写重复的代码**，这是因为很多情况下，我们希望在组件加载和更新时执行同样的操作。从概念上说，我们希望它在每次渲染之后执行 —— 但 React 的 class 组件没有提供这样的方法。即使我们提取出一个方法，我们还是要在两个地方调用它。

现在，我们使用 React Hooks 特性来改写上面的示例：

```js
import React, { useState, useEffect } from 'react';

export default function Example() {
  const [count, addCount] = useState(0);

  useEffect(() => {
    document.title = `You Clicked ${count} times!`;
  });

  return (
    <div>
      <p>You Clicked {count} times!</p>
      <button onClick={() => addCount(count + 1)}>Click me</button>
    </div>
  );
}
```

这段代码与前面的 class 具有同样的功能：在重新渲染后，更新页面标题。这里就使用到了我们今天的主角：useEffect

## useEffect

我们再来观察上面的示例：

```js
import React, { useState, useEffect } from 'react';

export default function Example() {
  const [count, addCount] = useState(0);

  //关键代码******start
  useEffect(() => {
    document.title = `You Clicked ${count} times!`;
  });
  //关键代码******end

  return (
    <div>
      <p>You Clicked {count} times!</p>
      <button onClick={() => addCount(count + 1)}>Click me</button>
    </div>
  );
}
```

示例中，我们在函数内部使用了另一个函数`useEffect`，它同`useState`一样也是一个 Hook，那么它的作用是什么呢？

通过使用`useEffect`，你可以概述 React 组件在渲染后执行某些操作，React 会保存你传递的函数（我们将它称之为 “effect”），并且在执行 DOM 更新之后调用它。例如在上面的例子中，我们在组件渲染后设置了 documnet 的 title 属性。除此之外，我们还可以进行其他的操作，例如获取数据或者调取其他命令式的 API。

其次，`useEffect`在 react 组件第一次渲染和之后每次更新后都会执行，所以我们不用再考虑“挂载”还是“更新”。React 保证了每次运行 effect 的同时，DOM 都已经更新完毕，所以我们不用像之前 class 中一样，分别考虑两种情况，**事实上**，你可以将`useEffect`看做`componentDidMount`、`componentDidUpdate`与`componentWillUnmount`三个函数的组合，我们可以在里面执行“发送网络请求”、“订阅监听”、“手动变更 DOM”等操作。

为什么在组件内部调用 useEffect？ 将 `useEffect` 放在组件内部让我们可以在 effect 中直接访问 count state 变量（或其他 props）。我们不需要特殊的 API 来读取它 —— 它已经保存在函数作用域中。还记得闭包吗？Hook 使用了 JavaScript 的闭包机制，而不用在 JavaScript 已经提供了解决方案的情况下，还引入特定的 React API。

现在，你应该已经明白了`useEffect`是如何工作的了。

## 需要清除的 effect

前面我们提到，使用`useEffect`传递的函数（或者说一些操作语句）我们称之为“effect”，在`useEffect`中的一些操作，例如网络请求，更新 DOM 等都叫 effct，大多数情况下，我们只要让这些“effect”去执行就行了，然后在某些特定情况下，我们并不能对它们进行“散养”。例如，我们时常会有**订阅外部数据源**的操作，这种情况下，在组件卸载后进行清除工作是非常重要的，可以防止内存泄露。像这一类的操作，我们称之为“需要清除的 effect”。

如果你是 React 老手，你应该对这类操作不陌生，在 React class 中我们通常在`componentDidMount`设置订阅，然后在`componentWillUnmount`中清除它。例如，假设我们有一个 `ChatAPI` 模块，它允许我们订阅好友的在线状态。以下是我们如何使用 class 订阅和显示该状态：

```js
import React, { Componnet } from 'react';

class FriendStatus extends Componnet {
  constructor(props) {
    super(props);
    this.state = {
      isOnline: null,
    };
  }

  componentDidMount() {
    ChatAPI.subcribe2FriendStatus(this.props.friend.id, this.handleStatusChange);
  }

  componentWillUnmount() {
    ChatAPI.unSubcribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
  }

  handleStatusChange = status => {
    this.setState({
      isOnline: status.isOnline,
    });
  };

  render() {
    const { isOnline } = this.state;
    if (isOnline === null) {
      return 'loading....';
    }
    return isOnline ? 'Online' : 'Offline';
  }
}

export default FriendStatus;
```

假设此时我们要求使用 Hook 来完成这个需求，你可能立马想到使用`useEffect`，但是你有没有思考一个问题，我们说过，`useEffect`是`componentDidMount`、`componentDidUpdate`与`componentWillUnmount`三个函数的组合，我们在里面设置了订阅，那么该在什么时候执行清除呢？`useEffect`已经为我们考虑到了这一点，事实上，它在设计时就考虑到了这一点，订阅与取消订阅的操作在同一个地方执行：**如果你的 effect 返回一个函数，React 将会在执行清除操作时调用它。**

那么我们可以这样来写：

```js
import React, { useState, useEfeect } from 'react';

function FriendStatus(props) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subcribe2FriendStatus(props.friend.id, this.handleStatusChange);

    //返回一个函数来执行清除订阅，在组件卸载时会调用

    return () => {
      ChatAPI.unSubcribeFromFriendStatus(props.friend.id, this.handleStatusChange);
    };
  });

  if (isOnline === null) {
    return 'loading....';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

事实上，“返回一个函数”就是`useEffect`为我们提供的清除机制，每一个 effect 都可以返回一个清除函数，这样我们可以将添加和清除订阅的操作放在一起，当然这是可选的，当我们需要清除时，就返回一个函数就可以了。

那 effect 是何时被清除的呢？React 会在组件卸载时执行清除操作，之前说过，effect 在每次渲染的时候都会执行，所以 React 会在执行当前 effect 之前对上一个 effect 进行清除。

## 深入 Effect

我们继续深入了解`useEffect`的一些特性

### 使用多个 Effect 实现关注点分离

假如我们要将计数器和好友状态指示器的逻辑组合在一起（当然这只是举例，现实工作中我们一定也会有这类需求），使用 class 的话我们应该怎么做呢？

```js
class FriendStatusWithCounter extends Componnet {
  constructor(props) {
    super(props);
    this.state = {
      count: 0,
      isOnline: null,
    };
  }

  componnetDidMount() {
    document.title = `You clicked ${this.state.count} times`;
    ChatAPI.subcribe2FriendStatus(this.props.friend.id, this.handleStatusChange);
  }

  componentDidUpdate() {
    document.title = `You clicked ${this.state.count} times`;
  }

  componentWillUnmount() {
    ChatAPI.unSubcribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
  }

  handleStatusChange = status => {
    this.setState({
      isOnline: status.isOnline,
    });
  };
  // ...
}
```

可以发现设置 document.title 的逻辑被分割到了 `componentDidMount` 和 `componentDidUpdate` 两个函数中，订阅逻辑又被分割到 `componentDidMount` 和 `componentWillUnmount` 中了。而且 `componentDidMount` 中同时包含了两个不同功能的代码。 事实上，在实际工作中，可能逻辑比这还要混乱，使用生命周期函数迫使我们拆分这些逻辑代码。

**使用 Hook 其中一个目的就是要解决 class 中生命周期函数经常包含不相关的逻辑**，那么使用 Hook 是如何来解决的呢？就像你可以使用多个 state 的 Hook 一样，你也可以使用多个 effect，这会将不相关逻辑分离到不同的 effect 中：

```js
function FriendStatusWithCounter() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subcribe2FriendStatus(props.friend.id, this.handleStatusChange);

    return () => {
      ChatAPI.unSubcribeFromFriendStatus(props.friend.id, this.handleStatusChange);
    };
  });
  // ...
}
```

Hook 允许我们按照代码的用途分离他们，而不是像生命周期函数那样。React 将按照 effect 声明的顺序依次调用组件中的每一个 effect。

### 为什么每次更新的时候都要运行 Effect

我们回过头看最开始用 class 定义的显示好友是否在线的 `FriendStatus` 组件，从 class 中 props 读取 friend.id，然后在组件挂载后订阅好友的状态，并在卸载组件的时候取消订阅：

```js
// ...
componentDidMount() {
    ChatAPI.subcribe2FriendStatus(this.props.friend.id, this.handleStatusChange);
  }

componentWillUnmount() {
  ChatAPI.unSubcribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
}
```

其实这里存在我们常犯的一个错误，思考一下，当组件已经显示在屏幕上时，props 中的 friend 发生变化会发生什么，组件将继续展示原来的好友状态。这是一个容易忽略的 bug，但是极易发生，甚至我们还会因为取消订阅时使用错误的 friend.id 导致内存泄露或崩溃问题。

所以此时我们需要添加 `componentDidUpdate` 来解决这个问题：

```js
// ...
componentDidMount() {
  ChatAPI.subcribe2FriendStatus(this.props.friend.id, this.handleStatusChange);
}

//关键代码******start
componentDidUpdate(prevProps) {

  //取消订阅之前的friend.id
  ChatAPI.unSubcribeFromFriendStatus(prevProps.friend.id, this.handleStatusChange);

  // 订阅新的friend.id
  ChatAPI.subcribe2FriendStatus(this.props.friend.id, this.handleStatusChange);

}
//关键代码******end


componentWillUnmount() {
  ChatAPI.unSubcribeFromFriendStatus(this.props.friend.id, this.handleStatusChange);
}
```

忘记正确地处理 `componentDidUpdate` 是 React 应用中常见的 bug 来源。

那么我们来看使用 Hook 的版本：

```js
function FriendStatus(props) {
  // ...
  useEffect(() => {
    // ...
    ChatAPI.subcribe2FriendStatus(props.friend.id, this.handleStatusChange);

    return () => {
      ChatAPI.unSubcribeFromFriendStatus(props.friend.id, this.handleStatusChange);
    };
  });

  // ...
}
```

我们并未做任何改动，因为自从我们使用`useEffect`，它就为我们避免了这个 bug，因为每次更新时都会运行 effect，它会在调用一个新的 effect 之前对前一个 effect 进行清理。现在你明白为什么每次更新时都会执行 effect 了吗？

### 通过跳过 effect 来实现性能优化

事实上，在每次渲染后都执行 effect 可能并不是我们想要的结果，因为这肯定会导致性能问题。在 class 组件中，我们可以通过在 `componentDidUpdate` 中添加对 prevProps 或 prevState 的比较逻辑解决：

```js
componentDidUpdate(prevProps, prevState) {
  if (prevState.count !== this.state.count) {
    document.title = `You clicked ${this.state.count} times`;
  }
}
```

这是很常见的需求，所以它被内置到了 useEffect 的 Hook API 中。如果某些特定值在两次重渲染之间没有发生变化，你可以通知 React 跳过对 effect 的调用，只要传递数组作为 useEffect 的第二个可选参数即可:

```js
useEffect(() => {
  document.title = `You clicked ${count} times`;
}, [count]); // 仅在 count 更改时更新
```

上面这个示例中，我们传入 `[count]` 作为第二个参数。这个参数是什么作用呢？如果 count 的值是 5，而且我们的组件重渲染的时候 count 还是等于 5，React 将对前一次渲染的 [5] 和后一次渲染的 [5] 进行比较。因为数组中的所有元素都是相等的(5 === 5)，React 会跳过这个 effect，这就实现了性能的优化。

当渲染时，如果 count 的值更新成了 6，React 将会把前一次渲染时的数组 [5] 和这次渲染的数组 [6] 中的元素进行对比。这次因为 5 !== 6，React 就会再次调用 effect。如果数组中有多个元素，即使只有一个元素发生变化，React 也会执行 effect。

对于有清除操作的 effect 同样适用：

```js
useEffect(() => {
  function handleStatusChange(status) {
    setIsOnline(status.isOnline);
  }

  ChatAPI.subscribeToFriendStatus(props.friend.id, handleStatusChange);
  return () => {
    ChatAPI.unsubscribeFromFriendStatus(props.friend.id, handleStatusChange);
  };
}, [props.friend.id]); // 仅在 props.friend.id 发生变化时，重新订阅
```

# 总结

- useEffect 可以在组件渲染后实现各种不同的副作用。有些副作用可能需要清除，所以需要返回一个函数
- 其他的 effect 可能不必清除，所以不需要返回
- 可以使用多个 effect 实现关注点分离
- 可以在 useEffect 中使用第二个参数来跳过某些 effect 实现性能优化

下一篇我们将介绍如何自定义 Hook。
