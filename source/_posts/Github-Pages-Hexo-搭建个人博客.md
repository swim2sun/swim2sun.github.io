---
title: Github Pages + Hexo 搭建个人博客
tags:
  - Hexo
  - Next
  - Blog
  - Github Pages
  - Travis
categories:
  - tech
date: 2017-09-24 01:33:00
---


###  前言

两年前我初次知道Jekyll和Github Pages，于是心血来潮搭建了自己的blog，然后到处找Jekyll的主题，折腾了一阵子终于把blog整成了自己喜欢的样子。但热情稍纵即逝，不久这个博客便被我遗忘，竟然连完整的文章都没写一篇。

此次，偶然间听闻Hexo，而Hexo的Next主题深深吸引了我，于是又是一番折腾，把blog又大刀阔斧地改成了Hexo。这期间也被别人主题中各种酷炫的效果所吸引过，但冷静下来后，还是决定把重心放在写作上，而把各种主题中影响写作和阅读的特性都摒弃掉。在此记录下自己搭建的过程，希望能帮助后来者更轻松的搭建自己的静态博客。不在乎多少人看，只希望借此养成归纳记录的好习惯。

<!--more-->

###  Github Pages + Hexo = 低成本的博客解决方案

#### Github Pages

几年前WordPress盛行，然而并没有吸引我，原因很简单--没钱。动态博客意味着你得有台服务器。但[Github Pages](https://pages.github.com/)出现了，它的初衷是为了方便搭建开源项目的网站，但程序员们发现用它来搭建博客简直完美，GitHub强大的服务器和CDN简直是专门为广大穷苦的屌丝程序员而准备的。而且Markdown的格式对于写作非常方便，因此静态博客开始火了。

####  Hexo

静态博客需要生成器，Github Pages本身支持[Jekyll](http://jekyllrb.com)，因此最初Jekyll的受众更多。但Jekyll插件功能比较薄弱，由于Jekyll是ruby写的，比不上现在如日中天的Node.js，如今被Hexo所超越也是情理之中。

###  搭建过程

####  Hexo的安装与使用

Hexo的安装与使用有详细的[官方教程](https://hexo.io/zh-cn/docs/index.html)，因此这里不再赘述。此处只提下我在安装过程中碰到的问题。

#####  npm permission denied

在使用npm全局安装hexo时可能会因权限不足而报错。如下：

```
npm ERR! Error: EACCES: permission denied, rename '/root/test/node_modules/.staging/lodash-9a2aabe2' -> '/root/test/node_modules/lodash'
npm ERR!     at destStatted (/usr/lib/node_modules/npm/lib/install/action/finalize.js:25:7)
npm ERR!     at FSReqWrap.oncomplete (fs.js:82:15)
```

这是因为npm默认的全局安装路径在`/usr/local`下面，通常暴力的方式是直接`sudo`运行npm。但这是不提倡的，官方给出了[解决方案](https://docs.npmjs.com/getting-started/fixing-npm-permissions).简单来说有两种：

1. 改变npm全局默认安装目录的权限
2. 改变npm默认安装目录

我选择了第二种，具体方法如下：

1. 在用户目录创建目录供npm使用: ` mkdir ~/.npm-global`
2. 修改npm配置：`npm config set prefix '~/.npm-global'`
3. 将目录添加到环境变量中：`export PATH=~/.npm-global/bin:$PATH`

#####  package-lock.json

当终于安装好了hexo，并生成了项目文件后，我激动地输入了`npm install`。然而，项目根目录下竟然多个了文件`package-lock.json`。一番搜索后发现这个文件保存了依赖的类库的具体版本信息，感觉对我来说作用并不大，于是按照[npm-issuse#17514](https://github.com/npm/npm/issues/17514)上面的方法，在`.npmrc`里加上如下配置：

```
package-lock=false 
```
然后就可以愉快地把`package-lock.json`删掉了。

####  Next主题的安装

之所以选择Next，是看中了Next主题的简洁，安装方法可以参考[官方教程](http://theme-next.iissnan.com/getting-started.html)，但有一些问题需要注意。

#####  主题配置文件的版本管理

刚开始我们会把主题`clone`到`themes`目录下，然后跟随教程修改主题的`_config.yml`配置文件进行一些个性化的配置。我们必然也需要对blog进行版本管理，然而当我们`commit`到Github后，问题出现了：`themes/next/_config.yml`文件并未提交到Github，因为这个文件属于next项目。

这个问题有两种解决方法：

1. `fork`一份next主题，将自己`fork`的next主题`clone`到`themes`目录下，将主题的配置文件更新到fort的next主题。
2. next主题支持把配置文件放到`source`目录下。可以把`themes/next/_config.yml`文件复制到`source/_data`目录下，重命名为`next.yml`。

#####  “百度站长”配置

在配置百度站长时，需要将验证文件放到`source`目录下，此处需要注意在文件的开头加上`layout:false`的配置，否则页面会被hexo包装渲染，导致无法通过验证。配置如下:

```
layout: false
---
(原页面内容)
```

