---
layout: post
title: "基于Eclipse搭建Hadoop源码环境"
categories:
- Hadoop

tags:
- Hadoop

---
Hadoop使用ant+ivy组织工程，无法直接导入Eclipse中。本文将介绍如何基于Eclipse搭建Hadoop源码环境。  
#准备工作
本文使用的操作系统为CentOS。需要的软件版本：hadoop-1.0.4、jdk-1.6.0_43、ant-1.9.2、eclipse3.6。
###安装与配置JDK
###安装Eclipse
###安装与配置Ant
###下载Hadoop源码
以上工作就不细说了，这些没啥难度，注意要配置好jdk和ant的环境变量。
##编译源码
进入hadoop项目的根目录，在bash中执行`ant eclipse`。命令行结束之后，就可以向Eclipse中导入工程。
##导入工程
File -> Import -> Existing Projects into Workspace -> Browse (选择hadoop-1.0.4目录) -> Finish，然后工程成功导入。  
完成上述工作以后，Eclipse提示一个错误：“Unbound classpath variable:'ANT_HOME/lib/ant.jar'in project 'hadoop-1.0.4'”。  
显然，我们需要设置系统的ANT_HOME变量，让Eclipse能够找到编译源码需要的Ant库，选择项目，然后打开Eclipse的Project->Properties->Java Build Path，在Libraries页编辑（单击“Edit”按钮）出错的项： ANT_HOME/lib/ant.jar，创建变量ANT_HOME（在接下来的第一个对话框里单击“Variable”,第二个对话框里单击“New”按钮），其值为Ant的安装目录。  
还有一个问题就是：TestRandomAlgorithm类“import com.sun.tools.javac.code.Attribute.Array;”提示找不到，可以将其注释或者将jdk目录下/lib/tools.jar加到工程中。OVER
