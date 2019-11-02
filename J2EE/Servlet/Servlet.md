# Servlet

- 原理
  - 广义：servlet是sun公司定义的一套JavaEE的接口，提供web服务
  - 狭义：是指Servlet接口的实现类。是一个具体的类，放在服务端运行，用于提供web服务的类
  - 与CGI区别：
    - CGI：通过多进程来处理客户端请求
    - Servlet：通过多线程处理客户端请求
  - Servlet设计时是独立于协议，但是有默认的协议：HTTP协议
  - 版本：
    - Servlet2.3
    - Servlet2.5
    - Servlet3.0 支持注解开发 支持文件上传
    - Servlet3.1
    - Servlet4.0
  - 两种开发方式
    - 注解开发3.0
    - XML配置开发
- 体系（哪些接口体系）
  - 开发一个提供HTTP服务的Servlet步骤
    - 写一个类继承HttpServlet，覆盖doGet方法或者doPost等方法
    - 编译这个类
    - 部署到Servlet容器中运行
    - 启动容器
- Servlet、Servlet容器、Tomcat之间的关系
  - Tomcat是Servle容器的一种
  - Tomcat实现了Servlet
  - 类似地：JDBC、数据库服务器、数据库驱动之间的关系
  - 类似地：java、JRE、JDK之间的关系
- Servlet核心对象
  - Session
  - ServletContext
  - 请求
  - 响应
  - 转发器
  - Cookie
- 生命周期
  - servlet的生命周期是由servlet容器（Tomcat就是常见的Servlet容器）管理的。
  - servlet中的init()方法有两个重载，一个是空参的，另外一个是带ServletConfig形参的，不是ServletContext
  - Servlet实例要释放（销毁）了，才会先调用destory()方法
  - Servlet是单实例的，多线程中，单实例不存在安全问题
- GET请求乱码解决：
  - 全局配置，在tomcat的server.xml中配置一个URIEncoding="GBK"
  - 获取的时候，已经是乱码。因为Tomcat默认采用ISO8859-1解码，所以只能还原 byte[] bs = rq.getParameter("key").getByte("ISO8859-1")；重新解码。String key = new String(bs,"GBK");
  - 如果用的是tomcat8.5+以上，默认的URIEncoding 是UTF-8，所以不用作任何配置，但是要求工程采用UTF-8编码
  - 可以为HttpServletRequest生成代理类对象，拦截以上几个获取参数的方法，进行还原
- POST请求乱码解决：
  - rq.setCharacterEncoding(arg0)
  - 统一解决方案，通过过滤器实现，如果使用spring，spring提供编码过滤器，只需要配置即可

### 域对象

在servlet中，一共3种作用域

| 类型           | 范围       | 简称        |
| -------------- | ---------- | ----------- |
| SevrletContext | 全局作用域 | application |
| HttpSession    | 会话作用域 | session     |
| SevrletRequest | 请求作用域 | request     |

>作用域的功能：确定一个范围，可以作为数据容器，数据结构是键值对结构。



作为域对象，有通用的三个方法:

| 方法                                              | 作用                                         |
| ------------------------------------------------- | -------------------------------------------- |
| public void setAttribute(String key,Object value) | 添加或覆盖一个键值对                         |
| public Object getAttribute(String key)            | 根据key获取value。如果value不存在，返回null  |
| public void remove(String kye)                    | 根据key移除一个键值对。如果key不存在，则忽略 |

> 注意：确保不用基本类型的情况下，可以放心大胆的进行强制转换。



## 虚拟目录的配置

Servlet.xml中

<host>标签下的<context>标签，一个<context>标签就代表一个Servlet应用

<context>标签的两个重要性：

```xml
<Context docBase="D:\tomcat8.0\wtcwebapps\Submit" path="/Submit"></>
```

| 方法                          | 含义                                          |
| ----------------------------- | --------------------------------------------- |
| docBase                       | Servlet应用的根目录，并且是在硬盘中的真实目录 |
| path                          | 指定url中的虚拟目录                           |
| localhost/Submit/login.action | Submit就是虚拟目录                            |

**应用场景**

可以根据自己的需求，创建多个虚拟目录

需要存储图片时，可以在本地硬盘指定一个目录作为图片存储

### **注解开发和web.xml开发**

| Web.xml                                                      | 注解                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| **<servlet>**    <servlet-name>Test</servlet-name> <servlet-class>com.pro.web.Test</servlet-class>    <init-param>      <param-name>charset1</param-name>      <param-value>UTF-8</param-value>    </init-param>    <init-param>      <param-name>charset2</param-name>      <param-value>GBK</param-value>    </init-param>    <load-on-startup>1</load-on-startup>  **</servlet>**  <**servlet-mapping**>    <servlet-name>Test</servlet-name>    <url-pattern>/test.action</url-pattern>  **</servlet-mapping>** | @WebServlet(urlPatterns= {"/test.action"},**loadOnStartup**=1,initParams= {@WebInitParam(name="charset1",value="UTF-8"),		     @WebInitParam(name="charset2",value="GBK")			}) |
| <filter>    <filter-name>CommentFilter</filter-name>    <filter-class>com.pro.filter.CommentFilter</filter-class>  </filter>  <filter-mapping>    <filter-name>CommentFilter</filter-name>    <url-pattern>/comment/*</url-pattern>  </filter-mapping> | @WebFilter("/comment/*")                                     |
| <listener> <listener-class>com.pro.listener.ChatUserSessionListener</listener-class>  </listener> | @WebListener                                                 |

### 请求转发和重定向的区别

- 本质区别：转发是服务器行为，重定向是客户端行为
- 重定向特点：两次请求，浏览器地址发生变化，可以访问自己web以外的资源，传输的数据会丢失
- 请求转发特定：一次请求，浏览器地址栏不变，访问的是自己本身的web资源，传输的数据不会丢失

**51.servlet生命周期及各个方法**

参考文章[Servlet 生命周期、工作原理](https://link.zhihu.com/?target=http%3A//www.cnblogs.com/xuekyo/archive/2013/02/24/2924072.html)

**52.servlet中如何自定义filter**

参考文章[Servlet Filter - javawebsoa - 博客园](https://link.zhihu.com/?target=http%3A//www.cnblogs.com/javawebsoa/archive/2013/07/31/3228858.html)