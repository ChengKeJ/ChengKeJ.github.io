---
layout: post
title:  "设计自定义线程池"
categories: 系统设计
tags:  线程池
keywords: 线程池,自定义线程池,BlockingQueue
date: 2023/10/14 21:43:25
---


## 什么是线程池？

线程池是一组预初始化的工作线程，由线程池管理器进行管理。线程池管理器负责将任务分发给工作线程并管理任务的执行。

线程池不是为每个任务创建一个新线程，这样做可能效率低下并导致资源争用。线程池允许一组线程被创建一次，然后用于多个任务。这可以提高应用程序的性能和可伸缩性。

![1*pOec-wF1ytIlwMIxQkQM3A.png](https://miro.medium.com/v2/resize:fit:1400/1*pOec-wF1ytIlwMIxQkQM3A.png)

<!--more-->
## 设计线程池：

要设计一个线程池，我们需要以下实体：

1. 线程池：将任务加入阻塞队列。
2. 阻塞队列：存储任务。
3. 任务执行器：执行任务。

![1*iOx_kUIuKsO2RLjtAdQHuA.png](https://miro.medium.com/v2/resize:fit:782/1*iOx_kUIuKsO2RLjtAdQHuA.png)

## 线程池：

1. 线程池类通过构造函数初始化阻塞队列的大小和线程池中可用的线程数。
2. 在构造函数中，我们创建给定数量的线程，并将任务执行器（实现了Runnable接口）的引用传递给线程并启动它们。
3. 线程池类具有submit(Runnable runnable)方法，用于将任务加入阻塞队列。

```java
import java.util.concurrent.BlockingQueue;
import java.util.concurrent.LinkedBlockingDeque;

public class ThreadPool {
    private BlockingQueue<Runnable> blockingQueue;

    // 定义队列的大小和线程数
    public ThreadPool(int queueSize, int noOfThreads) {
        blockingQueue = new LinkedBlockingDeque<>(queueSize);
        TaskExecutor taskExecutor = null;
        for (int i = 0; i < noOfThreads; i++) {
            taskExecutor = new TaskExecutor(blockingQueue);
            Thread thread = new Thread(taskExecutor);
            thread.start();
        }
    }

    public void submit(Runnable runnable) {
        blockingQueue.add(runnable);
    }
}
```

## 任务执行器：

1. 任务执行器是一个实现了Runnable接口的类，负责从阻塞队列中获取任务并执行它们。

```java
public class TaskExecutor implements Runnable {
    private BlockingQueue<Runnable> blockingQueue;

    public TaskExecutor(BlockingQueue<Runnable> blockingQueue) {
        this.blockingQueue = blockingQueue;
    }

    @Override
    public void run() {
        while (true) {
            try {
                // 从队列中取出任务并执行
                Runnable runnable = blockingQueue.take();
                runnable.run();
            } catch (InterruptedException e) {
                e.printStackTrace();
            }
        }
    }
}
```

现在，我们来测试一下我们的线程池：

```java
public class TestThreadPool {
    public static void main(String[] args) {
        ThreadPool threadPool = new ThreadPool(3, 2);
        for (int taskNumber = 1; taskNumber <= 7; taskNumber++) {
            TestTask testTask1 = new TestTask("abcd_" + taskNumber);
            threadPool.submit(testTask1);
        }
    }
}
```

> 我想补充一点，为什么我们使用阻塞队列来保存任务？

> 答案很简单，`BlockingQueue`允许线程在队列为空时等待新任务，并且只在有空间可用时才接受新任务，防止系统一次性处理过多任务导致超载。


