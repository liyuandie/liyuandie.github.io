---
title: 使用hexo搭建新的博客
draft: false
tags: [总结, hexo]
category: FE
date: '2020-05-08T02:31:42Z'
math: false
---

昨天在看别人写的文章时发现他的博客挺好看，是用 hexo 搭建的，之前我自己的博客是用 gatsby 搭建的，那会儿在学 react，正好堂兄推荐了，我就用了，主题也是直接 copy 他的，一直对样式不太满意，出于懒惰也一直没改，所以看到昨天那位大佬的博客后，决定自己用 hexo 重新搭建一个，之前的博客保留在:https://yanzulee.me/ 作为纪念。

这里简单记录一下用 hexo 搭建的过程，因为中间遇到的坑也不少

## 使用 hexo 新建博客项目

关于 hexo，我这里也不做过多介绍了，有兴趣可以自己在官网查看:[hexo](https://hexo.io/zh-cn/docs/)

### 安装 hexo

先全局安装 hexo 的命令行工具：

```bash
npm install -g hexo-cli
```

或者如果你熟悉 npm 的话，可以局部安装 hexo：

```bash
npm install hexo-cli
```

然后有两种方式执行 hexo 命令：

1. `npx hexo <command>`
2. 将 hexo 所在的目录下的 node_modules 添加到环境变量之中即可直接使用`hexo <command>`：

```bash
echo 'PATH="$PATH:./node_modules/.bin"' >> ~/.profile
```

### 新建项目

安装完 hexo 后，执行以下命令：

```bash
hexo init myBlog
cd myBlog
npm i
```

### 运行

项目就建好了之后，要运行项目，执行：

```bash
hexo server
```

这时打开 `http://localhost:4000/` 你就会看到你新建的 hexo 项目。

### 替换主题

hexo 有默认的主题，但是不太美观，hexo 官网也提供了很多主题模板，参考：https://hexo.io/themes/
但我没有使用官方提供的这些主题，找到了之前提到大佬使用的主题，是另外以为大佬开源的自制主题：[hexo-theme-matery](https://github.com/blinkfox/hexo-theme-matery/blob/develop/README_CN.md)
替换主题的方法和相关配置的修改，大佬的文档写的很清楚，也有中文版，我这里也不做介绍，根据文档可以很快设置好自己喜欢的博客风格。

### 网站配置

关于 hexo 网站的相关配置，参考：https://hexo.io/zh-cn/docs/configuration

### 部署

之前的博客部署使用的是 coding 的 pages 服务，很方便，所以我决定继续部署在 coding，如果你想使用 github pages 部署，hexo 的文档写的很清晰，可以参考：[部署 Github Pages](https://hexo.io/zh-cn/docs/github-pages)，我这里记录我部署在 coding 的过程。

首先在 coding 新建一个仓库，用于储存我的项目代码，取名叫`hexo-blog`，然后在新建一个仓库，用于存储我的静态文件，也就是`/public`文件夹下的东西，取名`hexo-blog-statics`，然后执行以下步骤：

1. 安装[hexo-deployer-git](https://github.com/hexojs/hexo-deployer-git)

```bash
npm install hexo-deployer-git --save
```

2. 修改 hexo 项目根目录下的`_config.yml`文件的以下配置：

```
deploy:
  type: git
  repo: <repository url> #这里填我的用于储存静态文件的仓库地址
  branch: [branch]
  message: [message]
```

3. 执行部署：

```bash
hexo g
hexo clean && hexo deploy
```

这里可省略为一步：

```bash
hexo clean && hexo deploy -g
```

> 由于 Hexo 的部署默认使用分支 master，所以如果你同时正在使用 Git 管理你的站点目录，你应当注意你的部署分支应当不同于写作分支。
> 一个好的实践是将站点目录和 Pages 分别存放在两个不同的 Git 仓库中，可以有效避免相互覆盖。
> Hexo 在部署你的站点生成的文件时并不会更新你的站点目录。因此你应该手动提交并推送你的写作分支。

这时会生成站点文件并推送至远程库，并完全覆盖该分支下的所有内容，然后在 coding 设置好 pages 服务，访问生成的链接就能看到我的博客了，然后 coding 也提供了自定义域名的功能，出于保留原来博客的原因，我在阿里云新买了域名，个人觉得阿里云比 godaddy 便宜一点，两年的价格相差了一倍，设置好域名后访问新设置的域名就能看到新地址下的博客了

剩下的事就是将原来的文章全部迁移过来了，迁移过程感觉比较友好，虽然耗时挺多，但基本无阻塞。

## 总结

个人觉得整个过程还是挺轻松的，所有用到的文档都写的很清楚，时间主要花费在调整自己喜欢的样式，个人觉得 hexo 比 gastby 好用，但主要是贡献者们提供的功能足够细化。对新博客比较满意。
