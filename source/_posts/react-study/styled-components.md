---
title: React学习日记三：styled-components
draft: false
tags: [react, sc, css]
category: React
date: "2017-07-11T23:32:23Z"
math: false
---

网页主要由三部分组成： 结构（ Structure） 、 表现（ Presentation） 和行为（ Behavior）：
* HTML —— 结构， 决定网页的结构和内容（ “是什么”）；  
* CSS —— 表现（ 样式） ， 设定网页的表现样式（ “什么样子”）；
* JavaScript（ JS） —— 行为， 控制网页的行为（ “做什么”）；

其中CSS是很重要的一部分，这就好比你修了一栋结构再好看的建筑，没有装饰，也只是灰色的钢筋水泥，不能给人带来美感。通常我们都是把样式单独提取出来放在```css```文件中，然后在JS或者JSX文件中import css文件即可，写样式也需要花费大量的时间，而这其中 给className命名的问题经常让我头痛，现在我发现了一个很好的解决办法，我觉得省去了一大笔烦恼。
# CSS
CSS怎么写这并不是我想说的，而是这里有一个问题，具体看一下：

我们随便写一段CSS，就叫```style-A.css```:
```css
.red {
  color: red;
  background-color: green;
}
```
然后我们在JSX文件中引用：
```jsx
import './style-A.css'

<ComponentA className = 'red'/>
```
这里有一点点不一样，要使用```className```而不是```class```，因为前面说过，```class```是js的关键字，想要说得不是这个，而是比如还有一个ComponentB：
```css
//style-B.css
.red {
  color: red;
  background-color: blue;
}
```

```jsx
import './style-B.css'

<ComponentB className = 'red'/>
```
那么两个组件的background-color到底是绿色还是蓝色？虽然是两个不同的css文件，但是className都叫'red'，就会产生冲突，这是因为CSS没有变量作用域啊，也就是两个className都叫'red'的组件，不知道到底该用哪个，这就让人头大了呀！

# React内联样式
找到了一个很好的解决办法，可以很好的解决命名冲突的问题。不是名字冲突了吗？那就不要名字了呗！React支持内联样式，可以直接在组件上写样式，把style作为组件的一个属性，很简单，像这样：
```jsx
<Component style = {{color:'red',backgroundColor:'green'}}/>
```
有两层```{}```是因为在传递属性的时候，如果属性值是js值，那就必须用一个```{}```包起来，而style属性的值又是一个object对象所以还有一层```{}```，这个用多了就慢慢习惯了。

但是，，，，自古英雄怕但是，这样写是不用命名了，但把每个组件的样式写在属性里，项目大了代码就很复杂了啊，而且很容易有代码重复的问题，我们经常都在解决这个问题。
# CSS in JS
有个国外大神Christopher Chedeau做过一个叫[CSS in JS](http://blog.vjeux.com/2014/javascript/react-css-in-js-nationjs.htm)的演讲，阐述了很多CSS存在的问题以及解决办法，于是出现了很多'css in js'的第三方库，对于我来说，简单好用是关键，于是朋友给我推荐了Styled Components，用过以后，我只能用一句歌词来表达我的感受，‘确认过眼神，我遇见对的人’
# Styled Components
简单看一下[Styled Components](https://github.com/styled-components/styled-components)是什么：
## 安装
首先在React项目中安装Styled Components：
```bash
$ npm install --save styled-components
```
## 使用
使用方法很简单，基本看一遍就会了
```jsx
import styled from 'styled-components';

//给组件定义样式
const Wrapper = styled.section`
background-color: blue;
margin: 10em;
`;

const Title = styled.h1`
font-size: 1.5em;
color: red;
`;

//然后可以将Wrapper和Title作为Component使用
render(
    <Wrapper>
        <Title>
            Hello styled component!
        </Title>
    </Wrapper>
);
```
## 根据props定义不同样式
Styled Components 可以根据props来定义不同的样式，像这样：
```jsx
import styled from 'styled-components';

//根据isShowClearBtn来控制是否显示Clear Button
const Clear = styled.button`
    float: right;
    position: relative;
    line-height: 20px;
    text-decoration: none;
    cursor: pointer;
    position: relative;
    display:${props => props.isShowClearBtn ? 'block' : 'none'};
    :hover {
	    text-decoration: underline;
    }
`;

render(
    <Clear isShowClearBtn={this.props.isShowClearBtn}>
        Clear Completed
    </Clear>
);
```
## 给任意组件绑定样式
Styled Components其实可以给任何组件绑定样式，比如自己定义的组件，或者第三方库提供的组件，都可以，但用法稍微有些不同，看一下：
```jsx
import styled from 'styled-components';

//给react-router提供的Link组件定义样式
const FilterBtn = styled(Link) `
    color: inherit;
    margin: 3px;
    padding: 3px 7px;
    text-decoration: none;
    border: 1px solid transparent;
    border-radius: 3px;
    cursor: pointer;
    border-color:${props => props.checked ? 'rgba(175, 47, 47, 0.5)' : '#ffffff'};
    :hover {
        border-color: rgba(175, 47, 47, 0.1);
    }
`;

//同样照常使用
render() {
        return (
            <TodoFooter>
                <CountActive>{this.props.todoDoneCount} items left </CountActive>
                <Router>
                    <Filter>
                        <FilterLi>
                            <FilterBtn to='/all' onClick={this.all} checked={this.state.isOnClick === 'all'}>All</FilterBtn>
                            <FilterBtn to='/active' onClick={this.active} checked={this.state.isOnClick === 'active'}>Active</FilterBtn>
                            <FilterBtn to='/completed' onClick={this.completed} checked={this.state.isOnClick === 'completed'}>Completed</FilterBtn>
                        </FilterLi>
                    </Filter>
                </Router>
                <Clear onClick={this.clear} isShowClearBtn={this.props.isShowClearBtn}>Clear Completed</Clear>
            </TodoFooter>
        )
    }
```
也就是其实可以把```styled```看作一个函数，它的参数就是你想设置样式的组件。
## 扩展样式
Styled Components 支持样式的继承，比如上面那个定义好了一个有样式的Button，现在需要第二个Button，除了颜色，其他所有样式都与之前的Button相同，那我们可以这样做：
```jsx
const Clear = styled.button`
    float: right;
    position: relative;
    line-height: 20px;
    text-decoration: none;
    cursor: pointer;
    position: relative;
    display:${props => props.isShowClearBtn ? 'block' : 'none'};
    :hover {
	    text-decoration: underline;
    }
`;

//定义另一个Button
const AnotherButton = Clear.extend`
    color: red;
`;
```
然后同样照常使用就行了。
以后就用Styled Components了！
## Refers
一些参考资料：
* https://github.com/styled-components/styled-components
* https://www.styled-components.com/docs/basics
* https://www.smashingmagazine.com/2017/01/styled-components-enforcing-best-practices-component-based-systems/