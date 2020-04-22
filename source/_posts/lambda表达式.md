---
title: lambda表达式
date: 2019-11-24 20:18:48
categories: Java
tags:
  - lambda
  - Java
---

## lambda表达式

### 格式：参数 -> (表达式)

如果代码要完成的计算无法放在一个表达式中，可以把代码放在{}中。

~~~java
Arrays.sort(str, (String first,String second) -> (return (first.length()-second.length()))
~~~

即使`lambda`无参数，也要提供空括号.

~~~java
()-> {for(int i=0;i<10;i++) System.out.println(i);}
~~~

如果可以推导出一个`lambda`的参数类型，则可以忽略参数类型

~~~java
Arrays.sort(friends,(first,second) -> (first.length()-second.length()));//friends是一个字符串数组
~~~

无需指定lambda的返回值类型，返回值类型会由上下文推导得到

~~~java
(String str1,String str2) -> str1.length()-str2.length();
~~~

<!--more--> 

### 函数式接口

对于只有一个抽象方法的接口，需要这种接口的对象时，就可以提供一个`lambda`，这种接口叫做函数式接口。

**lambda可以转换为函数式接口**，举例：

~~~java
Arrays.sort(words, (first,second)->first.length()-second.length());
people.removeIf(person -> (person.getName().length()>3)); //removeIf接受一个Predicate参数，这个接口专门用来传递lambda表达式
~~~

### 方法引用

可以`::`分隔方法名与对象或类名。主要有3种情况。

1. `object::instanceMethod`
2. `Class::staticMethod`
3. `Class:;instanceMethod`

前2种情况下，方法引用等价于提供方法参数的lambda表达式。

> System.out::println等价于x -> System.out.println(x);
>
> Math::power等价于(x,y) -> (Math.power(x,y));

  第三种情况下，第一个参数会成为方法的目标。

> String::compareToIgnoreCase等价于(x,y) -> x.compareToIgnoreCase(y);

方法引用中也可以使用`this`和`super`，如``super::instanceMethod`.

### 构造器引用

构造器引用用法与方法引用类似，只不过方法名为new。举例：

~~~java
int []::new  //等价于 x -> new int[x],x是数组的长度
Perosn [] people = stream.toArray(Person::new);   
~~~

### 变量作用域

通常会使用`lambda`访问外围方法或类中的变量

~~~java
public static void repeatMessage(String str){
    ActionListener listener = event ->
    {
        System.out.println(str);
    }
    ...
}
...
repeatMessage("hello");
~~~

这里str就是一个自由变量。我们说它被lambda捕获。

`lambda`表达式有3个部分：

1. 一个代码块
2. 参数
3. 自由变量的值，这里指非参数而且不在代码中定义的变量。

**在Java中，`lambda`就是闭包**。

**`lambda`只能捕获值不变的变量**，指变量初始化后就不会再为它赋新值。

### 处理lambda表达式

使用`lambda`的重点是延迟执行，例如

1. 在一个单独的线程中运行代码
2. 多次运行代码
3. 在算法的合适位置运行代码(例如，排序中的比较操作)
4. 发生某种情况时执行代码(如，点击了某个按钮，数据到达等)
5. 只在必要时运行代码

~~~java
interface IntConsumer{
	void accept(int value);
}
public static void repeat(int n, IntConsumer action){
	for (int i =0;i<n;i++){
		action.accept(i);
	}
}
Student.repeat(10,i-> System.out.println("CountDown:"+(9-i)));//调用
~~~

### 再谈Comparator

`Comparator`接口包含很多的静态方法来创建构造器，这些方法可用于lambda表达式或方法引用。

~~~java
Arrays.sort(people,Comparator.comparing(Person::getName));//按名字对people进行排序
Arrays.sort(people,Comparator.comparing(Person::getLastName).thencomparing(Person::getFirstName));//如果两人的姓一致则比较名；
~~~