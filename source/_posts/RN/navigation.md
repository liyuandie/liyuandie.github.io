---
title: React Native学习日记三：Navigation（导航）
draft: false
tags: [react, rn, react-navigation]
category: React Native
date: "2017-09-11T23:32:23Z"
math: false
---

开发一个App，自然少不了要实现各个页面之间的自由切换与跳转，我们把管理这类功能的工具称为navigator。像我们平时使用频繁的QQ，底部的导航栏，左侧滑出的菜单等，就是navigator的实际应用。某种意义上来说，navigator决定了你的App的结构。

使用React
Native进行开发，现在最火的导航工具应该就是[react-natvigation](https://reactnavigation.org/)了，这是facebook官方推荐使用的导航工具，有原生般的性能体验效果，应该算是React Native导航组件的主流军，而且是用JS实现的，简单易学，很容易上手，这里介绍一下一些基础知识和使用方法。

# 安装

安装很简单，直接在你的RN项目中用npm安装即可，无需其他配置：
```bash
npm install --save react-navigation
```

# Navigators

导航器navigator是react-natvigation中最核心的东西，决定了App页面之间的逻辑层次关系，react-natvigation为我们提供了三种不同类型的navigator：

* StackNavigator：层叠式页面，用于页面跳转和传递参数，新页面会覆盖在旧页面上。
* TabNavigator：默认是底部导航栏，可以实现在同一屏幕下不同页面的切换，类似于QQ底部的三个标签。
* DrawerNavigator：抽屉式导航，主要用于左右两侧菜单栏的实现。

接下来我们分别介绍一下这三种navigator。

## StackNavigator

我们先来看一个具体例子：

```js
import React, { Component } from 'react';
import { Text } from 'react-native';
import { StackNavigator } from 'react-navigation';
import { Icon } from 'react-native-elements';
import Profile from '../views/Profile.js';
import Profile_setting from '../views/Profile_setting'

const ProfileScreen = StackNavigator({
    profile: {
        screen: Profile,
        path: '/',
        navigationOptions: ({ navigation }) => ({
            title: '个人资料',
            headerStyle: {
                backgroundColor: '#BB3D00'
            },
            headerTitleStyle: {
                color: 'white',
                paddingLeft: 75,
                fontWeight: 'normal',
                fontSize: 20
            },
            headerLeft: (
                <Icon
                    name="menu"
                    size={30}
                    type="entypo"
                    iconStyle={{ paddingLeft: 10 }}
                    onPress={() => navigation.navigate('DrawerOpen')}
                    color="white"
                />
            ),
            headerRight: (
                <Icon
                    name="pencil"
                    size={25}
                    type="material-community"
                    iconStyle={{ paddingRight: 10 }}
                    onPress={() => navigation.navigate('profile_setting')}
                    color="white"
                />
            ),
        }),
    },
    profile_setting: {
        screen: Profile_setting,
        path: '/profile/profile_setting',
        ight
    },
});
export default ProfileScreen;
```
这是我学习使用的时候，仿照某社交软件做的个人资料页面。看一下效果图：

![profile](/blogimgs/profile.png)

可以看到主要有两个页面，一个是个人资料的展示页面```profile```，一个是更改个人资料的设置页面```profile_setting```，两个页面采用的是层叠式的导航，点击右上角小铅笔后会跳转到设置页面。注意我们这里所展示的代码并不是两个页面视图本身，而是它们的导航组件，具体的视图界面我们是在别的文件中定义好，然后```import```过来作为组件使用的，我们这里定义的只是两个页面的跳转关系，还有一些导航栏属性，比如title等。

首先，在我们这一视块下，有两个页面，我们可以把```ProfileScreen```看作是一个组件，而它下面包含两个页面，这个组件只是App的一部分，在你点击某处后进入。

我们可以看到，定义一个StackNavigator只需将其···import```,然后在其中定义我们的想要的页面即可，每一个页面可以看作一个对象，对象的内容就是我们对这个页面定义的一些导航属性。那我们来看一下StackNavigator有哪些导航属性：

* navigationOptions：配置StackNavigator的一些属性。如title、headerStyle等。
* screen：定义界面显示的组件，最重要的一个属性，也就是决定我们的页面是哪一个，需要填入import之后的页面。
* mode：定义跳转风格：

```card```：使用iOS和安卓默认的风格。

```modal```：iOS独有的使屏幕从底部画出。类似iOS的present效果 。

* headerMode：返回上级页面时动画效果：

```float```：iOS默认的效果

```screen```：滑动过程中，整个页面都会返回

```none```：无动画

* cardStyle：自定义设置跳转效果：

```transitionConfig```： 自定义设置滑动返回的配置

```onTransitionStart```：当转换动画即将开始时被调用的功能

```onTransitionEnd```：当转换动画完成，将被调用的功能

* path：路由中设置的路径的覆盖映射配置。  
* initialRouteName：设置默认显示的初始页面组件，必须是上面已注册的页面组件，如果不设置，则默认显示第一个页面。  
* initialRouteParams：初始路由参数。

###  navigationOptions

navigationOptions配置页面的一些属性，我们单独来看一下有哪些：

* title：标题，如果设置了这个导航栏和标签栏的title就会变成一样的，不推荐使用
* header：可以设置一些顶部导航的属性，如果隐藏顶部导航栏只要将这个属性设置为null
* headerTitle：设置导航栏标题，推荐
* headerBackTitle：设置跳转页面左侧返回箭头后面的文字，默认是上一个页面的标题。可以自定义，也可以设置为null
* headerTruncatedBackTitle：设置当上个页面标题不符合返回箭头后的文字时，默认改成"返回"
* headerRight：设置导航条右侧。可以是按钮或者其他视图控件
* headerLeft：设置导航条左侧。可以是按钮或者其他视图控件
* headerStyle：设置导航条的样式。背景色，宽高等
* headerTitleStyle：设置导航栏文字样式
* headerBackTitleStyle：设置导航栏‘返回’文字样式
* headerTintColor：设置导航栏颜色
* headerPressColorAndroid：安卓独有的设置颜色纹理，需要安卓版本大于5.0
* gesturesEnabled：是否支持滑动返回手势，iOS默认支持，安卓默认关闭

可以看到我们的例子中，我就单独定义headerLeft，用的第三方库引入的Icon。多试试，就会慢慢掌握这些属性的联系和使用方法。

### 跳转

那么我们是怎么实现跳转的呢？所有注册给 StackNavigator 的组件都会接受到一个navigation参数，在需要跳转的页面，我们在配置navigationOptions的时候传入```navigation```,注意这里是对象解构，然后在我们的代码中就可以使用```navigation.navigate()```方法来实现跳转，比如在我们的代码中，我们为自定义的headerRight绑定了点击回调事件，在点击之后就可以跳转到设置页面：
```js
navigationOptions: ({ navigation }) => ({
            title: '个人资料',
            ...
            headerRight: (
                <Icon
                    name="pencil"
                    size={25}
                    type="material-community"
                    iconStyle={{ paddingRight: 10 }}
                    onPress={() => navigation.navigate('profile_setting')}
                    color="white"
                />
            ),
        }),
```
在同一个StackNavigator下，我们可以用这个方法实现页面间的任意跳转。

## TabNavigator

TabNavigator用来实现底部导航栏的页面管理，具体用法跟StackNavigator大同小异，还是看一个例子：

```js
import React, { Component } from 'react';
import { TabNavigator } from 'react-navigation';
import { Image } from 'react-native';
import { Icon } from 'react-native-elements';
import PictureScreen from '../Views/Picture/Home.js';
import PersonalScreen from '../Views/Personal/Home.js';
import TeamScreen from '../Views/Team/Home.js';

const TabBar = TabNavigator(
  {
    Picture: {
      screen: PictureScreen,
      navigationOptions: {
        title: '众晓',
        tabBarLabel: '海报',
        tabBarIcon: ({ tintColor }) => (
          <Icon name="picture" type="simple-line-icon" color={tintColor} />
        ),
      },
    },
    Team: {
      screen: TeamScreen,
      navigationOptions: {
        title: '团队',
        tabBarIcon: ({ tintColor }) => (
          <Icon name="people" type="simple-line-icon" color={tintColor} />
        ),
      },
    },
    Personal: {
      screen: PersonalScreen,
      navigationOptions: {
        title: '我的',
        tabBarIcon: ({ tintColor }) => (
          <Icon name="user" type="simple-line-icon" color={tintColor} />
        ),
      },
    },
  },
  {
    tabBarPosition: 'bottom',
    animationEnabled: true,
    tabBarOptions: {
      showIcon: true,
      style: {
        backgroundColor: 'white',
        borderTopWidth: 0,
      },
      indicatorStyle: {
        display: 'none',
      },
      iconStyle: {
        width: 25,
        height: 25,
      },
      labelStyle: {
        fontSize: 12,
        marginTop: 0,
        marginBottom: -5,
      },
      tabStyle: {
        height: 49,
      },
      inactiveTintColor: '#595757',
      activeTintColor: '#ff8201',
    },
  },
);

export default TabBar;
```
这是我做的另一个App里的TabNavigator，底部导航栏有三个tab，模式跟手机QQ差不多，可以在同一屏幕下点击三个tab自由切换页面。

我们来看一下TabNavigator的配置属性，相同的一些属性就不作介绍了，看一些不同的：

### navigationOptions:

      title：标题，会同时设置导航条和标签栏的title。  
  
      tabBarVisible：是否隐藏标签栏。默认不隐藏(true)。  
  
      tabBarIcon：设置标签栏的图标。需要给每个都设置。  
  
      tabBarLabel：设置标签栏的title。推荐使用这个来代替title。 
  
### TabNavigatorConfig 
  
    tabBarPosition：设置tabbar的位置，iOS默认在底部，安卓默认在顶部。（属性值：'top'，'bottom'）  
  
    swipeEnabled：是否允许在标签之间进行滑动  
  
    animationEnabled：是否在更改标签时显示动画  
  
    lazy：是否根据需要懒惰呈现标签，而不是提前，意思是在app打开的时候将底部标签栏全部加载，默认false,推荐为true  
  
    initialRouteName： 设置默认显示的页面组件名称
  
    backBehavior：按 back 键是否跳转到第一个Tab(首页)， none 为不跳转  
 
    tabBarOptions：配置标签栏的一些属性

### tabBarOptions
tabBarOptions在ios和安卓下有些许差别，分别介绍：

#### ios

    activeTintColor：label和icon的前景色 活跃状态下  
  
    activeBackgroundColor：label和icon的背景色 活跃状态下  
  
    inactiveTintColor：label和icon的前景色 不活跃状态下  
  
    inactiveBackgroundColor：label和icon的背景色 不活跃状态下  
  
    showLabel：是否显示label，默认开启 style：tabbar的样式

    style：标签栏的样式  
  
    labelStyle：label的样式

    tabStyle：tab的样式

### 安卓  
  
    activeTintColor：label和icon的前景色 活跃状态下  
  
    inactiveTintColor：label和icon的前景色 不活跃状态下  
  
    showIcon：是否显示图标，默认关闭  
  
    showLabel：是否显示label，默认开启 style：tabbar的样式  
  
    labelStyle：label的样式 upperCaseLabel：是否使标签大写，默认为true  
  
    pressColor：material涟漪效果的颜色（安卓版本需要大于5.0）  
  
    pressOpacity：按压标签的透明度变化（安卓版本需要小于5.0）  
  
    scrollEnabled：是否启用可滚动选项卡 tabStyle：tab的样式  
  
    indicatorStyle：标签指示器的样式对象（选项卡底部的行）。安卓底部会多出一条线，可以将height设置为0来暂时解决这个问题  
  
    labelStyle：label的样式  
  
    iconStyle：图标样式

## DrawerNavigator

DrawerNavigator定义左侧菜单栏下的页面。下面是与StackNavigator中介绍的例子同一个App中的DrawerNavigator使用代码：

```js
const MainNavigator = DrawerNavigator(
  {
    Profile: {
      screen: ProfileScreen,
      navigationOptions: {
        drawerLabel: ProfileDrawerItem
      }
    },
    Tantan: {
      screen: TantanScreen,
      navigationOptions: {
        drawerLabel: "探探",
        drawerIcon: (
          <Icon
            name="users"
            color="white"
            size={20}
            type="feather"
            style={{
              width: 45,
              height: 50,
              alignItems: "center",
              justifyContent: "center"
            }}
          />
        )
      }
    },
    Anlian: {
      screen: AnlianScreen,
      navigationOptions: {
        drawerLabel: "匿名暗恋表白",
        drawerIcon: ({ tintColor }) => (
          <Icon
            name="email-open"
            color="white"
            size={20}
            style={{
              width: 50,
              height: 50,
              alignItems: "center",
              justifyContent: "center"
            }}
            type="material-community"
          />
        )
      }
    },
    Setting: {
      screen: SettingScreen,
      navigationOptions: {
        drawerLabel: "设置",
        drawerIcon: ({ tintColor }) => (
          <Icon
            name="settings"
            color="white"
            size={20}
            style={{
              width: 50,
              height: 50,
              alignItems: "center",
              justifyContent: "center"
            }}
            type="feather"
          />
        )
      }
    },
    Guidence: {
      screen: GuidanceScreen,
      navigationOptions: {
        drawerLabel: "新手引导",
        drawerIcon: (
          <Icon
            name="question-circle"
            color="white"
            size={20}
            style={{
              width: 50,
              height: 50,
              alignItems: "center",
              justifyContent: "center"
            }}
            type="font-awesome"
          />
        )
      }
    },
    Share: {
      screen: ShareScreen,
      navigationOptions: {
        drawerLabel: "推荐给好友",
        drawerIcon: (
          <Icon
            name="share-alternative"
            color="white"
            size={20}
            style={{
              width: 50,
              height: 50,
              alignItems: "center",
              justifyContent: "center"
            }}
            type="entypo"
          />
        )
      }
    }
  },
  {
    drawerBackgroundColor: "black",
    initialRouteName: "Tantan",
    drawerWidth: SCREEN_WIDTH * 0.83,
    contentOptions: {
      activeTintColor: "white",
      inactiveTintColor: "#ffffff",
      itemStyle: {
        marginTop: 15,
        paddingLeft: 15
      },
      labelStyle: {
        fontSize: 18,
        marginLeft: 5,
        fontWeight: "normal"
      }
    }
  }
);
```
上效果图，一目了然：

![drawer](/blogimgs/drawer.png)

点击上面的头像，就进入前面说到的资料展示页面，所以其实一共有6个菜单选项，每一个点进去又是一个单独组件，有可能又是一个navigator组件，也有可能就显示单纯的React组件。

还是来看DrawerNavigator的属性，同样，重复的我们就不介绍了，介绍特殊的：

### DrawerNavigatorConfig

    drawerWidth - 抽屉的宽度

    drawerPosition - 选项是左```left```或右```right```。 默认为左侧位置

    contentComponent - 用于呈现抽屉内容的组件，例如导航项。接收抽屉的导航。默认为DrawerItems，也可自定义custom contentComponent使用，后面介绍

    contentOptions - 配置抽屉内容，后面单独介绍

    initialRouteName - 默认显示的页面名称

    order - 定义抽屉项目顺序的routeNames数组
      
    path - 提供routeName到路径配置的映射，它覆盖routeConfigs中设置的路径。

    backBehavior - 后退按钮是否会切换到初始路由？ 如果是，设置为initialRoute，否则为none。 默认为initialRoute

###  contentOptions

contentOptions用来设置抽屉每一项的样式等，具体可设置以下属性：

    activeTintColor - 活动标签的标签和图标颜色

    activeBackgroundColor - 活动标签的背景颜色

    inactiveTintColor - 非活动标签的标签和图标颜色

    inactiveBackgroundColor - 非活动标签的背景颜色

    onItemPress(route) - 绑定标签被点击时的回调事件

    itemsContainerStyle - 标签栏的样式

    itemStyle - 图标和标签的样式

    labelStyle - 当您的标签是字符串时，会覆盖itemStyle中的文本样式

    iconContainerStyle - 图标的容器的样式

### Navigation Options
    drawerLabel：定义标签内容，一般为```string```,也可以是自定义的组件

    drawerIcon:定义标签前面的图标;

### custom contentComponent
有时我们希望抽屉的内容不止是默认的DrawerItem，即不满足与文字和图标，这时我们可以自定义一个contentComponent，然后传入即可，比如我们上面的头像那一块，我们来看一下是怎么实现的：

```js
const ProfileDrawerItem = props => (
  <View style={{ flex: 1 }}>
    <View
      style={{
        marginTop: 20,
        marginBottom: 40,
        justifyContent: "center",
        alignItems: "center"
      }}
    >
      <Avatar large rounded source={require("./src/images/eason.png")} />
      <Text style={{ color: "white", lineHeight: 40, fontSize: 20 }}>
        好啊那你抱紧啊
      </Text>
      <Text style={{ color: "white", lineHeight: 20, fontSize: 15 }}>
        点击查看/编辑
      </Text>
    </View>
  </View>
);
```
整个这一段代码，其实就是我们所看到头像和下面的昵称，就是一个很简单的用React Native自己实现的小组件，然后在Profile页面的drawerLabel设置为我们自定义的这个组件就可以了，我们说了，一切都是组件：

```js
    ...
Profile: {
      screen: ProfileScreen,//这里就是第一个例子介绍StackNavigator
      navigationOptions: {
        drawerLabel: ProfileDrawerItem
      }
    },
    ...
```
项目完整代码在我的github上可以看到：https://github.com/liyuandie/TantanRN
# 总结

react-navigation简单易懂，上手容易，能满足我们的各种需求，在React Native 0.44版本后就已经中将Navigator删除。个人很推荐这个库作为代替Navigator的工具，不接受任何反驳，毕竟这也是facebook官方推荐的项目，有兴趣的同学可以尝试用一下，很好用。

另外，这里有一个react-navigation做的体验demo，可以拿expo扫描二维码体验一下：[NavigationPlayground](https://expo.io/@react-navigation/NavigationPlayground)

![demo](/blogimgs/NavigationPlayground.png)

# Refers
* http://gitbook.cn/gitchat/column/5a17c2e113c02f4a35ca5a7d/topic/5a17d8f713c02f4a35ca652b
* https://reactnavigation.org/docs/api-reference.html
* https://reactnavigation.org/docs/getting-started.html








