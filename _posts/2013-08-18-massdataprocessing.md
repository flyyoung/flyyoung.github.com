---
layout: post  
title: "海量数据处理"  
categories:  
- 算法  
tags:  
- 算法  
- 海量数据处理  

---
##基本方法
---
###1、Hash法
---
###2、Bit-map法
利用Bit-map方法解决海量数据的查重和去重问题。
<script src="https://gist.github.com/flyyoung/56d6dedd526c47d9950f.js"></script>
使用C++的bitset实现起来更加简单。

---
###3、Bloom filter法
下面是一个简单的Bloom Filter的实现：
<script src="https://gist.github.com/flyyoung/4b3a2bd91e965794e554.js"></script>

参考：  

- [BloomFilter–大规模数据处理利器(解决空查问题)](http://www.dbafree.net/?p=36)
- [深入理解Bloom Filter](http://blog.csdn.net/liuaigui/article/details/6602683)
- [[大数据量]布隆过滤器(Bloom Filter)适用类型以及具体示例](http://myeyeofjava.iteye.com/blog/1613818)


---
###4、数据库优化法
---
###5、倒排索引法
---
###6、外排序法
---
###7、Trie树
Trie树的典型应用是用于统计和排序大量的字符串，经常被搜索引擎系统用于文本词频统计。  
下面是一个利用Trie统计某个单词出现的频数的实例。  
<script src="https://gist.github.com/flyyoung/aaf37be9b26e99283737.js"></script>

参考：[数据结构 练习21-trie的原理分析和应用](http://www.cnblogs.com/dyllove98/archive/2013/07/05/3174375.html)

---
###8、堆
---
###9、双层桶法
---
###10、MapReduce法
参考：[用MapReduce大刀砍掉海量数据离线处理问题
](http://www.xcoder.cn/html/web/j2ee/2013/0607/10735.html)