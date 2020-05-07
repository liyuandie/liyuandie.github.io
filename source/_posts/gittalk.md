---
title: 配置gittalk时显示Error：Not Found的问题解决
draft: false
tags: [总结, hexo, gittalk]
category: FE
date: '2020-05-08T03:29:42Z'
math: false
---

新搭建的 boke 使用的主题中，很好的集成了`Gitalk`、`Gitment`、`Valine` 和 `Disqus` 等评论模块，但作者推荐使用的是`Gitalk`，之前也使用过，所以我选择了它，关于`Gitalk`的使用，各种配置，网上的教程一大堆，我这里不做介绍，主要记录一个困扰我几个小时问题：

在按照网上教程填好各种配置后，页面上使用显示`Error：Not Found`的报错，尝试了各种办法，搜了各种同类问题的解决办法看，始终解决不了，在临近崩溃的边缘，突然看到一个[issue](https://github.com/gitalk/gitalk/issues/379)，看了之后尝试了一下，问题解决了

解决的办法是这样，`Gitalk`的配置中有一项`rope`，这个配置项作者在文档中介绍的是，`GitHub repo`，也就是你用来存储评论的远端仓库地址，我在 github 创建的仓库地址为：https://github.com/liyuandie/hexo-blog-comment 所以我这里自然填的是这个，但是当我把`rope`这项配置改为`hexo-blog-comment`时，我的报错问题就解决了，测试评论的发表和显示都没问题，也就是说这里根本不用写完整地址，在你填完其他配置项后，他是能找到你的 github 地址的，只用填仓库名称就行了，但是这点在文档中并未提及，不知道是我的原因还是文档原因，这个我下来会仔细查看一下源码看看作者的实现，先将这个问题记录在这里。
