## **java分布式面试题之Dubbo部分**

**java分布式面试题之Dubbo部分**

Dubbo官网提出总共有六种容错策略

- Failover Cluster模式

失败自动切换，当出现失败，重试其它服务器。(默认)

- Failfast Cluster

快速失败，只发起一次调用，失败立即报错。通常用于非幂等性的写操作，比如新增记录。

- Failsafe Cluster

失败安全，出现异常时，直接忽略。通常用于写入审计日志等操作。

- Failback Cluster

失败自动恢复，后台记录失败请求，定时重发。通常用于消息通知操作。

- Forking Cluster

并行调用多个服务器，只要一个成功即返回。通常用于实时性要求较高的读操作，但需要浪费更多服务资源。可通过forks=”2”来设置最大并行数。

- Broadcast Cluster

广播调用所有提供者，逐个调用，任意一台报错则报错。(2.1.0开始支持)通常用于通知所有提供者更新缓存或日志等本地资源信息。

总结：在实际应用中查询语句容错策略建议使用默认Failover Cluster，而增删改建议使用Failfast Cluster或者使用Failover Cluster（retries=”0”）策略防止出现数据重复添加等等其它问题。建议在设计接口时候把查询接口方法单独做一个接口提供查询。

2、使用dubbo遇到过哪些问题？

增加提供服务版本号和消费服务版本号

这个具体来说不算是一个问题，而是一种问题的解决方案，在我们的实际工作中会面临各种环境资源短缺的问题，也是很实际的问题，刚开始我们还可以提供一个服务进行相关的开发和测试，但是当有多个环境多个版本，多个任务的时候就不满足我们的需求，这时候我们可以通过给提供方增加版本的方式来区分.这样能够剩下很多的物理资源，同时为今后更换接口定义发布在线时，可不停机发布，使用版本号.引用只会找相应版本的服务，例如：

```
<dubbo:serviceinterface="com.xxx.XxxService" ref="xxxService" version="1.0"/>
<dubbo:referenceid="xxxService" interface="com.xxx.XxxService" version="1.0"/>
```

3、dubbo reference注解问题？

@Reference只能在SpringBean实例对应的当前类中使用，暂时无法在父类使用；如果确实要在父类声明一个引用，可通过配置文件配置dubbo：reference，然后在需要引用的地方跟引用SpringBean一样就可以了.

4、出现RpcException：No provider available for remote service异常怎么办？

- 检查连接的注册中心是否正确
- 到注册中心查看相应的服务提供者是否存在
- 检查服务提供者是否正常运行

5、服务提供者没挂，但在注册中心里看不到？

首先，确认服务提供者是否连接了正确的注册中心，不只是检查配置中的注册中心地址，而且要检查实际的网络连接。

其次，看服务提供者是否非常繁忙，比如压力测试，以至于没有CPU片段向注册中心发送心跳，这种情况减小压力将自动恢复。

6、Dubbo的连接方式有哪些？

Dubbo的客户端和服务端有三种连接方式，分别是：广播，直连和使用zookeeper注册中心。

7、Dubbo广播

这种方式是dubbo官方入门程序所使用的连接方式，但是这种方式有很多问题。在企业开发中，不使用广播的方式。taotao-manager服务端配置：

```
!-- applicationContext-service.xml 文件中 -->
<!-- 提供方应用信息，用于计算机依赖关系 -->
<dubbo:application name="taotao-manager-service” />
<!-- 使用 multicast 广播暴露服务地址 -->
<dubbo:registry address="multicast：//224.5.6.7：1234" />
<!-- 使用 dubbo 协议在 20880 协议暴露服务 -->
<dubboprotocol name="dubbo" port="20880" />
<!-- 声明需要暴露的服务接口 -->
<dubbo:service interface="com.taotao.manager.service.TestService" ref="testServiceImpl" />
```