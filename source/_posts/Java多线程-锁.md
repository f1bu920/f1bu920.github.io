---
title: Java多线程-锁
date: 2020-09-10 16:57:52
categories: Java多线程
tags:
  - 锁
---

# Java中的锁

本篇文章主要介绍juc包下与锁相关的API和组件，包括**`Lock`接口、`AQS`抽象队列同步器、重入锁、读写锁和`Condition`接口。**

## Lock接口

锁是用来控制多个线程访问资源的方式，除了`synchronized`之外，juc包下还提供了`Lock`接口来实现锁功能。其提供了与`synchronized`类似的同步功能，但**需要显示的获取、释放锁**，并且提供了更加强大的功能，如**可中断的获取锁和超时获取锁等**。

`Lock`接口提供了`synchronized`关键字不具备的特性：

|        特性        |                             描述                             |
| :----------------: | :----------------------------------------------------------: |
| 尝试非阻塞的获取锁 | 当前线程尝试获取锁，如果锁这一时刻没有被其他线程获取到，则成功获取并持有锁 |
|   可中断的获取锁   | 获取到锁的线程能响应中断，拥有锁的线程被中断时，会抛出中断异常并释放锁 |
|     超时获取锁     |        在指定时间内获取锁，如果超时仍未获取锁，则返回        |

`Lock`接口的默认实现类为`ReentrantLock`，`部分API如下：`

|                         void lock()                          |      获取锁，调用该方法的线程尝试获取锁，获取成功后返回      |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|     void lockInterruptibly() throws InterruptedException     |                        可中断的获取锁                        |
|                      boolean tryLock()                       |           尝试非阻塞的获取锁，调用此方法后立即返回           |
| boolean tryLock(long var1, TimeUnit var3) throws InterruptedException |    超时获取锁，有三种可能会返回：超时、成功获取锁、被中断    |
|                        void unlock()                         |                            释放锁                            |
|                   Condition newCondition()                   | 获取等待通知组件，该组件和当前的锁绑定，只有获取了锁才能调用此方法，并且调用后释放锁 |

<!--more-->

## 抽象队列同步器AQS

**抽象队列同步器是用来构造锁和其他同步组件的基础框架，其使用了一个`private volatile int`类型的成员变量`state`表示同步状态，通过内置的FIFO队列来完成资源获取线程的排队。**

**同步器是基于模板方法设计的，子类通过继承`AbstractQueuedSynchronizer`并实现它的同步方法来管理同步状态。同步器提供了三个安全的方法来对同步状态进行更改：`getState`、`setState`、`compareAndSetState(int expect,int update)`。**子类推荐被定义为同步组件的一个静态内部类，其定义了一些获取和释放同步状态的方法。**同步器既可以支持独占式获取同步状态，也可以支持共享式获取同步状态，这样可以实现不同的同步组件，如`ReentrantLock`、`ReentrantReadWriteLock`、`CountDownLatch`等**。

**同步器是实现锁的关键，利用同步器来实现锁的语义。锁是面向使用者的，定义了锁与使用者交互的接口，隐藏了实现细节；同步器是面向锁的实现者的，简化了锁的实现方式，屏蔽了同步状态管理、线程排队、等待与唤醒等底层操作。**

### 队列同步器的接口

同步器是基于模板方法设计的，所以**通过继承同步器并重写指定的方法，将同步器子类组合在自定义同步组件中，并调用同步器所提供的模板方法，就会调用我们重写的方法，从而实现自定义同步组件的逻辑**。

重写指定的方法时，需要使用下面三个方法对同步状态进行访问和修改：

- getState()
- setState()
- compareAndSetState(int expect,int update)

同步器可供重写的方法如下：

|                  方法名称                   |                             描述                             |
| :-----------------------------------------: | :----------------------------------------------------------: |
|    protected boolean tryAcquire(int arg)    | 独占式的获取同步状态。实现该方法需要查询当前状态并判断当前状态是否符合预期，在进行CAS设置同步状态 |
|    protected boolean tryRelease(int arg)    |                     独占式的释放同步状态                     |
|   protected int tryAcquireShared(int arg)   | 共享式的获取同步状态，返回大于等于0的值，获取成功，否则获取失败 |
| protected boolean tryReleaseShared(int arg) |                      共享式释放同步状态                      |
|    protected boolean isHeldExclusively()    |            当前同步器是否在独占式状态下被线程占用            |

实现自定义同步组件时，会调用同步器的模板方法：

|                           方法名称                           |                             描述                             |
| :----------------------------------------------------------: | :----------------------------------------------------------: |
|              public final void acquire(int arg)              | 独占式获取锁。如果获取成功，则返回；否则进入同步队列等待。会调用重写的tryAcquire()方法 |
|       public final void acquireInterruptibly(int arg)        |              与acquire()方法相同，但响应中断。               |
| public final boolean tryAcquireNanos(int arg, long nanosTimeout) |     在acquireInterruptibly(int arg)基础上增加了超时限制      |
|            public final boolean release(int arg)             |    独占式释放同步状态，在释放后会唤醒同步队列中下一个节点    |
|           public final void acquireShared(int arg)           |     共享式获取同步状态，如果未获取到，则进入同步队列等待     |
|    public final void acquireSharedInterruptibly(int arg)     |           在acquireShared(int arg)基础上可响应中断           |
| public final boolean tryAcquireSharedNanos(int arg, long nanosTimeout) |  在acquireSharedInterruptibly(int arg)基础上增加了超时限制   |
|         public final boolean releaseShared(int arg)          |                      共享式释放同步状态                      |
|          public final Thread getFirstQueuedThread()          |                 获取同步队列上下一个等待线程                 |

同步器提供的模板方法可以分为三类：独占式获取和释放同步状态、共享式获取和释放同步状态、查询同步队列中等待线程情况。通过这些模板方法，自定义同步组件可以实现自己的语义。



### 队列同步器的实现

队列同步器的实现主要分为**同步队列、独占式同步状态的获取与释放、共享式同步状态的获取与释放以及超时获取同步状态**

#### 同步队列

**同步器依赖内部的一个FIFO的双向队列来完成线程的排队。当前线程获取同步状态失败时，同步器将当前线程和等待状态等信息构造成一个`Node`节点加入到同步队列中，并阻塞当前线程。当释放同步状态时，会唤醒同步队列首节点线程，使其再次尝试获取同步状态。**

![](https://f1bu920.github.io/images/AQS同步队列.jpg)

节点时构成同步队列的基础，同步器记录首节点(head)和尾节点(tail)，没有成功获取同步状态的线程会被构造出节点添加到同步队列的尾部。**同步器提供了一个基于CAS的安全设置尾节点的方法：`compareAndSetTail(Node expect,Node update)`，设置成功后，当前节点才会与之前的尾节点建立关联。**

![](https://f1bu920.github.io/images/AQS同步队列设置尾节点.jpg)

同步队列遵循FIFO规则，释放同步状态时，会唤醒后继节点，而**后继节点在获取同步状态后会把自己设置为首节点，注意因为只有一个线程能成功获取同步状态，所以不需要通过CAS来保证安全性**。

#### 独占式获取和释放同步状态

通过调用同步器的`acquire()`方法来独占式的获取同步状态。

```java
    public final void acquire(int arg) {
        if (!this.tryAcquire(arg) && this.acquireQueued(this.addWaiter(AbstractQueuedSynchronizer.Node.EXCLUSIVE), arg)) {
            selfInterrupt();
        }
    }
```

该方法首先调用自定义的`tryAcquire()`方法尝试获取同步状态，如果同步状态获取失败，则构造成`Node`节点并通过`addWaiter()`方法添加到同步队列尾部，最后通过`acquireQueued`方法使得该**节点以“死循环”的方式尝试获取同步状态，如果获取不到则阻塞当前线程。而被阻塞线程的唤醒主要靠前驱节点出队和阻塞线程被中断来实现。**

![](https://f1bu920.github.io/images/AQS独占式节点自旋获取同步状态.png)

**节点进入同步队列后，就进入一个自旋的过程，不断检查前驱节点是否是头节点，如果是，则尝试获取前驱节点，否则处于等待状态。**

![](https://f1bu920.github.io/images/AQS独占式获取同步状态流程.png)

总结：

**在获取同步状态时，同步器维护一个同步状态，获取同步状态失败的线程都会被加入到队列中并在队列中进行自旋；移出队列的条件是前驱节点是头节点并且成功获取了同步状态。在释放同步状态时，调用`tryRelease()`方法释放同步状态，然后唤醒头节点的后继节点。**



#### 共享式获取和释放同步状态

共享式获取和独占式获取的区别在于同一时刻能否有多个线程同时获取到同步状态。

**共享式访问资源时，其他共享式访问均被允许，而独占式访问被阻塞；独占式访问资源时，同一时刻其他访问均被阻塞。通过调用同步器的`acquireShared()`方法可以共享式的获取同步状态。**

在`acquireShared()`方法中，同步器调用`tryAcquiredShared()`方法尝试获取同步状态，当返回值大于等于0时，成功获取同步状态；否则失败进入自旋。在自旋过程中，如果当前节点的前驱节点为头节点，尝试获取同步状态，获取成功则退出自旋。

共享式通过调用`releaseShared()`方法释放同步状态，释放同步状态后，会唤醒后续在等待的节点，**共享式释放必须保证同步状态安全释放，因为释放同步状态的操作可能同时来自多个线程，一般通过循环和CAS来保证。**



#### 独占式超时获取同步状态

同步器通过调用`doAcquireNanos(int arg, long nanosTimeout) throws InterruptedException`方法可以超时获取同步状态。此方法在可响应中断的基础上，增加了超时获取特性。

该方法在自选过程中，当前节点的前驱节点为头节点时，尝试获取同步状态，获取成功则从方法返回。**如果获取失败，则判断是否超时，如果没有超时，重新计算超时间隔`nanosTimeout`，然后使当前线程等待`nanosTimeout`纳秒。如果`nanosTimeout`小于等于1000纳秒，将不会使线程进入超市等待，而是进行自旋。因为超时非常短的情况下，进入超时等待会变得不精确。**



## 重入锁

重入锁`ReentrantLock`，表示该锁能够支持对一个资源重复加锁，此外，`ReentrantLock`还支持公平锁和非公平锁。

```java
    public ReentrantLock(boolean fair) {
        this.sync = (ReentrantLock.Sync)(fair ? new ReentrantLock.FairSync() : new ReentrantLock.NonfairSync());
    }
```

### 重进入

与`synchronized`隐式支持重进入不同，**`ReentrantLock`再次获取同步状态时，会判断当前线程是否是获取锁的线程，如果是获取锁的线程再次请求，则将同步状态值进行增加并返回true，表示获取成功。**

**`ReentrantLock`在释放同步状态时，会减少同步状态值。如果该锁被获取了n次，那么前n-1次释放都会返回false，只有同步状态值为0时，才会返回true，表示释放成功。**



### 公平性与非公平性

公平性指的是锁的获取顺序与请求的时间顺序，也就是FIFO。

**对于非公平锁，只要CAS设置同步状态成功，即表示成功获取了锁；而对于非公平锁，在同步队列中时，会判断是否有前驱节点，如果有前驱节点，则需要先等待前驱节点获取锁之后才能尝试获取锁。**

**一般情况下，非公平锁性能会比公平锁更好。公平锁保证了锁的获取按照FIFO原则，而代价是大量的线程切换；非公平锁虽然可能会造成线程饥饿，但极少的线程切换保证了更大的吞吐量。**

 

## 读写锁

**读写锁`ReentrantReadWriteLock`在同一时刻允许多个读线程访问，但在写线程访问时，所有的读线程和其他写线程均被阻塞。读写锁维护了一对锁，一个读锁和一个写锁，通过分离读锁和写锁，使得并发性能相比排它锁有很大的提升。**

`ReentrantReadWriteLock`特性：

|    特性    |                             说明                             |
| :--------: | :----------------------------------------------------------: |
| 公平性选择 | 支持非公平（默认）和公平的锁获取模式，吞吐量还是非公平优于公平 |
|   重入性   | 该锁支持重入锁，以读写线程为例：读线程在获取读锁之后，能够再次读取读锁，而写线程在获取写锁之后可以同时再次获取读锁和写锁 |
|   锁降级   |  遵循获取写锁，获取读锁再释放写锁的次序，写锁能够降级为读锁  |



### 读写锁接口

`ReadWriteLock`接口定义了获取读锁和获取写锁的方法，即`readLock()`方法和`writeLock`方法。而`ReentrantReadWriteLock`还定义了一些监控内部工作状态的方法。

|         方法名          |                       描述                       |
| :---------------------: | :----------------------------------------------: |
| int getReadLockCount()  | 返回当前读锁被获取的次数，不等于获取读锁的线程数 |
| int getReadHoldCount()  | 返回当前线程获取读锁的次数，使用ThreadLocal保存  |
| boolean isWriteLocked() |                判断写锁是否被获取                |
| int getWriteHoldCount() |             返回当前写锁被获取的次数             |



### 读写锁的实现

`ReentrantReadWriteLock`的 实现主要包括**读写状态的设计、写锁的获取与释放、读锁的获取与释放、锁降级。**

#### 读写状态的设计

读写锁的的读写状态就是同步器的同步状态，需要在一个变量`private int volatile state`上维护多个读线程和一个写线程的状态，所以需要按位切割使用这个变量。

**读写锁将一个32位变量分为两个部分，高16位表示读，低16位表示写。读写锁通过位运算迅速确定读和写各自的状态，当同步状态不为0，写状态(高16位)为0时，代表读锁被获取。**



#### 写锁的获取与释放

**写锁是一个支持重进入的排它锁。如果当前线程获取写锁，则增加写状态。如果当前线程在获取写锁时，读锁已被获取或者该线程不是已经获取写锁的线程，则当前线程进入等待**。

写锁的释放与`ReentrantLock`一致，每次释放时减少写状态，当写状态为0时代表写锁已被释放，从而等待的读写线程能够继续访问读写锁。



#### 读锁的获取与释放

**读锁是一个支持重进入的共享锁，能够被多个读线程获取，并线程安全的增加读状态。如果当前线程已获取读锁，则增加读状态；如果当前线程在获取读锁时，写锁已被其他线程获取，则进入等待；如果当前线程获取了写锁或写锁未获取，则当前线程(CAS)增加读状态，成功获取读锁。**

读锁的释放每次均线程安全的减少读状态。



#### 锁降级

锁降级指的是写锁降级为读锁，具体就是把持当前拥有的写锁，再获取到读锁，随后释放写锁的过程。

锁降级中读锁的获取是必须的，是为了保证数据的可见性。如果不获取读锁而直接释放写锁，那么其他写线程可能会获取写锁修改数据。如果获取了读锁，那么其他写线程就会被阻塞。 



## Condition接口

任意一个Java对象都有一套方法`wait()、notify()、notifyAll()`等，可以与`synchronized`配合实现等待/通知模式。Condition接口也提供了类似的方法来配合Lock接口实现等待/通知模式。

### Condition接口方法

Condition对象是通过`Lock`接口的`newCondition()`方法构造出来的，所以Condition对象依赖`Lock`对象。

当前线程调用`await()`方法后，会释放锁并等待；其他线程调用`signal()`方法通知当前线程后，当前线程才会从`await()`方法返回，并且在返回前已经获得了锁。

|                          方法名                           |                             描述                             |
| :-------------------------------------------------------: | :----------------------------------------------------------: |
|         void await() throws InterruptedException          | 当前线程进入等待状态直到被通知或者被中断，返回时代表已经获得了Condition对象对应的锁 |
|                void awaitUninterruptibly()                |            当前线程进入等待直到被通知，不响应中断            |
|  long awaitNanos(long var1) throws InterruptedException   |          当前线程进入等待状态直到被通知、中断、超时          |
| boolean awaitUntil(Date var1) throws InterruptedException |     当前线程进入等待状态直到被通知、中断或者到达某个时间     |
|                       void signal()                       | 唤醒一个等待在Condition上的线程，返回前必须获得与Condition关联的锁 |
|                     void signalAll()                      |               唤醒所有等待在Condition上的线程                |



### Condition实现

`ConditionObject`是AQS的内部类，每个Condition对象都有一个队列，该队列是实现等待/通知的关键。Condition的实现主要包括：**等待队列、等待和通知。**

#### 等待队列

等待队列和同步队列中的节点类型都是同步器的静态内部类`AbstractQueuedSynchronizer.Node`。**等待队列是一个FIFO的队列，在队列中的每个节点都包含了一个线程引用，这个线程就是等待在Condition上的线程，如果一个线程调用了`await()`方法，那么该线程就会释放锁、构造成节点并加入等待队列。**

**一个Condition包含一个等待队列，其记录了等待队列的首尾节点。**



#### 等待

调用`await()`方法，会使当前线程进入等待队列并释放锁，**相当于同步队列的首节点移动到等待队列的尾节点处。**

![](https://f1bu920.github.io/images/Condition等待.png)

调用`await()`方法的线程会被构造成节点并添加到等待队列的尾部，然后释放同步状态，唤醒后续节点。



#### 通知

调用`signal()`方法，将会唤醒等待队列的首节点，并将节点移到同步队列中去。

![](https://f1bu920.github.io/images/Condition通知.png)

节点加入到同步队列中去后，进而调用同步器的方法开始竞争同步状态，成功获取同步状态后，被唤醒的线程将从之前的`await()`处返回，此时已经成功获取了锁。













