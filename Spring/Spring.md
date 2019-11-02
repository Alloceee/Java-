# Spring

### spring全家桶

Spring Core ->Spring Security-> Spring Data->Spring Boot->Spring Cloud(微服务架构) ->Spring Cloud DataFlow

## spring

spring是一个开源框架，为简化企业级应用开发而生，spring可以使简单的Javabean实现以前只有EJB才能实现的功能，Spring是一个IOC和AOP的容器框架

### 主要核心：

- 控制反转（IOC），传统的java开发模式中，当需要一个对象时，我们会自己使用new或者getInstance等直接或者间接调用构造方法创建一个对象。而在Spring开发模式中，spring容器使用了工厂模式为我们创建了所需要的对象，不需要我们自己创建了，直接调用spring提供的对象就可以 了，这是控制反转的思想。
- 依赖注入（DI），spring使用javabean对象的set方法或者带参数的构造方法为我们在创建所需对象时将其属性自动设置所需要的值，就是依赖注入的思想。DI是实现IOC一种常见的方式。
- 面向切面编程（AOP），在面向对象编程（OOP）思想中，我们将实物纵向抽象成一个个的对象，而在面向切面编程中，我们将一个个的对象某些类似的方面横向抽成一个切面，对这个切面进行一些如权利控制、事务管理，记录日志等公用操作处理的过程就是面向切面编程的思想。AOP底层是动态代理，**如果是接口采用JDK动态代理，如果是类采用CGLIB方式实现动态代理**

### **四大原则**

- 使用POJO进行轻量级和最小侵入式开发
- 通过依赖注入和基于接口编程实现松耦合
- 使用AOP和默认习惯进行声明式编程
- 使用AOP和模板（template）减少模式化代码

### Spring配置

- Spring提供使用xml、注解、Java配置、groovy配置实现Bean的创建和注入
- 无论是xml配置、注解配置还是Java配置，都被称为配置元数据，所谓元数据即描述数据的数据。元数据本身不具备任何可执行能力，只能通过外界代码来对这些元数据行解析后进行一些有意义的操作。Spring容器解析这些配置元数据进行Bean初始化、配置和管理依赖

### SpringIOC

#### 什么是SpringIOC容器

- IOC（Inversion of Control） 控制反转：是Spring Core最核心的部分。含义：把底层类作为参数传递给上层类，实现上层对下层的“控制”
- SpringIOC负责创建对象，管理对象。通过依赖注入（DI），装配对象，配置对象，并且管理这些对象的整个生命周期
- BeansFactory是springIOC容器的具体体现，用来包装和管理前面各种Beans（处理message resource的机制，用于国际化，事件传播以及应用层的特别配置）。BeansFactory接口是springIOC容器的核心接口

#### IOC容器的优势

- 避免在各处使用New来创建类，并且可以做到统一维护
- 创建实例的时候不需要了解其中的细节

### 依赖注入三种的实现方式

- 1. 构造器注入
- 2. Setter方法注入
- 3. 接口注入
- 注解注入Annotation
- 构造方法注入和设值注入有什么区别
  - 在设值注入方法支持大部分的依赖注入，如果我们仅需要注入int、string和long型的变量，我们不要用设值的方法注入。对于基本类型，如果我们没有注入的话，可以为基本类型设置默认值。在构造方法注入方法中不支持大部分的依赖注入，因为在调用构造方法中必须传入正确的构造参数，否则的话为报错
  - 设值注入不会重写构造方法的值。如果我们对同一个变量同时使用了构造方法主又使用了设值注入的话，那么构造方法注入不会覆盖设值注入的值，很明显，因为构造方法只在对象被创建时调用
  - 在使用设值注入时有可能还不能保证某种依赖是否已经被注入，也就是说这时对象的依赖关系有可能是不完整的。而在另一种情况下，构造器注入则不允许生成依赖关系不完整的对象
  - 在设值注入时如果对象A和对象B相互依赖，在创建对象A时Spring会抛出异常，因为在B对象被创建之前A对象不能被创建，反之亦然，所以Spring用设值注入的方法解决了循环依赖的问题，因对象的设值方法是在对象被创建之前被调用的
- 自动装配：是依赖注入的具体过程

| 注解       | 作用                                                         |
| ---------- | ------------------------------------------------------------ |
| @AutoWired | ByType                                                       |
| @Resource  | 先ByName，如果有对应的bean直接注入，如果找不到再继续ByType，如果有两个以上相同类型的Bean则报错，否则注入 |
| @Qualifier | 限定，配合其他注解一起使用                                   |



### SpringIOC工作流程

- 读取bean配置信息，在spring容器中生成spring定义注册表
- 根据bean注册表实例化bean
- 将bean实例放到spring容器中（bean缓存池）
- 应用程序使用bean

### SpringIOC支持功能

- 依赖注入
- 依赖检查
- 自动装配
- 支持集合
- 指定初始化方法和销毁方法
- 支持回调方法

### SpringIOC容器的核心接口

- ##### BeanFactory：Spring框架最核心的接口
  
  - 提供IOC的配置机制
  - 包含Bean的各种定义，便于实例化Bean
  - 建立Bean之间的依赖关系
  - Bean生命周期的控制
- ##### ApplicationContext（继承多个接口）
  
  - 继承BeanFactory：能够管理、装配Bean
  - 继承ResourcePatternResolver：能够加载资源文件
  - MessageSoursce：能够实现国际化等功能
  - ApplicationEventPublisher：能够注册监听器，实现监听机制
- ##### BeanFactory和ApplicationContext有什么区别
  
  - BeanFactory是Spring框架的基础设施，面向Spring（提供基础功能），BeanFactory可以理解为含有Bean集合的工厂类。BeanFactory包含了各种bean的定义，以便在接收到客户端请求时将对应的bean实例化
  - BeanFactory还能在实例化对象的时候生成协作类之间的关系，此举将Bean自身与bean客户端的配置中解救出来。BeanFactory还包含了bean生命周期的控制，调用客户端的初始化方法和销毁方法
  - ApplicationContext面向使用Spring框架的开发者（提供更多的功能）。表面上和BeanFactory一样具有bean定义，bean关联关系的设置，根据请求分发bean的功能，但application context还提供了支持国际化的文本信息，统一的资源文件读取方式，已在监听器中注册的Bean的事件等功能
- ##### BeanDefinition：主要用来描述bean的定义
- ##### BeanDefinitionRegistry：提供向IOC容器注册BeanDefinition对象的方法

- ##### getBean方法的代码逻辑
  
  - 转化BeanName
  - 从缓存中加载实例
  - 实例化Bean
  - 检测parentBeanFactory
  - 初始化依赖的Bean
  - 创建Bean

### SpringBean的作用域

- singleton：Spring的默认作用域，容器里拥有唯一的Bean实例（适合无状态的Bean）
- prototype：针对每个getBean请求，容器都会创建一个Bean实例（适合有状态的Bean）
- 如果是web容器：
  - request：会为每个Http请求创建一个Bean实例
  - session：会为每个session创建一个Bean实例
  - globalSession：会为每个全局Http Session创建一个Bean实例，该作用域仅第Portlet有效

### SpringBean的生命周期

- 创建->销毁

### SpringAOP

- ##### 关注点分离：不同的问题交给不同的部分去解决，可以进行日志记录，权限判断，异常处理等
  
  - 面向切面编程AOP正是此种技术的体现
  - 通用化功能代码的实现，对应的就是所谓的切面（Aspect）
  - 业务功能代码和切面代码分开后，架构将变得高内聚低耦合
  
- ##### AOP的三种织入方式
  
  - 编译时织入：需要特殊的Java编译器，如AspectJ
  - 类加载时织入：需要特殊的Java编译器，如AspectJ和AspectWerkz
  - 运行时织入：Spring采用的方式，通过动态代理的方法，实现简单
  
- ##### AOP的主要名称概念
  
  - Aspect：通用功能的代码实现
  - Target：被织入Aspect的对象
  - Join Point：可以作为切入点的机会，所有方法都可以作为切入点
  - Pointcut：Aspect实际被应用在的Join Point，支持正则
  - Advice：类里的方法以及这个方法如何织入到目标方法的方式
  
- ##### Advice的种类
  
  - 前置通知（Before）
  - 后置通知（AfterReturning）
  - 异常通知（AfterThrowing）
  - 最终通知（After）
  - 环绕通知（Around）
  
- ##### AOP的实现：JdkProxy和Cglib
  
  - 由AopProxyFactory根据AdvicedSupport对象的配置来决定
  - 默认策略如果目标类是接口，则用JDKProxy来实现，否则用后者
  - JDKProxy的核心：InvocationHandler接口和Proxy类，通过Java内部反射机制来实现
  - Cglib（Code Generation lib）：以继承的方式动态生成目标类的代理，借助ASM实现
  - 反射机制在生成类的过程中比较高效
  - ASM在生成类之后的执行过程中比较高效
  

案例:SpringBoot+AOP

1. pom.xml

```xml
 <!--  切面编程    -->
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-aop</artifactId>
        </dependency>
        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-configuration-processor</artifactId>
            <optional>true</optional>
        </dependency>
```

2. class类

```java
@Aspect
@Component
public class LogAspect {
    private static final Logger LOGGER = LoggerFactory.getLogger(LogAspect.class);
    @Pointcut("execution(public * com.springboot.demo.controller.*.*(..))")
    public void webLog(){}

    @Before("webLog()")
    public void doBefore(JoinPoint joinPoint){
        //接收到请求，记录请求内容
        ServletRequestAttributes attributes = (ServletRequestAttributes) RequestContextHolder.getRequestAttributes();
        HttpServletRequest request = attributes.getRequest();
        //记录下请求内容
        LOGGER.info("URL : "+request.getRequestURL().toString());
        LOGGER.info("IP : "+request.getRemoteAddr());
        LOGGER.info("请求类方法 : "+joinPoint.getSignature());
        LOGGER.info("请求类方法参数 : "+ Arrays.toString(joinPoint.getArgs()));
        LOGGER.info("==============================");
    }

    @AfterReturning(returning = "ret" , pointcut = "webLog()")
    public void doAfterReturning(Object ret){
        //处理完请求，返回内容
        LOGGER.info("REQUEST : "+ret);
    }
}
```

| 注解                                                       | 作用                                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ |
| @Aspect                                                    | 标注这个类是切面编程的类                                     |
| @Component                                                 | 将该类添加到spring容器中交给spring管理                       |
| @Pointcut()                                                | @Pointcut("execution(public * com.springboot.demo.controller.*.*(..))") |
| @Before("webLog()")                                        |                                                              |
| @AfterReturning(returning = "ret" , pointcut = "webLog()") |                                                              |



## 请描述一下Spring的事务

- 声明式事务管理的定义：
  - 用在Spring配置文件中声明式的处理事务来代替代码式的处理事务。这样的好像是，事务不侵入开发的组件，具体的来说，业务逻辑对象就不会意识到正在事务管理之中，事实上也应该如此，因为事务管理时属于系统层面的服务，而不是业务逻辑的一部分，如果想要改变事务管理规划的话，也只需要在定义文件中重新配置即可，这样维护起来及其方便。
- ACID
- 隔离级别
- 事务传播



## Spring框架中用到了哪些设计模式

- 代理模式——在AOP和remoting中被用的比较多
- 单例模式——在spring配置文件中定义的bean默认为单例模式
- 模板方法——用来解决代码重复的问题，比如RestTemplate,JmsTemplate,JpaTemplate
- 前端控制器——Spring提供了DispatcherServlet来对请求进行分发
- 视图帮助（View Helper）——Spring提供了一系列的JSP标签，高效宏来辅助将分散的代码整合在视图里
- 依赖注入——贯穿于BeanFactory/ApplicationContext接口的核心理念
- 工厂模式——BeanFactory用来创建对象的实例



## Spring能帮我们做什么

- spring能帮我们根据配置文件创建及组装对象之间的依赖关系
- spring面向切面编程能帮助我们无耦合的实现日志记录，性能统计，安全控制。spring面向切面编程能提供一种更好的方式来完成，一般通过配置文件，而且不需要在现在代码中添加任何额外代码，现有代码专注业务逻辑
- spring能非常简单的帮我们管理数据库事务，采用spring框架我们只需获取连接，执行SQL，其他事务相关的都交给spring来管理了
- spring还能与第三方数据库访问框架（Hibernate、JPA）无缝集成，而且自己也提供了一套JDBC访问模板，来方便数据库访问
- spring还能与第三方Web（如Struts、JSF）框架无缝集成，而且自己也提供了一套SpringMVC框架，来方便web层搭建
- spring能方便的与JavaEE（如JavaMail、任务调度）整合，与更多技术整合（比如缓存框架）

### 常用注解

| 注解           | 作用                 |
| -------------- | -------------------- |
| @Component()   | 将容器加载到spring中 |
| @AutoWired     | 自动装配             |
| @Primary       |                      |
| @Value         |                      |
| @Configuration |                      |



### 如何解决get和post乱码问题

- 怎样开启注解装配
  - 注解装配在默认情况下是不开启的，为了使用注解装配，在配置文件中配置<context:annotation-config/>元素
- IOC的优点是什么
  - IOC或者依赖注入是把应用的代码量降到最低，他使应用容易测试，单元测试不再需要单例和JNDI查找机制，最小的代价和最小的侵入性使松耦合得以实现，IOC容器支持加载服务时的饿汉式初始化和懒加载

### spring框架中的单例Beans是线程安全的吗

- 大部分的spring beans并没有可变的状态（比如dao类和service了），所以在某种程度上说spring的单例beans是线程安全的。如果你的bean有多种状态的话（比如view model对象）就需要自行保证线程安全
- undefined
- 最浅显的解决办法是将多态bean的作用域有singleton变为prototype



**62.Springbean的加载过程(推荐看Spring的源码)**

参考文章[看看Spring的源码（一）——Bean加载过程](https://link.zhihu.com/?target=http%3A//geeekr.com/read-spring-source-1-how-to-load-bean/)

**63.Springbean的实例化(推荐看Spring的源码)**

参考文章[看看Spring的源码(二)——bean实例化](https://link.zhihu.com/?target=http%3A//geeekr.com/read-spring-source-two-beans-initialization/)

**64.Spring如何实现AOP和IOC(推荐看Spring的源码)**

参考文章[Java轻量级业务层框架Spring两大核心IOC和AOP原理](https://link.zhihu.com/?target=http%3A//www.360doc.com/content/15/0116/21/12385684_441408260.shtml)

**65.Springbean注入方式**

参考文章[spring四种依赖注入方式 - 上善若水任方圆 - ITeye技术网站](https://link.zhihu.com/?target=http%3A//blessht.iteye.com/blog/1162131)

**66.Spring的事务管理**

这个主题的参考文章没找到特别好的，[http://blog.csdn.net/trigl/article/details/50968079这个还可以。](https://link.zhihu.com/?target=http%3A//blog.csdn.net/trigl/article/details/50968079%E8%BF%99%E4%B8%AA%E8%BF%98%E5%8F%AF%E4%BB%A5%E3%80%82)

**67.Spring事务的传播特性**

参考文章[Spring事务的传播特性 - 陈建秋 - 博客频道 - CSDN.NET](https://link.zhihu.com/?target=http%3A//blog.csdn.net/lfsf802/article/details/9417095)

**68.springmvc原理**

参考文章[SpringMVC工作原理_张晓龙_新浪博客](https://link.zhihu.com/?target=http%3A//blog.sina.com.cn/s/blog_7ef0a3fb0101po57.html)

**69.springmvc用过哪些注解**

参考文章[详解Spring MVC 4常用的那些注解](https://link.zhihu.com/?target=http%3A//aijuans.iteye.com/blog/2160141)































