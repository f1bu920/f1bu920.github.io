---
title: Java多线程-线程间的通信
date: 2020-04-13 14:52:18
category: Java多线程
tags:
  - Java
  - 多线程
---

[TOC]

一般来讲，线程内部有自己私有的线程上下文，互不干扰。但当我们需要多个线程相互协作时，就需要利用线程间的通信。以下介绍几种常用的通信机制。

<!--more-->

## 锁与同步

Java中锁的概念是基于对象的，所以又叫对象锁。**一个锁同一时间只能被一个线程持有，也就是说一个锁如果被一个线程持有，其他线程要想获得这个锁只能等待这个锁被释放。**

线程同步就是**线程按照一定的顺序执行**。可以利用锁来实现同步。

如果我们想让两个线程按顺序打印，可以利用锁来实现：

```java
public class ThreadTest {
    public static void main(String[] args) throws InterruptedException {
        ThreadTest threadTest = new ThreadTest();
        Thread a = new Thread(() -> {
            threadTest.testMethod();
        });
        Thread b = new Thread(() -> {
            threadTest.testMethod();
        });
        a.start();
        b.start();
    }

    private synchronized void testMethod() {
        synchronized (this) {
            for (int i = 0; i < 20; i++) {
                System.out.println(Thread.currentThread().getName() + ": " + i);
            }
        }
    }
}
```

这样线程a启动后首先获得锁，线程b启动后因为锁被线程a持有而一直处于等待状态，直到线程a执行完锁被释放。



## 等待通知机制

如上例基于锁的实现中，线程b会一直尝试去获得锁，如果失败了，再继续去尝试。这可能会很耗费资源。这时可以考虑等待/通知机制。

**等待/通知机制是基于`Object`类的`wait()`方法和`notify()`、`notifyAll()`方法实现的。`notify()`会随机唤醒一个等待的线程，`notifyAll()`会唤醒所有处于等待状态的线程**。

**注意`wait()`方法会使线程释放锁！**

如果我们想要线程a、b交替打印的话，可以使用等待/通知机制：

```java
public class ThreadTest {
    public static void main(String[] args) throws InterruptedException {
        ThreadTest threadTest = new ThreadTest();
        Thread a = new Thread(() -> {
            threadTest.testMethod();
        });
        Thread b = new Thread(() -> {
            threadTest.testMethod();
        });
        a.start();
        b.start();
    }

    private synchronized void testMethod() {
        synchronized (this) {
            for (int i = 0; i < 20; i++) {
                System.out.println(Thread.currentThread().getName() + ": " + i);
                this.notify();
                try {
                    this.wait();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}
```

在这个demo中，线程a、b先打印自己的内容，然后调用`notify()`方法唤醒另一个在等待的线程，然后调用`wait()`方法进入等待并释放锁。

**注意等待通知机制使用的是同一个对象锁，如果是使用不同的对象锁，是不能用等待通知机制的。**



## 信号量

JDK中提供了一个类似地实现了信号量功能的类`Semaphore`，这里我们利用`volatile`关键字自己实现信号量通信。

**`volatile`关键字能够保证内存的可见性，如果用`volatile`声明了一个变量，在一个线程里改变了这个变量的值，那么在其他线程是立马可见更改后的值的。**

举例：两个线程a、b轮流递增地打印数字；(线程a打印0，线程b打印1，线程a打印2)

```java
public class ThreadTest {
    private static volatile int signal = 0;
    public static void main(String[] args) throws InterruptedException {
        ThreadTest threadTest = new ThreadTest();
        Thread a = new Thread(() -> {
            while (signal<5){
                if (signal%2==0){
                    System.out.println("ThreadA: "+signal);
                    synchronized (threadTest){
                        signal++;
                    }
                }
            }
        });
        Thread b = new Thread(() -> {
            while (signal<5){
                if (signal%2==1){
                    System.out.println("ThreadB: "+signal);
                    synchronized (threadTest){
                        signal++;
                    }
                }
            }
        });
        a.start();
        b.start();
    }
}
//结果
ThreadA: 0
ThreadB: 1
ThreadA: 2
ThreadB: 3
ThreadA: 4
```

**`volatile`需要原子操作，而`signal++`并非原子操作，所以需要使用`synchronized`上锁。**

信号量常用于处理公共资源时，此时需要多个线程相互合作。



## 管道

管道是基于“管道流”的通信方式。JDK提供了`PipedWriter`、`PipedReader`、`PipedOutputStream`、`PipedInputStream`。其中，前两个是基于字符的，后两个是基于字节的。

```java
public class ThreadTest {
    static class ReaderThread implements Runnable {
        private PipedReader reader;

        public ReaderThread(PipedReader reader) {
            this.reader = reader;
        }

        @Override
        public void run() {
            System.out.println("this is ReaderThread");
            int receive = 0;
            try {
                while (((receive = reader.read()) != -1)) {
                    System.out.print((char) receive);
                }
            } catch (IOException e) {
                e.printStackTrace();
            }
        }
    }

    static class WriterThread implements Runnable {
        private PipedWriter writer;

        public WriterThread(PipedWriter writer) {
            this.writer = writer;
        }

        @Override
        public void run() {
            System.out.println("this is WriterThread");
            int receive = 0;
            try {
                writer.write("write test!");
            } catch (IOException e) {
                e.printStackTrace();
            } finally {
                try {
                    writer.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String[] args) throws IOException, InterruptedException {
        PipedReader pipedReader = new PipedReader();
        PipedWriter pipedWriter = new PipedWriter();
        pipedWriter.connect(pipedReader);
        new Thread(new ReaderThread(pipedReader)).start();
        Thread.sleep(1000);
        new Thread(new WriterThread(pipedWriter)).start();
    }
}
//结果
this is ReaderThread
this is WriterThread
write test!
```

通过线程的构造函数，我们传入了`PipedReader`、`PipedWriter`对象。

1. 线程`ReaderThread`开始执行
2. 线程`ReaderThread`使用`reader.read()`进入阻塞
3. 线程`WriterThread`开始执行
4. 线程`WriterThread`用`writer.write()`向管道中写入字符
5. 线程`WriterThread`使用`writer.close()`结束管道写入
6. 线程`ReaderThread`接受管道输出的字符串并打印
7. 执行完毕。

很明显，管道通信多用于IO相关。



## 其他通信相关

#### `join`方法

`join()`方法是`Thread`类的实例方法，它可以让当前线程进入等待状态，直到join的这个线程执行完毕，再继续执行当前线程。

有时候，主线程创建并启动子线程，如果子线程要进行大量耗时计算而主线程需要子线程计算的结果，就可以用到`join()`方法，避免主线程早于子线程结束。



#### `sleep`方法

`sleep()`方法是`Thread`类的静态方法，它可以让当前线程睡眠一段时间。

**注意，与`wait(long)`不同，`sleep(long)`不会释放当前的锁！**



#### `ThreadLocal`类

`ThreadLocal`是本地线程副本变量工具类，内部使用一个弱引用的`Map`来维护。

它使每个线程都有自己独立的变量，互不干扰。它为每个线程都创建了副本，每个线程都可以访问自己内部的副本变量。

最常见的`ThreadLocal`类应用场景就是用来解决数据库连接、Session管理等。



#### `InheritableThreadLocal`

它不仅仅是当前线程可以存取副本值，它的子线程也可以存取这个副本值。