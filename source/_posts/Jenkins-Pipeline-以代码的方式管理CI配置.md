---
title: Jenkins Pipeline - 以代码的方式管理CI配置
tags:
  - Jenkins
  - CI
  - Java
categories:
  - Java
date: 2017-10-10 01:31:51
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

<!-- more -->

### Jenkins Pipeline

体验了travis-ci和gitlab-ci后我开始查找Jenkins有没有类似`.gitlab-ci.yml`和`.gitlab-ci.yml`这样的配置文件。结果还真有——`Jenkinsfile`。

Jenkins Pipeline提供了一组可扩展的工具，用于通过`PipelineDSL`为代码创建简单到复杂的构建过程。下面将以基于`springboot`的`helloword`项目为例子，介绍Jenkins Pipeline的使用。

#### 在项目根目录下创建Jenkinsfile文件

##### Springboot Demo

我准备了个SpringBoot的demo项目（[Github](https://github.com/swim2sun/springboot-jenkinsfile-demo))，运行`./gradlew bRun`可启动程序。
访问`http://localhost:8080/user/find`会返回如下结果：

```json
{"name":"Milo","email":"foobar@gmail.com"}
```
为了简化流程，我们定个小目标：将其部署在Jenkins所在的服务器上。

> 实际项目中项目部署的服务器与Jenkins所在的服务器一般是不同的，此处只是为方便演示。

##### Jenkinsfile

在项目的根目录下，创建一个名为`Jenkinsfile`的文件：

```groovy
node {
    echo "Hello World!"
}
```

此处暂且不进行构建的工作，而是在控制台里输出`Hello World!`。Jenkinsfile所用的语言是`groovy`，所以应该指定IDE用Groovy编辑器打开Jenkinsfile以便于编辑。

> 关于Jenkinsfile的语法可以看[官方文档](https://jenkins.io/doc/book/pipeline/)，看到英文就怕的可以看看[翻译的版本](https://www.w3cschool.cn/jenkins/jenkins-epas28oi.html)

#### 在Jenkins中创建Pipeline项目

在Jenkins中创建一个类型为Pipeline的项目。

![在Jenkins中创建Pipeline项目](http://7xkd53.com1.z0.glb.clouddn.com/UC20171009_162854.png)

为了让Jenkins可以从Github上拉取代码，需要在项目中配置部署公钥。

![deploy key](http://7xkd53.com1.z0.glb.clouddn.com/UC20171009_183350.png)

在Jenkins中配置项目的git地址。

![set git address](http://7xkd53.com1.z0.glb.clouddn.com/UC20171009_183531.png)

保存后点击`立即构建`，可以看到构建成功，控制台成功输出了`Hello World`。

![Hello World](http://7xkd53.com1.z0.glb.clouddn.com/UC20171009_183924.png)

#### 构建Java项目

我们的最终目的是构建[springboot-demo](git@github.com:swim2sun/springboot-jenkinsfile-demo.git)这个项目，所以需要对Jenkins稍作修改。

```groovy
node {
    checkout scm

    stage('Build') {
        sh './gradlew assemble'
        archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
    }
}
```

1. `checkout scm`用于检出代码。
2. 调用shell脚本`./gradlew assemble`进行构建。
3. `archiveArtifacts`函数将构建产物进行打包。

提交代码后进行构建，构建成功后在Jenkins中可以但看到构建的产物。

![构建产物](http://7xkd53.com1.z0.glb.clouddn.com/UC20171010_003929.png)

#### 发布项目

SpringBoot项目可打包成fat-jar，直接通过`java -jar`命令运行，本项目采用的就是这种部署方式。

```groovy
node {
    checkout scm

    stage('Build') {
        sh './gradlew assemble'
        archiveArtifacts artifacts: 'build/libs/*.jar', fingerprint: true
    }

    stage('Deploy') {
        def jar_name = "demo-1.0.jar"
        sh """
        cp ${env.WORKSPACE}/build/libs/${jar_name} ~/servers
        cd ~/servers
        if [ \$(pgrep -f ${jar_name} | wc -l) -gt 0 ]; then
            pkill -9 -f ${jar_name}
            echo "stop application"
        fi
        JENKINS_NODE_COOKIE=DONTKILLME nohup java -jar ${jar_name} &  
        """
    }
}
```

1. `${env.WORKSPACE}`使用了Jenkins的环境变量，而`${jar_name}`使用了groovy代码`def jar_name = "demo-1.0.jar"`定义的变量。
2. 在普通的Jenkins shell脚本中采用重命名`BUILD_ID`的方式防止启动的后台任务被杀死，而在pipeline中这种方法无效，而必须使用`JENKINS_NODE_COOKIE `。

至此，简单的构建和发布流程已经完成。




