---
title: 写一个并发请求的Case
date: 2017-08-26 21:31:03
tags: [Java]
categories: [Backend]
---

有些场景下，你可能要检查代码有没有并发问题，验证幂等或者做一下简单的压力测试， 这时候需要写一个并发请求的程序，那么怎么做呢？
先上代码
```
        final long time_start = System.currentTimeMillis();
        ExecutorService service = Executors.newFixedThreadPool(10);

        int client_num = 10;

        final CountDownLatch countDownLatch = new CountDownLatch(1);

        for (int i = 0; i < client_num; i++) {
            Runnable job = new Runnable() {
                @Override
                public void run()  {
                    Worker worker = new Worker();
                    try {
                        countDownLatch.await();
                        worker.doWork();
                    }catch(Exception e){
                        e.printStackTrace();
                    }

                }
            };

            service.submit(job);
        }

        countDownLatch.countDown();

        service.shutdown();

        while (!service.isTerminated()) {
        }
        System.out.println("time consuming is " + (System.currentTimeMillis() - time_start));
```

我们来拆解下这个Case，看看都做了些什么：

### 1. 容器准备
首先，你需要一个线程池来调用任务处理，虽然你可以每次new一个线程来做，但是无论是在生产环境还是测试，都不太建议这样做。本示例用的是
`ExecutorService service = Executors.newFixedThreadPool(10)`  其效果是构造一个固定线程数的线程池，这个线程池的coreSize和maxSize都是10， 也就是说，最多可以同时处理10个任务，多了的就要排队。
线程池装载完任务后，执行submit , 但是此时任务并未执行，具体原因看下面。
### 2. 并发执行 
这里是比较tricky的一个点，因为要模拟完全并发的情况，就要保证所有任务同一时间触发，这里我们用了一个java.util.concurrent的工具类 CountDownLatch， 从名字大概可以猜测到，这个是一个像栅栏一样的工具，先把羊圈在里面，等count数到0的时候，打开栅栏，放出群羊。。
使用步骤： 首先，初始化countDownLatch计数为1。 然后， 在业务代码里在执行业务逻辑之前我们嵌入了一行 `countDownLatch.await()` 。最后， 当所有任务都装载完毕提交后，执行`countDownLatch.countDown()`，如同发令枪一般，所有的任务同时触发，并发量达到client_num的数量10。
### 3. 任务结束
任务结束的时候调用的是线程池的shutdown方法, 看一下这个方法的注释
> Attempts to stop all actively executing tasks, halts the processing of waiting tasks, and returns a list of the tasks that were awaiting execution.  

意思是这个方法只是尝试去停止所有任务，但是并不保证都能结束，并且没有结束的任务是以list返回的。
所以后面又跟了一个
```
 while (!service.isTerminated()) {
    }
```
循环一直等待所有的任务结束完毕，最后再统计所有任务的执行时间。

## 结语
这是一段比较简单的并发测试代码，单元测试足够用，但是如果用做规模化的压力测试，还需要考虑压力机与测试机的连接以及数据热点等一系列问题，这又是个很大的话题了。