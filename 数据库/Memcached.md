协议简单，基于文本行的协议
基于libevent的事件处理
内置内存储存方式
memcached不互相通信的分布式。


协议简单
memcached的服务器客户端通信不使用复杂的xml等格式，而使用简单的基于文本行的网络协议。
因此，通过telnet也能在memcached上保存/读取数据.


基于libevent的事件处理
libevent是一个程序库，它将linux的epoll、BSD类操作系统的kqueue等I/O模型事件处理功能封装成统一的接口。
即使对服务器的连接数增加，也能发挥O(1)的性能。memcached使用libevent库，因此能在linux、BSD、Solaris等操作系统上发挥其高性能。


内置内存储存方式
为了提高性能memcached中的数据都储存在memcached内置的内存储存空间中，由于数据存在内存中，因此重启memcached、重启操作系统都会导致全部数据消失。
当内容容量达到指定值之后，就基于LRU算法（最近最少使用算法）自动删除不使用的缓存。
memcached本身是为缓存而设计的服务器，因此并没有考虑数据的永久性储存问题。


memcached不互相通信的分布式
首先，memcached是“分布式”缓存服务器，但服务器端没有分布式功能，各个memcached不会互相通信。
具体怎么实现分布式，得靠客户端的分布式策略（分布式算法）实现。

Memcached储存方式：
数据储存方式：Slab Allocation
数据过期方式：Lazy Expiration + LRU（最近最少使用算法）


数据储存方式
Slab Alloction的基本原理是：按照预先规定的大小，将分配的内存分割成特定长度的块（chunk），以求完全解决内存碎片问题。
   将分配的内存分割成各种尺寸的块（chunk），并把尺寸相同的块分成组（chunk的集合）
其中几个概念：
page：memcached分配给Slab的内存空间，默认大小是1M。分配给Slab之后，根据slab的大小，切分成块（chunk）。
chunk：用于缓存内容的内存空间。
Slab class：特定空间大小的chunk组。
memcached存入数据之前，根据收到的数据大小，选择最合适数据大小的slab。
memcached中保存着slab内空闲chunk的列表，根据列表选择chunk，然后将数据缓存于其中。


通俗点将，memcached启动后，先将配置设定的page大小（默认每个page空间大小为1M），将设定大小的内存平均分割为一个个page。
然后，page将按照memcached里面设定的参数，将page切分成特定大小的chunk组。比如有个page（1M）其中这个page里面每个chunk的空间大小都为128bytes。
不同的page可能切分的chunk大小不同，这样可以将不同大小内容的数据，选择一个相差不多的page（chunk组）存放数据。

缺点：
由于分配的chunk空间大小是特定长度的，因此无法充分的利用内存。比如将100字节的数据缓存到 128bytes 的chunk中，将会浪费28字节的空间。


数据过期方式
Lazy Expiration原理：
memcached内部不会监视记录是否过期，而是在get时查看记录的时间戳，检查记录是否过期。这种技术称为Lazy Expiration(惰性过期)。所以memcached不会在过期监视上面消耗CPU资源。


LUR原理：
memcached会优先使用已超时记录的空间，但即使如此，也会发生追加新记录时空间不足的情况。当空间不足时，memcached就会使用LUR（Least Recently Used）机制来分配空间。从缓存的角度看，这个模型还是十分理想的。但是，如果某条缓存正在被其他程序访问，这时候memcached再去LUR的话就会报错。