---
title: Spring/SpringBoot常用注解
date: 2020-05-04 15:07:41
categories: SpringBoot
tags:
  - Spring
  - SpringBoot
---

## 1. @SpringBootApplication

这个注解是SpringBoot项目的基石，所有创建的SpringBoot项目都会默认在主类上加上这个注解。

![SpringBoot注解@SpringBootApplication.PNG](https://f1bu920.github.io/images/SpringBoot注解@SpringBootApplication.PNG)

我们可以把`@SpringBootApplication`看成是`@Configuration`、`@EnableAutoConfiguration`和`@ComponentScan`注解的集合。

![](https://f1bu920.github.io/images/SpringBoot注解@SpringBootApplication详情.PNG)

这三个注解的作用分别如下：

- `@EnableAutoConfiguration`：启动SpringBoot的自动配置机制。
- `@ComponentScan`：扫描被`@Component`(`@Service`、`@Controller`)注解的Bean，注解会默认扫描该类所在包下的所有类。
- `@Configuration`：允许在Spring上下文中注册额外的Bean或导入其他配置类。

<!--more-->

## 2. Spring Bean相关

#### 2.1 @Autowired

自动导入对象到类中，被注入进的类同样要被Spring容器管理。例如：`AdminUserServiceImpl`注入到`AdminController`类中。

![](https://f1bu920.github.io/images/Service@Autowired.PNG)

![](https://f1bu920.github.io/images/Controller@Autowired.PNG)

#### 2.2 @Component、@Repository、@Service、@Controller

我们一般使用`@Autowired`注解来让Spring容器帮我们自动装配Bean。我们可以使用`@Component`、`@Repository`、`@Service`和`@Controller`注解将类标记为可用于`@Autowired`自动装配的类。

- `@Component`：通用的注解，可标记任意类为Spring组件。如果一个类不知道属于那一层，可以使用此注解。
- `@Reponsitory`：对应持久层即DAO层，主要用于数据库相关操作。
- `@Service`：对应服务层。主要负责一些复杂的逻辑，需要用到DAO层。
- `Controller`：对应Spring MVC的控制层。主要用于接受用户请求并调用`Service`层返回数据给前端。

#### 2.3 @RestController

`@RestController`注解是`@Controller`和`@ResponseBody`注解的合集，表示这是个控制器Bean，并且将返回值直接填入HTTP的响应体中，是REST风格的控制器。

单独使用`@Controller`主要用于返回一个视图，这是传统Spring MVC的应用，属于前后端不分离的场景；使用`RestController`或者使用`@Controller`加`@ResponseBody`返回JSON或XML形式的数据，用于前后端分离的场景。

#### 2.4 @Scope

声明Spring Bean的作用域。如：`@Scope("singleton")`。其有以下作用域：

- `singleton`：唯一Bean实例，Spring中的Bean默认都是单例的。
- `prototype`：每次请求都会创建一个新的Bean实例。
- `request`：每次HTTP请求都会产生一个新的Bean，该Bean仅在当前HTTP request内有效。
- `session`：每次HTTP请求都会产生一个新的Bean，该Bean仅在当前HTTP session内有效。

#### 2.5 @Configuration

一般用于声明配置类，可以使用`@Component`注解代替。

![](https://f1bu920.github.io/images/@Configuration注解.PNG)



## 3. 处理常见的HTTP请求类型

常见的有5种请求类型：

- `GET`：请求从服务器获取特定资源。
- `POST`：在服务器上创建一个新的资源。
- `PUT`：更新服务器上的资源。客户端提供更新后的整个资源。
- `DELETE`：删除服务器上的特定资源。
- `PATCH`：更改服务器上上的资源，客户端提供更改的属性，可看成局部更新。

#### 3.1 GET请求

`@GetMapping("/login")`等价于`@RequestMapping(value="/login", method=RequestMethod.GET)`。

![@GetMapping.PNG](https://f1bu920.github.io/images/@GetMapping.PNG)

#### 3.2 POST请求

`@PostMapping("/login")`等价于`@RequestMapping(value="/login", method=RequestMethod.POST)`。

![@PostMapping.PNG](https://f1bu920.github.io/images/@PostMapping.PNG)

#### 3.3 PUT请求

`@PostMapping("/users/{userId}")`等价于`@RequestMapping(value="/users/{userId}", method=RequestMethod.PUT)`。

#### 3.4 DELETE请求

`@DeleteMapping("/users/{userId}")`等价于`@RequestMapping(value="/users/{userId}", method=RequestMethod.DELETE)`。

#### 3.5 PATCH 请求

`@PatchMapping("/profile")`等价于`@RequestMapping(value="/profile", method=RequestMethod.PATCH)`。



## 4. 前后端传值



