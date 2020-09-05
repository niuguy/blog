---
title: CPU／Load偏高的排查步骤
date: 2018-08-26 21:34:35
tags: [Profile]
categories: [Backend]
---

## 1. 查找 java进程
`ps -ef|grep java`

## 2. 查找耗时最高线程
`top -Hp #进程id#`

## 3. 将线程id转换为16进制
`printf “%x\n” #线程id#`

## 4. 使用stack查找相应线程栈信息
`jstack -l #进程id#| grep #16进制线程id#`

## 5. 开始分析线程堆栈
先复习一下线程的各种状态， 包括 NEW(新建)， Runnable(可运行)， Running(运行) ，Block(阻塞)， Dead(死亡)   其中阻塞还分为 等待阻塞、同步阻塞。
转换关系如下图：

![线程状态转换.jpg](https://user-images.githubusercontent.com/1400357/91354176-366e9d00-e7e4-11ea-83f6-9997243b0ecb.jpg)

(图片来自 https://my.oschina.net/mingdongcheng/blog/139263)

接下来看一下jstack打出来的线程堆栈，大概是这样的：
```
"Timer-0" #25 daemon prio=5 os_prio=31 tid=0x00007fd051bb1800 nid=0xfabb in Object.wait() [0x0000700008e90000]
   java.lang.Thread.State: WAITING (on object monitor)
	at java.lang.Object.wait(Native Method)
	at java.lang.Object.wait(Object.java:502)
	at java.util.TimerThread.mainLoop(Timer.java:526)
	- locked <0x00000007a22af328> (a java.util.TaskQueue)
	at java.util.TimerThread.run(Timer.java:505)

   Locked ownable synchronizers:
	- None
```
需要重点关注有没有以下几个状态： 
1. DeadLock  死锁
2. Waiting on Condition  资源等待
3. Blocked  阻塞
具体的case分析可以参考这篇文章  [Java线程Dump分析工具—jstack - 残雪余香 - 博客园](http://www.cnblogs.com/nexiyi/p/java_thread_jstack.html)
