---
title: Java多线程-volatile
date: 2020-04-22 15:16:25
categories: Leetcode
tags:
  - Java
  - 多线程
---

## 基本概念

#### 内存可见性

JMM有一个主内存，每个线程都有自己私有的工作线程，工作内存中保留了一些变量在主内存的拷贝。

**内存可见性，指的是线程之间的可见性，当一个线程修改了共享变量后，另一个线程可以读取到这个更改后的值。**

#### 重排序

为了优化程序性能，对原有的指令执行顺序进行优化重新排序。重排序可能发生在多个阶段，比如编译重排序、CPU重排序。

#### happens-before原则

是一个给程序员使用的规则，只要遵守`happens-before`原则，JVM就能保证指令在多线程之间的顺序性符合预期。



## volatile的内存语意

在Java中，`volatile`主要有两个功能：

- **保证内存可见性**
- **禁止指令重排序**

#### 内存可见性

当一个线程对`volatile`修饰的变量进行写操作后，JMM会立即把该线程对应的本地内存中的共享变量的值刷新到主内存。当一个线程对`volatile`修饰的变量进行读操作时，JMM会立即把该线程对应的本地内存置为无效，从主内存中读取共享变量的值。

所以，**`volatile`与锁具有相同的内存效果。`volatile`的写与锁的释放、`volatile`的读与锁的获取有相同的内存语意。**

#### 禁止重排序

**JMM是通过内存屏障实现的禁止重排序。**



## volatile的用途

`volatile`可以保证内存可见性和禁止重排序。

在保证内存可见性上，`volatile`与锁具有相同的语意，所以可以当做轻量级的锁来使用。但是`volatile`仅仅能保证单个变量的读、写具有原子性，而锁可以保证整个临界区代码具有原子性。所以**锁更强大，`volatile`性能更有优势。**

在禁止重排序上，`volatile`也十分有用，例如单例模式有一种实现方式就是“双重锁检查”。

```java
public class Singleton{
    private static volatile Singleton instance;
    
    public static Singleton getInstance(){
        if(instance==null){
            synchronized(Singleton.class){
                if(instance==null){
                    instance = new Singleton();
                }
            }
        }
        return instance;
    }
}
```

如果上述变量声明中不使用`volatile`关键字，可能会发生错误。