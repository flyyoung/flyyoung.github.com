---
layout: post
title: "Spring源码编译"
categories:
- Java

tags:
- Spring

---
##前言
Spring源码之前有读过，主要读的是IOC部分。由于换了电脑，加上想再看看其他部分，遂决定重新来过。[Spring](https://github.com/SpringSource/spring-framework)源码现已迁移至GitHub。

##准备工作
Git和JDK1.7+是必需的。因为最新的spring-framework是采用gradle编译的，故需要下载gradle。

##源码下载
我下载的是Spring3.2.x版本

	git clone git://github.com/SpringSource/spring-framework.git

##源码编译
1、修改gradlew.bat中关于jvm内存设置的参数

原始设置：

	set GRADLE_OPTS=-XX:MaxPermSize=1024m -Xmx1024m -XX:MaxHeapSize=256m %GRADLE_OPTS%

现在设置：

	set GRADLE_OPTS=-XX:MaxPermSize=512m -Xmx512m -XX:MaxHeapSize=256m %GRADLE_OPTS%

不然会报如下错误：

	Error occurred during initialization of VM

	Could not reserve enough space for object heap

	Could not create the Java virtual machine
2、在cmd中进入spring-framework目录中，运行 `gradlew.bat build -x test`，跳过所有的测试。因为测试总会有失败，原因在spring-oxm的测试用例有问题，导致编译失败。

3、安装所有的spring-* jars到本地Maven cache中，运行`gradlew.bat install`。

4、将源码导入eclipse中。

4.1、cmd窗口执行命令`gradlew.bat cleanEclipse :spring-oxm:compileTestJava eclipse -x :eclipse`

4.2、按照eclipse File > Import... > Existing Projects into Workspace导入所有的springFramework项目

4.3、在cmd窗口执行`gradlew.bat :eclipse`

4.4、按照eclipse File > Import... > Existing Projects into Workspace导入springFramework 父项目

##总结
整个过程可谓历尽千辛万苦啊。看看成果吧。
![spring 项目截图](/assets/images/spring.jpg)



