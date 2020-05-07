---
title: React学习日记二：开发环境搭建
draft: false
tags: [react, node, VScode]
category: React
date: '2017-07-06T23:32:23Z'
math: false
---

# 开发环境搭建

学习一个框架，肯定要先搭好它的开发环境，这不用多说，我在网上找了一些教程，大概将生态下的开发环境搭好了。

## node

首先 node 不说了，这是肯定必须的，node 环境下需要装三个东西：

- nvm(可选)
- npm(node.js 自带)
- nrm

### nvm

简单来说 nvm 是 node.js 的版本管理工具，可以在自己电脑上随时切换管理不同版本的 node。

### npm

npm 不用说了，是 node 自带的，习惯用 yarn 也可以装一个，我不知道是不是 windows 下 npm 有些问题，有时候 yarn 比 npm 好用，所以我更多时候用 yarn。

### nrm

nrm(npm registry manager )是 npm 的镜像源管理工具，有时候国外资源太慢，那么我们可以用这个来切换镜像源。
首先全局安装 nrm：

```bash
npm install -g nrm
```

安装完后就可以立即使用了，执行`nrm ls`可以列出可用的源：

```bash
$ nrm ls

  npm ---- https://registry.npmjs.org/
  cnpm --- http://r.cnpmjs.org/
  taobao - https://registry.npm.taobao.org/
  nj ----- https://registry.nodejitsu.com/
  rednpm - http://registry.mirror.cqupt.edu.cn/
  npmMirror  https://skimdb.npmjs.com/registry/
  edunpm - http://registry.enpmjs.org/
```

可以测一下哪个源比较快，像这样：

```bash
nrm test taobao
taobao - 497ms
```

一般我测出来都是 taobao 这个源比较快，所以就用这个了：

```bash
nrm use taobao
```

## 编辑器

编辑器我用微软出的 VScode，用起来比较方便，还可以自己安一些插件，方便自己写代码。
下载地址：https://code.visualstudio.com/

## create-react-app

create-react-app 算是现在最流行的 react starter 项目，毕竟是 Facebook 官方出的，github 上的 star 应该有 30000+了，足以说明一切。
用起来也特别简单：

### 安装

首先全局下安装 create-react-app：

```bash
npm install -g create-react-app
```

### 快速开始

reate-react-app 已经预先配置好了 Webpack 和 Babel，可以直接使用。

ES6、热更新、编译检查 Flow、测试、代码压缩、文件名带 hash、service worker 等都已经支持了：

```bash
create-react-app first-app
cd first-app/
```

然后执行`npm start`:

```bash
npm start

> first-app@0.1.0 start C:\Users\Crowford\first-app
> react-scripts start

Starting the development server...

Compiled successfully!

You can now view first-app in the browser.

  Local:            http://localhost:3000/
  On Your Network:  http://192.168.0.100:3000/

Note that the development build is not optimized.
To create a production build, use yarn build.
```

神奇的发现它会自动打开浏览器，并访问http://localhost:3000/， 可以看到项目启动起来了。

在`App.js`里随便修改一点东西`Ctrl+s`保存，不用手动刷新，浏览器已经自动完成刷新了。

热更新，Amazing!
