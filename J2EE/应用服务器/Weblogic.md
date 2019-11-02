# Weblogic

- WebLogic是美国Oracle公司出品的一个application server，确切的说是一个基于JAVAEE架构的中间件，WebLogic是用于开发、集成、部署和管理大型分布式Web应用、网络应用和数据库应用的Java应用服务器。将Java的动态功能和Java Enterprise标准的安全性引入大型网络应用的开发、集成、部署和管理之中。
- 往白了说，weblogic就是和我们常用的tomcat差不多的部署jeva web程序的服务器。那么weblogic又和tomcat有什么区别呢？我们在什么时候该用tomcat，什么时候该用weblogic？

## Weblogic和Tomcat不同点

1. tomcat只能算是web container，是官方指定的Jsp&servlet容器，只实现了jsp和servlet的相关规范，不支持EJB

2. weblogic是将J2EE的应用服务器（web container+EJB container），包括EJB，JSP，servlet，JMS等，属于全能型

3. weblogic server凭借其出色的集群技术，拥有处理关键web应用系统问题所需的性能、可扩展性和高可用性。Weblogic Server既实现了网页集群，也实现了EJB组件集群，而且不需要任何专门的硬件或操作系统支持。网页集群可以实现透明复制、负载均衡以及表示内容容错。无论是网页集群，还是组件集群，对于电子商务解决方案所要求的可扩展性和可用性都是至关重要的。共享的客户机/服务器和数据库连接以及数据缓存和EJB都增强了性能表现。这是其他web应用系统所不具备的。所以，在扩展性方面Weblogic是远远超过了Tomcat.

   