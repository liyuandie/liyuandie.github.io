---
title: React Native学习日记一：基础知识
draft: false
tags: [react, expo, rn]
category: React Native
date: "2017-09-02T23:32:23Z"
math: false
---
# 前记

最近开始学习React Native，同样，将学习过程中一些有用的知识点和心路历程记录在这里。

React Native的知识点大部分跟React是相同的，会React的话，学起RN来会非常简单，很容易上手，Web开发的应用有一定局限性，所以掌握一点原生应用的开发也是非常有必要的。总的来说，RN的开发思想跟React是基本一致，同样都是组件化的开发思想，而不同的在于一些原生API的使用、样式与布局的写法以及原生组件的不同。但是，会React的基础下，会很容易掌握这些东西。

话不多说，跟随我的脚步，一起进步吧！
# Getting Started
[Create React Native App](https://github.com/react-community/create-react-native-app)是创建一个React Native应用最简单的办法，不需要安装或配置任何工具来构建本地代码 - 不需要安装Xcode或Android Studio，对与初学者来说，是一个很好的Starter项目。

首先全局安装Create React Native App：

```bash
npm install -g create-react-native-app
```
然后这时你就可以用```create-react-native-app```命令来创建你自己的项目了：
```bash
create-react-native-app RN-demo

cd RN-demo
npm start
```
这时会在本地起一个development server，并生成一个二维码，如下图所示：
![QR](/blogimgs/QR.png)

然后这时你需要在手机上安装[expo](https://expo.io/learn)软件，打开软件，扫描这个二维码，然后你就可以看到你的应用在expo软件中运行起来了。这里要注意，扫描二维码时需要手机与电脑在同一wifi下。

![RNstart](/blogimgs/RNstart.png)

轻轻摇晃手机，打开开发菜单，打开Hot Realoading：

![menu](/blogimgs/menu.png)

在```App.js```中顺便修改一点东西，不用手动刷新，你就会神奇的发现，你做的修改已经在应用中加载出来了！

# Components

我们已经顺利的创建了自己的React Native应用，接下来就看看我们怎样使用React Native进行开发吧！

先从一些基本的显示组件开始。在 Web 领域最基础的显示组件如 div、p、h1、ul、li、span、img 等在 React Native 里都有类似对应的显示组件，我们先来介绍几个用得比较多的。

## View

View相当于```div```，可以自己指定高度height和宽度width，如果不指定大小的话，它的大小将取决于子组件的大小。

## Text

Text类似于```p```或者说```span```，主要用于包文字。在React Native中，所有文字必须包在Text组件中，基本用法如下：

```jsx
<View style={{height:300, width:500}}>
        <Text>Hello React Native!</Text>
        <Text>This is my fisrt React Native App.</Text>
      </View>
```

## ScrollView（滚动视图）

ScrollView用于包裹一列组件，可以是不同的组件，并可以设置一些属性来控制我们的滚动视图。

```jsx
<ScrollView>
      <Text style={{ backgroundColor: 'green' }}>Hello React Native!</Text>
      <Text style={{ backgroundColor: 'yellow' }}>line 2!</Text>
      <Image
        style={{ width: 250, height: 250 }}
        source={{ uri: 'http://e.hiphotos.baidu.com/baike/pic/item/ac6eddc451da81cb6de1cb4d5a66d0160924312e.jpg' }}
      />
    </ScrollView>
```
### horizontal属性
当此属性为true的时候，所有的子视图会在水平方向上排成一行，而不是默认的在垂直方向上排成一列。默认值为false。

## FlatList

FlatList的作用与ScrollView，但是ScrollView只适用于子组件数量不多的情况，因为即使子组件没有在屏幕里出现，也会渲染出ScrollView所包的所有子组件，子组件太多会有内存占用的问题，而FlatList只会渲染出出现在当前屏幕中的元素，但是它比较适用于数据结构比较类似的元素，我们看一下它的用法就知道了：

```jsx
<FlatList
    data={[
        {key: 'Devin'},
        {key: 'Jackson'},
        {key: 'James'},
        {key: 'Joel'},
        {key: 'John'},
        {key: 'Jillian'},
        {key: 'Jimmy'},
        {key: 'Julie'},
    ]}
    renderItem={({item}) => <Text>{item.key}</Text>}
/>
```
FlatList 有两个props，一个是```data```,用于传递数据，一个是```renderItem```,用于将```data```里的每一个元素渲染成格式化的组件。

## Image
相当于Web中```img```,用于显示多种不同类型的图片，通过传递source属性来获取图片资源。其中source的值可以通过```require```方法来请求本地图片：

```jsx
<Image
    style={styles.icon}
    source={require('./icon.png')}
  />
```
也可以通过传入```url```获取网络图片：

```jsx
<Image
  style={styles.logo}
  source={{url:'http://facebook.github.io/react/img/logo_og.png'}}
/>
```
然后我们可以通过设置resizeMode属性来决定当组件尺寸和图片尺寸不成比例的时候如何调整图片的大小。

* ```cover```: 在保持图片宽高比的前提下缩放图片，直到宽度和高度都大于等于容器视图的尺寸（如果容器有padding内衬的话，则相应减去）。译注：这样图片完全覆盖甚至超出容器，容器中不留任何空白。

* ```contain```: 在保持图片宽高比的前提下缩放图片，直到宽度和高度都小于等于容器视图的尺寸（如果容器有padding内衬的话，则相应减去）。译注：这样图片完全被包裹在容器中，容器中可能留有空白。

* ```stretch```: 拉伸图片且不维持宽高比，直到宽高都刚好填满容器。

* ```repeat```: 重复平铺图片直到填满容器。图片会维持原始尺寸。仅iOS可用。

* ```center```: 居中不拉伸。

例如：

```jsx
<Image source={require('./icon.png')} resizeMode='center' />
```

## TextInput

TextInput相当于Web中的```<input type="text" />```,主要用于文字输入。最简单的用法就是丢一个TextInput到应用里，然后订阅它的onChangeText事件来读取用户的输入。注意，从TextInput里取值这就是目前唯一的做法！也就是使用在onChangeText中用setState把用户的输入写入到state中，然后在需要取值的地方从this.state中取出值。看一个简单的例子：

```jsx
<TextInput
    underlineColorAndroid="transparent"
    style={styles.input}
    onChangeText={text => this.setState({ phoneNum: text })}
  />
```
需要注意的是在android和ios下默认样式有些不同，按需调整样式即可。

## Button

Button想当于Web中的button，可以指定title，同样可以通过onPress来绑定被点击后的回调事件，看一个基础用法:

```jsx
<Button
  onPress={() => { Alert.alert('You tapped the button!')}}
  title="Press Me"
  color="red"
/>
```

# 样式与布局

## 样式
前面我们提到，React中的样式可以用styled component来写，实际写法还是CSS那一套，而React Native中有更简单的写法，我们可以直接用Javascript来写样式。

React Native中每个基础组件都有一个基础属性：```style```，它的值可以是一个普通的对象，稍微有些不同的是，因为我们是用JS来写的，所以写的时候要用驼峰格式（camelCase），看一个例子：

```jsx
<Text style={{color:'blue',fontSize:18}}>Hello React Native</Text>
```
在一个文件中，我们可以将所有组件的样式提取出来，用React Native提供的```StyleSheet```的```create```方法来创建，然后在组件中直接引用就行了。

```jsx
import React, { Component } from 'react';
import { StyleSheet, Text, View } from 'react-native';

export default class LotsOfStyles extends Component {
  render() {
    return (
      <View>
        <Text style={styles.red}>just red</Text>
        <Text style={styles.bigblue}>just bigblue</Text>
        <Text style={[styles.bigblue, styles.red]}>bigblue, then red</Text>
        <Text style={[styles.red, styles.bigblue]}>red, then bigblue</Text>
      </View>
    );
  }
}

const styles = StyleSheet.create({
  bigblue: {
    color: 'blue',
    fontWeight: 'bold',
    fontSize: 30,
  },
  red: {
    color: 'red',
  },
});
```
可能你已经发现了，style的值可以是对象，也可以是对象数组，后面的样式会继承兵覆盖前面的样式，这样代码复用更方便了。

## 指定大小（设置height与width）

我们可以通过给style prop指定height和width，来确定组件的尺寸大小，要注意的是，指定height和width的值的时候不需要带单位，直接用数字就行，包括使用padding等值的时候也是一样。

```jsx
import React, { Component } from 'react';
import { View } from 'react-native';

export default class FixedDimensionsBasics extends Component {
  render() {
    return (
      <View>
        <View style={{width: 50, height: 50, backgroundColor: 'powderblue'}} />
        <View style={{width: 100, height: 100, backgroundColor: 'skyblue'}} />
        <View style={{width: 150, height: 150, backgroundColor: 'steelblue'}} />
      </View>
    );
  }
}
```
## flex布局
 直接指定height和width适用与不论屏幕大小，尺寸都固定的组件，更多时候我们需要组件的尺寸随屏幕大小的改变而改变，这时我们需要用到flex布局，我们称之为弹性布局，主要核心思想是，我们通过指定父组件的flex属性来确定子组件在其中的排列方式，使子组件可以根据这个来分配占据屏幕的大小。

 flex布局方式使用起来很方便，也很容易理解，这里分享两篇阮一峰老师写的关于flex布局的介绍，写的很详细，可以看一下:
 * [Flex 布局教程：语法篇](http://www.ruanyifeng.com/blog/2015/07/flex-grammar.html?^%$)
 * [Flex 布局教程：实例篇](http://www.ruanyifeng.com/blog/2015/07/flex-examples.html)

# 总结

这一篇的基础知识先介绍这么多，总的来说，组件的使用方法与React出入不大，样式与布局方面有些不同，但是使用多了自然也就习惯了，给我的感觉是，使用起来很方便。推荐一篇中文文档，不熟悉英文的可以参考一下：

[React Native中文文档](https://reactnative.cn/docs/0.51/getting-started.html#content)
