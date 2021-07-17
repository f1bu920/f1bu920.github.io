---
title: Java多线程-线程池原理
date: 2020-05-20 17:00:03
categories: Java多线程
tags:
  - Java 
  - 多线程
---

# 线程池原理

## 线程池的优势

1. 创建/销毁线程会消耗系统资源，线程池可以复用已创建的线程。
2. 控制并发的数量。线程并发数量过多，会抢占系统资源并造成阻塞。
3. 可以对线程做一些简单的管理。

## ThreadPoolExecutor的原理

Java中线程池顶层接口为`Executor`，`ThreadPoolExecutor`是其一个具体实现类。

<!--more-->

#### ThreadPoolExecutor的构造方法

```java
//五个参数的构造方法    
public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, Executors.defaultThreadFactory(), defaultHandler);
    }

//六个参数的构造方法
    public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, threadFactory, defaultHandler);
    }

    public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, RejectedExecutionHandler handler) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue, Executors.defaultThreadFactory(), handler);
    }

    public ThreadPoolExecutor(int corePoolSize, int maximumPoolSize, long keepAliveTime, TimeUnit unit, BlockingQueue<Runnable> workQueue, ThreadFactory threadFactory, RejectedExecutionHandler handler) {
        this.ctl = new AtomicInteger(ctlOf(-536870912, 0));
        this.mainLock = new ReentrantLock();
        this.workers = new HashSet();
        this.termination = this.mainLock.newCondition();
        if (corePoolSize >= 0 && maximumPoolSize > 0 && maximumPoolSize >= corePoolSize && keepAliveTime >= 0L) {
            if (workQueue != null && threadFactory != null && handler != null) {
                this.corePoolSize = corePoolSize;
                this.maximumPoolSize = maximumPoolSize;
                this.workQueue = workQueue;
                this.keepAliveTime = unit.toNanos(keepAliveTime);
                this.threadFactory = threadFactory;
                this.handler = handler;
            } else {
                throw new NullPointerException();
            }
        } else {
            throw new IllegalArgumentException();
        }
    }
```

- `int corePoolSize`：核心线程数量的最大值。

  线程池新建线程时，如果当前线程数量小于`corePoolSize`，则新建的是核心线程。如果超过`corePoolSize`，则新建的是非核心线程。默认情况下，核心线程会一直存在于线程池中，即使是处于闲置状态。而非核心线程如果超过指定的时间，就会被销毁。

- `int maximumPoolSize`：线程总数最大值。

  线程总数=核心线程数+非核心线程数。

- `long keepAliveTime`：非核心线程闲置超时时长。

  非核心线程闲置超过这个时间就会被销毁。如果设置`allowCoreThreadTimeOut=true`，就会作用于核心线程。

- `TimeUnit unit`：keepAliveTime的单位

  一个枚举类型，包括以下属性：

  ```java
  public enum TimeUnit {
      NANOSECONDS(1L),//1微毫秒
      MICROSECONDS(1000L),//1微秒
      MILLISECONDS(1000000L),//1毫秒
      SECONDS(1000000000L),//1秒
      MINUTES(60000000000L),//1分钟
      HOURS(3600000000000L),//1小时
      DAYS(86400000000000L);//1天
  ```

- `BlockingQueue<Runnable> workQueue`：任务队列，维护着等待执行的Runnable对象。

  当所有的核心线程都在运行时，新添加的任务就会被添加到这个任务队列中。如果队列满了，就会创建非核心线程执行。有以下常见的任务队列：

  1. `SynchronousQueue`：同步队列，内部容量为0。接收到任务时，会直接提交给线程处理。如果没有闲置线程，就新建一个线程，所以为了防止出现当前线程数大于`maximumPoolSize`，一般指定`maximumPoolSize`为`Integer.MAX_VALUE`。
  2. `LinkedBlockingQueue`：链式任务队列，底层数据结构是链表，默认大小为`Integer.MAX_VALUE`。如果不指定大小，因为任务队列不会满，所以`maximumPoolSize`会失效，线程数永远不会超过`corePoolSize`，因此永远不会创建非核心线程。
  3. `ArrayBlockingQueue`：数组任务队列，底层数据结构为数组，需要指定队列的大小。线程数小于`corePoolSize`时，新建核心线程；超过`corePoolSize`但任务队列没满时，将任务入队；任务队列满了，就会创建非核心线程；如果总线程数超过`maximumPoolSize`，就会执行拒绝处理策略。
  4. `DelayQueue`：延时队列，内部容量为0，队列内元素必须实现Delayed接口。这个队列接受任务时，入队后只有到达了指定时间才会执行任务。

- `ThreadFactory threadFactory`：创建线程的工厂，用于批量创建线程，统一在创建线程时设置一些参数，如是否是守护线程、线程的优先级等。如果不指定，会新建一个默认的线程工厂。

  ```java
  DefaultThreadFactory() {
      SecurityManager s = System.getSecurityManager();
      this.group = s != null ? s.getThreadGroup() :           Thread.currentThread().getThreadGroup();
      this.namePrefix = "pool-" + poolNumber.getAndIncrement() + "-thread-";
  }
  ```
  
  
  
- `RejectedExecutionHandler handler`：就是上面提到的拒绝处理策略。线程数大于`maximumPoolSize`时会执行，有四种常见的拒绝处理策略。

  1. `ThreadPoolExecutor.AbortPolicy`：默认拒绝处理策略，丢弃任务并抛出`RejectedExecutionException`异常。
  2. `ThreadPoolExecutor.DiscardPolicy`：丢弃新来任务，不抛出异常。
  3. `ThreadPoolExecutor.DiscardOldestPolicy`：丢弃队列头部任务，然后尝试执行程序，如果失败，重复此过程。
  4. `ThreadPoolExecutor.CallerRunsPolicy`：由调用线程处理此任务。



#### 线程池的状态

线程池本身有一个调度线程，用于管理布控整个线程池的任务和事务，如创建线程、销毁线程、任务队列管理、线程队列管理等。

`ThreadPoolExecutor`类中有一个控制线程状态的属性`private final AtomicInteger ctl`，并且定义了5个线程的状态，分别为：

```java
    private static final int RUNNING = -536870912;
    private static final int SHUTDOWN = 0;
    private static final int STOP = 536870912;
    private static final int TIDYING = 1073741824;
    private static final int TERMINATED = 1610612736;
```

- 线程池新建后处于RUNNING状态。
- 调用`shutdown()`方法后处于SHUTDOWN状态，线程池不能接受新的任务，清除一些空闲`worker`，并等待任务队列中的任务完成。
- 调用`shutdownNow()`方法后处于STOP状态，线程池不能接受新的任务，中断所有线程，丢弃任务队列中的全部任务。`poolsize`和任务队列的size置为0。
- 当所有的任务已终止，`ctl`记录的任务数量为0，线程就会变为TIDYING状态，接着会执行`terminated()`方法。
- 执行完`terminated()`方法后，线程池变为TERMINATED状态。



#### 线程池的处理流程

![](https://f1bu920.github.io/images/线程池处理流程.jpg)

1. 提交一个任务到线程池后，判断核心线程数是否已满，如果未满就新建核心线程；否则进入下一个流程。**注意这里不管核心线程是否有空闲，都会新建一个新的核心线程，为的是让核心线程数快速达到`corePoolSize`**.
2. 判断任务队列是否已满，未满就将任务添加到任务队列，然后空闲的核心线程就会依次去任务队列中取任务来执行，满了就进入下一个流程。(**线程复用的体现**)
3. 任务队列满了，就创建非核心线程执行任务，如果线程数大于`maximumPoolSize`，就执行拒绝处理策略。

处理任务的核心是`execute()`方法，其源码如下：

```java
 public void execute(Runnable command) {
        if (command == null) {
            throw new NullPointerException();
        } else {
            //获取线程状态
            int c = this.ctl.get();
            //当前线程数小于核心线程最大值，调用addWorker创建核心线程执行任务
            if (workerCountOf(c) < this.corePoolSize) {
                if (this.addWorker(command, true)) {
                    return;
                }

                c = this.ctl.get();
            }
			//如果不小于corePoolSize，将任务添加进workQueue中
            if (isRunning(c) && this.workQueue.offer(command)) {
                int recheck = this.ctl.get
                //二次检查，防止多线程状态下线程池变为非RUNNING状态
                //如果isRunning返回false，则remove这个任务，并执行拒绝处理策略
                if (!isRunning(recheck) && this.remove(command)) {
                    this.reject(command);
                    //线程池处于RUNNING状态，但没有线程则新建线程
                } else if (workerCountOf(recheck) == 0) {
                    this.addWorker((Runnable)null, false);
                }
            //如果放入workQueue失败，就创建非核心线程
            //如果创建非核心线程也失败，则执行拒绝处理策略
            } else if (!this.addWorker(command, false)) {
                this.reject(command);
            }
        }
    }
```

这里对线程池状态进行了二次检查，入队前进行了一次`isRunning()`判断，入队后，又进行了一次`isRunning()`判断。这是因为**在多线程环境下，线程池的状态是时刻发生变化的。判断将`command`加入`workQueue`是线程池之前的状态，如果没有二次检查，有可能线程池变为非RUNNING状态，那么`command`将永远没有机会得到执行。**

写个程序验证：

```java
public class ThreadTest implements Runnable {
    @Override
    public void run() {
        try {
            Thread.sleep(300);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public static void main(String[] args) {
        ArrayBlockingQueue<Runnable> queue = new ArrayBlockingQueue<>(5);
        ThreadPoolExecutor threadPoolExecutor = new ThreadPoolExecutor(5, 10, 60, TimeUnit.HOURS, queue);
        for (int i = 0; i < 16; i++) {
            threadPoolExecutor.execute(new ThreadTest());
            System.out.println("线程池中活跃的线程数："+ threadPoolExecutor.getActiveCount());
            if (queue.size()>0){
                System.out.println("任务队列中的线程数："+queue.size());
            }
        }
    }
}
```

运行结果为：

```java
线程池中活跃的线程数：1
线程池中活跃的线程数：2
线程池中活跃的线程数：3
线程池中活跃的线程数：4
线程池中活跃的线程数：5
线程池中活跃的线程数：5
任务队列中的线程数：1
线程池中活跃的线程数：5
任务队列中的线程数：2
线程池中活跃的线程数：5
任务队列中的线程数：3
线程池中活跃的线程数：5
任务队列中的线程数：4
线程池中活跃的线程数：5
任务队列中的线程数：5
线程池中活跃的线程数：6
任务队列中的线程数：5
线程池中活跃的线程数：7
任务队列中的线程数：5
线程池中活跃的线程数：8
任务队列中的线程数：5
线程池中活跃的线程数：9
任务队列中的线程数：5
线程池中活跃的线程数：10
任务队列中的线程数：5
Exception in thread "main" java.util.concurrent.RejectedExecutionException: Task ThreadTest@6f539caf rejected from java.util.concurrent.ThreadPoolExecutor@79fc0f2f[Running, pool size = 10, active threads = 10, queued tasks = 5, completed tasks = 0]
	at java.base/java.util.concurrent.ThreadPoolExecutor$AbortPolicy.rejectedExecution(ThreadPoolExecutor.java:2055)
	at java.base/java.util.concurrent.ThreadPoolExecutor.reject(ThreadPoolExecutor.java:825)
	at java.base/java.util.concurrent.ThreadPoolExecutor.execute(ThreadPoolExecutor.java:1355)
	at ThreadTest.main(ThreadTest.java:20)

```

新建一个线程池，核心线程数为5，总线程数为10，任务队列容量为5，拒绝执行策略为默认的`AbortPolicy`，其会在线程数超出15时抛出异常。为了达到效果，调用了`sleep()`方法。



#### 线程池的复用

`ThreadPoolSize`在创建线程时，会将线程封装为工作线程`worker`，并放入工作线程组中，然后这个`worker`反复从任务队列中去取任务来执行。新建线程调用`addWorker(Runnable firstTask, boolean core)`方法，这里`core=true`代表是核心线程，`core=false`代表是非核心线程。

`addWorker`分为两部分，创建`worker`和启动`worker`。

创建`worker`需要一个全局锁`ReentrantLock mainLock`。新建核心线程和非核心线程都需要获得全局锁。

启动`worker`：`t.start();`

`addWorker()`方法中创建`Worker`对象的部分源码为:

```java
    private final class Worker extends AbstractQueuedSynchronizer implements Runnable {
        private static final long serialVersionUID = 6138294804551838833L;
        final Thread thread;
        Runnable firstTask;
        volatile long completedTasks;

        Worker(Runnable firstTask) {
            this.setState(-1);
            this.firstTask = firstTask;
            this.thread = ThreadPoolExecutor.this.getThreadFactory().newThread(this);
        }
```

其实现了Runnable接口，所以worker也是一个线程任务。在构造方法中，创建了一个线程，线程的任务就是自己。因此调用线程对象的`start()`方法，会触发Worker类的`run()`方法。

`Worker`类的`run()`方法中，

1. 通过`getTask()`方法获取等待执行的任务。
2. 通过`task.run()`执行具体的任务。
3. 只有当所有的任务都执行完毕才会停止运行。

而`getTask()`是从线程池中获取的任务。**即所有的任务都放在`ThreadPoolExecutor`中，线程池启动多个`Worker`去执行任务，每个`worker`不停的从`ThreadPoolExector`的`workQueue`中取出任务，并执行`task.run()`方法，直至所有的任务执行完毕。**



## 四种常见的线程池

`Executors`类中提供了几个静态方法来创建线程池。

#### newCachedThreadPool

```java
public static ExecutorService newCachedThreadPool() {
   return new ThreadPoolExecutor(0, 2147483647, 60L, TimeUnit.SECONDS, new SynchronousQueue());
}
```

`CachedThreadPool`的执行流程如下：

1. 提交任务进线程池。
2. 因为`corePoolSize`等于0，所以永远不会创建核心线程，线程最大值为`Integer.MAX_VALUE`。
3. 尝试将任务添加进`SynchronousQueue`。
4. 如果入队成功，等待被当前运行的线程空闲后拉取执行；如果当前没有空闲线程，则创建一个非核心线程从`SynchronousQueue`中拉取任务执行。
5. 如果`SynchronousQueue`中已有任务在等待，则入队操作会阻塞。

`CachedThreadPool`适用于需要执行很多短时间任务的场景，因为其线程复用率比较高，可以显著提高性能。而且线程60s后回收，也不会占用太多资源。



#### newFixedThreadPool

```java
    public static ExecutorService newFixedThreadPool(int nThreads, ThreadFactory threadFactory) {
        return new ThreadPoolExecutor(nThreads, nThreads, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue(), threadFactory);
    }
```

可以看到，`corePoolSize==maximumPoolSize`，所以只能创建核心线程，永远不会创建非核心线程。因为`LinkedBlockingQueue`默认大小为`Integer.MAX_VALUE`，所以如果核心线程空闲，则交给核心线程处理；如果不空闲则入队等待空闲线程。

- 与`CachedThreadPool`的对比
  - `FixedThreadPool`只会创建核心线程，`CacheThreadPool`只会创建非核心线程。
  - 在`getTask()`方法中，如果队列中没有任务，则会一直阻塞在`LinkedBlockingQueue.take()`，线程不会被回收，而`CachedThreadPool`中因为全是非核心线程，所以如果没有任务，线程会在60s后被回收。
  - 因为不会被回收，所以没有任务时，`FixedThreadPool`占用资源更多。
  - 几乎都不会触发拒绝处理策略，但原理不同。`FixedThreadPool`是因为任务队列很大，而`CachedThreadPool`则是因为线程池容量很大。



#### newSingleThreadExecutor

```java
    public static ExecutorService newSingleThreadExecutor() {
        return new Executors.FinalizableDelegatedExecutorService(new ThreadPoolExecutor(1, 1, 0L, TimeUnit.MILLISECONDS, new LinkedBlockingQueue()));
    }
```

有且仅有一个核心线程，使用了`LinkedBlockingQueue`作为任务队列。所以不会创建核心线程，所有任务按照先来先执行的顺序进行。如果唯一的线程不空闲，则新来的任务在任务队列中等待。



#### newScheduledThreadPool

```java
    public static ScheduledExecutorService newScheduledThreadPool(int corePoolSize) {
        return new ScheduledThreadPoolExecutor(corePoolSize);
    }

    public ScheduledThreadPoolExecutor(int corePoolSize) {
        super(corePoolSize, 2147483647, 10L, TimeUnit.MILLISECONDS, new ScheduledThreadPoolExecutor.DelayedWorkQueue());
    }
```

创建一个定长线程池，支持定时及周期性任务执行。