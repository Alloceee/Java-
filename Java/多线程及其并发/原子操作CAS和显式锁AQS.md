### CAS(compare and swap)

CAS的原理是拿期望的值和原本的一个值作比较，如果相同则更新成新的值。

### AQS(AbstractQueuedSynchronizer)

AOS是一个从来构建锁和同步器的框架，使用AQS能简单且高效的构造出应用广泛的大量的同步锁，比如我们提到的ReenTrantLock,Semaphore，其他的诸如ReentrantReadWriteLock,SynchronousQueue,FutureTask等等都是基于AQS的。

AQS核心思想是，如果被请求的共享资源空闲，则将当前请求资源设置为有效的工作线程，并且将共享资源设置为锁定状态，如果被请求的共享资源被占用，那么就需要一套线程阻塞等待以及被唤醒时锁分配的机制，这个机制AQS是用CLH队列锁实现的，即将暂时获取不到锁的线程加入到队列中。

> CLH（Craig,Landin and Hagersten）队列是一个虚拟的双向队列（虚拟的双向队列即不存在队列实例，仅存在结点之间的关联关系）。AQS是将每条请求共享资源的线程封装成一个CLH锁队列的一个结点（Node）来实现锁的分配。

**CAS机制是什么？有什么缺点，会出现什么问题**

CAS是英文单词Compare And Swap的缩写，翻译过来就是比较并替换。CAS机制当中使用了3个基本操作数：内存地址V，旧的预期值A，要修改的新值B。

CAS的缺点：

1.CPU开销较大在并发量比较高的情况下，如果许多线程反复尝试更新某一个变量，却又一直更新不成功，循环往复，会给CPU带来很大的压力。

2.不能保证代码块的原子性CAS机制所保证的只是一个变量的原子性操作，而不能保证整个代码块的原子性。比如需要保证3个变量共同进行原子性的更新，就不得不使用Synchronized了。

3.ABA问题这是CAS机制最大的问题所在。什么是ABA问题？引用原书的话：如果在算法中的节点可以被循环使用，那么在使用“比较并交换”指令就可能出现这种问题，在CAS操作中将判断“V的值是否仍然为A？”，并且如果是的话就继续执行更新操作，在某些算法中，如果V的值首先由A变为B，再由B变为A，那么CAS将会操作成功。怎么避免ABA问题？Java中提供了AtomicStampedReference和AtomicMarkableReference来解决ABA问题。 