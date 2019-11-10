# Redis

### 定义：

是一种开源的、基于内存的、可持久化的、高性能的Key-value数据存储系统。Redis全称为：Remote Dictionary Server(远程数据服务)，该软件使用c语言编写，典型的NoSQL数据库服务器，Redis是一个key-value存储系统，它支持丰富的数据类型，如：string、list、set、zset(sorted set)、hash。遵循BSD协议

### 特点：

- 高性能（内存存储，仅在需要时持久化到硬盘）

- 数据类型丰富（String、Hash、List、Set、SortedSet）

- **支持事务处理**：事务是一个单独的隔离操作，事务中的所有命令都会序列化、按顺序地执行，事务在执行的过程中，不会被其他客户端发送来的命令请求所打断。事务是一个原子操作，要么全部执行，要么全不执行。

- **批量操作（pipeline机制）**：一次请求/响应服务器能实时处理新的请求即使旧的请求还未被响应，这个就可以将多个命令发送到服务器，而不用等待回复，最后在一个步骤中读取该答复，这就是管道（pipeline）

- 支持设置Key有效期

- **支持主从复制（Master-Slave）和故障自动迁移**：为了使在部分节点失败或者大部分节点无法通信的情况下集群仍然可以使用，所以集群使用了主从复制模型。每个节点都会有N-1复制品

- 支持大规模集群部署

- 支持Pub/Sub消息通讯机制

- 支持Lua脚本实现复杂的数据库操作

### Redis可以做什么

持久化存储、高速缓存、消息中间件

具体实现：

1. 记录帖子的点赞数、评论数和点击数 (hash)。
2. 记录用户的帖子 ID 列表 (排序)，便于快速显示用户的帖子列表 (zset)。
3. 记录帖子的标题、摘要、作者和封面信息，用于列表页展示 (hash)。
4. 记录帖子的点赞用户 ID 列表，评论 ID 列表，用于显示和去重计数
(zset)。
5. 缓存近期热帖内容 (帖子内容空间占用比较大)，减少数据库压力 (hash)。
6. 记录帖子的相关文章 ID，根据内容推荐相关帖子 (list)。
7. 如果帖子 ID 是整数自增的，可以使用 Redis 来分配帖子 ID(计数器)。
8. 收藏集和帖子之间的关系 (zset)。
9. 记录热榜帖子 ID 列表，总热榜和分类热榜 (zset)。
10. 缓存用户行为历史，进行恶意行为过滤 (zset,hash)。



### Redis数据结构

Redis底层采用c语言编写

#### 字符串

采用与C语言类似的SDS字符串

SDS与C语言字符串的区别：

| C                                  | SDS                               |
| ---------------------------------- | --------------------------------- |
| 缓冲区溢出                         | 避免缓冲区溢出                    |
| 获取字符串长度的时间复杂度为O（n） | 获取字符串长度的时间复杂度为O(1)  |
| 插入n的字符的时间复杂度为O(n)      | 插入n的字符的时间复杂度最多为O(n) |
|                                    |                                   |
|                                    |                                   |
#### 链表



### Redis与其他key-value缓存产品有一下三个特点；

- Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候再次加载进行使用
- Redis不仅仅支持简单的Key-value类型的数据，同时还提供list、set、zset、hash等数据结构的存储
- Redis支持数据的备份，即master-slave模式的数据备份

### Redis优势

- 性能极高—Redis能读的速度是110000次/s，写的速度是81000次/s
- 丰富的数据类型
- 原子—Redis的所有操作都是原子性的，同时Redis还支持对几个操作全并后的原子性执行
- 丰富的特性—Redis还支持publish/subscribe，通知，Key过期等等特性

- Redis的主要缺点是数据库容量受到物理内存的限制，不能用作海量数据的高性能读写，因此Redis适合的场景主要局限在较小数据量的高性能操作和运算上

### Redis与其他key-value存储有什么不同？

- Redis有着更为复杂的数据结构并且提供对他们的原子性操作，这是一个不同于其他数据库的进化路径。Redis的数据类型都是基于基本数据结构的同时对程序员透明，无需进行额外的抽象。
- Redis运行在内存中但是可以持久化到磁盘，所以在对不同数据集进行高速读写时需要权衡内存，应为数据量不能大于硬件内存。在内存数据库方面的另一个优点是， 相比在磁盘上相同的复杂的数据结构，在内存中操作起来非常简单，这样Redis可以做很多内部复杂性很强的事情。 同时，在磁盘格式方面他们是紧凑的以追加的方式产生的，因为他们并不需要进行随机访问。

### Redis最适合的场景有哪些

#### 会话缓存（Session Cache）

最常用一种使用Redis的情景，用Redis缓存会话会比其他存储（如Memache）的优势在于：Redis提供持久化。当维护一个不是严格要求一致性的缓存时，例如购物车

#### 全页缓存（FPC）

除基本的会话token之外，Redis还提供很简单的FPC平台。回到一致性问题，即使重启了Redis实例，因为有磁盘的持久化，用户也不会看到页面加载速度的下降，这是一个极大改进， 类似PHP本地FPC

#### 队列

Redis在内存存储引擎领域的一大优点是提供List和set操作，这使得Redis能作为一个很好的消息队列平台来使用。Redis作为队列使用的操作，就类似于本地程序语言（如Python）对List的push/pop操作

#### 排行榜/计数器

Redis在内存中对数字进行递增或递减操作实现的非常好。集合（Set）和有序集合（SortedSet）也使得我们在执行这些操作的时候变得非常简单。

#### 发布/订阅

甚至可以用Redis的发布/订阅功能来建立聊天系统

### 穿透查询

- 客户端查询数据，先去缓存层中查看数据是否存在，如果存在直接返回，减轻了存储层的压力。如果不存在，再去存储层查询，就是“穿透”查询。讲查询的信息写到缓存层，以供一下查询，就是“回种”，回种完成之后将数据返回给客户端，完成一次请求操作。

### Redis支持的Java客户端都有哪些

Redisson、Jedis、Lettuce等等，官方推荐使用Redisson

Redisson是一个高级的分布式协调Redis客户端，能帮助用户在分布式环境轻松实现一些Java的对象

### 安装Redis

[下载地址](https://github.com/MicrosoftArchive/redis/releases)Windows版本

下载.zip版本，更加灵活

1. 切换目录到解压文件目录 cd C:\Users\AlmostLover\Desktop\学习资料\Redis-x64-3.2.100
2. 安装 redis-server.exe --service-install

```shell
C:\Users\AlmostLover>cd C:\Users\AlmostLover\Desktop\学习资料\Redis-x64-3.2.100

C:\Users\AlmostLover\Desktop\学习资料\Redis-x64-3.2.100>redis-server.exe --service-install
```

> 安装时没有指定端口，使用默认端口（6379），没有指定名称，默认Redis
>
> 安装时需要指定名称，因为会安装多个实例

```shell
redis-server.exe --service-install --service-name redis001 --port 6380 --requirepass 12345
```

3. 安装成功

![1566997639868](http://www.yewenshu.top/1566997639868.png)

![1566997613165]( http://www.yewenshu.top/1566997831189.png )

4. 启动服务并进入数据库

```shell
redis-cli.exe -h localhost -p 6380 -a 12345
```

> -h端口号 -p端口号

![1566998764359](http://www.yewenshu.top/1566997831189.png)

5.  keys * 查看所有数据

![1566997982282]( http://www.yewenshu.top/1566997982282.png )

6. 卸载

```shell
redis-server.exe --service-uninstall --service-name Redis
```

#### Ubuntu

```shell
apt install redis-server#安装
redis-server#启动服务端
redis-cli#启动客户端
```



### Redis常用命令

| Server命令                                                   | Key命令                                                      | 数据类型操作命令    |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------- |
| select（选择某个库（0-15），默认在0下操作）                  | keys（获取所有key）                                          | String(字符串)      |
| dbsize（当前库下有多少条书库）                               | del                                                          | Hash(哈希表)        |
| flushdb（清空当前库的数据）\|flushall（清空所有库的数据）    | exists(查看key是否存在。存在1，不存在0)                      | List(列表)          |
| save（将数据从内存中保存的硬盘上，主线程保存数据）\|dbsave（生成子进程保存数据，不会阻塞） | rename                                                       | Set(集合)           |
| config get                                                   | type                                                         | SortedSet(有序集合) |
| config set（可以修改配置文件的信息，但是不会修改配置文件，保存在内存中，实例重启就会恢复） | expire(设定数据的生命周期)\|persist\|ttl(查看存活时间还有多久) |                     |
| config rewrite（将修改的信息，从内存中保存到配置文件）       | move\|migrate                                                |                     |
| shutdown                                                     |                                                              |                     |

ttl(查看存活时间还有多久)   -1表示没有生命周期的限制 -2表示已经不存在

persist已经设置生命周期，打算取消

move （安装的redis001表示一个实例，一个实例有多个库，默认情况下一个实例16个库）表示一个库移动到另一个库

### Jedis连接Redis

1. 下载jedis.jar文件
2. 编写java类（对字符串的操作）

```java
public static void main(String[] args){
    //连接数据库
    Jedis jedis = new Jedis("localhost",6380);
    jedis.auth("12345");//输入密码
    
    jedis.select(10);
    jedis.flushDB();
    
    jedis.set("country","china");
    System.out.println(jedis.exists("country"));
    System.out.println(jedis.strlen("country"));//获取value的长度
    
    jedis.append("country","is great!");//在字符串值上添加一部分字符串
    System.out.println(jedis.get("country"));
    
    jedis.setrange("country",9,"amazing!");//修改Value的一部分
    System.out.println(jedis.get("country"));
    
    jedis.getrange("country",6,7);//取得value的一部分
    
    jedis.set("age","100");
    jedis.incr("age");//增加1
    jedis.incrBy("age",10);//每次增加10
    jedis.decr("age");//减少1
    jedis.decrBy("age",10);
    
    jedis.mset("name","zhangsan","age","40","sex","male");//批量添加
    
    jedis.close();//关闭
}
```

3. 对hash类型的操作

```java
public static void main(String[] args){
     //连接数据库
    Jedis jedis = new Jedis("localhost",6380);
    jedis.auth("12345");//输入密码
    
    HashMap<String,String> hp = new HashMap<>();
    hp.put("name","zhangsan");
    hp.put("age","40");
    
    jedis.hmset("hpcomputer",hp);//向redis中存入一个Hash类型数据
    System.out.println(jedis.hlen("hpcomputer"));//得到hash类型元素的个数
    
    jedis.hset("hpcomputer","madein","China");//向hash类型中添加一个元素
    Map<String,String> all = jedis.hgetAll("hpcomputer");//返回元素集合
    for(String k:all.keySet()){
        Syetem.out.println(k + ":" + all.get(k));
    }
    jedis.hget("hpcomputer","price");//price:500
    Set<String> keys = jedis.hkeys("hpcomputer");//得到全部key
}
```

4. 对list类型的操作

```java
public static void main(String[] args){
     //连接数据库
    Jedis jedis = new Jedis("localhost",6380);
    jedis.auth("12345");//输入密码
    
    jedis.lpush("list-1","aa","bb","cc");//左边添加
    jedis.rpush("list-1","dd","ee","ff");//右边添加
    jedis.lpop("list-1");//左边取走
    jedis.rpop("list-1");//右边取走
    
    jedis.llen("list-1");//查询类型元素个数
    jedis.lindex("list",2);//查询列表某个位置上的元素
    
    List<String> lv = jedis.lrange("list-1",2,4);
    for(String s:lv){
        System.out.println(s);
    }
}
```

5. 对Set集合类型的操作

```java
public static void main(String[] args){
     //连接数据库
    Jedis jedis = new Jedis("localhost",6380);
    jedis.auth("12345");//输入密码
    
    jedis.sadd("set1","tom","jim","peter");
    jedis.scard("set1");//查询元素个数
    jedis.srem("set1","tom");//移除元素
    Set<String> ss = jedis.smembers("set1");//获取set中所有成员
    for(String s:ss){
        System.out.println(s);
    }
    Set<String> sin = jedis.sinter("set1","set2");//取多个集合的交集
    jedis.sinterstore("set3","set1","set2");//将set1,set2的交集放入set3中
    
}
```



### String存储

最基本的数据类型，二进制安全

#### key设计注意事项

一般以业务功能模块，比如购物车key:cart:001,表示1号用户的购物车

简短，明了为主，节约内存

#### 简短字符串缓存

set key value

get key

#### 结构体或对象的存储

- set user:1 value //value为xml或json格式
- mset user:1:name deer user:1:age 18
- mget user:1:name user:1:age

#### 常见应用场景

##### 计数功能（文章编号001）

INCR article:001

GET article:001

##### 各类场景下（单机或分布式）的标识号

incrby serialN0 1000

> 用redis生成主键、序列号（性能高）

##### 集群环境下的session共享

使用spring session与resdis完成session共享

### Hash

String元素组成的字典，适合用于存储对象

#### 淘宝购物车

HINCRBY cart:001 prod:01 1(商品数量增加)

hgetall cart:001(获取所有商品)

优点：简单直观，使用合理可减少内存空间消耗

缺点：要控制zipList和hashTable之间的转化，hashTable更消耗内存

### List

列表，按照String元素插入顺序排序

面试题目：

如何利用List实现栈、队列

1. 先进后出：栈 = LPUSH + LPOP ->FILO
2. 先进先出：队列 = LPUSH + RPOP

什么是阻塞队列？

Blocking MQ(阻塞队列) = LPUSH + BRPOP

使用场景：

公众号推送



### Set应用场景

String元素组成的无序集合，通过哈希表实现，不允许重复

sadd key member1 member2

微信抽奖活动，活动ID为001，如何实现微信抽奖工鞥能，基于Redis设计活动

1. 当lison点击参数抽奖时，数据放入set集合

sadd act:001 004

2. 开始抽奖2名中奖者

srandmember act:001 2或spop act:001 2

3. 查看有多少用户参加了本次抽奖

smembers act:001

### Sorted Set

通过分数来为集合中的成员进行从小到大的排序



## 面试

### 为什么要用Redis/为什么要用缓存

#### 高性能

假如用户第一次访问数据库中的

#### 高并发



### 缓存中间件—Memcache和Redis的区别

#### Memache：

- 简单易用，代码层次类似Hash
- 支持简单数据类型
- 不支持数据持久化存储（如果数据库宕机则数据丢失）
- 不支持主从同步
- 不支持分片（将大数据打碎分配到多个物理结点的方案）

#### Redis:

- 数据类型丰富
- 支持数据磁盘持久化存储
- 支持主从同步（和MySQL一样）
- 支持分片

> Redis的速度比Memache要快很多，因为memache多线程模型引入了缓存一致性和锁，加锁带来了性能损耗  

### 为什么Redis能这么快（100000+QPS）

- 完全基于内存，绝大部分请求是纯粹的内存操作，执行效率高
- 数据结构简单，对数据操作也简单
- 采用单线程，单线程也能处理高并发（并发只由一个处理单元来处理来自多个客户端的流请求）请求，想多核也可启动多实例（避免频繁的上下文切换和锁竞争，使得效率更高）

> 单线程只是处理网络请求时采用单线程，进行持久化时根据情况采用子线程

- 使用多路I/O复用模型，非阻塞IO

#### 多路I/O复用模型

FD：File Descriptor，文件描述符

一个打开的文件通过唯一的描述符进行引用，该描述符是打开文件的元数据到文件本身的映射

##### Select系统调用

Redis采用的I/O多路复用函数：epoll/kqueue/evport/select?

有限选择时间复杂度为O(1)的I/O多路复用函数作为底层实现

以时间复杂度为O(n)的select作为保底

基于react设计模式监听I/O事件（文件事件处理器采用I/O多路复用，同时监听多个FD，但set,read,write,close时间产生时，文件事件处理器就会回调FD绑定的事件处理器，虽然整个文件处理器是在单线程模式上运行的，但是通过I/O多路模块的使用实现了同时对多个FD读写的监控，提高了网络通信模型的性能，同时可以保证整个redis服务实现的简单）

### MySQL里有2000w数据，Redis中只有20w数据，如果保证redis中的数据都是热点数据eB

redis内存数据集大小上升到一定大小的时候，就会实行数据淘汰策略。

### Redis常见的性能问题和解决方案

1. master最好不要做持久化工作，如RDB内存快照和AOF日志文件
2. 如果数据比较重要，某个slave开启AOF备份，策略设置成每秒同步一次
3. 为了主从复制的速度和连接的稳定性，master和slave最好在同一个局域网内
4. 尽量避免在压力大的主库上增加从库
5. 主从复制不要采用网状结构，尽量使用线性结构：Master<–Slave1<–Sleave2

### 说说你用过的Redis的数据类型

1. String：最基本的数据类型，二进制安全

2. Hash：String元素组成的字典，适合用于存储对象

3. List：列表，按照String元素插入顺序排序

4. Set：String元素组成的无序集合，通过哈希表实现，不允许重复

5. SortedSet：通过分数来为集合中的成员进行从小到大的排序

6. 用于技术的HyperLogLog，用于支持存储物理位置信息的Geo

   #### 底层数据类型基础

   1. 简单动态字符串
   2. 链表
   3. 字典
   4. 跳跃表
   5. 整数集合
   6. 压缩列表
   7. 对象

### 从海量key里查询出某一固定前缀的Key

keys pattern：查询所有符合给定模式pattern的key

### 缓存一致性的策略

1. 删除缓存
2. 实时更新-low
3. 定时任务的办法
4. 架构层次的办法-消息机制MQ===触发第三方服务来更新数据
5. 缓存失效机制



## 应用

### 缓存主页

首先，缓存主页的目的并不是提高性能，而是减少[数据库](https://www.2cto.com/database/)访问压力，有效推迟数据库I/O瓶颈的到来。实现主页缓存的方法有很多，但是鉴于项目中使用了Redis对数据库读写做了缓存，因此把顺便也就把主页也缓存了吧。

## 实现思路

编写一个过滤器，在过滤器中拦截对主页的访问请求。此时向Redis服务器查询主页html的缓存，如果有则直接返回给客户端，如果没有，则在过滤器中截获JSP的渲染结果，放到Redis缓存中，以供下次使用。我们设定缓存过期时间为10分钟。

## 实现

实现需要注意的地方有两点：

如何在Servlet过虑器中使用Spring容器 如何截获JSP渲染结果

对于问题一，在Servlet Filter中使用Spring容器

对于问题二，我们可以继承`HttpServletResponseWrapper`类来巧妙实现：

```java
public class ResponseWrapper extends HttpServletResponseWrapper {
    private PrintWriter cachedWriter;
    private CharArrayWriter bufferedWriter;
 
    public ResponseWrapper(HttpServletResponse response) {
        super(response);
        // 这个是我们保存返回结果的地方
        bufferedWriter = new CharArrayWriter();
        // 这个是包装PrintWriter的，让所有结果通过这个PrintWriter写入到bufferedWriter中
        cachedWriter = new PrintWriter(bufferedWriter);
    }
 
    @Override
    public PrintWriter getWriter() {
        return cachedWriter;
    }
 
    /**
     * 获取原始的HTML页面内容。
     *
     * @return
     */
    public String getResult() {
        return bufferedWriter.toString();
    }
}
```

然后在过滤器中使用该类：

```java
public class CacheFilter implements Filter, ApplicationContextAware {
    private static final Logger log = LoggerFactory.getLogger(CacheFilter.class);
 
    private static ApplicationContext ctx;
 
    @Override
    public void init(FilterConfig config) throws ServletException {
    }
 
    @Override
    public void doFilter(ServletRequest servletRequest, ServletResponse servletResponse, FilterChain filterChain) throws IOException, ServletException {
        HttpServletResponse resp = (HttpServletResponse) servletResponse;
        HttpServletRequest req = (HttpServletRequest) servletRequest;
 
        // 如果不是访问主页，放行
        if (false == req.getRequestURI().equals("/")) {
            filterChain.doFilter(servletRequest, resp);
            return;
        }
 
 
        // 访问的是主页
        // 从缓存中得到主页html
        String html = getHtmlFromCache();
        if (null == html) {
            // 缓存中没有
            // 截取生成的html并放入缓存
            log.info("缓存不存在，生成缓存");
            ResponseWrapper wrapper = new ResponseWrapper(resp);
            // ***** 以上代码在请求被处理之前执行 *****
 
            filterChain.doFilter(servletRequest, wrapper);
 
            // ***** 以下代码在请求被处理后前执行 *****
 
            // 放入缓存
            html = wrapper.getResult();
            putIntoCache(html);
 
        }
 
        // 返回响应
        resp.setContentType("text/html; charset=utf-8");
        resp.getWriter().print(html);
    }
 
    @Override
    public void destroy() {
 
    }
 
    @Override
    public void setApplicationContext(ApplicationContext applicationContext) throws BeansException {
        this.ctx = applicationContext;
    }
 
    private String getHtmlFromCache() {
        StringRedisTemplate redis = (StringRedisTemplate)ctx.getBean("redisTemplate");
        return redis.opsForValue().get("home");
    }
 
    private void putIntoCache(String html) {
        StringRedisTemplate redis = (StringRedisTemplate)ctx.getBean("redisTemplate");
        redis.opsForValue().set("home", html, TimeUnit.MINUTES.toSeconds(10)); // 10分钟
    }
}
```

按照这个逻辑，当客户的`GET`请求为`/`时，`CacheFilter`会首先向`Redis`发起请求获取主页的html代码，如果成功，则直接返回给客户端，失败，则通过刚刚写好的`ResponseWrapper`截获主页JSP的渲染结果，放入`Redis`，并设置过期时间为10分钟。这样下次请求时就可以直接从缓存中读取，而不需要经过 `Controller` -> `Service` -> `Dao` -> `Database`这么费事的流程了。

