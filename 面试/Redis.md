**Redis面试题大全含答案**

**1.什么是Redis？**
答：Remote Dictionary Server(Redis)是一个开源的使用ANSI C语言编写、支持网络、可基于内存亦可持久化的日志型、Key-Value数据库，并提供多种语言的API。
它通常被称为数据结构服务器，因为值（value）可以是 字符串(String), 哈希(Map), 列表(list), 集合(sets) 和 有序集合(sorted sets)等类型。

**2.Redis的特点什么是？**
\1. 支持多种数据结构，如 string(字符串)、 list(双向链表)、dict(hash表)、set(集合)、zset(排序set)、hyperloglog(基数估算)
\2. 支持持久化操作，可以进行aof及rdb数据持久化到磁盘，从而进行数据备份或数据恢复等操作，较好的防止数据丢失的手段。
\3. 支持通过Replication进行数据复制，通过master-slave机制，可以实时进行数据的同步复制，支持多级复制和增量复制，master-slave机制是Redis进行HA的重要手段。
单进程请求，所有命令串行执行，并发情况下不需要考虑数据一致性问题。

**3.Redis数据类型有哪些？**
答：String(字符串)
Hash(hash表)
List(链表)
Set(集合)
SortedSet(有序集合zset)

**4.Redis中的常用命令哪些？**
incr 让当前键值以1的数量递增，并返回递增后的值
incrby 可以指定参数一次增加的数值，并返回递增后的值
incrby 可以指定参数一次增加的数值，并返回递增后的值
decrby 可以指定参数一次递减的数值，并返回递减后的值
incrbyfloat 可以递增一个双精度浮点数
append 作用是向键值的末尾追加value。如果键不存在则将该键的值设置为value。返回值是追加后字符串的总长度。
mget/mset 作用与get/set相似，不过mget/mset可以同时获得/设置多个键的键值
del 根据key来删除value
flushdb 清除当前库的所有数据
hset 存储一个哈希键值对的集合
hget获取一个哈希键的值
hmset 存储一个或多个哈希是键值对的集合
hmget 获取多个指定的键的值
hexists 判断哈希表中的字段名是否存在 如果存在返回1 否则返回0
hdel 删除一个或多个字段
hgetall 获取一个哈希是键值对的集合
hvals 只返回字段值
hkeys 只返回字段名
hlen 返回key的hash的元素个数
lpush key value向链表左侧添加
rpush key value向链表右侧添加
lpop key 从左边移出一个元素
rpop key 从右边移出一个元素
llen key 返回链表中元素的个数 相当于关系型数据库中 select count(*)
lrange key start end lrange命令将返回索引从start到stop之间的所有元素。Redis的列表起始索引为0。
lrange也支持负索引 lrange nn -2 -1 如 -1表示最右边第一个元素 -2表示最右边第二个元素，依次类推。
lindex key indexnumber 如果要将列表类型当做数组来用，lindex命令是必不可少的。lindex命令用来返回指定索引的元素，索引从0开始
如果是负数表示从右边开始计算的索引，最右边元素的索引是-1。
Lset key indexnumber value 是另一个通过索引操作列表的命令，它会将索引为index的元素赋值为value。
sadd key value 添加一个string元素到,key对应的set集合中，成功返回1,如果元素已经在集合中返回0
scard key 返回set的元素个数，如果set是空或者key不存在返回0
smembers key 返回key对应set的所有元素，结果是无序的
sismember key value 判断value 是否在set中，存在返回1，0表示不存在或者key不存在
srem key value 从key对应set中移除给定元素，成功返回1，如果value 在集合中不存在或者key不存在返回0
zadd key score value 将一个或多个value及其socre加入到set中
zrange key start end 0和-1表示从索引为0的元素到最后一个元素（同LRANGE命令相似）
zrange key 0 -1 withscores 也可以连同score一块输出，使用WITHSCORES参数
zremrangebyscore key start end 可用于范围删除操作
ping 测试redis是否链接 如果已链接返回 PONG
echo value测试redis是否链接 如果已链接返回 echo命令后给定的值
keys * 返回所有的key 可以加*通配
exists key判断string类型一个key是否存在 如果存在返回1 否则返回0
expire key time(s) 设置一个key的过期时间 单位秒。时间到达后会删除key及value
ttl key 查询已设置过期时间的key的剩余时间 如果返回-2表示该键值对已经被删除
persist 移除给定key的过期时间
select dbindex 选择数据库(0-15)
move key dbIndex 将当前数据库中的key转移到其他数据库中
dbsize 返回当前数据库中的key的数目
info 获取服务器的信息和统计
flushdb 删除当前选择的数据库中的key
flushall 删除所有数据库中的所有key
quit 退出连接

**5.Redis的配置以及持久化方案有几种？**
以下两种
RDB方式
AOF方式

**什么是RDB方式？**
是RDB是对内存中数据库状态进行快照
RDB方式：将Redis在内存中的数据库状态保存到磁盘里面，RDB文件是一个经过压缩的二进制文件，通过该文件可以还原生成RDB文件时的数据库状态（默认下，持久化到dump.rdb文件，并且在redis重启后，自动读取其中文件，据悉，通常情况下一千万的字符串类型键，1GB的快照文件，同步到内存中的 时间是20-30秒）
RDB的生成方式：
1、执行命令手动生成
有两个Redis命令可以用于生成RDB文件，一个是SAVE，另一个是BGSAVE SAVE命令会阻塞Redis服务器进程，直到RDB文件创建完毕为止，在服务器进程阻塞期间，服务器不能处理任何命令请求，BGSAVE命令会派生出一个子进程，然后由子进程负责创建RDB文件，服务器进程（父进程）继续处理命令请求，创建RDB文件结束之前，客户端发送的BGSAVE和SAVE命令会被服务器拒绝

2、通过配置自动生成
可以设置服务器配置的save选项，让服务器每隔一段时间自动执行一次BGSAVE命令，可以通过save选项设置多个保存条件，但只要其中任意一个条件被满足，服务器就会执行BGSAVE命令
例如：
save 900 1
save 300 10
save 60 10000
那么只要满足以下三个条件中的任意一个，BGSAVE命令就会被执行
服务器在900秒之内，对数据库进行了至少1次修改
服务器在300秒之内，对数据库进行了至少10次修改
服务器在60秒之内，对数据库进行了至少10000次修改

**什么是AOF方式？**
AOF持久化方式在redis中默认是关闭的，需要修改配置文件开启该方式。
AOF：把每条命令都写入文件，类似mysql的binlog日志
AOF方式：是通过保存Redis服务器所执行的写命令来记录数据库状态的文件。
AOF文件刷新的方式，有三种：
appendfsync always - 每提交一个修改命令都调用fsync刷新到AOF文件，非常非常慢，但也非常安全
appendfsync everysec - 每秒钟都调用fsync刷新到AOF文件，很快，但可能会丢失一秒以内的数据
appendfsync no - 依靠OS进行刷新，redis不主动刷新AOF，这样最快，但安全性就差
默认并推荐每秒刷新，这样在速度和安全上都做到了兼顾
AOF数据恢复方式
服务器在启动时，通过载入和执行AOF文件中保存的命令来还原服务器关闭之前的数据库状态，具体过程：
载入AOF文件
创建模拟客户端
从AOF文件中读取一条命令
使用模拟客户端执行命令
循环读取并执行命令，直到全部完成
如果同时启用了RDB和AOF方式，AOF优先，启动时只加载AOF文件恢复数据