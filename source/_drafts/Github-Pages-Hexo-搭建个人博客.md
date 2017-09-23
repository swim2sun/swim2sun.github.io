---
title: Github Pages + Hexo 搭建个人博客
date: 2017-09-24 01:33
categories:
  - tech
tags:
  - Hexo
  - Next
  - Blog
  - Github Pages
  - Travis
---

# 前言

两年前我初次知道Jekyll和Github Pages，于是心血来潮搭建了自己的blog，然后到处找Jekyll的主题，折腾了一阵子终于把blog整成了自己喜欢的样子。但热情稍纵即逝，不久这个博客便被我遗忘，竟然连完整的文章都没写一篇。

此次，偶然间听闻Hexo，而Hexo的Next主题深深吸引了我，于是又是一番折腾，把blog又大刀阔斧地改成了Hexo。这期间也被别人主题中各种酷炫的效果所吸引过，但冷静下来后，还是决定把重心放在写作上，而把各种主题中影响写作和阅读的特性都摒弃掉。在此记录下自己搭建的过程，希望能帮助后来者更轻松的搭建自己的静态博客。不在乎多少人看，只希望借此养成归纳记录的好习惯。

# Github Pages + Hexo = 低成本的博客解决方案

几年前WordPress盛行，然而并没有吸引我，原因很简单--没钱。动态博客意味着你得有台服务器。但[Github Pages](https://pages.github.com/)出现了，它的初衷是为了方便搭建开源项目的网站，但程序员们发现用它来搭建博客简直完美，GitHub强大的服务器和CDN简直是专门为广大不舍得花钱买服务器的屌丝程序员而准备的。

Hexo
