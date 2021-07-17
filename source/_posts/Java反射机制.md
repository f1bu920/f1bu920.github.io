---
title: Java反射机制
date: 2020-07-08 10:03:18
categories: Java
tags:
  - 反射
  - Java
---

## Java反射

> Java 反射机制在程序**运行时**，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性。这种 **动态的获取信息** 以及 **动态调用对象的方法** 的功能称为 **Java 的反射机制**。

> 反射机制很重要的一点就是“运行时”，其使得我们可以在程序运行时加载、探索以及使用编译期间完全未知的 `.class` 文件。换句话说，Java 程序可以加载一个运行时才得知名称的 `.class` 文件，然后获悉其完整构造，并生成其对象实体、或对其 fields（变量）设值、或调用其 methods（方法）。

反射可以实现在运行时知道一个类的属性和方法。

![](https://f1bu920.github.io/images/Javareflect.png)

<!--more-->

### Java反射的优缺点

- 优点

  可以实现动态的创建对象和编译，有很大的灵活性。

- 缺点

  反射是一种解释操作，对性能有影响，如使用反射来创建对象效率比不上直接使用`new`关键字，因为要先查找类资源，再使用类加载器创建。



### Class类

`Class`类是实现反射的基础。`Class`类的构造器是私有的，这意味着只有JVM才能创建一个`Class`类型的对象，而不可以直接使用`new`关键字。共有三种方法来获得一个`Class`对象：

1. `Class.forName("类路径")`：通过调用`forName`方法，通过一个类的全量限定名来获得。
2. `类名.class`：任何一个类都有一个隐藏的成员变量`class`，通过类的静态成员变量来获得
3. `对象名.getClass()`：通过一个类的对象的`getClass`方法获得。



### Java反射API

反射 API 用来生成 JVM 中的类、接口或则对象的信息。

1. `Class` 类：反射的核心类，可以获取类的属性，方法等信息。
2. `Field `类：Java.lang.reflec包中的类，表示类的成员变量，可以用来获取和设置类之中的属性值。
3. `Method` 类： Java.lang.reflec包中的类，表示类的方法，它可以用来获取类中的方法信息或者执行方法。
4. `Constructor` 类： Java.lang.reflec 包中的类，表示类的构造方法。

**使用反射需要先获取操作类的`Class`对象，通过`Class`对象可以获取类的信息和方法属性。**

- 获取`public`的变量信息`getFields()`。
- 获取变量信息`getDeclaredFields()`。
- 获取`public`的方法信息`getMethods()`
- 获取方法信息`getMethods()`。
- 获得方法访问权限`getModifiers()`
- 获得方法返回类型`getReturnType()`
- 获得方法参数`getParameters()`

**获取到方法后，使用`invoke`反射调用私有方法。对于私有方法和变量，注意要先获得访问权`setAccessible(**true**)`**。



### 利用反射动态创建对象实例

1. 使用 `Class` 对象的 `newInstance()`方法来创建该 `Class` 对象对应类的实例，但是这种方法要求该 `Class` 对象对应的类有默认的空构造器。 调用 `Constructor` 对象的 `newInstance()`

2. 先使用 `Class` 对象获取指定的 `Constructor` 对象，再调用 `Constructor` 对象的 `newInstance()`方法来创建 `Class` 对象对应类的实例,通过这种方法可以选定构造方法创建实例。



反射在`JavaEE`中也有广泛的应用，如`Class.forName("com.mysql.cj.jdbc.Driver.class")`就是用来加载MySQL的驱动类。此外，`Spring`中也用到了大量的反射。

### 参考链接

[Java反射机制](http://tengj.top/2016/04/28/javareflect/)

[Java反射由浅入深](https://juejin.im/post/598ea9116fb9a03c335a99a4)

[Java反射高频面试题](https://juejin.im/post/5ea92cbd5188256d9a28cd40)