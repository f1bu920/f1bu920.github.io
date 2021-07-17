---
title: Java多线程-并发容器集合
date: 2020-05-26 17:27:13
categories: Java多线程
tags:
  - Java
  - 多线程
  - 集合
---

# 并发容器集合

`java.util`包下提供了一些容器类，其中`vector`和`HashTable`是线程安全的容器类。但这些容器类实现同步的方式是通过对方法加锁(`synchronized`)来实现的。这样的话读写操作均需要锁操作，造成效率低下。

因此，Java5之后提供了一些并发容器来在多线程下代替同步容器，提高容器的并发访问性，同时定义了线程安全的复合操作。

## 并发容器类

![](https://f1bu920.github.io/images/Java并发容器类结构.png)

<!--more-->

### 并发Map

#### ConcurrentMap接口

`ConcurrentMap`接口继承了`Map`接口，并在`Map`接口上新定义了几个方法：

```java
public interface ConcurrentMap<K, V> extends Map<K, V> {
    //插入元素
    V putIfAbsent(K var1, V var2);
    //移除元素
    boolean remove(Object var1, Object var2);
    //替换元素
    boolean replace(K var1, V var2, V var3);
    //替换元素
    V replace(K var1, V var2);
}
```

`putIfAbsent`：如果插入的`key`相同，则不替换原有的`value`值。

`remove`：这个`remove`方法增加了对`value`的判断，如果要删除的key-value不能与`Map`中的key-value对应上，则不会删除该元素。

`replace(K var1, V var2, V var3)`：增加了对`value`的判断，如果key-oldValue能与`Map`中原有的key-value对应上，才进行替换操作。

`replace(K var1, V var2)`：不会对`Map`中原有的key-value进行比较，如果存在key，则直接替换。

#### ConcurrentHashMap类

[参考链接](https://www.ibm.com/developerworks/cn/java/java-lo-concurrenthashmap/index.html)

`ConcurrentHashMap`类提供了一种粒度更细的加锁机制来实现多线程下的更高性能，这种机制叫做分段锁，分段锁在并发环境下会实现更高的吞吐量，而在单线程环境下只会损失非常小的性能。

`ConcurrentHashMap`会把数据分成一段段的来存储，然后给每一段数据分配一把锁，当一个线程占用锁访问一个段数据时，其他线程能够访问其他数据段。执行有些需要跨段的方法时，如`size()`、`containsValue()`等，可能需要锁定整个表而不仅仅是某个段，这需要按照顺序锁定所有段，操作完毕后，按顺序释放所有段的锁。

![](https://f1bu920.github.io/images/ConcurrentHashMap.png)

`ConcurrentHashMap`是由`Segment`数组结构和`HashEntry`数组结构组成。`Segment`是一种可重入锁`ReentrantLock`，在`ConcurrentHashMap`中扮演锁的角色，`HashEntry`则用于存储键值对数据。一个`ConcurrentHashMap`里包含一个`Segment`数组，`Segment`的结构和`HashMap`类似，是一种数组和链表结构。一个`Segment`中包含一个`HashEntry`数组，每个`HashEntry`是一个链表结构的元素，每个`Segment`守护着一个`HashEntry`数组中的元素，当对`HashEntry`中的元素进行修改时，必须首先获得它的`Segment`锁。

具体的`ConcurrentHashMap`源码分析会另行学习。



#### ConcurrentNavigableMap接口与ConcurrentSkipListMap类

`ConcurrentNavigableMap`接口继承了`NavigableMap`接口，这个接口提供了针对给定搜索目标返回最接近匹配项的导航方法。

`ConcurrentNavigableMap`接口的主要实现类是`ConcurrentSkipListMap`类。这个类使用的底层数据结构是跳表(SkipList)的数据结构。

跳表是一种以空间换时间的数据结构，可以使用CAS操作保证并发安全性。详情参考下面链接：

[什么是跳表](https://cloud.tencent.com/developer/article/1463023)

[跳表（SkipList）及ConcurrentSkipListMap源码解析](https://blog.csdn.net/sunxianghuang/article/details/52221913)



### 并发Queue

JDK并没有提供线程安全的List类，因为对于List来说，实现一个通用并且没有并发瓶颈的线程安全List很难。

但JDK提供了队列和双端队列的线程安全类：`ConcurrentLinkedDeque`和`ConcurrentLinkedQueue`。这两个类是通过CAS来实现线程安全的。



### 并发Set

JDK提供了`ConcurrentSkipListSet`，是线程安全的有序集合。底层使用`ConcurrentSkipMap`实现。