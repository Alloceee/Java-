# Struts2

- struts是按照一个MVC模式设计的web层框架，其实他是一个大大的servlet，这个servlet名为ActionServlet，或者是ActionServlet的子类。我们可以在web.xml文件中将符合某种特征的所有请求交给这个servlet处理，这个servlet再参照一个配置文件（通常为/WEB-INF/struts-config.xml）将各个请求分别分配给不同的action去处理
- 扩展：struts的配置文件可以有多个，可以按照模块配置各自的配置文件，这样可以防止配置文件的过度膨胀
- ActionServlet把请求交给action去处理之前，会将请求参数封装成一个formbean对象（就是一个Java类，这个类中的每个属性对应一个请求参数），封装成一个什么样的formbean对象呢。看配置文件
- 要说明的是，ActionServlet把formbean对象传递给action的execute方法之前，可能会调用formbean的validate方法进行校验，只有校验通过后才将这个formbean对象传递给action的execute方法，否则，它将返回一个错误页面，这个错误页面由Input属性指定，（看配置文件）
- action执行完后要返回显示的结果视图，这个结果视图是用一个ActionForward对象来表示的，actionforward对象通过struts-config.xml配置文件的配置关联到某个jsp页面，因为程序中使用的是在struts-config.xml配置文件为jsp页面设置的逻辑名，这样可以实现action程序代码与返回的jsp页面名称的解耦

**58.Springmvc与Struts区别**

参考文章：
[同是流行MVC框架，比较Strtus2和SpringMVC的区别 - 汤长海的博客 - 博客频道 - CSDN.NET](https://link.zhihu.com/?target=http%3A//blog.csdn.net/tch918/article/details/38305395)
[SpringMVC与Struts2区别与比较总结 - Java我人生的技术博客 - 博客频道 - CSDN.NET](https://link.zhihu.com/?target=http%3A//blog.csdn.net/chenleixing/article/details/44570681)

