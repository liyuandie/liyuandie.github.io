---
title: React学习日记四：用React实现一个Todos应用
draft: false
tags: [react, sc]
category: React
date: "2017-07-20T23:32:23Z"
math: false
---

最近学完React的基本概念，闲下来的时候就自己写了一个Todos的小应用。这里做个简略的说明，才疏学浅，不是想给新手看，我也是新手，只是希望大家相互交流共同进步！
# 开始之前
我想先说一说我为什么要学React。因为React有很多优点，再者，很容易上手，现在也很流行，所以弄潮儿嘛，就学咯！那React有什么优点呢？我按照我的理解简单说一下：
1. React速度很快：它并不直接对DOM进行操作，引入了一个叫做虚拟DOM的概念，安插在javascript逻辑和实际的DOM之间，性能好。
2. 跨浏览器兼容：虚拟DOM帮助我们解决了跨浏览器问题，它为我们提供了标准化的API，浏览器兼容性很好。
3. 一切都是component：代码更加模块化，重用代码更容易，可维护性高。
4. 热更新！

那有人会说：“那你为啥不学Vue？”      emmmm...因为我比较帅。

我们来看一下做的Todos应用需要有哪些功能：
* 可以在最上面的input里，使用回车来添加任务。
* 在中间的任务列表里，由checkbox来控制任务的状态。
* 已完成的任务有一个line-through的样式。
* 当鼠标移到每一个任务时，都会出现删除按钮提供删除。
* 在顶部有一个全选按钮，用于控制所有的任务状态。
* 还有未完成任务数的显示。
* 可以分类显示全部、已完成和未完成。
* 可以清空已完成的任务。
* 模拟一个登录界面，所有用户数据存储在localStorage中。

差不多就是这些了，最后效果图如下：

![todos demo](/blogimgs/todos.png)

上面就是我们需要用React实现的功能，完整代码在我的github上可以看到，可以用作参考：[React-Todos](https://github.com/liyuandie/react/tree/master/todo)

我并不是要一步一步在这里讲怎么做完这个应用的，可以结合各个组件逐个分析一下我们是怎么做的。另外我的应用里用到了[React Router](https://github.com/ReactTraining/react-router/)，不熟悉的话可以参考一下：[官方文档](https://reacttraining.com/react-router/)
# 组件
React是组件化的开发思想，而“函数就是组件，组件就是函数”，铭记这句话。推荐一个up主的视频，可以很好的帮助理解这句话：[React30分钟快速入门](https://www.bilibili.com/video/av14447835/)

然后我们来逐个组件看一下：
## App组件
先来看一看总的组件，也就是App：
```jsx
import React, { Component } from "react";
import {
  BrowserRouter as Router,
  Route,
  Redirect,
  Switch,
  withRouter
} from "react-router-dom";
import styled from "styled-components";
import { TodosAll, TodosActive, TodosCompleted } from "./Todos.jsx";

const LogoutBtn = styled.button`
  padding: 0 15px;
  font-size: 14px;
  border-radius: 4px;
  height: 32px;
  font-weight: 500;
  touch-action: manipulation;
  cursor: pointer;
  border: 1px solid transparent;
  user-select: none;
  color: rgba(0, 0, 0, 0.65);
`;
const AuthP = styled.p`text-align: right;`;
const TodoLogin = styled.section`
  background: #fff;
  margin: 130px 0 40px 0;
  position: relative;
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 25px 50px 0 rgba(0, 0, 0, 0.1);
`;
const TodoappH1 = styled.h1`
  position: absolute;
  top: -155px;
  width: 100%;
  font-size: 60px;
  font-weight: 200;
  text-align: center;
  color: rgba(175, 47, 47, 0.15);
  text-rendering: optimizeLegibility;
  :matches(article, aside, nav, section) {
    font-size: 1.5em;
    -webkit-margin-before: 0.83em;
    -webkit-margin-after: 0.83em;
  }
`;
const Input = styled.input`
  padding: 16px 16px 16px 60px;
  border: none;
  background: rgba(0, 0, 0, 0.003);
  box-shadow: inset 0 -2px 1px rgba(0, 0, 0, 0.03);
  position: relative;
  margin: 0;
  width: 100%;
  font-size: 24px;
  font-family: inherit;
  font-weight: inherit;
  line-height: 1.4em;
  outline: none;
  color: inherit;
  box-sizing: border-box;
  font-smoothing: antialiased;
`;
const LoginBtn = styled.button`
  padding: 0 15px;
  margin: 10px 40%;
  font-size: 14px;
  border-radius: 4px;
  height: 32px;
  font-weight: 500;
  touch-action: manipulation;
  cursor: pointer;
  border: 1px solid transparent;
  user-select: none;
  color: rgba(0, 0, 0, 0.65);
  background-color: #fff;
  border-color: #d9d9d9;
`;

class Login extends Component {
  constructor() {
    super();
    this.state = {
      user: "",
      password: "",
      hasLogin: false,
      isLoading: false
    };
  }

  setUser = e => {
    this.setState({ user: e.currentTarget.value });
  };

  setPassword = e => {
    this.setState({ password: e.currentTarget.value });
  };

  login = () => {
    if (this.state.user === "" || this.state.password === "") {
      alert("请输入完整的用户名和密码！");
      return;
    }
    localStorage.setItem("isLogin", "true");
    localStorage.setItem("username", this.state.user);
    fakeAuth.login(() => {
      this.setState({ hasLogin: true });
    });
    this.setState({ isLoading: true });
  };

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
}

const fakeAuth = {
  login(cb) {
    setTimeout(cb, 2000);
  },
  signout(cb) {
    localStorage.setItem("isLogin", "false");
    setTimeout(cb, 100);
  }
};

const AuthBtn = withRouter(({ history }) => {
  let user = localStorage.getItem("username");
  let isLogin = JSON.parse(localStorage.getItem("isLogin"));
  return isLogin ? (
    <AuthP>
      {user},welcome!<LogoutBtn
        type="button"
        onClick={() => {
          fakeAuth.signout(() => history.push("/"));
        }}
      >
        退出登录
      </LogoutBtn>
    </AuthP>
  ) : (
    <AuthP>请登录</AuthP>
  );
});
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

export default App;
```
用ES6写React最大的不同就是，组件可以通过继承React.Components来得到，并且初始化state也不需要冗长的getInitalialState，直接在构造函数里操作this.state即可。对整个App来说，我们一开始显示是登录界面，然后在Login组件中用```this.state.hasLogin```来控制是否处于登录状态：
```jsx
class Login extends Component {
  constructor() {
    super();
    this.state = {
      user: "",
      password: "",
      hasLogin: false,
      isLoading: false
    };
  }
```
在完成登录步骤后，点击登录按钮，用```setState()```方法将```this.state.hasLogin```值更新为```true```，并将用户名和密码一并存入localStorage：
```jsx
login = () => {
    if (this.state.user === "" || this.state.password === "") {
      alert("请输入完整的用户名和密码！");
      return;
    }
    localStorage.setItem("isLogin", "true");
    localStorage.setItem("username", this.state.user);
    fakeAuth.login(() => {
      this.setState({ hasLogin: true });
    });
    this.setState({ isLoading: true });
  };
、、、
<LoginBtn
          type="primary"
          loading={this.state.isLoading}
          icon="login"
          onClick={this.login}
        >
          登录
        </LoginBtn>
```
这时会触发更新渲染，通过React Router提供的Redirect组件跳转至Home界面：
```jsx
 let from = { pathname: "/" };
 if (this.state.hasLogin) {
   return <Redirect to={from} />;
  }
```
至于Home组件怎么实现，我们稍后介绍。最后我们将整个App渲染到DOM上即可：
```jsx
import App from "./App.jsx";

ReactDOM.render(<App />, document.getElementById("root"));
```

## Home组件
Home组件，也就是我们App的功能主体，具体代码如下：
```jsx
import React from "react";
import AddItem from "./AddItem.jsx";
import TodoList from "./TodoList.jsx";
import TodoFoot from "./TodoFoot.jsx";
import styled, { injectGlobal } from "styled-components";

const TodoApp = styled.div`
  background: #fff;
  margin: 130px 0 40px 0;
  position: relative;
  box-shadow: 0 2px 4px 0 rgba(0, 0, 0, 0.2), 0 25px 50px 0 rgba(0, 0, 0, 0.1);
`;
const AnotherFooter = styled.footer`
  margin: 65px auto 0;
  color: #bfbfbf;
  font-size: 10px;
  text-shadow: 0 1px 0 rgba(255, 255, 255, 0.5);
  text-align: center;
`;

injectGlobal`
body {
    font: 14px 'Helvetica Neue', Helvetica, Arial, sans-serif;
    line-height: 1.4em;
    background: #f5f5f5;
    color: #4d4d4d;
    min-width: 230px;
    max-width: 550px;
    margin: 0 auto;
    font-smoothing: antialiased;
    font-weight: 300;
    padding: 0;
}

input:matches([type="radio"], [type="checkbox"]){
    margin-top: 3px;
    margin-right: 2px;
    margin-bottom: 3px;
    margin-left: 2px;
    padding-top: initial;
    padding-right: initial;
    padding-bottom: initial;
    padding-left: initial;
    background-color: initial;
    border-top-color: initial;
    border-top-style: initial;
    border-top-width: initial;
    border-right-color: initial;
    border-right-style: initial;
    border-right-width: initial;
    border-bottom-color: initial;
    border-bottom-style: initial;
    border-bottom-width: initial;
    border-left-color: initial;
    border-left-style: initial;
    border-left-width: initial;
}
input{
    margin-top: 0em;
    margin-right: 0em;
    margin-bottom: 0em;
    margin-left: 0em;
    font-style: normal;
    font-weight: 400;
    font-size: 11px;
    font-family: -apple-system;
    font-variant-caps: normal;
    color: initial;
    letter-spacing: normal;
    word-spacing: normal;
    line-height: normal;
    text-transform: none;
    text-indent: 0px;
    text-shadow: none;
    display: inline-block;
    text-align: start;
    
    outline: none;
}

li {
    display: list-item;
    text-align: -webkit-match-parent;
}
label {
    cursor: default;
}
button {
    margin: 0;
    padding: 0;
    border: 0;
    background: none;
    font-size: 100%;
    vertical-align: baseline;
    font-family: inherit;
    font-weight: inherit;
    color: inherit;
    appearance: none;
    font-smoothing: antialiased;
    outline: none;
}
`;

class Home extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      todos: localStorage.getItem("Todos")
        ? JSON.parse(localStorage.getItem("Todos"))
        : [],
      filter: props.filter ? props.filter : "all",
      isAllChecked: false
    };
  }

  addTodo = todoItem => {
    this.state.todos.unshift(todoItem);
    this.setState({ todos: this.state.todos });
    localStorage.setItem("Todos", JSON.stringify(this.state.todos));
  };

  changeTodoState = (id, isDone, isChangeAll = false) => {
    if (isChangeAll) {
      this.setState({
        todos: this.state.todos.map(todo => {
          todo.isDone = isDone;
          return todo;
        }),
        isAllChecked: isDone
      });
    } else {
      for (let todo of this.state.todos) {
        if (todo.id === id) {
          todo.isDone = isDone;
        }
      }
      this.setState({ todos: this.state.todos });
    }
    localStorage.setItem("Todos", JSON.stringify(this.state.todos));
  };

  clearDone = () => {
    let todos = this.state.todos.filter(todo => !todo.isDone);
    this.setState({
      todos: todos
    });
    localStorage.setItem("Todos", JSON.stringify(this.state.todos));
  };

  deleteTodo = index => {
    this.state.todos.splice(index, 1);
    this.setState({ todos: this.state.todos });
    localStorage.setItem("Todos", JSON.stringify(this.state.todos));
  };

  filterTodo = p => {
    this.setState({
      filter: p
    });
  };

  newTodo = () => {
    const status = this.state.filter;
    let newtodos = [];
    if (status === "active") {
      newtodos = this.state.todos.filter(todo => !todo.isDone);
    } else if (status === "completed") {
      newtodos = this.state.todos.filter(todo => todo.isDone);
    } else if (status === "all") {
      newtodos = this.state.todos;
    }
    return newtodos;
  };

  render() {
    let props = {
      todoDoneCount:
        (this.state.todos && this.state.todos.filter(todo => !todo.isDone))
          .length || 0,
      isShowClearBtn: this.state.todos.filter(todo => todo.isDone).length > 0
    };
    return (
      <div>
        <TodoApp>
          <AddItem
            todos={this.state.todos}
            addTodo={this.addTodo}
            changeTodoState={this.changeTodoState}
          />
          <TodoList
            deleteTodo={this.deleteTodo}
            todos={this.newTodo()}
            changeTodoState={this.changeTodoState}
            id={this.state.todos.id}
          />
          <TodoFoot
            clearDone={this.clearDone}
            {...props}
            filterTodo={this.filterTodo}
          />
        </TodoApp>
        <AnotherFooter>
          <p>Created by Yanzu.Lee</p>
        </AnotherFooter>
      </div>
    );
  }
}

const TodosAll = () => {
  return <Home filter="all" />;
};
const TodosActive = () => {
  return <Home filter="active" />;
};
const TodosCompleted = () => {
  return <Home filter="completed" />;
};
export { TodosAll, TodosActive, TodosCompleted };
```
### 状态state
我们知道React的主流思想就是，所有的state状态和方法都是由父组件控制，然后通过props传递给子组件，形成一个单方向的数据链路，保持各组件的状态一致。所以我们在这个父组件Home上，看的东西稍微有点多。一点点来看：
```jsx
constructor(props) {
    super(props);
    this.state = {
      todos: localStorage.getItem("Todos")
        ? JSON.parse(localStorage.getItem("Todos"))
        : [],
      filter: props.filter ? props.filter : "all",
      isAllChecked: false
    };
  }
```
在constructor内，我们先是初始化了state，分别有三个，一个是todos的列表，存在localStorage中，一个是filter状态，一个是所有的todos是否全选的状态。
### 方法
```jsx
// 添加一个任务，参数是一个todoItem的object
addTodo = todoItem => {...
  };

// 改变任务的状态，id是第几个，isDone是状态，isChangeAll是控制全部状态的
changeTodoState = (id, isDone, isChangeAll = false) => {...
  }; // 参数默认为false

// 清空已完成
clearDone = () => {...
  };

// 删除面板上第几个任务
deleteTodo = index => {...
  };
  
// 改变filter
filterTodo = p => {...
  };

// 控制渲染状态
newTodo = () => {...
  };
  
// react用于渲染的函数
render() {...
}
```
我们可以从render函数看到整个组件的结构，可以看到其实结构非常简单，就是上中下。上面的AddItem自然就是用来输入任务的地方，中间就是展示并操作todos列表的，而底部就是显示数据并提供筛选操作的，而最底部就是一个简单的Footer。
```jsx
render() {
    let props = {
      todoDoneCount:
        (this.state.todos && this.state.todos.filter(todo => !todo.isDone))
          .length || 0,
      isShowClearBtn: this.state.todos.filter(todo => todo.isDone).length > 0
    };
    return (
      <div>
        <TodoApp>
          <AddItem
            todos={this.state.todos}
            addTodo={this.addTodo}
            changeTodoState={this.changeTodoState}
          />
          <TodoList
            deleteTodo={this.deleteTodo}
            todos={this.newTodo()}
            changeTodoState={this.changeTodoState}
            id={this.state.todos.id}
          />
          <TodoFoot
            clearDone={this.clearDone}
            {...props}
            filterTodo={this.filterTodo}
          />
        </TodoApp>
        <AnotherFooter>
          <p>Created by Yanzu.Lee</p>
        </AnotherFooter>
      </div>
    );
  }
```
我们可以看到，其他的方法都是传到子组件上，就不一一详细说如何实现的了。总体的思想就是，方法在父组件定义，通过props传给需要的子组件进行调用传参，最后返回到父组件上执行函数，存储数据、改变state并触发重新渲染。方法并不需要bind(this)，因为我们使用的是箭头函数。
## AddTodo组件
```jsx
import React from "react";
import styled from 'styled-components';

const TodoInput = styled.input`
    padding: 16px 16px 16px 60px;
    border: none;
    background: rgba(0, 0, 0, 0.003);
    box-shadow: inset 0 -2px 1px rgba(0, 0, 0, 0.03);
    position: relative;
    margin: 0;
    width: 100%;
    font-size: 24px;
    font-style: italic;
    font-family: inherit;
    font-weight:  100;
    line-height: 1.4em;
    outline: none;
    color: black;
    box-sizing: border-box;
    font-smoothing: antialiased;
`
const Allchecked = styled.input`
    transform: rotate(90deg);
    appearance: none;
    outline: none;
    position: absolute;
    top: 15px;
    left: -12px;
    width: 60px;
    height: 34px;
    text-align: center;
    border: none;
    background: none;
    :before {
        content: '❯';
        font-size: 22px;
        color: #e6e6e6;
        padding: 10px 27px 10px 27px;
    }
    :checked:before {
        color: #737373;
    }
`
const Title = styled.h1`
    position: absolute;
    top: -155px;
    width: 100%;
    font-size: 100px;
    font-weight: 100;
    text-align: center;
    color: rgba(175, 47, 47, 0.15);
    text-rendering: optimizeLegibility;
    :matches(article, aside, nav, section) {
        font-size: 1.5em;
        -webkit-margin-before: 0.83em;
        -webkit-margin-after: 0.83em;
    }
`

class AddTodo extends React.Component {

    allChangeState = (event) => {
        this.props.changeTodoState(null, event.target.checked, true);
    }

    keyUp = (event) => {
        if (event.keyCode === 13) {
            let value = event.target.value;
            if (!value) return false;
            let newID = 0;
            if (this.props.todos.length > 0) {
                newID = this.props.todos[0].id + 1;
            }
            let newTodoItem = {
                text: value,
                isDone: false,
                id: newID,
            };
            event.target.value = "";
            this.props.addTodo(newTodoItem);
        }
    }

    render() {
        return (
            <header>
                <Title>todos</Title>
                <TodoInput onKeyUp={this.keyUp} type="text" placeholder="what needs to be done ?" />
                <Allchecked type="checkbox" checked={this.props.isAllChecked} onChange={this.allChangeState} />
            </header>
        )
    }
}

export default AddTodo;
```
到了子组件，方法就没那么多了，一般子组件就是绑定事件。可以看到在子组件绑定了keyUp事件，用来确定回车键并调用父组件传来的addTodo()，将新生成的todo任务作为参数传入。
## TodoItem组件
```jsx
import React from "react";
import styled from 'styled-components';

const CheckBox = styled.input`
    height: 40px;
    text-align: center;
    width: 40px;
    height: auto;
    position: absolute;
    top: 10px;
    bottom: 0;
    margin: auto 0;
    border: none;
    appearance: none;
    :after {
        content: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="-10 -18 100 135"><circle cx="50" cy="50" r="50" fill="none" stroke="#ededed" stroke-width="3"/></svg>');
    }
    :checked:after {
        content: url('data:image/svg+xml;utf8,<svg xmlns="http://www.w3.org/2000/svg" width="40" height="40" viewBox="-10 -18 100 135"><circle cx="50" cy="50" r="50" fill="none" stroke="#bddad5" stroke-width="3"/><path fill="#5dc2af" d="M72 25L42 71 27 56l-4 4 20 20 34-52z"/></svg>');
    }
`
const TodoLi = styled.li`
    position: relative;
    font-size: 24px;
    border-bottom: 1px solid #ededed;
`

const TodoSpan = styled.label`
    white-space: pre-line;
    word-break: break-all;
    padding: 15px 60px 15px 15px;
    margin-left: 45px;
    display: block;
    line-height: 1.2;
    transition: color 0.4s;
    color:  ${props => props.isDone ? '#d9d9d9' : 'black'};
    text-decoration: ${props => props.isDone ? 'line-through' : 'none'};
`
const Delete = styled.button`
    display: ${props => props.isShowDelete ? 'block': 'none'};
    position: absolute;
    top: 0;
    right: 10px;
    bottom: 0;
    width: 40px;
    height: 40px;
    margin: auto 0;
    font-size: 30px;
    color: #cc9a9a;
    margin-bottom: 11px;
    border: none;
    background: none;
    transition: color 0.2s ease-out;
    :hover {
        color: #af5b5e;
    }
    :after {
        content: '×';  
    }
`

class TodoItem extends React.Component {
    constructor() {
        super();
        this.state = {
            isShowDelete: false,
        };
    }

    showDeleteBtn = ()=> {
        this.setState({isShowDelete: true});
    }

    hideDeleteBtn = ()=> {
        this.setState({isShowDelete: false});
    }

    changeState = () => {
        let isDone = !this.props.isDone;
        this.props.changeTodoState(this.props.id, isDone);
    }

    DeleteItem = () => {
        this.props.deleteTodo(this.props.index);
    }

    render() {
        return (
            <TodoLi onMouseEnter = {this.showDeleteBtn} onMouseLeave = {this.hideDeleteBtn}>
                <CheckBox type="checkbox" checked={this.props.isDone} onChange={this.changeState} />
                <TodoSpan isDone={this.props.isDone}>{this.props.text}</TodoSpan>
                <Delete isShowDelete = {this.state.isShowDelete} onClick={this.DeleteItem}></Delete>
            </TodoLi>
        )
    }
}
export default TodoItem;
```
此组件就是定义具体每一条todo任务，每条任务有一个CheckBox来完成或取消完成任务，然后是任务描述，最后是一个删除按钮，通过isShowDelete来控制是否显示，当鼠标悬浮于某条任务上时，通过对onMouseEnter和onMouseLeave绑定回调事件来改变isShowDelete的状态。
## Todos组件
```jsx
import React from "react";
import TodoItem from "./TodoItem.jsx"
import styled from 'styled-components';


const Main = styled.section`
    position: relative;
    z-index: 2;
    border-top: 1px solid #ededed;
`
const TodoUl = styled.ul`
    margin: 0;
    padding: 0;
    list-style: none;
`

class Todos extends React.Component {
    TodoList = () => {
        return (
            <Main>
                <TodoUl>
                    {this.props.todos.map((todo, index) => {
                        return <TodoItem {...todo} {...this.props} id={todo.id} key={todo.id} />
                    })}
                </TodoUl>
            </Main>
        )
    }

    render() {
        return <this.TodoList/>;
    }
}
export default Todos;
```
此组件是整个任务列表，是TodoItem组件的父组件，又通过筛选其父组件传递下来的props中的todos，来渲染显示任务列表。
## TodoFoot组件
```jsx
import React from "react";
import styled from 'styled-components';
import {
    BrowserRouter as Router,
    Link
} from 'react-router-dom'

const TodoFooter = styled.footer`
    color: #777;
    padding: 10px 15px;
    height: 20px;
    text-align: center;
    border-top: 1px solid #e6e6e6;
    :before {
        content: '';
        position: absolute;
        right: 0;
        bottom: 0;
        left: 0;
        height: 50px;
        overflow: hidden;
        box-shadow: 0 1px 1px rgba(0, 0, 0, 0.2), 0 8px 0 -3px #f6f6f6, 0 9px 1px -3px rgba(0, 0, 0, 0.2), 0 16px 0 -6px #f6f6f6, 0 17px 2px -6px rgba(0, 0, 0, 0.2);
    }
`
const CountActive = styled.span`
    float: left;
    text-align: left;
`
const Filter = styled.ul`
    margin: 0;
    padding: 0;
    list-style: none;
    position: absolute;
    right: 0;
    left: 0;
    height: 19.2px;
`
const FilterLi = styled.li`
    display: inline;
`
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
`
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
`

class TodoFoot extends React.Component {

    constructor() {
        super();
        this.state = {
            isOnClick: '',

        };
    }

    clear = () => {
        this.props.clearDone();
    }

    all = () => {
        this.props.filterTodo("all");
        this.setState({ isOnClick: 'all' });
    }


    active = () => {
        this.props.filterTodo("active");
        this.setState({ isOnClick: 'active' });
    }

    completed = () => {
        this.props.filterTodo("completed");
        this.setState({ isOnClick: 'completed' });
    }

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
}
export default TodoFoot;
```
这是底部组件，上面显示未完成的任务数、三个筛选按钮、以及清除已完成任务的按钮，点击三个筛选按钮时，向上传递父组件的filterTodo方法的参数，调用该函数，改变父组件中的filter这一state，从而触发渲染，达到控制显示不同状态任务的目的。
# 总结
回过头看Todos应用，会觉得React带给我们的组件化的思想用起来太舒服了。我们通过父组件来控制状态，并通过props传递，来保证组件内的状态一致。我们可以非常有效的维护我们的交互代码，因为我们一眼就知道，哪个事件属于哪个组件管理。对于props和state的使用，我们需要在实践中才能切身体会其中的妙处。整个应用还有很多瑕疵，但我相信随着以后使用React开发多了以后，就更得心应手了，我用到的大多都是些基本用法，要想掌握React这门技术，还有很多东西需要学习。



