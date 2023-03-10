---
title: Markdown
draft: false
tags: [markdown]
category: 工具
date: '2018-09-19T16:13:23Z'
math: false
---

Markdown 是我们在日常工作学习中都会用到一门标记语言，这里简单记录一下自己关于使用 Markdown 的一些心得

# 关于 Markdown

Markdown 是一种轻量级标记语言，它以纯文本形式(易读、易写、易更改)编写文档，并最终以 HTML 格式发布。
Markdown 也可以理解为将以 Markdown 语法编写的语言转换成 HTML 内容的工具。

# Markdown 的优点

- 纯文本，所以兼容性极强，可以用所有的文本编辑器打开
- 只用专注于写文本，不用过多考虑排版的问题
- 格式转换方便，可以轻松转换成 html
- Markdown 的标记语法有极好的可读性

# 语法

Markdown 其实语法内容也比较少，一条条详细来看：

### 标题

在 Markdown 中，只需在文本前面加上`#`即可设置为标题，同时`#`的数量对应标题等级，例如：两个`#`代表二级标题，这里建议在写标题的时候在`#`和标题文本之间保留一个空格，这是标准的 Markdown 写法

### 列表

列表格式有两种方式，第一种是无序列表，直接在文字前面加`-`，例如：

```html
- 文本1 - 文本2 - 文本3
```

第二种是有序列表，直接在文本前面加`1.` `2.` `3.`就行了，例如：

```html
1. 文本1 2. 文本2 3. 文本3
```

### 链接

插入链接不需要单独的按钮或者标签，直接用`[链接名称](链接地址)`这样的语法即可，例如：

```html
[我的博客](yanzule.me)
```

### 图片

插入图片的语法与插入链接的语法相似，使用`![](图片链接地址)`来插入一张图片，例如：

```html
![](http://upload-images.jianshu.io/upload_images/259-0ad0d0bfc1c608b6.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
```

### 引用

引用他人的文字时，在引用的文本前面加`>`,例如:

```html
> 猛兽总是独行，牛羊才成群结队
```

显示的效果如下：

> 猛兽总是独行，牛羊才成群结队

###后续慢慢补充
