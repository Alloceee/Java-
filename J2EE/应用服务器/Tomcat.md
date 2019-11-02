# Tomcat嵌入版

1. 引入tomcat 的jar包

```java
 public static void main(String[] args) throws LifecycleException, InterruptedException {
        //创建tomcat对象
        Tomcat tomcat = new Tomcat();
        //设置端口号
        tomcat.setPort(8080);
        //添加工程项目，第一个参数为项目名
        StandardContext context = (StandardContext) tomcat.addWebapp("/web"
                //静态资源路径
                ,new File("res").getAbsolutePath());
        //注册Servlet
        Wrapper wrapper = Tomcat.addServlet(context,"IndexServlet",IndexServlet.class);
        //添加servlet映射
        wrapper.addMapping("/index");
        tomcat.start();
        tomcat.getServer().wait();
    }
```

**Tomcat，Apache，JBoss的区别**

Apache:HTTP服务器(WEB服务器)，类似IIS，可以用于建立虚拟站点，编译处理静态页面，可以支持SSL技术，支持多个虚拟主机等功能。
Tomcat:Servlet容器，用于解析jsp，Servlet的Servlet容器，是高效，轻量级的容器。缺点是不支持EJB，只能用于java应用。
Jboss:应用服务器，运行EJB的J2EE应用服务器，遵循J2EE规范，能够提供更多平台的支持和更多集成功能，如数据库连接，JCA等，其对Servlet的支持是通过集成其他Servlet容器来实现的，如tomcat和jetty。