---
title: Java异常
date: 2020-07-05 11:02:00
categories: Java
tags:
  - Java
  - Exception
---

## Java异常

下图是Java异常层次结构图：

![](https://f1bu920.github.io/images/Java异常结构.png)

在Java中，**所有的异常都有一个公共父类`Throwable`，其有两个重要的子类`Error`错误和`Exception`异常。其中，`Error`大多是虚拟机层面发生的错误，是程序无法处理的错误；而`Exception`则是用户程序可以捕获或可以处理的异常。`Exception`可以分为运行时异常`RuntimeException`和非运行时异常；又可以分为不受检查异常`Unchecked Exception`和检查异常`Checked Exception`。**

 <!--more-->

`Exception`中有一个重要的子类运行时异常`RuntimeException`，常见的异常如空指针异常`NullPointerException`、算数异常`ArithmeticException`、数组下标越界异常`ArrayIndexOutOfBoundsException`、丢失资源异常`MissingResourceException`、找不到类异常`ClassNotFoundException`和参数异常`IllegalArgumentsException`等。**这些异常都是不检查异常，程序可以选择捕获处理也可以选择不处理。而`RuntimeException`之外的异常都是检查异常，必须选择`try-catch`进行捕获或者`throws`进行抛出，否则通不过编译。**

### Java异常处理

**Java异常处理本质上是抛出异常和捕获异常。**

运行时异常由Java运行时系统自动抛出，允许程序忽略。而对于所有的检查异常，必须进行捕获处理或者抛出给它的调用者，如`IOException`。

- 捕获异常

  通过`try-catch-finally`语句块进行捕获异常。可能发生异常的代码块放在`try`中，并使用`catch`进行捕获发生的异常，可以选择使用`finally`块进行资源的关闭，因为`finally`块中的代码一定会被执行。

  **注意有多个`catch`语句时，异常会依次进行检查，直到被匹配。因此异常要先小后大，先子类后父类。**

- 抛出异常

  抛出异常分为手动抛出和系统抛出。

  程序中可以使用`throw`关键字手动抛出异常。对于不受检查异常，系统会自动抛出异常。

  如果在一个方法中可能导致一个异常但不处理，则必须在方法声明处使用`throws`指出抛出异常的列表。

  **如果是不受检查异常`Unchecked Exception`，如`Error`和`RuntimeException`，那么可以不使用`throws`关键字来声明抛出的异常，在运行时系统会自动抛出。	**

  **如果是检查异常`Checked Exception`，则必须选择进行`try-catch`捕获处理或者`throws`抛出。否则无法通过编译。**

  