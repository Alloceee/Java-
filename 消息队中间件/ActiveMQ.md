ActiveMq
	JMS规范
		什么是JMS（Java Messaging Service）规范？
		包含要素
		消息类型
		P2P模型
		Topic(PUB\SUB)模型
	ActiveMQ使用
		安装和部署
		原生ActiveMQ的API编程
		与Spring集成
			Xml配置方式
			SpringBoot
		Request-Response模式
		实战：用户注册的异步处理
	ActiveMQ高级特性和用法
		嵌入式MQ
		消息存储的持久化机制
		消息持久订阅
		消息的可靠性
		通配符式分层订阅
		死信队列DLQ(Dead Letter Queue)
		镜像队列
		虚拟主题
		组合Destinations
	实战：限时订单
	企业级高可用集群部署方案
		Shared
			File System 
			DB
		Replicated LevelDB Store
		Broker-Cluster