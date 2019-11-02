微服务和分布式区别

微服务，一般是从业务角度，看服务是不是能够相对独立，服务接口相互调用

分布式一般是从架构层面考虑，把某个服务单独出来的方案 -- 架构层面提出

微服务一般是业务层面考虑，为了各个业务之间相互耦合性低（组合/插拔）-- 产品经理

### Rpc普遍存在的问题需求

![1569330173010](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569330173010.png)

需求：

1、rpc调用需要定制。额外的工作量
2、分布式服务中，服务动辄几十上百，相互之间的调用错综复杂，相互依赖严重
3、对集群性的服务，需要负载策略
4、对集群性的服务，能动态扩展节点

### dubbo简介

一个分布式、高性能、透明化（无侵入性）的RPC服务框架。
提供服务自动注册、自动发现等高效服务治理方案。
其功能主要包括：高性能NIO通讯及多协议集成，服务动态寻址与路由，软负载均衡与容错，依赖分析与降级等。

![1569330746810](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569330746810.png)

### 功能

1、container负责启动、加载、运行provider
2、provider启动时，向registry注册自己的服务
3、cousumer启动时，向registry订阅自己的服务
4、registry提供provider列表给consumer，实时推送变动情况
5、consumer根据provider列表，按负载算法选一台provider调用
6、monitor统计rpc的调用频次

### 项目部署中的dubbo

![1569330361823](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569330361823.png)

1、一台应用服务(application)内，既有对外提供服务(provider)，也有依赖外部服务(consumer)
2、 provider涉及：registry/protocol/service/method/provider
3、 consumer涉及：registry/reference/method/consumer
4、每台服务接口的信息，都会反映到monitor。以application的名称标识属于哪个应用。

### Dubbo标签

![1569330547817](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569330547817.png)

1、标签属性有继承关系，即：下层有设置则使用，未配置则沿用上一级的设置
2、 timeout/retries/ loadbalance消费方未设置，则沿用服务方的设置。

### 常规配置（xml方式与properties方式）

http://dubbo.apache.org/en-us/blog/download.html

http://dubbo.apache.org/zh-cn/docs/user/configuration/xml.html

#### 服务提供者

1. 引入dubbo依赖包
2. 把serviceimpl配置进入spring容器，管理服务（dubbo只能支持spring管理的服务）
3. 把spring管理的服务，转换成rpc服务对外开放
4. 启动spring容器

#### 服务消费者

1. 引入dubbo依赖包
2. 在spring中配置引入的远程服务
3. web工程，启动spring容器
4. 让spring容器加入dubbo配置文件
5. 尽量不在web.xml中分开，在listen和servlet中分开配置springmvc和dubbo.xml

![1569331487463](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569331487463.png)

### 注解方式配置

![1569331504510](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569331504510.png)

### Api方式配置

![1569331535795](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569331535795.png)

### 项目中类的依赖关系

![1569331550538](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569331550538.png)

![1569331560533](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569331560533.png)

1. dubbo的service注解有两个功能，第一注册服务到spring容器，第二能暴露为rpc服务--容器有两个实例
2. zk的集群还是单节点，对应用服务器来说，没有区别，集群zk抗灾.

dubbo初始化过程：

1、标签入口 ---- DubboNamespaceHandler

 

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps1.jpg) 

2、下面每个配置标签 ---- 对应一个ReferenceBean实例

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps2.jpg) 

2.1、把dubbo：referencce （dubbo：**service同理**）标签配置的属性，全读出来 ---- set进入ReferenceBean对象，对象实例由IOC容器管理

2.2、ReferenceBean（ServiceBean同理）实现了，initializingBean接口，因此初始化完成时，会调用其afterPropertiesSet方法

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps3.jpg) 

2.3、afterPropertiesSet方法内，进行dubbo服务配置（创建消费端的代理对象/服务端的中转对象/向zk注册信息/订阅信息等）

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps4.jpg) 

这里会对标签中设置的每个协议，进行一行处理

 

2.4、协议创建中转对象和消费代理

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps5.jpg) 

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps6.jpg) 

2.5、dubbo的初始化结构图：主线是ServiceBean和ReferenceBean的初始化

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps7.jpg) 

3、spi机制概念

​	-------------本质是解决同一个接口，有多种实现时，使用者如何能够方便选择实现的问题

3.1、同一个接口，多个实现（类似设计模式--策略模式）

 

3.2、看jdk的spi如何配置使用的

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps8.jpg) 

​	jdk中，选择SpiService的实现，方法是在jar中放置一个META-INF/services目录，目录中存放一个文本文件（文件名----是SpiService接口的全路径名），文本中列入你选择的实现类（一行放一个------是实现类的全路径名）

​	有了上述配置，在java程序中，使用ServiceLoader.load(SpiService.class)，即可将配置中选择的实现类，实例化并放入一个集合中，供我们使用，如下图：

 

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps9.jpg) 

 

3.3、dubbo的spi  ------ 比jdk的选择方案，要牛叉一点

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps10.jpg) 

​	与jdk相比，dubbo将选择权下放到了配置文件中（你配置谁，它使为你实例化谁）

 

​	dubbo的目标，以上图cluster为例，failsafe/failover/failfast都是cluster的一种实现，现在我	们可以在标签配置时，方便地进行选择

 

4、dubbo中的api的使用流程

4.1、凡是dubbo中， 接口上有 @SPI标注的，都表明此接口支持扩展，如下图

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps11.jpg) 

4.2、单独建立jar工程，引入dubbo依赖（注意依赖scope设为provided）

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps12.jpg) 

4.3、在工程中，为要扩展的接口，编写实现类

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps13.jpg) 

4.4、在资源文件中，为每个实现类，定义分配一个key

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps14.jpg) 

4.5、在消费端，便可使用上面步骤中定的实现策略（以key指代）

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps15.jpg) 

5、dubbo的Spi实现原理---解读其核心ExtensionLoader 

5.1、看源码疑云：

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps16.jpg) 

 

疑问：dubbo中reference 使用的protocol --- 是静态类变量

​	而ReferenceConfig（ReferenceBean的父级）是每个标签一个实例对象（每个对象配置的protocol是不同的）

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps17.jpg) 

此时protocol的对应一定出问题

 

结论：上面步骤中，得到的protocol是个代理类，不是真实的协议实现

 

5.2、ExtensionLoader的加载步骤

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps18.jpg) 

5.2.1、*getExtensionLoader*(Protocol.**class**)为protocol接口生成一个加载器

5.2.2、getAdaptiveExtension()，使用加载器生成一个代理对象---- protocol接口对象

5.2.3、代理对象执行时，根据参数（扩展名extName）选择实际对象 ------

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps19.jpg) 

5.2.4、最后的效果：每个接口扩展点----- 对应一个ExtensionLoader加载器，如：

protocol   -------------- ExtensionLoader实例< protocol>

filter  -------------- ExtensionLoader实例< filter  >

loadbalance  -------------- ExtensionLoader实例< loadbalance  >

5.2.5、代理类的逻辑

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps20.jpg) 

代理类的创建，是通过动态代码，生成一个类源码，然后经过编译得到代理类的class，如上图

 

 

代理类生成源码的逻辑，只生接口中，标注了@Adaptive的方法，如下：

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps21.jpg) 

5.2.6、dubbo的spi整体执行逻辑

![img](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\wps22.jpg) 

a、dubbo启动加载实现类时，以   key-实例 方式map缓存各个实现类

b、实际调用时，通过key --取实现需要那个实现

c、调用的发生，由生成的代理对象的来发起，最终是从URL总线中，找出extName值，

   extName做为别说，在缓存map中取出正确的实现实现类3.dubbo在项目的用法
​	类与类的依赖关系说明
​	项目之间的依赖划分
​	实战拆解分布式
4.dubbo源码解析与示例
​	dubbo的模块及层级
​	dubbo的初始化过程
​	dubbo的服务暴露
​	dubbo的服务引用
​	dubbo的服务拦截
​	服务的注册与发现

Spring Cloud与Dubbo的区别是什么？

![wuliao](http://www.wityx.com/image/201905/3CE0E72EC516AB2EDA045E0CA63D8812.png)

## **Dubbo远程调用之公司内部提供的服务**

**Dubbo远程调用之公司内部提供的服务**

一家对外提供服务的公司，例如百度，腾讯，阿里，京东，58 同城等，公司内部有多个事业群，事业部门，每个事业部门内部又有若干个子部门，子部门里面有多个不同的小组负责各自的业务。提供对外的服务。

公司内部，外部提供的服务不仅多，而且细分，还有交叉的情况。前面的例子是访问互联网上的服务，使用的是 http 请求网络资源。相对来说访问服务方式单一，处理服务的效率相对较低。公司内部服务之间可以使用多种不同的方式访问服务。

使用单一应用访问天气服务

图一：

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/E0D1424AA8570DBFD8D94B18F326BBF5.png)

图二：

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/5841A6ECDF762AC398182135AA64075B.png)

A、新建 web 项目 01-

项目结构：

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/1DD7705B9FBAC7FDEE06724D5FB46D77.png)

B、 新建数据类

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/30908D12DC5B9C49E8FB3468C633CC8D.png)

重写 toString()

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/36AFE86FA5E1DB935C771D4700F1BC7F.png)

C、 定义 Service 接口

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/36AFE86FA5E1DB935C771D4700F1BC7F.png)

D、定义 Service 接口的实现类

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/BD9BA0E68B1D10B3460FE29364676CBE.png)

E、 定义 Servlet，提供访问地址

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/1297CA920005FE8FC495F9F52D85B137.png)

F、 定义访问添加服务的

首先加入 jQuery 库文件,放到项目的 js 目录

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/3CE7C5A7555CD4FC1D091B029935954B.png)

index.jsp

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/A0BA44F7A963197425A8B994207C682C.png)

G、执行 web 应

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/A9B7B2F6E8599CD422FD60E372B32191.png)

使用独立应用提供天气服务

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/A14D6B766472E6CD8DCFE79C0E1001AA.png)

（1）独立的应用提供服务

在一台或多台物理机器上，运行的独立应用程序，供多个客户端访问天气服务。

### A、把 01-weatherService 应用复制，名称 02-companyProviderWeather

B、 去掉 js 文件夹，index.jsp 文件

C、 使用 Servlet 提供服务

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/F3D0F678C7EF4B92B0F49FD86DDACCA.png)

（2）在独立的应用中访问天气服务

在一台独立的计算上， 通过应用访问天气服务。

A、把 01-weatherService 应用复制，名称 03-companyConsumeWeather

B、 去掉 src 目录下的 java 代码

C、 修改 index.jsp 中的访问服务 Servlet 的地

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/4393B18DF45B858386A58BC35B298EF5.png)

D、运行应用

发 布 两 个 应 用 到 tomat 服 务 器 。 03-companyConsumeWeather 应 用 访 问

02-companyProviderWeather 提供的服务。 两个应用是独立部署到不同的机器， 使用两个

![Dubbo远程调用之公司内部提供的服务_www.wityx.com](http://www.wityx.com/image/201908/1B1D48E16E94E6AF3DB6D0A048E7F394.png)