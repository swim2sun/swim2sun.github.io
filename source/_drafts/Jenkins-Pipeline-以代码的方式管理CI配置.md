---
title: Jenkins Pipeline - 以代码的方式管理CI配置
tags: 
	- Jenkins 
	- CI
	- Java
categories:
	- Java
---

### 为何应该将CI的配置与代码放在一起

我们团队一直用Jenkins来进行项目的持续集成，使用方式一直局限于传统的WebUI的操作方式来配置项目的构建、发布的过程，一些发布的脚本也直接写在Jenkins里面。

这导致CI的配置与代码分离，而且无法进行版本控制。其实CI的配置本身也是工程的一部分，如今主流的做法都是将CI的配置与代码放在一起。例如：

* 托管在GitHub上的项目，根目录下一般都有个`.travis.yml`文件，该文件包含了`travis-ci`的配置，比如[本博客的源码](https://github.com/swim2sun/swim2sun.github.io)。
* GitLab-CI的配置也是写在项目的根目录下的`.gitlab-ci.yml`文件里。

这样做的好处显而易见：

* 可与代码同步进行版本控制，有一定容灾能力
* 团队能够管理审查，易于传播和编辑（复制粘贴更容易）

CI的配置本身就是代码，所以为什么不跟其他代码放在一起呢？

### Jenkins Pipeline




