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

#### 4.1 @PathVariable和@RequestParam

`@PathValiable`用于获取路径参数，`@RequestParam`用于获取查询参数。

![](https://f1bu920.github.io/images/@PathVariable.PNG)

如果我们请求的URL为：`/blogs/edit/1`，获得的数据就是`blogId=1`。

![](https://f1bu920.github.io/images/@RequestParam.PNG)

如果我们请求的URL为：`/blogs/save?blogTitle=title`，获得的数据就是`blogTitle=title`。

#### 4.2 @RequestBody

用于读取Request请求的Body部分，并且`Content-Type`为`application/json`格式的数据，接收到数据后自动绑定到Java对象上去。系统会使用`HttpMessageConverter`或者自定义的`HttpMessageConverter`将请求的body中的json字符串转换为Java对象。

![](https://f1bu920.github.io/images/@RequestBody.PNG)

**注意：一个请求方法只可以有一个`@RequestBody`，但可以有多个`RequestParam`和`PathVariable`。**



## 5. 读取配置信息

很多时候需要把一些常用的配置信息放在配置文件中，Spring提供了以下方法来读取去这些配置信息。

#### 5.1 @value

使用`@value("${property}")`读取一些简单的配置信息。

#### 5.2 @ConfigurationProperty

通过`@ConfigurationProperty`读取配置信息并与Bean绑定。

可以像使用Bean一样，将其注入到类中。

#### @PropertySource

`@PropertySource`指定读取`properties`文件。



## 6. 参数校验

在前端已经进行校验的情况下，为了避免一些http工具向后台请求一些违法的数据，还是要对传入后端的数据在进行一遍数据校验。

JSR就是一套数据校验标准，其定义了很多常用的校验注解，我们可以直接将这些校验注解加在JavaBean的属性上，这样就可以在需要校验时进行校验了。

校验时使用的是`Hibernate Validator`框架，SpringBoot的`spring-boot-starter-web`依赖中已经包含了`hibernate-validator`包，不需要额外导入。**所有的注解，推荐使用`javax.validation.constraints`。**

#### 6.1 一些常用的字段验证的注解

- `@NotEmpty`：被注释的字符串不能为`null`也不能为空。
- `@NotBlank`：被注释的字符串不能为`null`，且必须包含一个非空字符。
- `@Null`：被注释的元素必须为`null`。
- `@NotNull`：被注释的元素必须不为`null`。
- `@AssertTrue`：被注释的元素必须为`true`
- `@AssertFalse`：被注释的元素必须为`false`
- `@Pattern(regex= ,flag= )`：被注释的元素必须符合指定的正则表达式。
- `@Email`：被注释的元素必须符合Email格式。
- `@Min(value)`：被注释的元素必须是一个数字，且必须大于等于指定的`value`
- `@Max(value)`：被注释的元素必须是一个数字，且必须小于等于指定的`value`
- `@DecimalMin(value)`：被注释的元素必须是一个数字，且必须大于等于指定的`value`
- `@DecimalMax(value)`：被注释的元素必须是一个数字，且必须小于等于指定的`value`
- `@Size(max= ,min= )`：被注释的元素的大小必须在指定的范围之内
- `@Digits(integer, fraction)`：被注释的元素必须是一个数字，且必须在可接受的范围之内
- `@Past`：被注释的元素必须是一个过去的日期
- `@Future`：被注释的元素必须是一个未来的日期
- ......

#### 6.2 验证请求体(RequestBody)

给需要验证的参数加上`@Valid`注解，如果验证失败，将抛出`MethodArgumentNotValidException`。

#### 6.3 验证请求参数(PathVariable和RequestParam)

**验证请求参数时，一定要在类上加上`@Validated`注解，这个注解会告诉Spring去校验方法参数。**

![](https://f1bu920.github.io/images/@Validated.PNG)

![](https://f1bu920.github.io/images/@Valid.PNG)



## 7.全局处理Controller异常

1. `@ControllerAdvice`：注解定义全局异常处理类。
2. `@ExceptionHandler`：注解申明异常处理方法。

例如，对于参数校验时抛出的`MethodArgumentNotValidException`异常，可以在类上加上`@ControllerAdvice`注解，在处理方法上加上`@ExceptionHandler()`并指定处理的异常类。

![](https://f1bu920.github.io/images/@ControllerAdvice.PNG)



## 8. JPA相关

#### 8.1 创建表

1. `@Entity`：声明一个类对应一个数据库实体。
2. `Table`：设置表名。

![](https://f1bu920.github.io/images/JPA相关@Entity.PNG)

#### 8.2 创建主键

如上图所示，使用`@Id`将一个字段声明为主键。此外，还需要声明主键的生成策略，在MySQL中一般使用`@GeneratedValue(strategy = GenerationType.IDENTITY)`，主键自动递增。这里`GenerationType`是枚举类型，其有以下几种：

```java
public enum GenerationType {
    //使用一个特定的数据库表来存储主键，持久化引擎通过关系数据库的一张特定的表格来生成主键
    //好处是不依赖于外部环境和数据库的具体实现，在不同数据库间可以很容易的移植。
    TABLE,
    //在某些数据库中不支持主键自增长，比如Oracle,其提供了一种叫做"序列(sequence)"的机制生成主键。
    SEQUENCE,
    //主键自增长
    IDENTITY,
    //把主键生成策略交给持久化引擎(persistence engine),持久化引擎会根据数据库在以上三种主键生成策略中选择其中一种。
    //这也是JPA默认的生成策略
    AUTO;
    private GenerationType() {
    }
}
```



#### 8.3 设置字段类型

`@Column`声明字段。举例：

```java
    @Column(name = "username",nullable = false,length = 225)
    private String username;
    @Column(name = "password",columnDefinition = "varchar(225) default 123456")
    private String password;
```



#### 8.4 指定不可持久化字段

利用`@Transient`声明不需要与数据库映射的字段，在保存时不保存进数据库。

也可以将属性声明为`static`、`final`或直接声明为`transient`类型。



#### 8.5 声明大字段

`@Lob`：声明字段为大字段。



#### 8.6 创建枚举类型的字段

使用`@Enumerated`注解创建枚举类型字段。

#### 8.7 删除、修改数据

`@Modifying`注解表示该操作是修改操作，注意一般会与`@Transactional`配合使用。

#### 8.8 关联关系

- `@OneToOne`：声明一对一关系
- `OneToMany`：声明一对多关系
- `ManyToOne`：声明多对一关系
- `ManyToMany`：声明多对多关系。

## 9. 事务@Transactional

使用`@Transactional`注解在要开启事务的方法上开启事务。

注意，在`Transactional`注解中如果不配置`rollbackFor`属性，那么只会在遇到`RuntimeException`时才会回滚。加上`rollbackFor=Exception.class`后在遇到非运行时异常时也会回滚。

`Transactional`作用于类上时，表示该类的所有方法都配置了这个事务属性；当类上配置了`Transactional`，方法上再次配置则会覆盖类上的配置信息。



## 10. json数据处理

#### 10.1 过滤json数据

使用`@JsonIgnoreProperties`注解作用于类上来过滤指定的字段不返回或者不解析。

也可以使用`@JsonIgnore`作用于类的属性上，作用相同。

#### 10.2 格式化json数据

使用`@JsonFormat`来格式化json数据。



## 11. 单元测试

`@ActiveProfiles`一般用于测试类上，指定生效的配置文件。

`Test`声明一个方法为测试方法。

`@Transactional`被声明的测试方法的数据会回滚，避免污染测试数据。

`WithMockUser`，Spring Security提供的用于模拟一个真实用户，并且可以赋予权限。

平时写SpringBoot单元测试时，需要在类上加上`@RunWith(SpringRunner.class)`和`@SpringBootTest`。

```java
@RunWith(SpringRunner.class)
@SpringBootTest
public class AdminUserMapperTest {
    @Resource
    private AdminUserMapper adminUserMapper;

    @Test
    @Transactional
    public void test() {
    }
}
```

