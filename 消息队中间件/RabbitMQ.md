# RabbitMQ

RabbitMq
	AMQP规范和
RabbitMQ基本概念
		要素
			生产者、消费者、消息
			信道
			交换器、队列、绑定、路由键
		消息的确认
		交换器类型
			Direct
			Fanout
			Topic
		虚拟主机
	RabbitMQ在Windows下安装和运行
	原生Java客户端使用
	消息发布时的权衡
		失败通知
		发布者确认
		事务
		备用交换器
	消息消费时的权衡
		消息的获得方式
		QoS预取模式
		可靠性和性能的权衡
	消息的拒绝
		消息的拒绝方式
		死信交换器
	控制队列
		临时队列
		永久队列
		队列级别消息过期
	消息的属性
		属性列表
		消息的持久化
	与Spring集成
		Xml配置方式
		SpringBoot
		实战：应用解耦
	安装配置
		下载安装和日常管理
		web监控平台
	集群化与镜像队列

### 简介

MQ全称为Message Queue，消息队列（MQ）是一种应用程序对应用程序的通信方法。应用程序通过读写入队列的消息（针对应用程序的数据）来通信，而无需专用连接来链接它们。消息传递指的是程序之间通过在消息中发送数据进行通信，而不是通过直接调用彼此来通信。队列的使用除去了接收和发送应用程序同时执行的请求。其中较为成熟的MQ产品有IBM WEBSPHERE MQ等等。

RabbitMQ是一个在AMQP基础上完成的，可复用的企业消息系统。他遵循Mozilla Public License开源协议。

AMQP，即Advanced Message Queuing Protocol，一个提供统一消息服务的应用层标准高级消息队列协议，是应用层协议的一个开放标准，为面向消息的中间件设计，基于此协议的客户端和消息中间件可传递消息，并不受客户端/中间件不同产品，不同开发语言等条件的限制。Erlang中的实现由RabbitMQ等。

### 安装

先下载erlang，再安装rabbitmq

[erlang下载地址](https://www.erlang.org/downloads)

[rabbitmq下载地址](https://www.rabbitmq.com/download.html)

本机查看管理页面

http://127.0.0.1:15672/

端口号：5672

#### 用户角色

1、超级管理员(administrator)
可登陆管理控制台，可查看所有的信息，并且可以对用户，策略(policy)进行操作。
2、监控者(monitoring)
可登陆管理控制台，同时可以查看rabbitmq节点的相关信息(进程数，内存使用情况，磁盘使用情况等)
3、策略制定者(policymaker)
可登陆管理控制台, 同时可以对policy进行管理。但无法查看节点的相关信息(上图红框标识的部分)。
4、普通管理者(management)
仅可登陆管理控制台，无法看到节点信息，也无法对策略进行管理。
5、其他
无法登陆管理控制台，通常就是普通的生产者和消费者。

![1567341536943](C:\Users\AlmostLover\Desktop\MarkDown笔记\消息队列\1567341536943.png)

