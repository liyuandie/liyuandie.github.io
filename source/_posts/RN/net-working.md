---
title: React Native学习日记五：网络请求
draft: false
tags: [react, rn, fetch]
category: React Native
date: "2017-09-25T23:32:23Z"
math: false
---

开发App经常需要从远程地址中获取数据或资源。我们可能需要给某个REST API发起POST请求以提交用户数据，又或者可能仅仅需要从某个服务器上获取一些静态内容，这一篇介绍一下我们怎样在RN中发起网络请求以及处理相应数据。开始之前可以参考这个[简短的视频教程](http://v.youku.com/v_show/id_XMTUyNTEwMTA5Ng==.html)加深理解，这里也奉上一份参考文档：[Using Fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch)。

# 使用Fetch

React Native提供了和web标准一致的Fetch API，用于满足开发者访问网络的需求。如果你之前使用过XMLHttpRequest(即俗称的ajax)或是其他的网络API，那么Fetch用起来将会相当容易上手。这篇我们只会介绍Fetch的基本用法，并不会讲述太多细节，你可以搜索fetch api关键字来解更多信息。

## 发起网络请求

要从任意地址获取内容的话，只需简单地将网址作为参数传递给fetch方法即可（fetch这个词本身也就是获取的意思）：
```js
fetch('https://mywebsite.com/mydata.json')
```

Fetch还有可选的第二个参数，可以用来定制HTTP请求一些参数。你可以指定header参数，或是指定使用POST方法，又或是提交数据等等：
```js
fetch('https://mywebsite.com/endpoint/', {
  method: 'POST',
  headers: {
    'Accept': 'application/json',
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    firstParam: 'yourValue',
    secondParam: 'yourOtherValue',
  })
})
```
完整的参数列表可以参考：[Fetch Request文档](https://developer.mozilla.org/en-US/docs/Web/API/Request)。

## 处理服务器的响应数据

上面的例子演示了如何发起请求。很多情况下，你还需要处理服务器回复的数据。

网络请求天然是一种异步操作。所以Fetch方法会返回一个Promise，这种模式可以简化异步风格的代码：
```js
getMoviesFromApiAsync() {
    return fetch('https://facebook.github.io/react-native/movies.json')
      .then((response) => response.json())
      .then((responseJson) => {
        return responseJson.movies;
      })
      .catch((error) => {
        console.error(error);
      });
  }
```
你也可以在React Native应用中使用ES7标准中的```async/await```语法：
```js
// 注意这个方法前面有async关键字
  async getMoviesFromApi() {
    try {
      // 注意这里的await语句，其所在的函数必须有async关键字声明
      let response = await fetch('https://facebook.github.io/react-native/movies.json');
      let responseJson = await response.json();
      return responseJson.movies;
    } catch(error) {
      console.error(error);
    }
  }
```
这里别忘了catch住fetch可能抛出的异常，否则出错时你可能看不到任何提示。

当然不熟悉```Promise```和```async/await```的话，可以先参考以下资料：

   https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise

   https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise

   https://cnodejs.org/topic/560dbc826a1ed28204a1e7de

# 使用其他网络库

RN 里面其实也实现了```XMLHttpRequest```接口的。这意味着你可以使用第三方库比如[frisbee](https://github.com/niftylettuce/frisbee)或者[axios](https://github.com/axios/axios)，或者直接使用 XMLHttpRequest 接口。
```js
const request = new XMLHttpRequest();
request.onreadystatechange = (e) => {
  if (request.readyState !== 4) {
    return;
  }

  if (request.status === 200) {
    console.log('success', request.responseText);
  } else {
    console.warn('error');
  }
};

request.open('GET', 'https://mywebsite.com/endpoint/');
request.send();
```
需要注意的是，安全机制与网页环境有所不同：在应用中你可以访问任何网站，没有跨域的限制。

# WebSocket

RN 也是支持[WebSocket](https://developer.mozilla.org/en-US/docs/Web/API/WebSocket)的，拿来做实时应用（如 IM 或者游戏）很方便。如果真的要用WS开发实时应用的话， 强烈推荐一下[socket.io](https://github.com/socketio/socket.io)。

```js
const ws = new WebSocket('ws://host.com/path');

ws.onopen = () => {
  // 打开一个连接

  ws.send('something'); // 发送一个消息
};

ws.onmessage = (e) => {
  // 接收到了一个消息
  console.log(e.data);
};

ws.onerror = (e) => {
  // 发生了一个错误
  console.log(e.message);
};

ws.onclose = (e) => {
  // 连接被关闭了
  console.log(e.code, e.reason);
};
```
现在你的应用已经可以从各种渠道获取数据了。

# Refers
* https://developer.mozilla.org/en-US/docs/Web/API/Request
* https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch
* https://github.com/socketio/socket.io
* https://developer.mozilla.org/en-US/docs/Web/API/WebSocket
* https://github.com/axios/axios


