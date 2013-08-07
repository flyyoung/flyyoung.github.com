---
layout: post
title: "Spring技术内幕"
categories:
- java

tags:
- Spring

---
##前言
这篇文章是《Spring技术内幕》的读书笔记，主要记录我对Spring源码的研究和思考。  

---

##目录
* [第1章 Spring IOC](#first chapter)  
	* [1.1 IOC容器和上下文](#1.1)
	* 1.2 IOC容器和上下文初始化
		* 1.2.1 Bean定义信息的资源定位
		* 1.2.2 Bean定义信息的载入
		* 1.2.3 Bean定义信息的注册
	* 1.3 IOC容器的依赖注入
	* 1.4 总结
* 第2章 Spring AOP  

---
##正文
<a id="first chapter" name="first chapter"></a>
###第1章 Spring IOC
<a id="1.1" name="1.1"></a>
####1.1 IOC容器和上下文
