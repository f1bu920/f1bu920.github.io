---
title: MyBatis学习笔记
date: 2020-04-20 17:46:57
categories: MyBatis
tags:
  - SpringBoot
  - 持久层框架
  - MyBatis
---

# MyBatis学习笔记

`MyBatis`是一款优秀的持久层框架，支持自定义SQL、存储过程和高级映射。`MyBatis`免除了几乎所有的JDBC代码以及设置参数和获取结果集的工作。可以通过简单的XML或注解来配置和映射原始类型、接口和`POJO`为数据库中的记录。

## MyBatis安装与入门

[MyBatis下载链接](https://github.com/mybatis/mybatis-3/releases)

要使用`MyBatis`，只需将下载的jar文件置于类路径(classpath)中。

如果使用`Maven`构建项目就更简单了，只需将依赖添加到`pom.xml`文件中。

```xml
<dependency>
  <groupId>org.mybatis</groupId>
  <artifactId>mybatis</artifactId>
  <version>x.x.x</version>
</dependency>
```

<!--more-->

## XML配置

`MyBatis`的配置文件会影响`MyBatis`的行为的设置和属性信息。

#### 属性(properties)

属性可以在外部配置，也可以进行动态替换。

设置好的属性可以进行动态替换

```xml
<dataSource type="POOLED">
  <property name="driver" value="${driver}"/>
  <property name="url" value="${url}"/>
  <property name="username" value="${username}"/>
  <property name="password" value="${password}"/>
</dataSource>
```

这里`username`和`password`的值会由properities中的值来替换。

#### 设置(settings)

![MyBatis 3 - 配置.png](https://f1bu920.github.io/images/MyBatis 3 - 配置.png)

