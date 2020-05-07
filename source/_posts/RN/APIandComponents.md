---
title: React Native学习日记二：React Native 内置 API 和组件
draft: false
tags: [react, rn]
category: React Native
date: "2017-09-07T23:32:23Z"
math: false
---

学习一个框架，首先要了解它的一些基本知识、设计思想、基本用法等，然而我们真正用起来的时候，更多的是需要花很多时间来熟悉它一些API等，比如React Native，我们需要花时间来熟悉它的一些基础组件的使用，一些功能性的接口，这样才能真正在我们的工作中使用起来，上一篇我们已经介绍了一些基础常用的UI组件，这篇我们再来介绍几个我们能常用的，然后介绍一些内置的API，如发送网络请求、获取屏幕大小等。

# 组件

React Native 内置的组件有一些基础的组件，一些跨平台使用的组件，还有ios或者android平台特定的组件等，这里就只介绍一些能夸平台使用的组件，平台特定组件我们暂时不介绍，等用到的时候我们再来讨论。

## Picker

Picker是可以在iOS和Android上渲染原生的选择器，基础用法如下：
 ```jsx
<Picker
  selectedValue={this.state.language}
  onValueChange={(lang) => this.setState({language: lang})}>
  <Picker.Item label="Java" value="java" />
  <Picker.Item label="JavaScript" value="js" />
</Picker>
 ```
 介绍一些属性：
 ### onValueChange ```function```

 某一项被选中时执行此回调。调用时带有如下参数：

    itemValue: 被选中项的value属性
    itemPosition: 被选中项在picker中的索引位置

### selectedValue ```any```

默认选中的值。可以是字符串或整数。

## Slider 

Slider主要用于我们选择一个范围值的时候：

```jsx
<Slider
    value={3}         // 初始值
    disabled={false}  // 如果为 true，则无法选择
    minimumValue={1}  // 最小值
    maximumValue={10} // 最大值
    step={1}          // 步长
    onSlidingComplete={value => this.setState({ sliderFinalValue: value })}                     // 只在最后完成的时候触发
    onValueChange={value => this.setState({ sliderValue: value })} // 拖动选择过程中一直触发
/>
```
## ScrollView

上一篇已经简单介绍过ScrollView，这里详细介绍一下它的一些属性和方法，主要有：

* 设置horizontal为true，可以设置成横向滑动，默认为false。
* onMomentumScrollBegin，开始滑动会执行一次。
* onMomentumScrollEnd，滑动结束会执行一次。
* onScroll，滑动过程中会持续触发此回调函数，可以通过
scrollEventThrottle 设置回调函数执行频率。
* 设置pagingEnabled为true 可以一整屏的翻页。
* refreshControl，配置下拉刷新。
* stickyHeaderIndices，配置某些子元素不滚动，注意横向滑动的时候此参数无效。
* scrollTo(y: number | { x?: number, y?: number, animated?: boolean }, x: number, animated: boolean),滚动到指定的x, y偏移处。第三个参数为是否启用平滑滚动动画。
* scrollToEnd，移动到最后（如果是竖向滚动就是最下面，如果是横向滚动就是最右面）。

```jsx
onRefresh = () => {
    this.setState({ refreshing: true });
    setTimeout(() => { // 模拟异步加载数据
      // fetch data
      this.setState({ refreshing: false });
    }, 1000);
  }

        <Button title="ScrollTo" onPress={() => {
          this.refs.scrollView.scrollTo({ x: 300, y: 400, animated: true });
        }} />
        <Button title="ScrollToEnd" onPress={() => {
          this.refs.scrollView.scrollToEnd({ animated: true });
        }} />
        <ScrollView
          ref="scrollView"
          horizontal={true}
          onMomentumScrollBegin={e => Alert.alert('onMomentumScrollBegin: ' + e)}
          onMomentumScrollEnd={e => Alert.alert('onMomentumScrollEnd: ' + e)}
          pagingEnabled={true}
          stickyHeaderIndices={[0]} // 滑动的时候第一个不动
          refreshControl={
            <RefreshControl
              refreshing={this.state.refreshing}
              onRefresh={this.onRefresh}
            />
          }
        >
          {
            ([1, 2, 3, 4, 5]).map(i => (
              <View key={i}>
                <Text>image: {i}</Text>
                <Image
                  style={{ width: 250, height: 250 }}
                  source={{ uri: 'http://e.hiphotos.baidu.com/baike/pic/item/ac6eddc451da81cb6de1cb4d5a66d0160924312e.jpg' }}
                />
              </View>
            ))
          }
        </ScrollView>
```
关于refreshControl使用，参考：

* https://reactnative.cn/docs/0.51/refreshcontrol.html

## FlatList

FlatList是一种惰性渲染，也就是说只会渲染出屏幕里显示的那部分，这点我们之前提过，这对于列表内容很多的时候是一个很好用的组件，我们来详细看一下它的用法，先看一个例子：
```jsx
import React from 'react';
import {
  FlatList,
  Text,
  TouchableHighlight
} from 'react-native';

const MyItem = ({ title, age, selected, onPress }) => {
  return <TouchableHighlight
    style={{
      height: 50,
      backgroundColor: selected ? 'red' : 'white',
    }}
    onPress={() => onPress(title)}
  >
    <Text>this is {title}, age is: {age}...</Text>
  </TouchableHighlight>
}

class MyList extends React.Component {
  state = { selected: new Map() };

  _onPressItem = title => {
    const selected = this.state.selected;
    selected.set(title, !selected.get(title));
    this.setState({
      selected: selected,
    })
  };

  _renderItem = (props) => {
    const item = props.item;
    return <MyItem
      title={item.title}
      age={item.age}
      onPress={this._onPressItem}
      selected={!!this.state.selected.get(item.title)}
    />
  };

  render() {
    return (
      <FlatList
        style={{
          height: 500,
          borderColor: 'red',
          borderWidth: 1,
        }}
        data={this.props.data}
        renderItem={this._renderItem}
        numColumns={1}
        keyExtractor={(item, index) => item.title}
      />
    );
  }
}

export default MyList;

// 使用方
        <MyList
          data={[
            { title: 'java', age: 20 },
            { title: 'js', age: 21 },
            { title: 'rust', age: 18 },
            { title: 'python', age: 19 },
          ]}
        />
```
用到的属性：

* numColumns:指定布局列数，多列布局只能在非水平模式下使用，即必须是horizontal={false}。
* keyExtractor: (item: ItemT, index: number) => string ：此函数用于为给定的item生成一个不重复的key。Key的作用是使React能够区分同类元素的不同个体，以便在刷新时能够确定其变化的位置，减少重新渲染的开销。若不指定此函数，则默认抽取item.key作为key值。若item.key也不存在，则使用数组下标。

其他属性和方法的用法，参见：
* https://reactnative.cn/docs/0.51/flatlist.html#content

## Modal

一个可以自定义的弹窗，Modal组件可以用来覆盖包含React Native根视图的原生视图。
```jsx
<Modal
    animationType={'slide'}
    transparent={true}
    visible={this.state.visible}
    onRequestClose={() => this.setState({ visible: false })}
>
    <TouchableOpacity style={{ flex: 1 }} onPress={() => this.setState({ visible: false })}>
        <View style={styles.modalContainer}>
            <View style={styles.modalView}>
                <Text style={styles.modalText}>填写信息</Text>
            <View>
                <Text style={styles.name}>昵称</Text>
                <View style={styles.inputContainer}>
                    <TextInput
                      placeholder={this.state.userName}
                      underlineColorAndroid="transparent"
                      style={styles.input}
                      onChangeText={text => this.setState({ text })}
                    />
                  </View>
                <View style={styles.button_container}>
                    <Button
                      title="保存"
                      buttonStyle={[styles.button, { width: 280, borderRadius: 0 }]}
                      fontSize={20}
                      onPress={() => {
                        this.changeName(this.state.text);
                        this.setState({ visible: false });
                      }}
                    />
                </View>
            </View>
        </View>
      </View>
    </TouchableOpacity>
</Modal>
```
这是模拟的一个用户点击更改资料后弹出用于更改用户昵称的弹窗，用到的属性有：

* animationType：用来指定Modal弹出的动画效果，有'none', 'slide', 'fade'三种。
* transparent：设置为true,Modal将覆盖在一个透明的背景上。
* visible：用来控制Modal是否弹出，true时弹出，false时关闭，存在this.stata.visible中。
* onRequestClose：当弹窗关闭时指定的回调。

# API介绍

API是指不在界面显示的纯功能性接口，简单介绍几个常用的：

## Dimensions（屏幕尺寸）
使用Dimensions可以获取到屏幕尺寸，用法很简单：

```jsx
import {Dimensions} from 'react-native';

const SCREEN_WIDTH = Dimensions.get('window').width;//获取屏幕宽度

const styles = StyleSheet.create({
  image: {
    height: 220,
    width: SCREEN_WIDTH,//直接使用
    resizeMode: 'stretch',
  }，
})
```
这里有一点需要注意，屏幕发生旋转的时候不会出发重新渲染，获取到的屏幕尺寸不会改变，解决这个问题有两种办法:
* 监听屏幕改变事件，设置 state，触发渲染。
* 将获取屏幕尺寸写入生命周期函数中。

## Alert

跟Web中的```alert```一样，弹出一个提示框显示传入的内容：

```jsx
Alert.alert('注意，这里必须是字符串！如果是 object 或者 arr 会 crash！');
```
这里有点不同的是，我们是使用的Alert的alert方法，并非直接使用Alert。

## APP状态

APP状态有三种：

* active - 应用正在前台运行。
* background - 应用正在后台运行。用户既可能在别的应用中，也可能在桌面。
* inactive - 此状态表示应用正在前后台的切换过程中，或是处在系统的多任务视图，又或是处在来电状态中。

要获取当前的状态，你可以使用AppState.currentState，这个变量会一直保持更新。不过在启动的过程中，currentState可能为null，直到AppState从原生代码得到通知为止。

用法参见:https://reactnative.cn/docs/0.51/appstate.html#content

## 判断平台
React Native也没有完全做到说真正的跨平台使用，所以我们有时候会针对不同的平台做一些事情，使用Platform.OS可以获取到当前的设备平台：

```jsx
import {
    Text,
    Platform
} from 'react-native';

<Text>当前平台是：{Platform.OS}, Version: {Platform.Version}</Text>//直接使用
```
# 总结

暂时就介绍这么多了，介绍的都是我在实际项目中用到过的，React Native为我们提供的可不止这些，有更多需求的话可以直接上[官方文档](https://facebook.github.io/react-native/docs/getting-started.html)查看学习使用方法就行了。

