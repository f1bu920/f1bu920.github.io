---
title: Java多线程入门类与接口
date: 2020-04-06 11:38:24
categories: Java多线程
tags:
  - Java
  - 多线程
---

[TOC]

# Java入门类和接口

## `Thread`类和`Runnable`接口

Java中，JDK提供了`Thread`类和`Runnable`接口，我们有两种方法来实现自己的线程类。

- 继承`Thread`类，并重写`run`方法。
- 实现`Runnable`接口的`run`方法。

<!--more-->

#### 继承`Thread`类

```java
public class MyThread extends Thread {
    public static void main(String[] args) {
        MyThread myThread = new MyThread();
        myThread.start();
    }

    @Override
    public void run(){
        System.out.println("我是线程： "+Thread.currentThread().getName());
    }
}
```

**注意：一定要调用`start()`方法该线程才算启动。**

**`new`一个`Thread`，线程进入新建状态；调用`start()`方法会启动一个线程并使线程进入就绪状态，当分配到CPU时间片就可以运行了。`start()`方法会执行线程的相应准备工作，然后自动执行`run()`方法的内容，这才是真正的多线程。而直接调用`run()`方法，会把`run()`方法当成一个`main`线程下的普通方法去执行，还是在主线程中。**

多次调用`start()`方法会抛出`java.lang.IllegalThreadStateException`异常。

#### 实现`Runnable()`接口

`Runnable()`接口源码如下：

```java
@FunctionalInterface
public interface Runnable {
    /**
     * When an object implementing interface <code>Runnable</code> is used
     * to create a thread, starting the thread causes the object's
     * <code>run</code> method to be called in that separately executing
     * thread.
     * <p>
     * The general contract of the method <code>run</code> is that it may
     * take any action whatsoever.
     *
     * @see     java.lang.Thread#run()
     */
    public abstract void run();
}
```

可以看到`Runnable`接口是一个函数式接口，这代表着可以使用函数式编程简化。

```java
public class ThreadTest {
    public static void main(String[] args) {
        new Thread(() -> {
            System.out.println("我是线程： " + Thread.currentThread().getName());
        }).start();
    }
}
```

不采用`lambda`要这样用：

```java
public class ThreadTest {
    public static class thread1 implements Runnable{

        @Override
        public void run() {
            System.out.println("runnable "+Thread.currentThread().getName());
        }
    }

    public static void main(String[] args) {
        new Thread(new thread1()).start();
    }
}
```



#### `Thread`类构造器

`Thread`类是`Runnable`接口的实现类，其构造方法源码如下：

```java
    /**
     * Allocates a new {@code Thread} object. This constructor has the same
     * effect as {@linkplain #Thread(ThreadGroup,Runnable,String) Thread}
     * {@code (null, null, gname)}, where {@code gname} is a newly generated
     * name. Automatically generated names are of the form
     * {@code "Thread-"+}<i>n</i>, where <i>n</i> is an integer.
     */
    public Thread() {
        init(null, null, "Thread-" + nextThreadNum(), 0);
    }

    /**
     * Allocates a new {@code Thread} object. This constructor has the same
     * effect as {@linkplain #Thread(ThreadGroup,Runnable,String) Thread}
     * {@code (null, target, gname)}, where {@code gname} is a newly generated
     * name. Automatically generated names are of the form
     * {@code "Thread-"+}<i>n</i>, where <i>n</i> is an integer.
     *
     * @param  target
     *         the object whose {@code run} method is invoked when this thread
     *         is started. If {@code null}, this classes {@code run} method does
     *         nothing.
     */
    public Thread(Runnable target) {
        init(null, target, "Thread-" + nextThreadNum(), 0);
    }
    /**
     * Allocates a new {@code Thread} object. This constructor has the same
     * effect as {@linkplain #Thread(ThreadGroup,Runnable,String) Thread}
     * {@code (null, target, name)}.
     *
     * @param  target
     *         the object whose {@code run} method is invoked when this thread
     *         is started. If {@code null}, this thread's run method is invoked.
     *
     * @param  name
     *         the name of the new thread
     */
    public Thread(Runnable target, String name) {
        init(null, target, name, 0);
    }

```

可以看到`Thread`类的构造器会调用私有的`init()`方法来实现初始化。`init`方法的方法签名如下：

```java
    private void init(ThreadGroup g, Runnable target, String name,
                      long stackSize, AccessControlContext acc,
                      boolean inheritThreadLocals)
```

`init`方法参数的解释：

- `g`：线程组，制定这个线程在哪个线程组下
- `target`：指定要执行的任务；
- `name`：线程的名字，多个线程的名字是可以重复的。如果不指定名字，会默认指定为`"Thread-" + nextThreadNum()`。
- `acc`：用于初始化私有变量`inheritedAccessControlContext`。
- `inheritThreadLocals`：可继承的`ThreadLocal`，`Thread`类中有两个私有属性来支持`ThreadLocal`。

大多数情况下我们调用下面两种构造方法：

- `Thread(Runnable target)`
- `Thread(Runnable target, String name)`

#### `Thread`类的常用方法

- `currentThread()`：静态方法，返回对当前正在执行的线程对象的引用。
- `start()`：开始执行线程的方法，Java虚拟机会调用线程内的`run`方法。
- `yield()`：当前线程愿意让出对当前处理器的占用。要注意的是，就算当前线程调用了`yield()`方法，程序在调度的时候，也还有可能继续运行这个线程的。
- `sleep()`：静态方法，使当前线程睡眠指定时间。
- `join()`：使当前线程等待另一个线程执行完毕后再继续执行，内部调用的是`Object类`的`wait()`方法。



#### **`Thread`类与`Runnable`接口的比较**

实现一个自定义线程类，可以有继承`Thread`类和实现`Runnable`接口两种方法。

- 由于Java单继承、多实现的特点，`Runnable`接口使用更加灵活；
- `Thread`类就是`Runnable`接口的实现；
- `Runnable`接口更加符合面对对象，将线程单独进行对象的封装；
- `Runnable`接口的出现，降低了线程对象与线程任务的耦合性；
- 如果不需要使用`Thread`类的其他方法，使用`Runnable`接口更加轻量

综上，我们通常优先使用`Runnable`接口来自定义线程类。



## `Callable`、`Future`与`FutureTask`

通常我们使用`Runnable`和`Thread`来创建一个线程，但是`run`方法是没有返回值的。如果我们希望执行一个任务后返回一个值，我们可以使用JDK提供的`Callable`接口和`Future`类。这也是所谓的“异步”模型。

#### `Callable`接口

`Callable`接口与`Runnable`接口类似，同样是一个函数式接口，不同的是`Callable`接口**有返回值且支持泛型**。

```java
@FunctionalInterface
public interface Callable<V> {
    /**
     * Computes a result, or throws an exception if unable to do so.
     *
     * @return computed result
     * @throws Exception if unable to compute a result
     */
    V call() throws Exception;
}
```

**`Callable`接口一般是配合线程池工具`ExecutorSevice`来使用的。`ExecutorService`可以使用`submit`方法来让一个`Callable`接口执行，它会返回一个`Future`，我们后续的程序可以通过这个`Future`的`get`方法得到结果**。一个简单的demo如下：

```java
//自定义Callable
public class ThreadTest implements Callable<Integer>{

    @Override
    public Integer call() throws Exception {
        Thread.sleep(1000);
        return 2;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        //使用
        ExecutorService executor = Executors.newCachedThreadPool();
        //传入一个Callable对象
        Future<Integer> result = executor.submit(new ThreadTest());
        //注意调用get方法会阻塞当前线程，直到得到结果
        //实际编码中建议使用可以设置超时时间的重载get方法
        System.out.println(result.get());
    }
}
```



#### `Future`接口

`Future`接口的源码如下：

```java
public interface Future<V> {
    boolean cancel(boolean mayInterruptIfRunning);
    boolean isCancelled();
    boolean isDone();
    V get() throws InterruptedException, ExecutionException;
    V get(long timeout, TimeUnit unit)
        throws InterruptedException, ExecutionException, TimeoutException;
}
```

`cancel`方法是试图取消一个线程的执行。**注意是试图取消，不一定取消成功。**因为任务可能已完成、已取消或者不可取消。最后返回是否取消成功。

参数`mayInterruptIfRunning`代表是否采用中断的方式取消线程执行。

所以有时候，为了让线程有能够取消的可能，就会使用`Callable`代替`Runnable`。若只是为了可取消性，而不需要结果，可以声明`Future<?>`形式类型，并返回`null`作为结果。

#### `FutureTask`类

`Future`接口有一个实现类`FutureTask`，这个类实现了`RunnableFuture`接口，而`RunnableFuture`接口同时继承了`Runnable`接口和`Future`接口。

`RunnableFuture`接口源码如下：

```java
public interface RunnableFuture<V> extends Runnable, Future<V> {
    /**
     * Sets this Future to the result of its computation
     * unless it has been cancelled.
     */
    void run();
}
```

`Future`只是一个接口，里面的`cancel`、`get`、`isDone`等方法自己实现会很复杂，所以JDk提供了一个`FutureTask`类。使用示例：

```java
public class ThreadTest implements Callable<Integer> {

    @Override
    public Integer call() throws Exception {
        Thread.sleep(1000);
        return 2;
    }

    public static void main(String[] args) throws ExecutionException, InterruptedException {
        ExecutorService executor = Executors.newCachedThreadPool();
        FutureTask<Integer> futureTask = new FutureTask<>(new ThreadTest());
        //这里实际调用的是submit(Runnable task)方法
        executor.submit(futureTask);
        System.out.println(futureTask.get());
    }
}
```

这里调用`submit`方法是没有返回值的。这里实际上调用的是`submit(Runnable task)`方法，而上面那个demo中，调用的是`submit(Callable<T> task)`方法。

这里是使用`futureTask`直接`get`取值，而上面Demo中是使用返回的`Future`去取值。

**在高并发的情景下，有可能`Callable`和`FutureTask`会创建多次。`FutureTask`能确保在高并发下任务只被执行一次。**



#### `FutureTask`的几个状态

```java
    /**
     *state可能的状态转变路径如下：
     * Possible state transitions:
     * NEW -> COMPLETING -> NORMAL
     * NEW -> COMPLETING -> EXCEPTIONAL
     * NEW -> CANCELLED
     * NEW -> INTERRUPTING -> INTERRUPTED
     */
    private volatile int state;
    private static final int NEW          = 0;
    private static final int COMPLETING   = 1;
    private static final int NORMAL       = 2;
    private static final int EXCEPTIONAL  = 3;
    private static final int CANCELLED    = 4;
    private static final int INTERRUPTING = 5;
    private static final int INTERRUPTED  = 6;
```

`state`表示任务的运行状态。**初始状态为`NEW`。运行状态只会在`set`、`setException`、`cancel`方法中终止。`COMPLETING`、`INTERRUPTING`是任务完成后的瞬时状态。**

