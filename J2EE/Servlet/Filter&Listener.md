# Filter过滤器

Servlet提供了一个组件，叫做过滤器。

#### 作用

拦截请求。所以过滤器可以决定，当前请求是否倒带被拦截的资源。

它由两个接口组成：

```java
public interface Filter{
    public void init(FilterConfig config);
    public void destory();
    public void doFilter(ServletRequest rq,SevrletResponse rsp,FilterChain chain);
}
```

| 方法     | 作用                                                         |
| -------- | ------------------------------------------------------------ |
| init     | 过滤器初始化的时候使用                                       |
| destory  | 过滤器销毁的时候会被调用。Servlet容器销毁\|退出。只会被调用一次 |
| doFilter | 如果当时的请求能够匹配上过滤器的拦截路径，则该过滤器的doFilter |

#### 核心方法

```java
interface FilterChain{
public void doFilter(ServletRequest rq,ServletResponse rp);
}
```

工作原理：

![img](file:///C:\Users\ALMOST~1\AppData\Local\Temp\ksohtml9184\wps4.jpg)

**实战案例1：使用过滤器实现关键字过滤。【OK】**

1个页面，评论提交。

1个过滤器，做一些业务校验或者参数校验。

不允许用户输入一些不文明的词汇。

1个Servlet，接收来自页面的参数。保存到数据库。

 

**实战案例2：统一的POST参数编码过滤器。【OK】**

实战案例3：使用过滤器实现权限校验。

实战案例4：使用过滤器实现跨域。

实战案例5：使用顾虑器实现异常统一处理。

实战案例6：使用过滤器实现统一数据格式返回。



# Listener监听器

通过监听器，可以监听三大作用域的生命周期的各个阶段（方法）的调用。还可以监听三大作用域的属性变化。

案例：监听Session的创建和销毁，实现群聊项目中的在线人数监控

