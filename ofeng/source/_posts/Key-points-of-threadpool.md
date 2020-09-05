---
title: 线程池核心知识点
date: 2017-06-26 21:44:08
tags: [Java]
categories: [Backend]
---

—读《java并发编程》笔记
### 为什么需要线程执行框架
> 不用线程池是否也有异步执行任务的方案？  

有两种方案，一是单独起一个线程，任务由这个线程顺序执行。二是每个任务创建一个新线程，每个任务都由一个新线程去执行。

然而两种方案各有缺点：
第一种方案，因为是顺序执行，在任务的响应效率有较大影响。
第二种方案，创建线程是有资源消耗的，对于web应用这样的大流量请求，很容易就会达到资源瓶颈。

另外需要考虑的一些问题：
* 可否知道当前任务在被哪个线程运行了。
* 任务的执行顺序是怎样的，先入先出？优先队列？
* 多少任务正在同时执行
* 如果当前已经达到了系统性能瓶颈，怎么决定任务的优先级，哪些任务需要被移出执行队列。
* 任务执行前的准备，以及执行后的清理工作（释放资源）如何做。

为解决上面这些问题，客观上需要有一个管理线程资源的框架。这个框架可以优化线程执行所占资源，定制灵活的执行策略，让异步任务的执行更为健壮。

### 线程池及其创建
在java体系中，管理线程资源的框架便是线程池（thread pool）。线程池，顾名思义就是线程的资源池，是一种容器资源。它的主要作用就是让线程可以重用，并且保持一定数量的活跃线程，从而使任务响应时间缩短，通过对线程池运行参数的调优，可以达到任务吞吐量和相应时间的最优。

线程池的创建，一般情况下，是通过concurrent包提供的工厂方法Executors
比如
`Executors.newFixedThreadPool(int nThreads)` 会返回一个固定线程数量的实现了ExecutorService接口的线程池服务，如果当前任务的提交数量超过了这个限定数量，会将其排入一个LinkedBlockingQueue中排队。
除此之外，还有
Executors.newSingleThreadExecutor， Executors.newCachedThreadPool 等等

当然，如果看到过Executors 的源代码， 你就知道，还可以自己创建一个线程池，比如上述`Executors.newFixedThreadPool(int nThreads)` 其源码就是
 `return new ThreadPoolExecutor(nThreads, nThreads,0L, TimeUnit.MILLISECONDS,new LinkedBlockingQueue<Runnable>(),threadFactory);`

### 线程池的生命周期

线程池的生命周期有三个阶段，首先是running 然后是 shut down 最终是terminated
* running 顾名思义就是正常运行状态
* shut down 执行shutdownNow()后达到的状态，这个状态下线程池不再接受新任务，等待任务也阻*止。但是此时任务不一定运行完，事实上这个方法会返回一个list包括了还在运行的任务列表。
* terminated 就是在shutdown后所有任务已经完成的状态，此时没有任务在运行了。

## 线程池的参数
1. corePoolSize   核心线程数。这个参数的含义是线程池需要保持的最低线程数，不管当前有没有任务在运行，线程池中都会保持这个配置数量的活跃线程。当任务数量超过corePoolSize时，会被丢到工作队列中，**直到队列满之前，线程池中的线程数不会增加**。
2. maxPoolSize ：最大线程数。该参数指明了一个线程池中同时可以有多少活跃线程同时运行。
3. keepAliveTime   线程最大存活时间。 当池子中的线程数量超过了corePoolSize时，这个数值代表了那些超出的空闲线程可以存活的最大时间。
4. workQueue：自定义的线程池队列实例。有三种队列类型可以选择，分别是无界队列，有界队列，以及 同步队列（SynchronousQueue），其中Executors.newFixedThreadPool 使用的LinkedBlockingQueue 属于无界队列， Executors.newCachedThreadPool 使用的是同步队列。其中，使用默认的无界队列，可能会存在在并发量比较高时，队列占用内存过高可能导致其溢出的风险。
queue的配置可以同前面几个参数配合，以达到不同的效果：
比如：
	*  按照优先级处理一组数据，可以传入PriorityQueue 根据指定参数配置优先级， 这个时候队列长队可以设置相对较长，而corePoolSize可以设置相对小，从而达到吞吐量与占用资源的最优配置。
	*  对于大量短时间访问的请求，考虑用SynchronousQueue。同时设置一个相对较短的keepAliveTime以及极大的maxPoolSize,  SynchronousQueue的特性是如果要把一个元素加入队列，需要有另外一个线程等待接收，如果另外一个线程没有，则创建一个。这个特性很适合处理即时的消息传递，可以支撑较大的容量。
5. RejectedExecutionHandler: 队列饱和处理器。 当工作队列已经满时，要决定哪些任务保留哪些任务要丢弃，有几种策略可以选择，比如：AbortPolicy， CallerRunsPolicy，DiscardPolicy。 可以根据实际情况灵活选择。
