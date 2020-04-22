---
title: Java多线程-线程组和线程优先级
date: 2020-04-09 16:03:05
categories: Java多线程
tags:
  - Java
  - 多线程
---

[TOC]

# 线程组与线程优先级

## 线程组

Java使用`ThreadGoup`来表示线程组，我们可以使用线程组对线程进行批量控制。

每个`Thread`必然存在于一个`ThreadGroup`中，`Thread`不能独立于`ThreadGroup`存在。执行`main()`方法的主线程所在的线程组名字为"main"，如果我们`new Thread()`时不指定线程组，那么默认将父线程 (当前执行`new Thread`) 的线程组设为自己的线程组。举例：

```java
public class ThreadTest {
    public static void main(String[] args) {
        new Thread(() -> {
            System.out.println("当前线程组名称: " + Thread.currentThread().getThreadGroup().getName());
            System.out.println("当前线程名称: " + Thread.currentThread().getName());
        }).start();
        System.out.println("main方法所在线程的线程组名称： " + Thread.currentThread().getThreadGroup().getName());
    }
}
```

结果为：

![结果](https://f1bu920.github.io/images/线程组与线程优先级结果1.png)

<!--more-->

`ThreadGroup`管理着它下面的`Thread`，`ThreadGroup`是一个标准的向下引用的树状结构，这样是为了**防止“上级”线程被“下级线程”引用而无法有效地被GC回收。**



## 线程的优先级

Java中我们可以指定线程的优先级，从1~10。但在操作系统中并不是按1到10划分线程优先级，比如有些操作系统只支持三级划分：低、中、高。线程最终的优先级由操作系统决定。

Java默认的线程优先级为5，线程的执行顺序由调度程序确定，通常情况下，高优先级的线程会比低优先级的线程有更高的几率被运行。可以通过`Thread`类的`setPriority()`方法设定线程优先级。

**注：Java中的线程优先级只是给操作系统一个建议，并不是一定会按照这个优先级执行，真正的调用顺序由操作系统的线程调度算法决定。**

