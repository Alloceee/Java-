# 线程池

### 常用的线程池

- newSingleThreadExecutor:单线程线程池，此线程保证所有任务的执行顺序按照任务的提交顺序执行。
- newFixedThreadExecutor:固定大小线程池，每次提交一个任务就创建一个线程池，直到线程达到线程池的最大大小。
- newCachedThreadExecutor:可缓存线程池，此线程池不会对线程大小做限制，线程池的大小完全依赖于操作系统（或者说JVM）能够创建的最大线程大小。
- newScheduledThreadPool:大小无限的线程池，此线程池支持定时以及周期性执行任务的需求。

---

### 对线程池的理解(线程池如何使用，线程池的好处，线程池的启动策略)

- 合理使用线程池有三个好处
  - 第一：降低资源消耗。通过重复利用已创建的线程降低线程创建和销毁造成的消耗
  - 第二：提高响应速度。当任务到达时，任务可以不需要等到线程创建就能立即执行
  - 第三：提高线程的可管理性。线程是稀缺资源，如果无限制的创建，不仅会消耗系统资源，还会降低系统的稳定性，使用线程池可以进行统一的分配，调优和监控

### ThreadLocal的原理和应用场景

- 每一个ThreadLocal能够放一个线程级别的变量，可是它本身能够被多个线程共享使用，并且又能够达到线程安全的目的，且绝对线程安全。
- 应用场景：最常见的ThreadLocal使用场景为用来解决数据库连接、Session管理等

### 实现Runnable接口和Callable接口的区别

如果想让线程池执行任务的话需要实现的Runnable接口或Callable接口。Runnable接口或Callable接口实现类都可以被ThreadPoolExecutor或ScheduledThreadPoolExecutor执行。两者的区别在于Runnable接口不会返回结果，但是Callable接口可以返回结果。

### 执行execute()方法和submit()方法的区别是什么

- execute()方法用于提交不需要返回值的任务，所以无法判断任务是否被线程池执行成功与否
- submit()方法用于提交需要返回值的任务。线程池会返回一个future类型的对象，通过这个future对象可以判断任务是否执行成功，并且可以通过future的get()方法来获取返回值，get()方法会阻塞当前线程知道任务完成，而使用get(long timeout,TimeUnit unit)方法则会阻塞当前线程一段时间后立即返回，这时候有可能任务没有执行完。