---
title: Java泛型
date: 2020-07-12 11:45:56
categories: Java
tags:
  - Java泛型
---

## Java泛型

泛型的意思就是**参数化类型**。在没有引入泛型机制前，需要针对不同的数据类型重复编写相同的代码，而引入泛型后就可以**将数据类型与代码逻辑分离开，提高了程序的简洁性和可读性，同时也提供了编译时的类型转换安全检测功能。**

泛型可以将类型参数化，参数一旦确定，如果类型不匹配，就无法通过编译。

<!--more-->

### 泛型的使用

泛型的使用可以分为：

- 泛型类
- 泛型方法
- 泛型接口

### 通配符

对于任意一个类型，可以用字母来表示泛型类型，如`List<T>`，这种是确定的类型，而使用通配符`<?>`则可以指定泛型中的类型范围。

1. `<?>` 是无限定的通配符；
2. `<? extends A>`是有上界的通配符，限定类`A`和`A`的子类；
3. `<? super A>`是有下界的通配符，限定`A`和`A`的父类。

**即便容器的类型之间存在继承关系，但容器间是不构成继承关系的。所以需要将容器类型设置为`<? extends T>`和`<? super T>`来让容器间存在继承关系。**

![](https://f1bu920.github.io/images/Java泛型extends.png)

对于上界`<? extends T>`类型，在编译期，只知道要存放`T`和`T`的子类，具体类型不知道，所以往容器内插入数据不被允许。但读数据时，可以隐式的转化为基类或者Object，所以读操作没有影响。

![](https://f1bu920.github.io/images/Java泛型super.png)

对于下界`<? super T>`类型，规定了元素的最小粒度，必须是`T`或其父类，所以往里添加`T`或其子类都是可以的，因为可以隐式的转化为T类型。而读时无法转化为任意类型，只能用Object类存储。

注意：

- **上界`<? extends T>`不能往里存，只能往外取，适合频繁往外面读取内容的场景。**
- **下界`<? super T>`不影响往里存，但往外取只能放在Object数组中，适合经常往里面插入数据的场景**。



### 类型擦除

**泛型信息只存在于编译阶段，在进入JVM前，所有的泛型信息都会被擦除，这就是类型擦除。**

```java
public class Test {
    public static void main(String[] args) throws IOException {
        List<String> list1 = new ArrayList<>();
        List<Integer> list2 = new ArrayList<>();
        System.out.println(list1.getClass()==list2.getClass());
    }
}
//结果输出为true
```

上面举例中编译后`list1`和`list2`的类型都为`List.class`，泛型信息被擦除了。

在泛型类被类型擦除后，如果泛型类中没有指定上界，如`<T>`，就会被转译为`Object`类型；如果指定了上界，就会被转译为上界类型。

类型擦除保证了能与JDK5之前的代码兼容，但也带来了一些局限性，比如可以通过反射来绕过泛型。如：

```java
public class Test {
    public static void main(String[] args) throws NoSuchMethodException, InvocationTargetException, IllegalAccessException {
        List<String> list = new ArrayList<>();
        list.add("String");
        Class<? extends List> aClass = list.getClass();
        Method add = aClass.getMethod("add", Object.class);
        add.invoke(list, 777);
        System.out.println(list);
    }
}
//输出结果为[String, 777]
```

可以看到利用反射可以向指定了类型为`String`的集合中添加非`String`类型的元素。



### 参考链接

[深入理解Java泛型](https://juejin.im/post/5b614848e51d45355d51f792)

[Java泛型](https://blog.csdn.net/briblue/article/details/76736356)

