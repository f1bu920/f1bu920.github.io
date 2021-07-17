---
title: Java多线程-锁接口和类
date: 2020-05-25 14:05:51
categories: Java多线程
tags:
  - Java
  - 多线程
  - Lock
---

# Java多线程-锁接口和类

Java原生的锁是基于对象的锁，一般是配合`synchronized`使用的。而在`java.util.concurrent`包下，还提供了几个关于锁的接口和类。

### synchronized的不足

- 使用`synchronized`，临界区的代码同一时间只有一个线程能执行。即便临界区是只读操作。
- `synchronized`无法知道线程有没有成功获得锁。
- 使用`synchronized`如果临界区因为IO或者`sleep`等阻塞了，当前线程又没有释放锁，就会导致所有的线程都会等待。



### 锁的分类

锁可以根据不同的方式进行分类。

#### 可重入锁和不可重入锁

可重入锁就是支持重新进入的锁，也就是支持一个线程对资源重复加锁。

`synchronized`就是使用的可重入锁。可重入性实际上是基于线程的分配，而不是基于方法调用的分配。

`Lock`也是一个可重入锁。



#### 公平锁与非公平锁

这里的公平指的是先来先执行。如果一个锁对先请求获取的线程先满足，对后请求获取的线程后满足，这个锁就是一个公平锁。

一般，**非公平锁能提升效率，但非公平锁可能会导致一些线程长时间获取不到锁。**

`ReentrantLock`支持公平锁与非公平锁两种。



#### 读写锁和排它锁

`synchronized`和`ReetrantLock`都是排它锁，这些锁在同一时间内只允许一个线程进行访问。

而读写锁同一时刻允许多个线程访问。Java提供了`ReetrantReadAndWriterLock`类作为读写锁的默认实现，其内部维护了两个锁：一个读锁、一个写锁。通过分离读锁和写锁，使得读多写少的场景下效率大大提高。使用读写锁时，在写线程启动时，读线程和其他的写线程均被阻塞。



### JDK中关于锁的接口和类

#### 抽象类AQS/AQLS/AOS

`AQS`，抽象队列同步器，是JDK提供的一个“队列同步器”的基本功能实现。

`AQS`中的资源使用一个`int`类型的数据表示的，如果我们的资源数量超出了`int`的范围，就可以使用`AQLS`。`AQLS`与`AQS`几乎一样，只是把资源的类型变成了`long`类型。

`AQS`和`AQLS`都继承了一个类`AOS`：`AbstractOwnableSynchronizer`，用于表示锁与持有者的关系(独占模式)。

```java
public abstract class AbstractOwnableSynchronizer implements Serializable {
    private static final long serialVersionUID = 3737899427754241961L;
    //独占模式，锁的持有者
    private transient Thread exclusiveOwnerThread;

    protected AbstractOwnableSynchronizer() {
    }
    //设置锁持有者
    protected final void setExclusiveOwnerThread(Thread thread) {
        this.exclusiveOwnerThread = thread;
    }
    //获取锁的持有线程
    protected final Thread getExclusiveOwnerThread() {
        return this.exclusiveOwnerThread;
    }
}
```



#### 接口Condition/Lock/ReadWriteLock

`java.util.concurrent`包下有三个接口：`Condition`、`Lock`、`ReaderWriteLock`。其中，`Lock`和`ReadWriteLock`分别是锁和读写锁。

`ReadWriteLock`只有两个方法，分别返回读锁和写锁。

```java
public interface ReadWriteLock {
    Lock readLock();

    Lock writeLock();
}
```

`Lock`接口的方法如下：

```java
public interface Lock {
    //获得锁，如果锁被其他线程获取，则等待
    void lock();
    //通过这个方法获取锁时，如果这个线程正在等待获取锁，则这个线程能够中断等待状态。
    void lockInterruptibly() throws InterruptedException;
    //尝试获得锁，成功返回true，失败返回false。立即返回，拿不到锁时不会一直等待
    boolean tryLock();
    //与tryLock大体相同，但拿不到锁时会等待指定时间
    boolean tryLock(long var1, TimeUnit var3) throws InterruptedException;
    //释放锁，与synchronized不同，Lock必须手动释放锁，否则会可能陷入死锁
    void unlock();
    //实现类似于Object的wait/notify等待/通知机制
    Condition newCondition();
}
```

`Lock`接口中有一个方法可以获得`Condition`。每个对象都可以利用继承自`Object`的`wait/notify`方法来实现等待/通知机制。`Condition`也提供了类似的方法，通过与`Lock`的配合来实现等待/通知机制。

| 对比项                                       | Object监视器                  | Condition                                                    |
| -------------------------------------------- | ----------------------------- | ------------------------------------------------------------ |
| 前置条件                                     | 获取对象的锁                  | 调用`Lock.lock`获取锁，调用`Lock.newCondition()`获取`Condition`对象 |
| 调用方式                                     | 直接调用，如`object.notify()` | 直接调用，如`condition.await()`                              |
| 等待队列的个数                               | 一个                          | 多个                                                         |
| 当前线程释放锁进入等待状态                   | 支持                          | 支持                                                         |
| 当前线程释放锁进入等待状态，在等待状态中断   | 不支持                        | 支持                                                         |
| 当前线程释放锁并进入超市等待状态             | 支持                          | 支持                                                         |
| 当前线程释放锁进入等待状态直到将来的某个时间 | 不支持                        | 支持                                                         |
| 唤醒等待队列中的一个线程                     | 支持                          | 支持                                                         |
| 唤醒等待队列中的全部线程                     | 支持                          | 支持                                                         |

`Condition`与`Object`的`wait/notify`基本相似。`Condition.await()`对应`Object.wait()`，`Condition.signal/signalAll`对应`Object.notify/notifyAll`。



#### ReentrantLock

`ReentrantLock`是JDK提供的`Lock`接口的默认实现，实现了锁的基本功能。其是一个可重入锁，内部有一个抽象类`abstract static class Sync extends AbstractQueuedSynchronizer`继承了`AQS`，是自己实现的一个同步器，有两个非抽象类`static final class FairSync extends ReentrantLock.Sync`和`static final class NonfairSync extends ReentrantLock.Sync`，分别是公平同步器和非公平同步器，代表着`ReentrantLock`支持公平锁与非公平锁。

这两个同步器都调用了`AOS`的`setExclusiveOwnerThread(current);`方法，所以`ReentrantLock`的锁是独占的，也就是说是排他锁，不能共享。

```java
    public ReentrantLock(boolean fair) {
        this.sync = (ReentrantLock.Sync)(fair ? new ReentrantLock.FairSync() : new ReentrantLock.NonfairSync());
    }
```

在`ReentrantLock`的构造方法中，可以传入一个布尔类型的参数`fair`来指定其是否是公平锁，默认是非公平锁。



#### ReentrantReadWriteLock

`ReentrantReadWriteLock`类是`ReadWriteLock`接口的默认实现。与`ReentrantLock`类似，都是可重入的，支持公平锁与非公平锁。不同的是其还支持读写锁。

```java
public class ReentrantReadWriteLock implements ReadWriteLock, Serializable {
    //省略部分代码
    private final ReentrantReadWriteLock.ReadLock readerLock;
    private final ReentrantReadWriteLock.WriteLock writerLock;
    final ReentrantReadWriteLock.Sync sync;
    
    //构造方法，默认是非公平锁
     public ReentrantReadWriteLock() {
        this(false);
    }
    public ReentrantReadWriteLock(boolean fair) {
        this.sync = (ReentrantReadWriteLock.Sync)(fair ? new ReentrantReadWriteLock.FairSync() : new ReentrantReadWriteLock.NonfairSync());
        this.readerLock = new ReentrantReadWriteLock.ReadLock(this);
        this.writerLock = new ReentrantReadWriteLock.WriteLock(this);
    }
    
    //获取读锁与写锁的方法
    public ReentrantReadWriteLock.WriteLock writeLock() {
        return this.writerLock;
    }
    public ReentrantReadWriteLock.ReadLock readLock() {
        return this.readerLock;
    }
    
    static final class FairSync extends ReentrantReadWriteLock.Sync {
        //省略实现
    }
    static final class NonfairSync extends ReentrantReadWriteLock.Sync {
        //省略实现
    }
    abstract static class Sync extends AbstractQueuedSynchronizer{
        //省略实现
    }
    
    //读锁与写锁
    public static class ReadLock implements Lock, Serializable {
        private final ReentrantReadWriteLock.Sync sync;
        protected ReadLock(ReentrantReadWriteLock lock) {
            this.sync = lock.sync;
        }
        ...
    }
    public static class WriteLock implements Lock, Serializable {
        private final ReentrantReadWriteLock.Sync sync;
        protected WriteLock(ReentrantReadWriteLock lock) {
            this.sync = lock.sync;
        }
        ...
    }
}
```

可以看到，`ReentrantReadWriteLock`内部维护了两个同步器和两个`Lock`的实现类`ReadLock`和`WriteLock`。

实现了读写锁，但在写操作时，其他线程不能读也不能写，存在“写饥饿”。



#### StampedLock

`public class StampedLock implements Serializable `可以看到，`StampedLock`并没有实现`Lock`接口和`ReadWriteLock`接口，但其实现了“读写锁”的功能，并且性能更好。`StampedLock`把读锁和写锁分为乐观读锁和悲观读锁两种。

`StampedLock`避免了写饥饿现象，它的核心思想在于，**在读的时候如果发生了写，应该通过重试的方法来获取新的值，而不应该阻塞写操作。这种模式也是典型的无锁编程思想，与CAS自旋的思想一样。**`StampedLock`适合在读多写少的场景下使用，同时避免了写饥饿的产生。官方使用实例：

```java
public class Point {
	private double x, y;	
	private final StampedLock stampedLock = new StampedLock();
	//写锁的使用
	void move(double deltaX, double deltaY){
		long stamp = stampedLock.writeLock(); //获取写锁
		try {
			x += deltaX;
			y += deltaY;
		} finally {
			stampedLock.unlockWrite(stamp); //释放写锁
		}
	}
	
	//乐观读锁的使用
	double distanceFromOrigin() {
		long stamp = stampedLock.tryOptimisticRead(); //获得一个乐观读锁
		double currentX = x;
		double currentY = y;
		if (!stampedLock.validate(stamp)) { //检查乐观读锁后是否有其他写锁发生，有则返回false
			stamp = stampedLock.readLock(); //获取一个悲观读锁
			try {
				currentX = x;
			} finally {
				stampedLock.unlockRead(stamp); //释放悲观读锁
			}
		} 
		return Math.sqrt(currentX*currentX + currentY*currentY);
	}
	
	//悲观读锁以及读锁升级写锁的使用
	void moveIfAtOrigin(double newX,double newY) {
		long stamp = stampedLock.readLock(); //悲观读锁
		try {
			while (x == 0.0 && y == 0.0) {
				long ws = stampedLock.tryConvertToWriteLock(stamp); //读锁转换为写锁
				if (ws != 0L) { //转换成功
					stamp = ws; //票据更新为写锁的
					x = newX;
					y = newY;
					break;
				} else {
					stampedLock.unlockRead(stamp); //转换失败释放读锁
					stamp = stampedLock.writeLock(); //强制获取写锁
				}
			}
		} finally {
			stampedLock.unlock(stamp); //释放所有锁
		}
	}
}
```

乐观读锁的意思是假定在这个锁获取期间，共享变量不会被改变。在获取乐观读锁后进行了一些操作，然后调用`validate`方法，这个方法是验证是否有写操作执行过，如果有，则获取一个悲观读锁。

`StampedLock`获取锁时会返回一个`long`类型的变量，释放锁时再把这个变量传进去。

```java
    //用于操作state后获取stamp的值
    private static final int LG_READERS = 7;
    private static final long RUNIT = 1L;
    private static final long WBIT = 128L;
    private static final long RBITS = 127L;
    private static final long RFULL = 126L;
    private static final long ABITS = 255L;
    private static final long SBITS = -128L;
	//初始化state的值
    private static final long ORIGIN = 256L;
	//锁共享变量state
    private transient volatile long state = 256L;
	//读锁溢出时用来存储多出的读锁
    private transient int readerOverflow;
```

`StampedLock`用`long`类型变量的前7位(`LG_READERS`)来表示读锁，每获得一个悲观读锁，就加一(`RUNIT`)，每释放一个悲观读锁，就减一。而悲观读锁最多只能存储128个（7位限制），所以用一个`int`类型的变量来存储溢出的悲观读锁。

写锁用`state`变量剩下的位来表示，每次获得一个写锁，就加 0000 1000 0000(`WBIT`)。**每次释放一个写锁，并不是减`WBIT`，而是再加上`WBIT`，这样做的目的是让每次写锁都留下痕迹，解决CAS的ABA问题，也为乐观锁见检查变化`validate`方法提供基础。**

乐观读锁并没有改变`state`的值，而是在获取锁的时候记录`state`的状态，在操作完成后检查`state`的写状态部分是否发生变化，因为每次写锁都会留下痕迹。