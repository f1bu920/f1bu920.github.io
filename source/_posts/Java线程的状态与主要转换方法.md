---
title: Java线程的状态与主要转换方法
date: 2020-04-10 14:32:34
categories: Java多线程
tags:
  - Java
  - 多线程
---

[TOC]



## 操作系统中的线程状态转换

在现代操作系统中，线程被视为轻量级进程，所以操作系统中线程的状态其实是和操作系统中进程的状态是一致的。

![操作系统中线程状态转换.png](https://f1bu920.github.io/images/Java线程状态转换.png)

操作系统中线程主要有三个状态：

- 就绪状态(ready)：线程正在等待使用CPU，等分配到CPU时间片就可以进入running状态。
- 执行状态(running)：线程正在使用CPU。
- 等待状态(waiting)：线程经过等待事件的调用或者正在等待其他资源，如 I/O等。

<!--more-->

## Java线程的6个状态

Java中有一个枚举类，代表了Java线程的6个状态，其源码如下：

```java
public enum State {
         /**
         * Thread state for a thread which has not yet started.
         */
        NEW,
         /**
         * Thread state for a runnable thread.  A thread in the runnable
         * state is executing in the Java virtual machine but it may
         * be waiting for other resources from the operating system
         * such as processor.
         */
        RUNNABLE,
        /**
         * Thread state for a thread blocked waiting for a monitor lock.
         * A thread in the blocked state is waiting for a monitor lock
         * to enter a synchronized block/method or
         * reenter a synchronized block/method after calling
         * {@link Object#wait() Object.wait}.
         */
        BLOCKED,
           /**
         * Thread state for a waiting thread.
         * A thread is in the waiting state due to calling one of the
         * following methods:
         * <ul>
         *   <li>{@link Object#wait() Object.wait} with no timeout</li>
         *   <li>{@link #join() Thread.join} with no timeout</li>
         *   <li>{@link LockSupport#park() LockSupport.park}</li>
         * </ul>
         *
         * <p>A thread in the waiting state is waiting for another thread to
         * perform a particular action.
         *
         * For example, a thread that has called <tt>Object.wait()</tt>
         * on an object is waiting for another thread to call
         * <tt>Object.notify()</tt> or <tt>Object.notifyAll()</tt> on
         * that object. A thread that has called <tt>Thread.join()</tt>
         * is waiting for a specified thread to terminate.
         */ 
        WAITING,
        /**
         * Thread state for a waiting thread with a specified waiting time.
         * A thread is in the timed waiting state due to calling one of
         * the following methods with a specified positive waiting time:
         * <ul>
         *   <li>{@link #sleep Thread.sleep}</li>
         *   <li>{@link Object#wait(long) Object.wait} with timeout</li>
         *   <li>{@link #join(long) Thread.join} with timeout</li>
         *   <li>{@link LockSupport#parkNanos LockSupport.parkNanos}</li>
         *   <li>{@link LockSupport#parkUntil LockSupport.parkUntil}</li>
         * </ul>
         */
        TIMED_WAITING,
          /**
         * Thread state for a terminated thread.
         * The thread has completed execution.
         */  
        TERMINATED;
    }
```

#### `NEW`

处于`NEW`状态的线程尚未启动，即未调用`Thread`类的`start`方法。

`start`方法的源码如下：

```java
    public synchronized void start() {
        if (threadStatus != 0)
            throw new IllegalThreadStateException();
        group.add(this);

        boolean started = false;
        try {
            start0();
            started = true;
        } finally {
            try {
                if (!started) {
                    group.threadStartFailed(this);
                }
            } catch (Throwable ignore) {
            }
        }
    }
```

可以看到，`start()`方法的内部有一个`threadStatus`变量，如果它不为0，会直接抛出异常。

在第一次调用`start()`方法后，`threadStatus`变量会从0变为其他值，若第二次调用`start()`方法就会抛出`IllegalThreadStateException`异常。



#### `RUNNABLE`

`RUNNABLE`表示当前线程正在运行中。`RUNNABLE`状态的线程运行在虚拟机中，也有可能在等待操作系统的其他资源。**Java中`RUNNABLE`状态其实包括了操作系统线程的`ready`和`waiting`两个状态。**



#### `BLOCKED`

阻塞状态。处于`BLOCKED`状态的线程正等待锁的释放以进入同步区。



#### `WAITING`

等待状态。处于等待状态的线程要想变为`RUNNABLE`状态需要其他线程唤醒。

调用下面三个方法会使线程进入`WAITING`状态：

- `Object wait()`：使当前线程处于等待状态直到另一个线程唤醒它。
- `Thread.join()`：等待线程执行完毕，底层调用的是`Object`实例的`wait`方法。
- `LockSupport.park()`：除非获得调用许可，否则禁止当前线程进行线程调度。



#### `TIMED_WAITING`

超时等待状态。线程等待一个具体的时间，时间到后自动唤醒。

调用下面方法会使线程进入超时等待状态：

- `Tread.sleep(long millis)`：使当前线程睡眠指定时间
- `Object wait(long millis)`：线程休眠指定时间，等待期间可以通过`notify()`方法唤醒。
- `Thread.join(long millis)`：等待当前线程最多执行millis毫秒，如果`millis`为0，则会一直执行。
- `LockSupport.parkNanos(long nanos)`：除非获得调用许可，否则禁用当前线程进行线程调度指定时间。
- `LockSupport.parkUntil(long deadline)`：同上，也是禁止线程进行调度指定时间。



#### `TERMINATED`

终止状态，线程执行完毕。



## 线程状态的转换

![线程状态的转换](https://f1bu920.github.io/images/线程状态的转换.png)



#### `BLOCKED`与`RUNNABLE`状态的转换

处于`BLOCKED`状态的线程是因为在等待锁的释放。当线程获取到锁之后就会进入`RUNNABLE`状态。



#### `WAITING`状态与`RUNNABLE`状态的转换

将线程从`RUNNABLE`状态转换为`WAITING`状态主要有2种方法。

- `Object.wait()`

  调用`wait()`方法前线程必须持有对象的锁，**调用`wait()`方法后，会释放当前的锁，直到其他线程调用`notify()`或`notifyAll()`唤醒等待锁的线程**。

- `Thread.join()`

  **调用`join()`方法不会释放锁，会一直等待当前线程执行完毕(转换为`TERMINTED`状态)**。

  ```java
  public class ThreadTest {
      public static void main(String[] args) throws InterruptedException {
          Thread a = new Thread(() -> {
              new ThreadTest().testMethod();
          });
          Thread b = new Thread(() -> {
              new ThreadTest().testMethod();
          });
          a.start();
          a.join();
          b.start();
          System.out.println(a.getName() + ": " + a.getState());
          System.out.println(b.getName() + ": " + b.getState());
      }
  
      private synchronized void testMethod() {
          try {
              Thread.sleep(2000l);
          } catch (InterruptedException e) {
              e.printStackTrace();
          }
      }
  }
  
  
  //输出结果为
  Thread-0: TERMINATED
  Thread-1: TIMED_WAITING
  ```

  要是没有调用`join()`方法，`main`线程不管`a`线程是否执行完毕都会往下执行，但`a`线程启动后立刻调用了`join()`方法，这里`main`线程就会等待直到`a`执行完毕(转换为`TERMINTED`状态)。

  所以`a`会打印的状态为`TERMINATED`，而`b`线程可能为`TIMED_WAITING`状态(已进入同步方法)也可能为`RUNNABLE`状态(未进入同步方法)。



#### `TIMED_WAITING`与`RUNNABLE`状态的转换

`TIMED_WAITING`与`WAITING`状态类似，只是`TIMED_WAITING`等待时间是指定的。

- `Thread.sleep(long)`

  使当前线程睡眠指定时间。**这里的睡眠指的是暂停执行，并不会释放锁**。时间到后，会自动进入`RUNNABLE`状态。

- `Object.wait(long)`

  使线程进入`TIMED_WAITING`状态，**会释放锁**。经过指定时间后，自动唤醒，拥有去争夺锁的资格。

- `Thread.join(long)`

  使当前线程执行指定时间，并且使线程进入`TIMED_WAITING`状态。



## 线程中断

当线程启动后不需要它继续执行下去的时候，需要线程中断机制。线程中断机制是一种协作机制，**通过中断操作并不能直接终止一个线程，而是通知需要中断的线程自行处理。**

- `Thread.interrupt()`

  中断线程。并不会立即停止线程，而是设置线程的中断状态为`true`，默认为`false`。

- `Thread.interrupted()`

  测试当前线程是否被中断。调用一次这个方法会使中断状态设置为`true`，调用两次中断状态重新转变为`false`。

- `Thread.isInterrupted()`

  测试当前线程状态是否被中断，与上面方法不同的是不会影响线程的中断状态。