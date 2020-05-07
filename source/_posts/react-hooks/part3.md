---
title: React Hooks(三)：自定义Hook
draft: false
tags: [react hooks, react]
category: React
date: '2019-08-13T17:16:42Z'
math: false
---

## 自定义 Hook

这一篇我们介绍如何自定义 Hook，react 除了我们之前介绍的`useState`与`useEffect`两个 Hook 外，还有一些使用频率比较低的 Hook，比如`useContext`，这里我们就不做介绍了，除了使用这些内置的 Hook，我们还可以使用自定义的 Hook，自定义 Hook 可以将组建逻辑提取到可以重用的函数中。

在上一篇的介绍 Effect Hook 时，我们定义了一个订阅好友状态的组件：

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

现在假设在我们的聊天应用中有一个聊天列表，用户在线时需要把名字设置成绿色，这其实也是获取用户的在线状态，我们可以把上面类似的逻辑复制粘贴过来：

```js
import React, { useState, useEfeect } from 'react';

function FriendListItem(props) {
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

  return <li style={{ color: isOnline ? 'green' : 'red' }}>{props.friend.name}</li>;
}
```

这并不是理想的解决方案，在 `FriendStatus` 与 `FriendListItem` 共享的逻辑我们可单独抽离出来，我们来看看如何使用 Hook 来抽离两个组件共享的逻辑。

### 抽离自定义 Hook

抽离共享逻辑我们并不陌生，往往当我们想要在两个函数之间共享逻辑时，我们会把相同的部分抽离到第三个部分，不同的部分作为参数传递给第三个函数。在这里，我们的组件和 Hook 其实都是函数，所以一样可以用这种方式。

自定义的 Hook 是一个函数，react 要求我们命名 Hook 时要以'use'开头，才符合 Hook 规则，所以我们使用`useFriendStatus`作为自定义 Hook 的名称：

```js
import React, { useState, useEfeect } from 'react';

function useFriendStatus(friendID) {
  const [isOnline, setIsOnline] = useState(null);

  useEffect(() => {
    function handleStatusChange(status) {
      setIsOnline(status.isOnline);
    }

    ChatAPI.subcribe2FriendStatus(friendID, this.handleStatusChange);

    return () => {
      ChatAPI.unSubcribeFromFriendStatus(friendID, this.handleStatusChange);
    };
  });

  return isOnline;
}
```

现在我们已经完成了自定义 Hook 的工作，并没有做任何神奇的改变，所有逻辑都是直接拷贝过来的，唯一不同的是这并不是一个组件函数，所以我们返回的只是好友的在线状态，由于返回的是某个好友的在线状态，所以我们需要传递一个 friendID 作为参数

自定义好了 Hook，我们来看看如何使用。

### 使用自定义 Hook

在 `FriendStatus` 与 `FriendListItem`之间公用的逻辑就是我们抽离出来的部分，其作用是订阅某位好友是否在线，而`useFriendStatus`的作用就是这个，所以使用 Hook，只需要将原来组件中重复的逻辑替换成我们自定义的 Hook 就行了：

```js
function FriendStatus(props) {
  const isOnline = useFriendStatus(props.friend.id);

  if (isOnline === null) {
    return 'loading....';
  }
  return isOnline ? 'Online' : 'Offline';
}
```

```js
import React, { useState, useEfeect } from 'react';

function FriendListItem(props) {
  const isOnline = useFriendStatus(props.friend.id);

  return <li style={{ color: isOnline ? 'green' : 'red' }}>{props.friend.name}</li>;
}
```

### 总结

现在我们就完成了一个简单的自定义 Hook，以及对它的使用，其实非常简单。

React Hooks 极大的提高了我们的开发效率，关于 Hook 的分享就到这里了，希望以后大家都可以熟练的运用这个技能。
