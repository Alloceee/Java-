## HashMap的底层实现原理

#### 数据结构：

数组、链表、红黑树（JDK1.8之后）

#### 特点：

1. 快速存储

2. 快速查询（时间复杂度O(1)）

3. 可伸缩（数组可动态扩容，链表长度超过8时会转化为红黑树）

#### hash算法

每个对象都有hashCode，hash算法是对象自身的hashCode右移16位再进行异或运算

```java
(hashCode)^(hashCode)>>>16
```

数组的默认大小为16

下标的计算：（hashCode）&（16-1）= hashCode%16

常用方法hashCode对16取余

数据的扩容：当数据大于容量的**75%**是扩大到原来的**两倍**

#### hash冲突

hash冲突就是两个不同的对象计算出相同的下标

解决方法，变成单向链表，加入next记录下一个节点

### HashMap的长度为什么是2的幂次方

为了能让HashMap存储高效，尽量较少碰撞，也就是尽量把数据分配均匀，



### HashMap多线程操作导致死循环问题

在多线程下，进行Put操作会导致HashMap死循环，原因在于HashMap扩容resize()方法，由于扩容是新建一个数组，复制原数据到数据。由于数组下标挂有链表，所以需要复制链表，但是多线程操作可能会导致循环链表。

> JDK1.8已经解决了死循环问题



## HashTable和ConcurrentHashMap

#### HashMap

HashMap是线程不安全的，在并发环境下，可能会形成环状链表（扩容时可能造成），导致get操作时，cpu空转，所以，并发环境下使用HashMap是非常危险的。

#### HashTable

HashTable和HashMap的原理几乎一样，差别是HashTable不允许key和Value为Null；HashTable是线程安全的，但是HashTable线程安全的将所有get/put操作进行syschronized，相当于对整个哈希表加了一把大锁，多线程访问时，只要有一个线程访问或操作该对象，那其他线程只能阻塞，相当于所有操作串行化，在竞争激烈的并发场景中性能就会非常差。

HashTable性能差主要是由于所有操作需要竞争同一把锁，而如果容器中有多把锁，每一把锁锁一段数据，这样在多线程访问时不同段的数据时，就不会存在竞争了，这样便可以有效的提高并发效率，这就是concurrentHashMap所采用的“分段锁”思想。

#### ConcurrentHashMap

ConcurrentHashMap主干是个Segment数据，Segment继承了ReentrantLock，所以他是一种可重入锁。

区别：

底层数据结构：

JDK1.7的ConcurrentHashMap底层采用**分段数组+链表**实现，JDK1.8采用的数据结构更HashMap1.8的结构一样，**数组+链表/红黑树**。HashTable和JDK1.8之前的HashMap的底层数据结构类似都是采用**数组+链表**的形式，数组是HashMap的主体，链表则是主要为了解决哈希冲突而存在的。

实现线程安全的方式：

在JDK1.7的时候，ConcurrentHashMap（分段锁）对整个桶数组进行了分割分段（Segment），每一把锁只锁容器其中一部分数据，多线程访问容器里不同数据段的数据，就不会存在锁竞争，提高并发访问率。**到了JDK1.8的时候已经摒弃了Segment的概念，而是直接用Node数组+链表+红黑树的数据结构来实现，并发控制使用synchronized和CAS来操作。(JDK1.6以后对synchronized锁做了很多优化)**整个看起来就像是优化过且线程安全的HashMap,虽然在JDK1.8中还能看到Segment的数据结构，但是已经简化了属性，只是为了兼容旧版本。