# Cookie

Cookie存在于请求和响应中。

```
http://localhost/a/b/index.html 
响应头：
		Set-Cookie: name=zhangsan;Path=/a;Expires=UTC;doMain=localhost
		Set-Cookie: id=12580;Path=/a/b;Expires=UTC;doMain=baidu.com
请求头：
		Cookie:name=zhangsan;id=12580
下次请求：  
        http://localhost/a/hello.html 
浏览器会携带？  name=zhangsan.
	
下次请求：  
        http://localhost/a/b/c.html 
浏览器会携带？  name=zhangsan     id=12580 
```

#### 特点

1. Cookie的结构是键值对结构。对于value，浏览器会对Cookie进行URLEncode。 

2.  Cookie可以有多个键值对。  

3. 响应头是通过Set-Cookie来封装。请求头是通过Cookie来封装，其中Cookie中的多个键值对用;隔开，键和值用=号隔开。  

4. Cookie必须要挂载在某个路径上。下次请求时某个路径时，会携带该路径下有效的所有

Cookie。

​	Set-Cookie: Path=/;

​	通过Path属性来设置。

Cookie可以设置有效期。

​	Set-Cookie: Expires=GMT;

​	通过Expires属性来设置，可以不设置。

​	如果不设置，浏览器会默认为会话级别的Cookie。当当前窗口关闭，Cookie就会失效。

​	如果有效期过了，那么浏览器就会清除这些Cookie。下次请求就不会携带这些被清除的

​	Cookie过去。

Cookie必须限定于某个域名下，否则会存在跨域问题。

​	Set-Cookie: doMain=localhost;

​	由于HTTP服务端，可以任意设定doMain属性为其它域。因此浏览器为了安全，都会忽略这个属性。

​	现在几乎不用设置这个属性(doMain)。

 

Cookie的其它属性：

Cookie由HTTP服务器产生，由HTTP客户端存储。它是一种客户端存储技术。

​	Cookie可以被覆盖

​	例如：给浏览器发了一个Cookie，  appid=12590，有效期不确定。

​	当前需要删除该cookie或者更新Cookie的有效期。

​	Set-Cookie: appid=12580 

Servlet对Cookie的支持：

Servlet提供了一个Cookie类，通过Response.add()来为响应添加一个Cookie。

Cookie类方法如下：

![img](file:///C:\Users\ALMOST~1\AppData\Local\Temp\ksohtml9184\wps1.jpg) 

setMaxAge(-1) 不会被持久保存，浏览器一退出，就删除。

setMaxAge(0)  删除cookie。

# Session

sessionId存储在cookie中

Servlet的有一个接口：HttpSession，该接口的子类实例表示一个会话。

其原理是，Tomcat通过一个name的JESSIONID的Cookie来实现的。

Session存在于Tomcat中，由Tomcat管理。

Session也有生命周期和有效期。其生命周期可以通过监听器来监听，有效期默认为30分钟。

Session也是一个**域对象**。通过HttpServletRequest对象来获取。

**Servlet中的Session原理**

![img](file:///C:\Users\ALMOST~1\AppData\Local\Temp\ksohtml9184\wps2.jpg) 

 

**Session的创建**

Session默认是不存在，需要通过**request.getSession()**来创建。这句代码返回的响应如下：

Set-Cookie: JSESSIONID=200CE64074FE8E7109FF0C30DFE96A34; Path=/pro/; HttpOnly

Path说明：挂载路径（有效路径），pro项目下的所有资源。

没有设置Expires属性。说明该Cookie是会话级别的。关闭浏览器之后。浏览器会删除该Cookie，但是服务端不会立即删除。服务端（Tomcat）会等到Session过期了之后再删除。

HttpOnly：说明前端用document.cookie访问不到。

 

推荐：**可以只使用rq.getSession();** 如果要求性能。可以在创建Session的时候，使用rq.getSesion();获取或者销毁Session的时候使用rq.getSession(false);

 

HttpSession的方法：

 ![img](file:///C:\Users\ALMOST~1\AppData\Local\Temp\ksohtml9184\wps3.jpg)

Invalidate()//销毁Session

Session的使用：

可以作为用户在服务端的身份标识。例如，某些服务端操作，需要用户登陆的情况下，才能操作。可以在登录时，将用户信息存入Session。访问这些需要有登陆权限的操作时，先获取Session，再从Session里获取用户信息，如果获取得到用户信息，说明用户是已经登陆的身份。否则就是未登陆的身份。

 

小案例：

添加用户。只有管理员才能添加用户。





### session和cookie的区别

- 无论客户端做怎样的设置，session都能够正常工作，当客户端禁用cookie时将无法使用cookie对象
- 在存储的数据量方面：session能够存储任意类型的java对象，cookie只能存储string类型的对象
- session默认30分钟后失效

### 单点登录中如果cookie被禁了怎么办

- 单点登录的原理是后端生成sessionID，然后设置到cookie，后面的所有请求浏览器都会带上cookie，然后服务端从cookie里获取sessionID，在查询到用户信息。所以，登录的关键不是cookie，而是通过cookie保存和传输的sessionID，其本质是能获取用户信息的数据。除了cookie，还通常使用HTTP请求头来传输。但是这个请求头浏览器不会像cookie一样自动携带，需要手工处理。