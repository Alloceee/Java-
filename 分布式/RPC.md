## 基于分布式思想下的RPC解决方案

### RPC

远程过程调用（Remote Proceduce Call）

调用远程计算机上的服务，就像调用本地服务一样。

![1569068529106](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569068529106.png)

### RMI

RMI（remote method invocation），可以认为是RPC的java版本，允许运行在一个java虚拟机的对象调用运行在另一个java虚拟机上对象的方法

实现原理：

RMI使用的是JRMP（Java Remote Messageing Protocol）协议，JRMP是专门为java定制的通信协议，所以是纯java的分布式解决方案

#### 实现一个RMI程序

1. 创建一个远程接口，并继承java.rmi.Remote接口

   ```java
   public interface IOrder extends Remote {
       //付款的方法
       public String pay(String id) throws RemoteException;
   }
   ```

2. 实现远程接口，并且继承UnicastRemoteObject

   ```java
   /**
    * 接口实现类，继承UnicastRemoteObject
    */
   public class OrderImpl extends UnicastRemoteObject implements IOrder {
       protected OrderImpl() throws RemoteException{
           super();
       }
   
       @Override
       public String pay(String id) throws RemoteException {
           return "支付成功";
       }
   }
   ```

3. 创建服务器程序，同时使用createRegistry方法注册远程接口对象

   ```java
   public class Server {
       public static void main(String[] args) throws Exception {
           //接口实例化
           IOrder iOrder = new OrderImpl();
           //本地的服务注册到一个端口中
           LocateRegistry.createRegistry(6666);
           //把刚才的实例绑定到本地端口上的一个路径上
           Naming.bind("rmi://localhost:6666/order", iOrder);
       }
   }
   ```

4. 创建客户端程序，通过Naming类的lookup方法来远程调用接口中的方法

   ```java
   public class Client {
       public static void main(String[] args) throws Exception{
           //通过RMI发现服务并且转成一个对象
           IOrder iOrder = (IOrder) Naming.lookup("rmi://localhost:6666/order");
           //远程调用一下
           iOrder.pay("168888");
       }
   }
   ```

   

![1569070248930](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569070248930.png)

dubbo是实现rpc的优雅框架

## 手写一个RPC框架

1. 服务端定义接口和服务实现类并且注册服务
2. 客户端查询出服务
3. 客户端使用动态代理调用服务（动态代理）
4. 客户端代理把调用对象、方法、参数序列化成数据
5. 服务端反序列化数据成对象、方法、参数、
6. 服务端代理拿到这些对象和参数后通过反射的机制调用服务的实例

### 实现技术

Socket通讯、动态代理与反射、Java序列化

![1569133805345](C:\Users\AlmostLover\Desktop\MarkDown笔记\分布式\1569133805345.png)

RPC中的远程调用：给目标对象提供一个代理对象，并由代理对象控制对目标对象的引用；

目的：屏蔽掉具体实现的细节——通过网络通讯远程调用的增强

#### 序列化与反序列化

发送方将对象名称、方法名称、参数等对象信息变为二进制的01串

接收方将二进制的01串变为本来的对象名称、方法名称、参数等对象

反射：在运行时才知道要操作的类是什么，并且可以在运行时获取类的完整构造，并调用对应的方法

