---
title: React学习日记五：React Router
draft: false
tags: [react, react-router, URL]
category: React
date: "2017-08-02T23:32:23Z"
math: false
---

上一篇中介绍的Todos小应用中用到了[React Router](https://github.com/ReactTraining/react-router/)，应该是React中使用最多的router了。这里分享一些React Router的基本知识。
# 路由
先了解一下什么是路由吧。

[URL](https://en.wikipedia.org/wiki/URL)在Web中扮演非常重要的角色，通过URL，我们才能对应的找到我们需要的Web资源。

现在的网站，大多都不再是静态网页，JS的兴起，使我们可以让很多页面资源通过JS在前端动态生成。比如进入某宝网站首页，我们看到的是琳琅满目的商品，当某件商品吸引了你的注意的时候，你点开查看商品详情，理论上而言，这时可以在不刷新页面的情况下，通过发送Ajax请求等方法，可以动态的渲染出你想要东西，可是假设你要把这件东西分享给朋友，如果没有URL，这时你分享出去的地址，只能是你打开的首页地址，朋友哪里知道你说的是哪一件商品。所以URL的作用就来了，如果我们给不同的商品分配一个独一无二的URL，在你访问网站的时候，带上这个URL，就可以对应的找到你想要的商品，这样就方便了很多啊。

听说以前服务端渲染的时候，都是后端完成这项工作。现在我们可以在前端通过JS动态改变页面内容的时候，同时动态的给生成的资源分配一个URL或改变URL，保持页面内容与URL的一致性，这就是路由的作用了。

# React Router
React Router是React生态环境下使用最多的Router了，跟React组件化的开发思想很贴合，所以使用起来很方便。React Router官方文档推荐也我们使用facebook官方出的脚手架工具Create React App，这不正是我们所使用的吗？
## Quick Start
首先安装react-router-dom：

```bash
npm install react-router-dom
# or, if you're using yarn
yarn add react-router-dom
```

这里要注意一点，在V4版本以后，React Router将一些重要的东西抽取出来，分成了Web和Native两部分，我们在做Web开发的时候，要引用的是[react-router-dom](https://reacttraining.com/react-router/web/guides/philosophy)这一套，然后就可以在你的项目中用起来了。

## 基本组件
React Router中有三种类型的组件：路由器组件，路由匹配组件和导航组件。分别了解一下：

### 路由器
每个React Router应用程序的核心应该是一个路由器组件。对于Web项目，```react-router-dom```提供```BrowserRouter```和```HashRouter```路由器。这两个都会为你创建一个```history```对象。一般来说，如果你有一台响应请求的服务器，那么使用```BrowserRouter```，如果你使用的是静态文件服务器，则应该使用```HashRouter```。

假设我们使用```BrowserRouter```，我们使用的时候可以这样引用：
```jsx
import BrowserRouter as Router from "react-router-dom";
```

这样便于我们书写代码。

### 路线匹配（Route Matching）
有两个路由匹配组件：```Route```和```Switch```。

Route基本用法如下：

```jsx
<Route path="/" component={Home} />
<Route path="/about" component={About} />
<Route path="/topics" component={Topics} />
```
我们刚才说了，BrowserRouter会为我们创建一个history对象，当history中的```location.pathname```与Route中的```path```相匹配的时候，就会渲染后面的component，而component就是我们自己定义的一些组件，这个很好理解，比如当```location = { pathname: '/about' }```时，就会渲染出```Home```组件和```About```组件。为啥渲染出两个组件呢？因为这种匹配方式是一种前缀匹配，所有匹配到```/```前缀的页面都会渲染出```Home```组件。

那么有时候我们不想在任何页面都显示出主页内容该怎么办呢？

很简单，我们可以这样：
```jsx
<Route exact path="/" component={Home} />
<Route path="/about" component={About} />
<Route path="/topics" component={Topics} />
```
注意到我们```path="/"```的前面加了关键字```exact```，这有什么什么用呢？当我们加了exact，这时便是精确匹配，也就是只有当```pathname === "/"```时，才会渲染Home组件，这样就不要担心Home组件出现在每一个页面了。

那Switch呢？我们可以用Switch将Route包起来，像这样：

```jsx
<Switch>
  <Route exact path="/" component={Home} />
  <Route path="/about" component={About} />
  <Route path="/topics" component={Topics} />
</Switch>
```
如果我们不用Switch将Route包起来，在匹配到一个以后，还会继续往下匹配，更多时候我们需要的是在当前路径与某个path完成匹配后，就结束匹配，就像switch语句，Switch将迭代其所有子Route元素，并仅渲染与当前位置匹配的第一个元素。也就是说，Switch只渲染一个Route！

一般我们还会在最后增加一个Route，像这样：
```jsx
<Route exact path="/" component={Home} />
<Route path="/about" component={About} />
<Route path="/topics" component={Topics} />
<Route component={NoMatch}/>
```
该Route的path为空，也就是当用户输入路径与以上路径都不匹配时，就会渲染NoMatch组件，我们可以自己定义NoMatch组件，比如404。。。

## 基本用法
我们来看一个完整的基本例子：
```jsx
import React from "react";
import { BrowserRouter as Router, Route, Link } from "react-router-dom";

const BasicExample = () => (
  <Router>
    <div>
      <ul>
        <li>
          <Link to="/">Home</Link>
        </li>
        <li>
          <Link to="/about">About</Link>
        </li>
        <li>
          <Link to="/topics">Topics</Link>
        </li>
      </ul>

      <hr />

      <Route exact path="/" component={Home} />
      <Route path="/about" component={About} />
      <Route path="/topics" component={Topics} />
    </div>
  </Router>
);

const Home = () => (
  <div>
    <h2>Home</h2>
  </div>
);

const About = () => (
  <div>
    <h2>About</h2>
  </div>
);

const Topics = ({ match }) => (
  <div>
    <h2>Topics</h2>
    <ul>
      <li>
        <Link to={`${match.url}/rendering`}>Rendering with React</Link>
      </li>
      <li>
        <Link to={`${match.url}/components`}>Components</Link>
      </li>
      <li>
        <Link to={`${match.url}/props-v-state`}>Props v. State</Link>
      </li>
    </ul>

    <Route path={`${match.url}/:topicId`} component={Topic} />
    <Route
      exact
      path={match.url}
      render={() => <h3>Please select a topic.</h3>}
    />
  </div>
);

const Topic = ({ match }) => (
  <div>
    <h3>{match.params.topicId}</h3>
  </div>
);

export default BasicExample;
```
我们可以看到，首先整个页面是被一个Router包起来的，这是React Router规定的，所有组件必须用Router包起来，这样才会为我们生成history对象，完成我们的路径匹配。

然后一开始是一个div，包了一个列表，这可以看作是一个导航栏，而每一个导航按钮是一个```Link```，你可以把它看作一个```a```标签，这React Router为我们提供的组件，后面```to```带一个URL，这样我们点击以后，就动态的更改了URL，路径变化，所渲染的东西也就随之变化。

然后就是一堆Route，这个前面已经介绍过了，就不用多说了。

接下来就是我们自己定义的一些无状态组件，很简单，基本都是返回一个简单的```h2```标签。有点不同的是Topics组件，我们来看一下：
```jsx
const Topics = ({ match }) => (
  <div>
    <h2>Topics</h2>
    <ul>
      <li>
        <Link to={`${match.url}/rendering`}>Rendering with React</Link>
      </li>
      <li>
        <Link to={`${match.url}/components`}>Components</Link>
      </li>
      <li>
        <Link to={`${match.url}/props-v-state`}>Props v. State</Link>
      </li>
    </ul>

    <Route path={`${match.url}/:topicId`} component={Topic} />
    <Route
      exact
      path={match.url}
      render={() => <h3>Please select a topic.</h3>}
    />
  </div>
);
```
当我们点击导航栏的Topics时，会渲染出Topics组件，这没问题，但Topics页面下又有自己子导航栏，我们给Topics组件传入参数match，这时可以通过```match.url```获取到当前的URL，然后就用到了我们之前讲到的ES6新语法，字符串插值，将子导航栏下的路径拼接到当前URL后面，于是就产生了新的URL，但仍然是在我们的```"/topics"```路径下生成。

同样标签栏下面，我们需要设置Route，可以看到我们为点击任何子导航栏内容时，此时路径仍然是```"/topics"```，所以此时渲染的组件除了```h2```标签和子导航栏还有一个我们自己定义```h3```标签，当点击某个子导航栏下的Link标签后，URL发生变化，我们设置了另一个Route，相当于监听这一变化，这时匹配到这个Route，渲染的组件发生变化，在不刷新页面的情况下完成动态刷新。

最后定义了一个无状态组件叫Topic，也就是我们子导航栏没一项所对应的内容：
```jsx
const Topic = ({ match }) => (
  <div>
    <h3>{match.params.topicId}</h3>
  </div>
);
```
同样也是传入参数match，通过```match.params.topicId```获取到topicId，作为我们```h3```标签的内容来显示，这样我们只需要定义一个组件就可以完成动态加载内容。

至于match是哪儿来的，刚刚我们说过，每个被Router包着的页面都会动态的生成一个history对象作为props传入，而match就是这个对象中的一个内容，实在不清楚将props打出来就能一目了然了，我也不会去专门记住里面有什么，总之我们可以在里面取到我们想要的东西就是了。

## 权限管理Redirect(跳转)
了解了React Router的基本用法，我们来看一个它另一个复杂一点的用法。

上一篇用React实现一个Todos应用中，我们在Todos应用里实现了一个模拟登录，在我们没有登录的时候，显示的是登录界面，完成登录后，完成权限验证，才能进入应用的功能界面，整个大概流程就是这样，其中我们就用到了Redirect，可以很好的帮我们完成这一需求。那我们就还是用Todos应用作为例子来看一下是怎么用的。

完整代码就不在这里展示了，详情参考上一篇：[用React实现一个Todos应用](https://yanzulee.me/react-study/todos)

我们就我们的登录需求来说一下。首先看整个应用:
```jsx
class App extends Component {
  render() {
    return (
      <Router>
        <div>
          <Route component={AuthBtn} />
          <Switch>
            <Route exact path="/login" component={Login} />
            <PrivateRoute exact path="/" component={TodosAll} />
            <PrivateRoute path="/all" component={TodosAll} />
            <PrivateRoute path="/active" component={TodosActive} />
            <PrivateRoute path="/completed" component={TodosCompleted} />
          </Switch>
        </div>
      </Router>
    );
  }
}
```
一目了然，首先用Router包起来没得说，然后是一个path为空的Route，也就是说在任何颜面都会显示AuthBtn，也就是应用左上角的提示，在登录状态下会显示用户名和退出登录的按钮，未登录状态下会提示请登录，我们说了，登录与未登录我们作为state存在```this.state.hasLogin```中，这个很简单，不作赘述。

然后下面是用Switch包起来的一堆Route，第一个就是当路径与```"/login"```匹配时，渲染登录界面，也就是未登录状态。我们来看Login组件的render函数：
```jsx
render() {
    let from = { pathname: "/" };
    if (this.state.hasLogin) {
      return <Redirect to={from} />;
    }
    return (
      <TodoLogin>
        <TodoappH1>Log in please! </TodoappH1>
        <Input
          type="text"
          placeholder="请输入帐号"
          value={this.state.username}
          onChange={this.setUser}
        />
        <Input
          type="password"
          placeholder="请输入密码"
          value={this.state.password}
          onChange={this.setPassword}
        />
        <LoginBtn
          type="primary"
          loading={this.state.isLoading}
          icon="login"
          onClick={this.login}
        >
          登录
        </LoginBtn>
      </TodoLogin>
    );
  }
```
可以看到，render函数执行时会做一个判断，如果```this.state.hasLogin```为```true```，那么通过Redirect组件进行跳转，Redirect带一个to，即需要跳转至的页面路径；如果为```false```那么就渲染登录界面。

再说到Redirect，这里带的to的路径为```"/" ```，也就是说这时候便动态更改了URL，那么URL一改变，对应的资源也就随之改变。回过头看App：
```jsx
<PrivateRoute exact path="/" component={TodosAll} />
```
说明这时会渲染TodosAll这个组件，也就是我们的App的功能主体。这个流程其实就是未登录-登录的状态切换。这里有点不一样，我们不是使用的Route，而是PrivateRoute，我们来看一下它是什么：
```jsx
const PrivateRoute = ({ component: Component, ...rest }) => {
  let isLogin = JSON.parse(localStorage.getItem("isLogin"));
  return (
    <Route
      {...rest}
      render={props =>
        isLogin ? (
          <Component {...props} />
        ) : (
          <Redirect
            to={{
              pathname: "/login",
              state: { from: props.location }
            }}
          />
        )}
    />
  );
};
```
可能看出来了，其实PrivateRoute也是Route，只不过是经过了我们包装的Route。PrivateRoute这个组件接收参数，然后返回一个Route，```component: Component```是将组件作为参数传入，```...rest```传入props中剩余的所有参数，这是对象解构的写法。然后从localStorage中取到登录状态，同样做一个判断，如果是登录状态，则渲染我们传入的组件，如果是未登录，那么这里又会用一个Redirect跳转到登录界面。也就是说，我们点击了右上角的退出登录后，```isLogin```值变为```false```,这时Redirect带的to路径为```"/login"```，会与下面这个PrivateRoute的path相匹配，完成跳转，即登录-未登录这一流程。
```jsx
<Route exact path="/login" component={Login} />
```
又回到了我们的登录界面，同样在输入用户名和密码后，完成登录验证，在Login组件中又会做判断，又做一次跳转，动态渲染出TodosAll组件。

可能到这儿有人会有疑问，为什么要跳转来跳转去，我们直接用state控制是否登录控制刷新渲染不久行了吗？别忘了，我们说路由的功能是什么，我们是为了保证一个URL对应一个页面内容啊，直接用state控制渲染，是可以实现登录界面的和App功能界面的转换，但是那样URL并不会改变啊。所以我们要用Redirect来实现改变页面内容的同时，保证URL随之变化，这样才是我们想要的URL与资源一一对应的效果。

# 总结
关于React Router的使用暂时先写这么多吧，还有很多高级用法，希望能慢慢摸索到精髓。总之记住一句话，我们使用路由的目的，是为了保证URL与资源的一一对应。