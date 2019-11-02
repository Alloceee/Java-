# 线程池

### 线程和进程的区别

- 进程：具有一定独立功能的程序关于某个数据集合上的一次运行活动，是操作系统进行资源分配和调度的一个独立单位
- 线程：是进程的实体，是CPU调度和分派的基本单位，是比进程更小的可以独立运行的基本单位
- 特点：线程的划分尺寸小于进程，这使多线程程序拥有高并发性，进程在运行时各自内存单元相互独立，线程之间内存共享，这使多线程编程可以拥有更好的性能和用户体验
- 注意：多线程编程对于其他程序是不太友好的，占用大量CPU资源

#### 进程中的几种通信方式



#### 线程中的几种通信方式





### 启动一个线程是调用run()方法还是start()方法

- 区别
  - 调用start()方法会创建一个新的子线程并启动
  - run()方法只是Thread的一个普通方法的调用
- 启动一个线程是调用start()方法，使线程所代表的的虚拟处理机处于可运行状态，这意味着它可以由JVM调度并执行，这并不意味着线程就会立即运行
- run()方法是线程启动后要进行回调的(Callback)的方法

### 如何给run()方法传参

- 构造函数传参
- 成员变量传参
- 回调函数传参

### 如何实现处理线程的返回值

- 主线程等待法
- 使用Thread类的join（）阻塞当前线程以等待子线程处理完毕
- 通过Callable接口实现：通过FutureTask Or 线程池获取

### 线程的状态

- 新建（New）：创建后尚未启动的线程的状态，创建但是未调用Start（）方法
- 运行（Runnable）：包含Running和Ready，正在被CPU调用或者等待CPU的调用
- 无限期等待（Waiting）：不会被分配CPU执行时间，需要显式被唤醒
  - 没有设置Timeout参数的Object.wait()方法
  - 没有设置Timeout参数的Thread.join()方法
  - LockSupport.park()方法
- 限期等待（Timed Waiting)：在一定时间后由系统自动唤醒
- 阻塞（Blocked）:等待获取排他锁
- 结束（Terminated）:已终止线程的状态，线程已经结束执行

# 多线程

- ### 实现方式

  - 继承Thread
  - 实现Runnable接口
  - Thread和Runnable的关系
    - Thread是实现了Runnable接口的类，使得run支持多线程
    - 因类的单一继承原则，推荐多使用Runnable接口

- ### 同步实现方法

  - synchronized
  - wait：使一个线程处于等待状态，并释放所持有的对象的lock
  - notify：唤醒一个处于等待的线程，不确定唤醒哪一个，由JVM决定唤醒哪个线程，并且不是按照优先级

- ### 在Java中wait()和sleep()方法的不同？

  - 基本的差别
    - sleep是Thread类的方法，wait是Object类的方法
    - sleep()方法可以在任何地方使用
    - wait()方法只能在synchronized方法或synchronized块中使用，因为只有获得锁之后才能释放锁
  - 最主要的本质区别
    - Thread.sleep只会让出CPU，不会导致锁行为的改变，会一直持有锁
    - Object.wait不仅会让出CPU，还会释放已经占有的同步资源锁
    - wait通常被用于线程间的交互，sleep通常被用于暂停执行。


### notify和notifyAll的区别

用来唤醒线程

- notifyAll会让**所有**处于等待池的线程全部进入锁池去竞争获取锁的机会

- notify只会**随机**选取一个处于等待池中的线程进入锁池去竞争获取锁的机会

#### 锁池EntryList

假设线程A已经拥有了某个对象（不是类）的锁，而其他线程B、C想要调用这个对象的某个synchronized方法（或者块），由于B、C线程在进入对象的synchronized方法（或者块）之前必须先获得该对象的拥有权，而恰巧该对象的锁目前正在被线程A锁占用，此时B、C线程就会被阻塞，进入一个地方去等待锁的释放，这个地方便是该对象的锁池。

#### 等待池WaitSet

假设线程A调用了某个对象的wait()方法，线程A就会释放该对象的锁，同时线程A就进入到了该对象的等待池中，进入到等待池中的线程不会去竞争该对象的锁。

### yield函数

当调用Thread.yield()函数时，会给线程调度器一个当前线程愿意让出CPU使用暗示，但是线程调度器可能会忽视这个暗示，不会对锁有任何影响。

### 如何中断线程

##### 已经被抛弃的方法

通过调用stop()方法停止线程（不安全，还是可能引发线程不同步）

##### 目前使用的方法

调用interrupt()，通知线程应该中断了

1. 如果线程处于被阻塞状态，那么线程将立即退出被阻塞状态，并抛出一个InterruptedException异常
2. 如果线程处于正常活动状态，那么会将该线程的中断标志设置为true，被设置中断状态的线程将继续正常运行，不受影响。

需要被调度的线程配合中断

1. 在正常运行任务时，经常检查本线程的中断标志位，如果被设置了中断状态就自行停止线程
2. 如果线程处于正常活动状态，那么会将该线程的中断标志设置为true，被设置中断状态的线程将继续正常运行，不受影响。

```java
public class InterruptDemo {
    public static void main(String[] args) throws InterruptedException {
        Runnable interruptTask = new Runnable() {
            @Override
            public void run() {
                int i = 0;
                try {
                    while (!Thread.currentThread().isInterrupted()) {
                        Thread.sleep(100);
                        i++;
                        System.out.println(Thread.currentThread().getName() + "(" + Thread.currentThread().getState() + ") loop " + i);
                    }
                } catch (InterruptedException e) {
                    System.out.println(Thread.currentThread().getName() + "(" + Thread.currentThread().getState() + ")"+e.getMessage());
                }
            }
        };
        Thread t1 = new Thread(interruptTask, "t1");
        System.out.println(t1.getName() + "(" + t1.getState() + ") is new");

        t1.start();
        System.out.println(t1.getName() + "(" + t1.getState() + ") is started");

        Thread.sleep(300);
        t1.interrupt();
        System.out.println(t1.getName() + "(" + t1.getState() + ") is interrupted");

        Thread.sleep(300);
        System.out.println(t1.getName() + "(" + t1.getState() + ") is interrupted now");
    }
}

```

```
t1(NEW) is new
t1(RUNNABLE) is started
t1(RUNNABLE) loop 1
t1(RUNNABLE) loop 2
t1(RUNNABLE) is interrupted
t1(RUNNABLE)sleep interrupted
t1(TERMINATED) is interrupted now
```


