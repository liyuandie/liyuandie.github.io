---
title: React Native学习日记四：UI库的使用（React Native Elements）
draft: false
tags: [react, rn, react-native-elements]
category: React Native
date: "2017-09-20T23:32:23Z"
math: false
---

在使用React Native的过程中，一直困扰我一个问题，原生组件的样式太过单一，做出的App样式不够美观，甚至可以说，太丑了啦！而自己去定义各种组件的样式、包装组件未免太过麻烦了，那样我们会花大把时间在写组件的样式上，我就想，有没有别人为我们包装好的组件，可以直接使用呢？一番搜索之后，还真发现了一个开箱即用的第三方UI组件库：[React Native Elements](https://react-native-training.github.io/react-native-elements/)。

这是一个基于React Native的原生组件包装好的UI组件库，组件样式齐全，种类多，基本能满足我们做应用UI的需求，而且同样也是跨平台使用，可以直接在RN项目中应用，具有以下特点：
* 跨平台：跨Android、iOS和web一致的设计！
* 使用方便：完全使用Javascript实现，并且支持expo项目！
* 完美定制：随心所欲定制我们自己需要的组件！
* 社区主导：100%由社区构建，完全开源的项目！

所以，这一篇我们来介绍一下React Native Elements的使用。开始之前，这里有React Native Elements做的一个体验demo：[react-native-elements-app](https://github.com/react-native-training/react-native-elements-app):

![rne-demo](/blogimgs/rne-demo.png)

拿出手机，使用expo扫描上面的二维码，就可以尽情体验React Native Elements的各种组件了！

# Getting Started

React Native Elements提供一个全功能的UI套件，用于在原生应用程序中创建应用程序。许多伟大的UI组件都是围绕开源开发的。React Native Elements提供具有一致API和外观的现成套件。跟随我的脚步开始使用它吧！

## 安装
如果你的项目是使用```expo```或```create-react-native-app```创建的，那么你需要做的，就只是安装react-native-elements，因为React Native Elements中需要用到react-native-vector-icons，而expo和CRNA已经为我们封装好了该icon库。

```bash
yarn add react-native-elements
# or with npm
npm install --save react-native-elements
```

而如果你的项目是用```react-native init```初始化的RN项目，你需要执行以下步骤：

1.安装react-native-elements：

```bash
yarn add react-native-elements
# or with npm
npm i react-native-elements --save
```
2.安装react-native-vector-icons：

```bash
yarn add react-native-vector-icons
# or with npm
npm i --save react-native-vector-icons
```
3.执行link：

```bash
react-native link react-native-vector-icons
```

然后，你就可以在你的项目中用起来了。

## 组件

React Native Elements提供了成套的包装好的组件，我们咋可以直接将这些组件拿来使用，然后通过设置一些参数，就可以制作属于你自己的与众不同的组件。这里我介绍几个常用的：

### Button

我们知道，React Native提供的的Button组件是没有样式的，而这里的Button组件，可以轻松自定义Button的样式，先看一下基础用法：

```js
import { Button } from 'react-native-elements';

<Button
  title="登录"
  loading
  loadingProps={{ size: "large", color: "rgba(111, 202, 186, 1)" }}
  titleStyle={{ fontWeight: "700" }}
  buttonStyle={{
    backgroundColor: "rgba(92, 99,216, 1)",
    width: 300,
    height: 45,
    borderColor: "transparent",
    borderWidth: 0,
    borderRadius: 5
  }}
  containerStyle={{ marginTop: 20 }}
/>
```
看一下Button的一些props：
1. title：传入一个```string```，定义Button的标题。
2. titleStyle：传入值为样式对象，定义标题的文本样式。
3. buttonStyle：传入值为样式对象，定义Button的样式。
4. clear：设置为```true```可以使Button透明，默认为```false```。
5. disabled：设置为```false```按钮将不可点击，默认为```true```。
6. icon：定义Button的图标，未设置title时将代替title，设置了title将显示在title左侧。

### Icon

Icon采用[material icon](https://design.google.com/icons/)的name来作为prop,可以在[react-native-vector-icons directory](https://oblador.github.io/react-native-vector-icons/)搜索你想要的图标。

看一下基础用法：
```js
import { Icon } from 'react-native-elements'

<Icon
  raised
  name='heartbeat'
  type='font-awesome'
  color='#f50'
  onPress={() => console.log('hello')} 
/>
```
Icon的props：
1. name：icon的名字，是必须的，直接决定icon是哪一个material icon。
2. type：icon的默认type是```material```，可以使用```material-community, zocial, font-awesome, octicon, ionicon, foundation, evilicon, simple-line-icon, feather or entypo```中任意一种来覆盖默认类型。
3. size：icon的尺寸，传入一个数字即可。
4. color：icon的颜色。
5. iconStyle：自定义样式来继承并覆盖默认样式。
6. onPress：绑定点击回调事件。
7. reverse：翻转icon的颜色与背景颜色，通常用来使Icon看起来像一个按钮。
8. raised：设置为```true```会给icon增加一个box shadow，默认为```false```。
9. reverseColor：定义翻转后的图标颜色。

完整属性参考：https://react-native-training.github.io/react-native-elements/docs/icon.html

### List & ListItem
用来自定义列表组件，看一个例子：
```js
import React, { Component } from 'react';
import { StyleSheet} from 'react-native';
import { Text, Button, Icon, List, ListItem } from 'react-native-elements';

const list = [
    {
        title: '个人信息',
        subtitle: '编辑姓名等',
        detail: 'Info',
        icon: <Icon
            name="user-circle"
            size={22}
            color='blue'
            type='font-awesome'
            containerStyle={{ paddingRight: 25 }}
        />
    },
    {
        title: '隐私与权限',
        subtitle: '联系人，我的相册，朋友圈',
        detail: 'authority',
        icon: <Icon
            name="key"
            size={22}
            color='orange'
            type='font-awesome'
            containerStyle={{ paddingRight: 25 }}
        />

    },
    {
        title: '消息提醒与聊天',
        subtitle: '通知，聊天设置',
        detail: 'message',
        icon: <Icon
            name="chat"
            size={23}
            color='green'
            containerStyle={{ paddingRight: 24 }}
        />
    },
    {
        title: '数据和缓存',
        subtitle: '数据和缓存设置',
        detail: 'data',
        icon: <Icon
            name="settings-box"
            size={25}
            color='pink'
            type='material-community'
            containerStyle={{ paddingRight: 23 }}
        />
    },
    {
        title: '帐号与安全',
        subtitle: '修改手机号和密码',
        detail: 'safe',
        icon: <Icon
            name="lock"
            size={26}
            color='purple'
            containerStyle={{ paddingRight: 23 }}
        />
    },
]

class MyList extends Component {
    render() {
        return (
           <List containerStyle={styles.listContainer} >
               {list.map((l, i) => (
                   <ListItem
                       key={i}
                       title={l.title}
                       subtitle={l.subtitle}
                       leftIcon={l.icon}
                       containerStyle={styles.ListItemContainer}
                       titleStyle={{ fontSize: 15, }}
                       subtitleStyle={{ fontWeight: 'normal' }}
                       onPress={() => navigation.navigate(l.detail)}
                   />
                ))}
           </List>
        )
    }
}

const styles = StyleSheet.create({
    listContainer:{
        marginTop: 20, 
        borderTopWidth: 0, 
        borderBottomWidth: 0,
    },
    ListItemContainer:{
        borderTopWidth: 0,
        borderBottomWidth: 0,
        marginLeft: 10,
        height: 65
    },
})
```
这是之前提到的防探探应用的设置页面的一部分，看一下效果图：

![list](/blogimgs/list.png)

注意每一个ListItem都必须带一个key，渲染时候才会正确渲染，然后这里用到了以前我们介绍的map方法。

来看一下List和ListItem的属性：
#### List Props：
List只有一个prop，就是containerStyle，默认的值为：
```js
{
  marginTop: 20, 
  borderTopWidth: 1, 
  borderBottomWidth: 1, 
  borderBottomColor: #cbd2d9
}
```
可以自己传入一个样式对象来覆盖这些样式。
#### ListItem props
1. hideChevron：设置为```true```可以隐藏右侧Chevron。
2. chevronColor：设置Chevron的颜色。
3. containerStyle：定义容器的样式。
4. fontFamily：自定义字体，ios下默认为HelveticaNeue，android下默认为Sans Serif。
5. title：定义显示的title文本。
6. titleNumberOfLines：设置title的行数。
7. titleStyle：title的文本样式。
8. titleContainerStyle：title的容器的样式。
9. subtitle：设置title下显示的副标题。
10. leftIcon：设置左侧图标，传入值可以是一个object {name, color, style, type} (type defaults to material icons)，也可以是一个React Native组件，上图中我们使用的是Icon。
11. onPress：点击事件。

完整属性参考：https://react-native-training.github.io/react-native-elements/docs/lists.html

# 总结 
暂时介绍这么多，基本用法已经介绍，更多组件的使用，可以参考：[官方文档](https://react-native-training.github.io/react-native-elements/docs/overview.html)

其实各种组件的使用方法都大同小异，都是直接import过来使用，然后设置一些参数即可，这里我就不一一介绍了，我相信官网写的会比我这里介绍的更有权威性，总之我觉得这是一个很好的UI库，希望能帮助大家更好的使用React Native进行开发！




