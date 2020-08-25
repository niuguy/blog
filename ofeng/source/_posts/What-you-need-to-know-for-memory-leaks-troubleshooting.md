---
title: What you need to know for memory-leaks troubleshooting(排查内存泄漏你需要知道的套路)
date: 2020-08-25 17:12:06
tags: [Java]
categories: [Backend]
---


内存泄漏是一个比较棘手的问题，大多数时候没有明显的报错，这给问题排查带来很大困难，好在前人已经遇到很多类似问题，也留下了一些典型的排查套路，大部分时候只需要按图索骥，一步步来即可。
## 套路之工具篇
* ps或者jps
ps:查找活跃的进程
典型用法：`ps -ef|grep java`
jps:查找活跃的 java进程*
典型用法：`jps -v`
* jmap
jmap 可以将java内存打印出来，作为内存分析工具（如MAT）的输入
* jinfo
可以用来查看java进程的启动信息，可以看下内存堆大小的设置
* jstat
可以用来打印JVM的运行时GC统计信息, 典型用法如
 `jstat -gcutil 21891 250 2`
表示链接到21891这个进程，每250ms用gcutil打印一次，一共取2个实例，通常打印出来的效果是这样的
```
$ jstat -gcutil 19677 1000 2
  S0     S1     E      O      P     YGC     YGCT    FGC    FGCT     GCT
 85.04   0.00  15.15   3.61  44.32      4    0.834     1    0.705    1.538
 85.04   0.00  15.15   3.61  44.32      4    0.834     1    0.705    1.538
```
* MAT
可以分析jmaps生成的内存快照，如果你用Eclipse可以直接在eclipse->help->install software   输入 http://download.eclipse.org/mat/1.6.1/update-site/ 按照提示安装

PS：如果命令有任何不清楚，男人(man)是你的好朋友：）
##  套路之实战篇
### 1. 获取内存dump

> 进程还活着：  

* 使用ps查找java进程ID 
 `ps -ef|grep java 或者 jps` 
*  jmap 将内存dump到本地
`jmap -dump:live,format=b,file=dump.bin 19677`
会输出dump.bin文件

> 已经OOM挂了：  

提前在java 运行参数添加   `-XX:+HeapDumpOnOutOfMemoryError` 表示挂了需要dump当时内存，再添加 `-XX:HeapDumpPath=/tmp`表示dump内存存放的位置, 这样当应用down掉之后，会输出一个.hprof文件

### 2. 内存分析

* 使用MAT导入上一步生成的dump文件，分析之后你会看到类似这样一张图

![1.png](http://upload-images.jianshu.io/upload_images/1886630-f7c22cbb129aee79.png)

从这里为入口，可以查看哪些对象占用内存较大，内存泄漏的可疑点在哪里，比如这个case, 点到Leak Suspects这个链接可以看到

![2.png](http://upload-images.jianshu.io/upload_images/1886630-9211a81431320d97.png?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)

这是个比较直观的问题，HashMapEntry对象占用了超过99%的内存空间，可以直接看业务代码哪里HashMap使用出了问题。
这是MAT比较简单的应用，其他高级用法可以看MAT官方文档，或这篇文章 [Java程序内存分析：使用mat工具分析内存占用 - 孤剑 - 博客园](http://www.cnblogs.com/AloneSword/p/3821569.html)

## 结语
相信了解了这些工具与套路，下次遇到可疑内存泄漏或者OOM这样的严重问题，就不会茫然无措了。
