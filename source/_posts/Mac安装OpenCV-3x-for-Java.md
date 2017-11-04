---
title: Mac安装OpenCV 3.x for Java
date: 2017-11-04 10:24:37
tags: 
  - OpenCV
  - Java
categories:
  - Java
---

打算玩玩OpenCV，于是按照官方的[Tutorial](http://opencv-java-tutorials.readthedocs.io/en/latest/01-installing-opencv-for-java.html)开始安装`OpenCV for Java`，结果被坑得熬了个通宵才搞好...网上关于Mac安装`OpenCV 3.x for Java`资料几乎没有，故记录下来。

<!-- more -->

## 环境

```
OS: macOS 10.13
OpenCV: 3.3.1
IDE: IntelliJ IDEA 2017.2
```

## 安装步骤

### 1.安装XCode Command Line Tools

执行`xcode-select --install`就行了，如果弹出窗口就点击安装，否则会提示说已安装。

### 2.修改OpenCV编译参数

因为安装OpenCV默认是不带Java环境的，因此需修改编译参数。  

执行`brew edit opencv`，在打开的文件中把`-DBUILD_opencv_java=OFF`改为`-DBUILD_opencv_java=ON`。  

### 3.从源码编译安装OpenCV 3.3.1

执行`brew install --build-from-source opencv`会开始编译OpenCV，当前默认版本为OpenCV 3.3.1。

到此一切都很顺利，然而接下来却碰到了第一个问题...

### 4.问题：没有生成jar包

安装完成后，在`/usr/local/Cellar/opencv/3.x.x/share/OpenCV/java/`目录下应该会有`opencv-331.jar`这个文件。但我在安装完后发现`/usr/local/Cellar/opencv/3.x.x/share/OpenCV`下根本就没有`java`这个文件夹！反复修改了几次安装参数，反复编译了几次，均无法解决。查阅了不少资料也都没有有效的方法。  

我几乎要崩溃了，反复google之后在stackoverflow的某个问题下有人回答必须先执行`brew install ant`。未生成jar跟ant应该有很大的关联，但我的环境里已经安装了ant，不过不是使用brew安装的。于是抱着最后一丝希望，使用brew再次安装了ant之后重新编译。漫长的等待之后终于生成了java目录！原因竟然是***必须使用brew安装的ant***...

问题终于解决，于是开始撸opencv的Java代码，但第二个问题随之而来。

### 5.问题: java.lang.UnsatisfiedLinkError: no opencv_java331 in java.library.path

构建工具使用的是gradle，在`build.gradle`下添加了`opencv-331.jar`：

```groovy

dependencies {
    compile files('/usr/local/Cellar/opencv/3.3.1_1/share/OpenCV/java/opencv-331.jar')
}
```
但在运行程序的时候还是抛出了异常：

```
java.lang.UnsatisfiedLinkError: no opencv_java331 in java.library.path
```
原因是没有引入OpenCV的JNI库，在IDEA中添加以下JVM启动参数可解决。

```
-Djava.library.path="/usr/local/Cellar/opencv/3.3.1_1/share/OpenCV/java"
```
本以为问题就此解决，但运行后还是抛出了新的异常。

### 6.问题：opencv Library not loaded: /usr/local/opt/jpeg/lib/libjpeg.8.dylib

连续踩了这么多坑，加上当时已是深夜，头脑有些不清醒了，以为这个问题还是库的问题，于是把opencv下所有的库都引入了，结果还是不能解决。

反复搜索后发现本地的`/usr/local/opt/jpeg/lib/`目录下只有`libjpeg.9.dylib`，谜题总算解开--***本地libjpeg库的版本不同***。

跑了下Java代码，成功调用了opencv的人脸检测，opencv总算安装成功。

## 总结

OpenCV是使用C/C++写的，受本地环境的影响比较大，安装过程应该认真分析异常，保持冷静...

