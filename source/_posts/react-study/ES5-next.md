---
title: React学习日记一：ES5-ES2017语法
draft: false
tags: [react, ES5, ES6]
category: React
date: "2017-07-03T23:56:21Z"
math: false
---

最近打算开始学习React，但是在看官方文档的时候发现React可以使用很多ES6甚至ES2017的新特性，之前自认为熟练掌握JavaScript的我在了解这些新特性后简直觉得amazing，于是先学习了一波，记录一些能在React中常用的特性,主要涵盖内容如下：

* map / filter / reduce
* let / const
* 箭头函数（arrow functions）
* 字符串插值（string interpolation）
* class
* 数组解构
* 对象解构
* Promise
* async/await

下面一一详细记录：
# map/filter/reduce
这三个函数可以很轻松方便的对数组进行一些处理
## map
简单来说，map是对数组每一个元素进行一定处理，返回一个新数组（这点很重要！！！），让我们来看一看：

假设现在有这样一个需求：在数组``` arr=[1,2,3,4,5] ```的基础上得到一个新数组，要求每个元素在```arr```的基础上```+1```。对于我们来说当然so easy！
```jsx
const arr = [1, 2, 3, 4, 5];
let arr2 = [];
for (let i = 0; i < arr.length; i++) {
  arr2.push(arr[i] + 1);
}
console.log(arr2);// [2, 3, 4, 5, 6]
```
OK，需求变了现在要求在每一个元素基础上```+2```，行，要得：
```jsx
const arr = [1, 2, 3, 4, 5];
let arr2 = [];
for (let i = 0; i < arr.length; i++) {
  arr2.push(arr[i] + 2);
}
console.log(arr2);// [3, 4, 5, 6, 7]
```
发现了吧，上面两段代码虽然需求不同，但是代码基本一样，不一样的就是在元素转化的时候略微不同，我们可能还会遇到更对类似的需求：```+3```、```*5```、```-10```....，难道每次都要重复这段代码吗？不用！map很好的帮我们实现了这一需求，我们来看看怎么用：

第一个需求：
```jsx
const arr2 = arr.map(e => e + 1);
console.log(arr2);// [2, 3, 4, 5, 6]
```
第二个需求：
```jsx
const arr2 = arr.map(e => e + 2);
console.log(arr2);// [3, 4, 5, 6, 7]
```
代码一下少了好多啊，其实是map函数帮我们实现了定义一个新的空数组，然后```for```循环遍历```arr```，然后对每个元素做一些转化，放到新数组里，最后返回新数组,我们原来那些代码不就是为了完成这一系列事情吗？
map函数接收一个参数```f```，这个参数类型是```function```，即定义了要对每一个元素进行的转化。了解了这个，那我们就可以做更多事情了，比如：
```jsx
const arr1 = arr.map(e => e + 3);
const arr2 = arr.map(e => e * 5);
const arr3 = arr.map(e => e - 1);
console.log(arr1,arr2,arr3);// [4, 5, 6, 7, 8],[5, 10, 15, 20, 25],[0, 1, 2, 3, 4]
```
炫酷！
##filter
filter，顾名思义，就是对数组按照一定条件进行筛选，然后同样返回一个新数组。
在我们不知道filter之前，其实我们实现这类需求很简单，同样是先定义一个新数组，然后```for```循环遍历```arr```，然后按照一定条件进行筛选，比如要我们找出大于3的数，那我们只需要在```for```循环遍历的时候做```if```判断：```if (arr[i] > 3) ```然后将满足的元素```push```进新数组即可，像这样：
```jsx
const arr = [1, 2, 3, 4, 5]
let arr3 = [];
for (let i = 0; i < arr.length; i++) {
  if (arr[i] > 3) {
    arr3.push(arr[i]);
  }
}
console.log(arr3);//[4, 5]
```

也很简单对吧，可是我们用filter函数更简单，同样是选出大于3的数，我们来看看：
```jsx
const arr3 = arr.filter(e => e > 3)
console.log(arr3);//[4, 5]
```
明显简单多了好吧！
其实同理，filter也是自己为我们实现了那些步骤，所以我们只需要传人一个参数```f```，同样是一个```function```，来告诉它我们的筛选条件即可。Amazing！
##reduce
假设现在的需求是：求数组```arr = [1, 2, 3, 4, 5]```所有元素的积。当然难不倒我们啊！
``` jsx
let r = 1;
for (let i = 0 ; i < arr.length; i++) {
  r = r * arr[i];
}
console.log(r);//120
```
那我们看看用reduce怎么做：
``` jsx
const r = arr.reduce((acc, e) => acc * e, 1);
console.log(r);//120
```
靠！又简单了这么多！不对，有点不一样，为什么有两个参数？原来reduce函数需要接收两个参数，第一个参数是我们需要做的事情，同样是一个```function```，比如我们现在求积，那就是```(acc, e) => acc * e```;至于第二个参数，我们可以理解为初始值，我们现在是求积，初始值当然是1啊。

有点懵，我们先来看看求和怎么做：
```jsx
const r = arr.reduce((acc, e) => acc + e, 0);
console.log(r);//15
```
I got it！
这三个函数太贴心啦！
# let / const
之前的内容我们用到了两个东西，可能已经发现了，```let```和```const```，这两个也是个新东西，简单来说，```let```和```const```是用来代替我们之前定义变量的关键字```var```的，问题来了，为啥要代替啊？说真的，具体原因我似懂非懂，所以说不清楚清楚，只知道```var```存在一些问题，我看到这样一个例子，我觉得挺有趣的：

执行下面这段代码
```jsx
for(var i = 0; i < 10; i++) {
  setTimeout(() => {
    console.log(i);
  }, 100);
}
```
居然输出的是```10,10,10,10,10,10,10,10,10,10```？？？！！！，10个10！不应该是```0,1,2,3,4,5,6,7,8,9```吗？可真的就是10个10...
我看介绍说这是由于```var```没有```lexical scope```，什么鬼，不明白，要想输出0-9的话，需要把```var```改成```let```：
```jsx
for(let i = 0; i < 10; i++) {
  setTimeout(() => {
    console.log(i);
  }, 100);
}
```
试了下，真的是，，，涨姿势，听说这个例子还经常被面试官拿来装x，幸好看到了。
那么```const```呢？我的理解是，```const```和```let```的作用是一样的，都是用来定义变量，而不同的是```const```定义的变量其实是常量，也就是不能修改的。
OK！以后要用到```var```的地方就先改成```let```，如果是常量，那就换成```const```，简单粗暴QAQ...
# 箭头函数（arrow functions）
ES6引入了“箭头函数”的概念，以前我们都是用```function```定义函数表达式，而“箭头函数表达式”比```function```定义的函数表达式更短，并且不绑定自己的this，arguments，super或 new.target。比如：
```jsx
function func(e) {
  return e + 1;
}
```
我们现在可以这样写：
```jsx
const func = (e) => {return e + 1};
```
当只有一个参数时，圆括号是可选的，于是：
```jsx
const func = e => {return e + 1};
```
函数声明为单一表达式时，```{}```和```return```可以省略：
```jsx
const func = e => e +1;
```
这样写真的是简单多了，箭头函数还有更多高级的用法，包括支持默认参数，剩余参数，参数列表解构等等，有兴趣可以参考https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions
# 字符串插值（string interpolation）
我们写代码字符串自然少不了，有时字符串中会用到某些变量值，这时我们需要进行字符串拼接，例如：
```jsx
const name = 'Crawford';
const age = 18;
const tel = 13100000000
console.log('my name is: ' + name + ', and age is: ' + age + ', and tel is: ' + tel + '.');
```
说实话，有没有觉得代码太凌乱了，很多片段，阅读代码很不方便，特别是拼接的内容是对象属性的时候，例如：
```jsx
const me = {
  name: 'Crawford',
  age: 18,
  tel: 13100000000,
}
console.log('my name is: ' + me.name + ', and age is: ' + me.age + ', and tel is: ' + me.tel + '.');
```
眼花缭乱...ES6提供了一个实用的特性，叫字符串插值，用法是用反引号引用字符串，然后在里面就可以用```${}```引用任何JS表达式，例如上面代码可以改写成这样：
```jsx
const me = {
  name: 'Crawford',
  age: 18,
  tel: 13100000000,
}
console.log(`my name is: ${me.name}, and age is: ${me.age}, and tel is: ${me.tel}.`);
```
清晰明了多了，方便快捷，谁用谁知道。
#class
类实际上是个“特殊的函数”，就像你能够定义的函数表达式和函数声明一样，类语法有两个组成部分：类表达式和类声明。
##类声明
定义一个类的一种方法是使用一个类声明。要声明一个类，你可以使用带有class关键字的类名（这里是“Rectangle”）。
```jsx
class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
}
```
###提升
函数声明和类声明之间的一个重要区别是函数声明会声明提升，类声明不会。你首先需要声明你的类，然后访问它，否则像下面的代码会抛出一个ReferenceError：
```jsx
let p = new Rectangle(); 
// ReferenceError

class Rectangle {}
```
##类表达式
一个类表达式是定义一个类的另一种方式。类表达式可以是被命名的或匿名的。赋予一个命名类表达式的名称是类的主体的本地名称。
```jsx
/* 匿名类 */ 
let Rectangle = class {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};

/* 命名的类 */ 
let Rectangle = class Rectangle {
  constructor(height, width) {
    this.height = height;
    this.width = width;
  }
};
```
##使用 extends 创建子类
extends 关键字在类声明或类表达式中用于创建一个类作为另一个类的一个子类。
```jsx
class Animal { 
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Dog extends Animal {
  speak() {
    console.log(this.name + ' barks.');
  }
}

var d = new Dog('Mitzie');
// 'Mitzie barks.'
d.speak();
```
如果子类中存在构造函数，则需要在使用“this”之前首先调用super（）。

也可以扩展传统的基于函数的“类”：
```jsx
function Animal (name) {
  this.name = name;  
}
Animal.prototype.speak = function () {
  console.log(this.name + ' makes a noise.');
}

class Dog extends Animal {
  speak() {
    super.speak();
    console.log(this.name + ' barks.');
  }
}

var d = new Dog('Mitzie');
d.speak();
```
这里要注意，类不能扩展常规（不可构造/非构造的）对象。如果要继承常规对象，可以改用Object.setPrototypeOf():
```jsx
var Animal = {
  speak() {
    console.log(this.name + ' makes a noise.');
  }
};

class Dog {
  constructor(name) {
    this.name = name;
  }
}

Object.setPrototypeOf(Dog.prototype, Animal);// If you do not do this you will get a TypeError when you invoke speak

var d = new Dog('Mitzie');
d.speak(); // Mitzie makes a noise.
```
##使用 super 调用超类
super 关键字用于调用对象的父对象上的函数。
```jsx
class Cat { 
  constructor(name) {
    this.name = name;
  }
  
  speak() {
    console.log(this.name + ' makes a noise.');
  }
}

class Lion extends Cat {
  speak() {
    super.speak();
    console.log(this.name + ' roars.');
  }
}
```
相关连接：
* https://hacks.mozilla.org/2015/07/es6-in-depth-classes/
* http://www.infoq.com/cn/articles/es6-in-depth-classes

#数组解构&对象解构
这篇文档写的相当不错：
* https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment
马下慢慢看。
#Promise & async/await
* https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/Promise
* https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise
* https://cnodejs.org/topic/560dbc826a1ed28204a1e7de
#资料
新特性还不止这些，下面这些资料都写得很不错，想了解更多可以参考一下：
* http://es6.ruanyifeng.com/
* http://www.infoq.com/cn/es6-in-depth/
* https://hacks.mozilla.org/category/es6-in-depth/

