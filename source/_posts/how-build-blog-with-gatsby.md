---
title: 用Gatsby搭建自己的博客
draft: false
tags: [Gatsby, react]
category: React
date: '2017-07-02T18:36:42Z'
math: false
---

今天学习了怎样用 Gatsby 搭建自己的 blog，终于有了自己的 blog，开心，这里简单记录一下使用方式吧。顺便当做自己的第一篇博客，希望以后自己能多写博客来提高自己

# 安装 Gatsby

首先安装 Gatsby：

```bash
npm install -g gatsby
```

# 使用

你可以用 gatsby 新建项目，我使用了大神写好的现成的项目，因为我觉得他的各种设计风格很棒，有现成的用当然好

```bash
git clone https://github.com/magicly/gatsby-blog.git
npm install
```

# 运行

```
gatsby develop
```

# 打包部署

```
gatsby build
```

这时在 public 下生成所有的资源，我用的 coding.net 的 pages 服务，把 public 下的目录 push 到 master 分支就可以。

# 评论

对于第一次搭建自己 blog 的我，使用 gatsby 来搭建自己的 blog 方便多了，以后会在这里记录下我的学习历程，分享一些经验，也算留作一些纪念吧
emmmmmmm....就先这样吧

奉上大神的项目，随意使用：https://github.com/magicly/gatsby-blog.git
