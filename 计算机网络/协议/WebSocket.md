# WebSocket

### 介绍

WebSocket协议RFC 6455提供了一种标准化方法，可通过**单个TCP**连接在客户端和服务器之间建立**全双工双向通信通道**。它来自HTTP的不同TCP协议，但设计为使用端口80和443通过HTTP工作，并允许重用现有的防火墙规则。运行在TLS之上时，默认使用443端口。

WebSocket使客户端和服务器之间的数据交换变得更加简单，允许服务器主动向客户端推送数据。在WebSocket API中，浏览器和服务器只需要完成一次握手，两者之间就直接可以创建持久性的连接，并进行双向数据传输。

WebSocket交互以HTTP请求开始，该HTTP请求使用HTTP `"Upgrade"`标头升级，或者在这种情况下切换到WebSocket协议：

```
GET / spring-websocket-portfolio / portfolio HTTP / 1.1
Host：localhost：8080
Upgrade：websocket 
Connection：Upgrade
Sec-WebSocket-Key：Uc9l9TMkWGbHFD2qnFHltg ==
Sec-WebSocket-Protocol：v10.stomp，v11.stomp
Sec-WebSocket-Version：13
Origin：http：// localhost：8080
```

>- Connection必须设置为Upgrade，表示客户端希望连接升级
>- Upgrade字段必须设施webSocket，表示连接升级到WebSocket协议
>- Sec-WebSocket-Key是随机字符串，服务器会用这些数据来构造出一个SHA-1的信息摘要。如此操作，避免普通HTTP请求被误以为WebScoket协议
>- Sec-WebSocket-Version表示支持的版本。RFC6455要求使用的版本是13，之前的版本均应当弃用
>- Origin字段可选，通常用来表示在浏览器中发起此WebSocket连接所在的页面，类似Referer。但是，与Referer不同的是，Origin只包含了协议和主机名称。



### WebSocket和Socket的区别

Socket是传输控制层协议，WebScoket是应用层协议。



## 客户端介绍

### WebSocket 对象创建

```javascript
var socket = new WebSocket(url,[protocol]);
```

| 参数     | 描述                         |
| -------- | ---------------------------- |
| url      | 指定连接的URL                |
| protocol | 参数可选，指定可接收的子协议 |



### WebSocket 方法

| 方法           | 描述             |
| -------------- | ---------------- |
| Socket.send()  | 使用连接发送数据 |
| Socket.close() | 关闭连接         |

```javascript
function sendMessage() {
            if (socket.readyState === 1) {
                socket.send(JSON.stringify(message));
            }
        }
```

> 需要先打开连接再调用send方法，因此最好先做状态判断

### WebSocket 事件

| 事件    | 事件处理程序     | 描述                       |
| ------- | ---------------- | -------------------------- |
| open    | Socket.onopen    | 连接建立时触发             |
| message | Socket.onmessage | 客户端接收服务端数据时触发 |
| error   | Socket.onerror   | 通信发生错误时触发         |
| close   | Socket.onclose   | 连接关闭时触发             |



### WebSocket 属性

| 属性                  | 描述                                                         |
| --------------------- | ------------------------------------------------------------ |
| Socket.readyState     | 只读属性readyState表示连接状态，可以是以下值：               |
|                       | 0 -表示连接尚未建立                                          |
|                       | 1 -表示连接已建立，可以进行通信                              |
|                       | 2 -表示连接正在进行关闭                                      |
|                       | 3 -表示连接已经关闭或者连接不能打开                          |
| Socket.bufferedAmount | 只读属性bufferedAmount已被send()放入正在队列中等待传输，但是还没有发出的UTF-8文本字节数 |

```javascript
<script>
    $(function () {
        var socket;
        function openWebSocket() {
            //判断浏览器是否支持websocket
            if ((typeof WebSocket) == "undefined") {
                console.log("您的浏览器不支持WebSocket");
            } else {
                var socketUrl = 'ws://192.168.2.234:1234/socket/${user.uuid}';
                socket = new WebSocket(socketUrl);
                socket.onopen = function (ev) {
                    console.log("websocket open");
                };
                socket.onmessage = function (ev) {
                    console.log("收到的信息 : "+ev.data)
                socket.onerror = function (ev) {
                    console.log("websocket 已经关闭")
                };
                socket.onclose = function (ev) {
                    console.log("websocket 发生了错误")
                };
            }
        };
        openWebSocket();
        $("#sendContent").on('click',function () {
            //声明一个消息
            var message = {
                nickname: '${user.nickname}',
                content: $('#message').html()
            };
            if (socket.readyState === 1) {
                console.log("发送的信息 : "+JSON.stringify(message));
                socket.send(JSON.stringify(message));
            }
        });
    })
</script>
```









## 服务端介绍

#### 方法注解

| 方法              | 作用                                 |
| ----------------- | ------------------------------------ |
| @ServerEndpoint() | 服务端的路由映射                     |
| @OnOpen()         | 当客户端握手时被调用，只会被调用一次 |
| @OnMessage()      | 当客户端发来消息时被调用             |
| @OnClose()        | 当客户端被关闭时调用                 |
| @OnError()        | 当发生错误时被调用                   |

```java
    @OnOpen
    public void onOpen(Session session, @PathParam("userId")String userId){
        this.session = session;
    }
    @OnMessage
    public void onMessage(String message,Session session) {
        //注入的参数为前端传过来的数据
        System.out.println(message); 
    }
    @OnClose
    public void onClose (@PathParam("userId")String userId){
        webSocketList.remove(userId);
        //在线人数减一
        subOnlineCount();
    }
    @OnError
    public void onError(Session session,Throwable error){
        System.out.println("发生错误");
        error.printStackTrace();
    }
```

> Session代表会话，一次连接就是一个会话。意味着，N个客户端有N个session



## WebSocket + SpringBoot实现即时通信（群聊）

> 因为websocket注册的bean默认是自己管理，没有托管给spring，所以，此类是为了将websocket的bean托管给spring容器

```java
@Configuration
@ConditionalOnWebApplication
public class WebSocketConfig  {

    //使用boot内置tomcat时需要注入此bean
    @Bean
    public ServerEndpointExporter serverEndpointExporter() {
        return new ServerEndpointExporter();
    }

    @Bean
    public MySpringConfigurator mySpringConfigurator() {
        return new MySpringConfigurator();
    }
}
```

```java
/**
 *  以websocketConfig.java注册的bean是由自己管理的，需要使用配置托管给spring管理
 * @author AlmostLover
 */
public class MySpringConfigurator extends ServerEndpointConfig.Configurator implements ApplicationContextAware {

    private static volatile BeanFactory context;

    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        MySpringConfigurator.context = applicationContext;
    }

    @Override
    public <T> T getEndpointInstance(Class<T> clazz) throws InstantiationException {
        return context.getBean(clazz);
    }
}0
```



#### 前端页面





#### 后端控制器





#### 异常解决

java.io.EOFException

java.lang.IllegalStateException: The WebSocket session [1] has been closed and no method (apart from close()) may be called on a closed session



|      |      |
| ---- | ---- |
|      |      |
|      |      |

