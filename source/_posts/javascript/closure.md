---
title: 关于JavaScript闭包
draft: false
tags: [FE, JS]
category: JavaScript
date: '2019-07-22T18:36:42Z'
math: false
---

闭包（Closure）是 JavaScript 中的一个难点，闭包的概念极容易混淆，也不太容易理解，我在这里谈一谈我对闭包的理解。

### 一、变量的作用域

要理解闭包，首先应该理解一下 JavaScript 中的变量作用域。

JavaScript 中的变量以作用域来划分无非就是两种：全局变量和局部变量。

在 JavaScript 中，在函数内部是可以直接读取全局变量的，请看下面例子：

```js
var a = 99;

function f1() {
  console.log(a);
}

f1(); // 99
```

而在函数外部，是无法读取函数内部变量的：

```js
function f2() {
  var a = 100;
}

console.log(a); //error
```

这里要注意，在函数内部声明变量的时候，一定要使用关键字`var`。如果不使用的话，声明的变量将是全局变量：

```js
function f3() {
  a = 99;
}

f3();

console.log(a); //99
```

### 二、从外部读取局部变量

那么，我们需要在函数外部访问局部变量的时候应该怎么做呢？先来看一段代码：

```js
function f1() {
  var a = 99;

  function f2() {
    console.log(a);
  }

  return f2;
}
```

在上面的代码中，我们函数 f1 的内部又定义了一个函数 f2，在函数 f1 内部还声明了一个局部变量 a，这时 a 对于 f2 是可见的，但是反过来，f2 内部的局部变量对 f1 就是不可见的，这就是 JavaScript 中的”链式作用域“，子对象会一级一级向上寻找所有父对象的变量，所以，父对象的所有变量，对子对象都是可见的，反之则不行。

那么，我们可以这样思考，既然 f2 可以读取 f1 中的变量，那我们 f2 作为 f1 的函数值，不就可以在 f1 外部读取它的内部变量了吗：

```js
function f1() {
  var a = 99;

  function f2() {
    console.log(a);
  }

  return f2;
}

var res = f1();

res(); //99
```

### 三、闭包的概念

对于上面的代码来说，f2 就是闭包。

在网上查询资料以及各种专业文献上关于”闭包“的定义非常抽象，我的理解是，闭包是能够读取其他函数内部变量的函数。

在 JavaScript 语言中，只有函数内部的子函数才能读取其内部变量，因此可以把闭包理解成”定义在一个函数内部的函数“。

所以，从本质上来说，闭包就是将函数内部与函数内部连接起来的一座桥梁。

### 四、闭包的用途

闭包可以用在很多地方，我总结的闭包最大的用处有两个：

1. 读取函数内部的变量；
2. 让这些变量始终保持在内存中。

第一点我们前面已经提到，当你要要在函数外部读取其内部变量的时候，就可以使用闭包的方式，第二点怎么理解呢？请看下面的代码：

```js
function f1() {
  var a = 99;

  add = function() {
    a++;
  };

  function f2() {
    console.log(a);
  }

  return f2;
}

var res = f1();

res(); //99

add();

res(); // 100
```

在这段代码中，f2 就是闭包函数，我们一共调用它两次，第一次打印值为 99，第二次打印值为 100，这证明了函数 f1 中的变量 a 一直保存在内存中，并没有在 f1 调用后被自动清除。

为什么会这样呢？原因就在于 f1 是 f2 的父函数，而 f2 被赋给了一个全局变量，这导致 f2 始终在内存中，而 f2 的存在依赖于 f1，因此 f1 也始终在内存中，不会在调用结束后，被垃圾回收机制（garbage collection）回收。

这段代码中另一个值得注意的地方，就是"add=function(){a++}"这一行，首先在 add 前面没有使用 var 关键字，因此 add 是一个全局变量，而不是局部变量。其次，add 的值是一个匿名函数（anonymous function），而这个匿名函数本身也是一个闭包，所以 add 相当于是一个 setter，可以在函数外部对函数内部的局部变量进行操作。

### 五、使用闭包的注意事项

1. 由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在 IE 中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。

2. 闭包会在父函数外部，改变父函数内部变量的值。所以，如果你把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value），这时一定要小心，不要随便改变父函数内部变量的值。

### 六、深入理解

我们先来看下面两段代码，思考它们的运行结果，可以帮助我们更好的理解闭包的运行机制。

代码一：

```js
var name = 'The Window';

var object = {
  name: 'My Object',

  getNameFunc: function() {
    return function() {
      return this.name;
    };
  },
};

alert(object.getNameFunc()()); // The Window
```

代码二：

```js
var name = 'The Window';

var object = {
  name: 'My Object',

  getNameFunc: function() {
    var that = this;
    return function() {
      return that.name;
    };
  },
};

alert(object.getNameFunc()()); // My Object
```

可以看到上面两段代码输出了不同的结果。

对于代码一，在执行`object.getNameFunc()`时，getNameFunc 返回了一个匿名函数，该匿名函数并没有执行，最终执行该函数时，执行环境为 Window，所以此时的`this`指向 Window，输出结果是'The Window'。

对于代码二，在执行`object.getNameFunc()`时，执行了语句`var that = this;`，此时执行环境在对象 object 内部，所以`this`指向 Object，此并赋值给 that 作为一个内部变量，在调用`object.getNameFunc()()`时，此时，该函数起到了闭包的作用，调用内部变量`that`，所以输出’My Object‘。

## 总结

以上是我对”闭包“的个人理解，若有疏漏或理解错误，欢迎指正。
