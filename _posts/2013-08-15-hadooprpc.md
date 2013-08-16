---
layout: post
title: "Hadoop RPC源码分析"  
categories:
- Hadoop  
tags:
- Hadoop


---
##远程过程调用基础知识
---
###RPC原理
---
###RPC机制的实现
---
###Java远程方法调用
---
###Java动态代理  
[传送门](http://www.flyoung.me/2013/07/31/javaproxy/)  

---
###Java NIO
[传送门](http://www.flyoung.me/2013/08/11/javanio/)   
 
---
##Hadoop RPC
我读的源码是别人抽取出来的，现已经上传到github上。[传送门](https://github.com/flyyoung/hadoop-ipc)  
###Hadoop IPC的代码结构
 Hadoop作为一个存储与服务的基础性平台，同时它的内部有采用了master/slave架构，那么其内部通信和与客户端的交互就是必不可少的了。Hadoop在实现时抛弃了JDK自带的一个RPC实现——RMI，而自己基于IPC模型实现了一个高效的轻量级RPC。

**整体结构**  
在IPC包中，最重要的3个类是Server,Client和RPC，它们具有层次化的结构。  

1. RPC类是对Server、Client的具体化。在RPC类中规定，客户程序发出请求调用时，参数类型必须是Invocation；从服务器返回的值类型必须是ObjectWritable。
  
2. RPC类是对Server、Client的包装，简化用户的使用。如果一个类需充当服务器，只需通过RPC类的静态方法getServer获得Server实例，然后start。同时此类提供协议接口的实现。如果一个类充当客户端，可以通过getProxy或者waitForProxy获得一个实现了协议接口的proxy object，与服务器端交互。  

**Server类**  

1. 启动Listener进程。如果收到需要建立连接的请求，将建立连接，然后在上面捕获读操作的命令。收到命令之后，将把解析客户端发过来信息的工作委派给Connection。Connection把信息封装到Call对象中，放入队列中，待Handler处理。

2. 启动指定数目的Handler线程，处理客户端对指定方法调用的请求，然后把结果返回给客户端。  

**Client类**

1. 用Call封装好调用信息，然后借助从连接池中取出的Connection向服务器端发送，等待结果。如果到指定服务器的Connection不存在，将马上建立。Connection线程读取服务器方法调用的返回信息。完成之后，通知主线程。  

org.apache.hadoop.rpc的类图  
![org.apache.hadoop.rpc的类图](/assets/images/rpc.jpg "org.apache.hadoop.rpc的类图")  

---
##Hadoop IPC连接相关过程  
客户端的一次RPC调用的处理过程如下图：  
![客户端](/assets/images/rpcclient.gif)  
每一个Call都是由RPC Client发起。步骤说明：  
1. RPC Client发起RPC Call，通过JAVA反射机制转化为对Client.call调用。
2. 调用getConnection得到与RPC Server的连接。每一个RPC Client都维护一个HashMap结构的到RPC Server的连接池。具体建立连接的流程见下图。    
3. 通过Connection将序列化后的参数发送到RPC服务端。
4. 阻塞方式等待RPC服务端返回响应。  
![客户端流程](/assets/images/rpcclient2.jpg)


--- 
 服务器端的工作流程如图所示:  
![服务器端](/assets/images/rpcserver.gif)  
RPC Server作为服务提供者由两个部分组成：接收Call调用和处理Call调用。  
接收Call调用负责接收来自RPC Client的调用请求，编码成Call对象后放入到Call队列中。这一过程由Listener线程完成。具体步骤：  
1. Listener线程监视RPC Client发送过来的数据。  
2. 当有数据可以接收时，调用Connection的readAndProcess方法。  
3.  Connection边接收边对数据进行处理，如果接收到一个完整的Call包，则构建一个Call对象PUSH到Call队列中，由Handler线程来处理Call队列中的所有Call。  
处理Call调用负责处理Call队列中的每个调用请求，由Handler线程完成：  
1. Handler线程监听Call队列，如果Call队列非空，按FIFO规则从Call队列取出Call。  
2. 将Call交给RPC.Server处理。  
3. 借助JDK提供的Method，完成对目标方法的调用，目标方法由具体的业务逻辑实现。  
4. 返回响应。Server.Handler按照异步非阻塞的方式向RPC Client发送响应，如果有未发送出的数据，则交由Server.Responder来完成。  
交互过程如下图所示:  
![服务器端流程](/assets/images/rpcserver2.jpg)  

---  
##Hadoop IPC源码分析

---
##参考
[Hadoop源代码分析【RPC】](http://www.cnblogs.com/qlee/archive/2011/05/26/2058895.html)  
[源码级强力分析hadoop的RPC机制](http://weixiaolu.iteye.com/blog/1504898)  
[hadoop rpc机制 && 将avro引入hadoop rpc机制初探](http://www.alidata.org/archives/1413)  
[Hadoop中的RPC实现](http://blog.csdn.net/xhh198781/article/details/7268176)  
[hadoop RPC的发展及其应用层协议格式](http://fengshenwu.com/blog/2012/11/13/hadoop-rpc_develop/)  
[org.apache.hadoop.ipc-ipc.server](http://www.cnblogs.com/xuxm2007/archive/2012/06/22/2558599.html)  
[将Hadoop RPC框架应用于多节点任务调度](http://maohong.sinaapp.com/archives/134)  
