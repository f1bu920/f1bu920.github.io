---
title: JSP笔记
date: 2020-03-11 15:59:23
categories: Jsp
tags: Java
---

# JSP

### 简介

jsp是一个简化的Servlet设计，实现了在Java中使用HTML标签。Jsp是一种动态网页技术标准也是javaee的标准。Jsp与Servlet一样，是在服务器端执行的。

<!--more-->

## Jsp页面元素构成

静态内容、指令、表达式、小脚本、声明、注释

#### Jsp指令

- page指令：通常位于jsp页面顶端，同一个页面可以有多个page指令

  **page指令语法：<%@ page 属性1=“属性值“ 属性2=”属性值2“ ...%>**

  |    属性     |                  描述                  |        默认值         |
  | :---------: | :------------------------------------: | :-------------------: |
  |  language   |       指定JSP页面使用的脚本语言        |         java          |
  |   import    | 通过该属性引用脚本语言中使用到的类文件 |          无           |
  | contentType |    用来指定JSP页面所采用的编码方式     | text/html、ISO-8859-1 |

- include指令：将一个外部文件嵌入到当前jsp文件中，同时解析这个页面中的jsp语句

- taglib指令：使用标签库定义新的自定义标签，在JSP页面中启用定制行为

#### Jsp注释

- html的注释：<!-- html注释-->，客户端可见
- jsp注释：<%-- jsp注释--%>，客户端不可见
- jsp脚本注释：// 单行注释，/**/ 多行注释，客户端不可见

#### jsp脚本

在jsp页面中执行的代码

语法：<% java代码 %>

#### jsp声明

在jsp页面中定义变量和方法

语法：<%! java代码 %>

#### jsp表达式

在jsp页面中执行的表达式

语法：<% =表达式 %> **注意：表达式不以分号结束**

### jsp页面生命周期

jspService()方法被调用来处理客户端的请求。对每一个请求，JSP引擎创建一个新的线程来处理该请求。如果有多个客户端同时请求该JSP文件，则JSP引擎会创建多个线程，每个客户端请求对应一个线程。以多线程方式执行可以大大降低对系统的资源要求，提高系统的并发量和响应时间。但也要注意线程的同步问题。

## Jsp内置对象

九大内置对象：out、request、response、session、application、Page、pageContext、exception、config

#### out对象

out对象是JspWriter类的实例，是向客户端输出内容常用的对象，它会通过JSP容器自动转化为java.io.PrintWriter类对象。

常用方法：

- void println() 向客户端打印字符串
- void clear() 清除缓冲区的内容，如果在flush()之后调用会抛出异常
- void clearBuffer() 清除缓冲区的内容，在flush()之后调用不会抛出异常
- void flush() 将缓冲区内容输出到客户端
- int getBufferSize()，返回返回缓冲区以字节数的大小，如果不设缓冲区则为0
- int getRemaining()，返回缓冲区还剩余多少可用
- boolean isAutoFlush(), 返回缓冲区满时，是自动清空还是抛出异常
- void close()，关闭输出流

#### request对象

客户端的请求被封装在request对象中，通过它才能了解到客户的需求，然后做出响应。它是HttpServletRequest类的实例。request对象具有请求域，即在完成客户端的请求之前一直有效。常用方法：

- `String getParameter(String name)`，返回name指定参数的参数值
- `String[] getParameterValues(String name)`，返回包含参数name的所有值的数组
- `void setAttribute(String,Object)`, 存储此请求中的属性
- `object getAttribute(String name)`，返回指定属性的属性值
- `String getContentType()`，得到请求体的MIME类型
- `String getProtocol()`，返回请求用的协议类型和版本号
- `String getServerName()`，返回接受请求的服务器主机名
- `int getServerPort()`，返回服务器接受此请求所用的端口号
- `String getCharacterEncoding()`，返回字节编码方式
- `void setCharacterEncoding()`，设置请求的字节编码方式
- `int getContentLength()`，返回请求体的长度
- `String getRemoteAddr()`，返回发送此请求的客户端IP地址
- `String getRealPath(String path)`，返回一虚拟路径的真实地址
- `String request.getContextPath`，返回上文路径



#### response对象

response对象包含了响应客户端请求的有关信息，但在JSP中很少直接用到它。它是HttpServletResponse的实例。response对象具有页面作用域，即访问一个页面时，该页面内的response对象只能对这次访问有效，其他页面的response对象对当前页面无效。常用方法：

- `String getCharacterEncoding()`，返回响应用的字符编码
- `void setContentType(String type)`，设置响应的MIME类型
- `PrintWriter getWriter()`，返回一个可以向客户端输出字符的一个对象（与内置out对象有区别）
  - PrintWriter类型对象输出总是提前于内置out对象，可以配合flush()方法强制刷新缓冲区
- `sendRedirect(java.lang.String location)`，重新定向客户端的请求



#### 请求转发与请求重定向的区别

- 请求重定向：客户端行为，`response.sendRedirect(),` 从本质上讲等同于两次请求，前一次的请求对象不会保存，地址栏url地址会改变
- 请求转发：服务器行为，`request.getRequestDispatcher().forward(req,resp);`是一次请求，转发后请求对象会保存，地址栏的url地址不会改变

### session对象

session对象是一个JSP内置对象，它是HttpSession类的实例。

session对象在第一个JSP页面被加载时自动创建，完成会话期管理。

session表示客户端与服务器的一次会话。Web中的session指的是用户在浏览某个网站时从进入网站到浏览器关闭所经过的时间。

当一个客户访问服务器时，可能在服务器的几个页面之间切换，服务器应当通过某种方法知道是一个客户，就需要session对象。

在服务器内存中保存着不同用户的session，常见方法：

- `long getCreationTime()`，返回SESSION创建时间
- `public String getId()`，返回session创建时JSP引擎为它创建的唯一id号
- `public Object setAttribute(String name,Object value)`,使用指定名称将对象绑定到此对象
- `public Object getAttribute(String name)`,返回与此会话中的指定名称绑定在一起的对象，如果没有对象绑定在该名称下，则返回null
- `String[] getValueNames()`,返回一个包含此session中所有可用属性的数组
- `int getMaxInactiveInterval()`, 返回两次请求间隔多长时间此session被取消。

#### session的生命周期

- 创建：

  当客户端第一次访问某个JSP或Servlet时，服务器会为当前会话创建一个SessionId，每次客户端向服务端发送请求时，都会将此SessionId带过去，服务端会对此SessionId进行校验

- 活动：

  - 某次会话中通过超链接打开的新页面属于同一会话
  - 只要当前会话页面没有全部关闭，重新打开的浏览器窗口访问同一项目资源时属于同一会话
  - 除非本次会话的所有页面都关闭后重新访问某个Jsp或者Servlet将会创建新的会话
  - **注意**：**原有会话还存在，只是这个旧的SessionId仍然存在于服务器端，只不过再也没有客户端会携带它交与服务端校验。**

- 销毁：

  销毁的三种方式：

  1. 调用了session.invalidate()方法
  2. Session过期(超时)
  3. 服务器重新启动

- 设置超时时间：

  Tomcat的默认session超时时间为30分钟

  1. session.setMaxInactiveInterval(时间); //秒数

  2. 在web.xml中设置：

     > <session-config>
     >
     > <session-timeout>
     >
     > 10
     >
     > </session-timeout>
     >
     > </session-config>  //单位是分钟



#### application对象

application对象实现了用户间数据的共享，可存放全局变量

application开始于服务器的启动，结束于服务器的关闭

在用户的前后连接或不同用户的连接中，可以对application对象的同一属性进行操作

在任何地方对application对象属性的访问都将影响到其他用户对此的访问

服务器的启动和关闭决定了application对象的生命

application对象是ServletContext类的实例

常用方法：

-  `public void setAttribute(String name,Object value)`,使用指定名称将对象绑定到此会话
-  `public Object getAttribute(String name)`,返回与此会话中指定名称绑定在一起的对象，如果没有则返回null
-  `Enumeration getAttributeNames()`，返回所有可用属性名的枚举
-  `String getServerInfo()`, 返回JSP引擎名及版本号



#### page对象

page对象就是指向当前JSP页面本身，类似类中的this指针，它是java.lang.Object类的实例。常用方法如下：

- `class getClass()`, 返回此Object的类
- `int hashCode()`，返回此Object的hash码
- `boolean equals(Object obj)`，判断此Object与指定的Object是否相等
- `void copy(Object obj)`， 将此Object拷贝到指定的Object中
- `void clone()`，克隆此Object对象
- `String toString()`，将此Object对象转换为String类的对象
- `void notify()`, 唤醒一个等待的线程
- `void notifyAll()`，唤醒所有等待的线程
- `void wait(int timeout)`，使一个线程处于等待直到timeout结束或被唤醒
- `void wait()`，使一个线程处于等待直至被唤醒



#### pageContext对象

pageContext对象提供了对JSP页面内的所有的对象及名字空间的访问

pageContext可以访问到本页所在的session，也可以取本页面所在的application的某一属性值

pageContext对象相当于页面中所有功能的集大成者

pageContext对象的本类名也叫pageContext

常用方法：

- `JspWriter getOut()`，返回当前客户端响应被使用的JspWriter流（out）
- `HttpSession getSession()`，返回当前页中的HttpSession对象（session）
- `Object getPage()`，返回当前页面的Object对象（page）
- `ServletRequest getRequest()`，返回当前页的ServletRequest对象（request）
- `ServletResponse getResponse()`，返回当前页的ServletResponse对象（response）
- `void setAttribute(String name,Object attribute)`，设置属性及属性值
- `Object getAttribute(String name,int scope)`，在指定范围内取属性值
- `int getAttributeScope(String name)`，返回某属性的作用范围
- `void forward(String relativeUrLPath)`，使当前页面重导到另一页面
- `void include(String relativeUrlPath)`，在当前页面包含另一文件



#### config对象

config对象是在一个Servlet初始化时，Jsp引擎向它传递信息用的，此信息包括Servlet初始化时所要用到的参数(通过属性名和属性值构成)，以及服务器的有关信息(通过传递一个ServletContext对象)，常用方法：

- `ServletContext getServletContext()`, 返回含有服务器信息的ServletContext对象
- `String getInitParameter(String name)`，返回初始化参数的值
- `Enumeration getInitParameterNames()`，返回Servlet初始化所需所有参数的枚举



#### exception对象

exception对象是一个异常对象，当一个页面在运行过程中出现了异常，就产生这个对象。如果一个Jsp页面要引用此对象，就必须把isErrorPage设为true，否则无法编译。实际上是java.lang.Throwable的对象，常用方法：

- `String getMessage()`，返回描述异常的信息
- `String toString()`，返回关于异常的简短描述信息
- `void printStackTrace()`，显示异常及其栈轨迹
- `Throwable FillInStackTrace()`，重写异常的执行栈轨迹



## JavaBean技术

#### JavaBean简介及设计原则

- JavaBeans就是符合某种特定规范的java类。使用JavaBeans的好处是解决代码重复编写，减少代码冗余，功能区分明确，提高了代码可维护性。
- 设计原则
  1. 公有类
  2. 无参的共有构造方法
  3. 属性私有
  4. getter和setter方法

#### Jsp动作

Jsp动作元素为请求处理阶段提供信息。动作元素遵循xml元素的语法，有一个包含元素名的开始标签，可以有属性、可选的内容、与开始标签相匹配的结束标签。

- 第一类是与存取JavaBean有关的：

  `<jsp:useBean>、<jsp:setProperty>、<jsp:getProperty>`

- 第二类是6个基本元素：

  `<jsp:include>、<jsp:forward>、<jsp:param>、<jsp:plugin>、<jsp:params>、<jsp:fallback>`

- 第三类是与JSP Document有关的：

  `<jsp:root>、<jsp:declaration>、<jsp:scriptlet>、<jsp:expression>、<jsp:text>、<jsp:output>`

- 第四类是用来动态生成XML元素标签的值

  `<jsp:attribute>、<jsp:body>、<jsp:element>`

- 第五类主要用在Tag File中:

  `<jsp:invoke>、<jsp:dobody>`

#### 在Jsp页面中使用JavaBeans

1. 像使用普通java类一样，创建javabean实例

   ```java
   Users user = new Users();
   ```

2. 在Jsp页面中使用Jsp动作标签使用JavaBean

   - useBeans动作

     作用：在jsp页面实例化或者在指定范围内使用javabean：

     `<jsp:useBean id="标识符" class="java类名" scope="作用范围"/>`

   - setProperty动作

     作用：给已经实例化的JavaBeans对象的属性赋值

     1. `<jsp:setProperty name="JavaBean实例名" property="*"/>`(跟表单关联)
     2. `<jsp:setProperty name="JavaBean实例名" property="JavaBean属性名"/>`(跟表单关联)
     3. `<jsp:setProperty name="JavaBean实例名" property="JavaBean属性名" value="BeanValue"/>`(手工设置)
     4. `<jsp:setProperty name="JavaBean实例名" property="propertyName" param="request对象中的参数名"/>`(跟request参数相关联)

   - getProperty动作

     作用：获取指定JavaBean对象的属性值

     `<jsp:getProperty name="JavaBean实例名" property="属性名"/>`



#### JavaBean的四个作用域范围

说明：**使用useBean的scope属性指定JavaBean的作用范围**

- page：只在当前页面有效
- request：可以通过HttpRequest.getAttribute()方法取得JavaBean对象
- session：可以通过HttpSession.getAttribute()方法获得JavaBean对象
- application: 可以通过application.getAttribute()方法取得JavaBean对象



## Jsp状态管理

#### http协议无状态性

无状态是指,当浏览器发送请求给服务器时,服务器响应客户端请求,但当同一个浏览器再次发送请求给服务器时,服务器不知道是刚才的浏览器,这就是无状态协议.

#### 保存用户状态的两大机制

- Session
- Cookie

#### Cookie

Cookie是Web服务器保存在客户端的文本信息

Cookie的作用：		

- 对特定对象的追踪
- 保存用户网页浏览记录与习惯
- 简化登录

### JSP中创建与使用Cookie

创建Cookie对象：

`Cookie newCookie = new Cookie(String key,Object value)`;

写入Cookie对象：

`response.addCookie(newCookie);`

读取Cookie对象：

`Cookie[] cookies = request.getCookies();`

- 常用方法：

  |          方法名称           |             说明             |
  | :-------------------------: | :--------------------------: |
  | void setMaxAge(int expiry)  | 设置Cookie有效期，以秒为单位 |
  | void setValue(String value) |    Cookie创建后，对其赋值    |
  |      String getName()       |        获取Cookie名称        |
  |      String getValue()      |        获取Cookie的值        |
  |       int getMaxAge()       |      获取Cookie有效时间      |

  

### Session与Cookie对比

|            session             |           cookie           |
| :----------------------------: | :------------------------: |
|     在服务器端保存用户信息     |    在客户端保存用户信息    |
|  session中保存的是Object类型   |  cookie保存的是String类型  |
| 随会话的结束而将存储的数据销毁 | cookie可以长期保存在客户端 |
|         保存重要的信息         |      保存不重要的信息      |

## 指令与动作

#### include指令

- 语法：<%@ include file="URL" %>

#### include动作

- 语法：<jsp:include page="URL" flush="true|false"  />
  - page：要包含的页面
  - flush：被包含的页面是否从缓冲区读取

#### include指令与include动作的区别

|                 |            include指令            |                  include动作                   |
| :-------------: | :-------------------------------: | :--------------------------------------------: |
|    语法格式     |       <%@ include file=""%>       | <jsp:include page="..." flush="true\|false" /> |
| 发生作用的时间  |           页面转换期间            |                    请求期间                    |
|   包含的内容    |          文件的实际内容           |                   页面的输出                   |
| 转换成的Servlet | 主页面和包含页面转换成一个Servlet |      主页面和包含页面转换成独立的Servlet       |
|    编译时间     |       较慢---资源必须被解析       |                      较快                      |
|    执行时间     |               稍快                |           稍慢---每次资源必须被解析            |

#### forward动作

- 语法

  `<jsp:forword page="url"/>`

  等同于：`request.getRequestDispatcher("/url").forward(request,response);`

#### param动作

- 语法：

  `<jsp:param name="参数名" value="参数值"`

  常与<jsp: forward>连用，作为其子标签