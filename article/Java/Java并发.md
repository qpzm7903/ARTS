# 参考

Java并发编程实战

以及配套参考代码https://jcip.net/listings.html

​      



Java并发编程实战

## 基础知识

- - 为什么

  - - 怎么做

    - 原子性

    - 可见性

    - 线程封闭

    - - 栈封闭

      - - 基础类型的局部变量就是这样

      - threadLocal

      - - 线程和栈是什么关系

    - 不变性

    - 安全发布

    - - 一个对象的构造过程发生什么

      - - Object的构造
        - 子类的构造

      - final        filed 有什么用

      - 如何安全发布一个对象

      - - 静态初始化函数中初始化一个对象引用

        - 将对象引用保存到volatile类型的域或者AtomicReference对象中

        - 将对象引用保存到某个正确构造对象的final类型域中

        - 将对象引用保存到一个由锁保护的域中

## 对象的组合

- 将不安全、或则线程安全的对象组合得到一个线程安全的对象
      - 要考虑所有的状态

## 第五章 基础构建模块

          委托是最有效的策略

  - - 同步容器类

    - - vector
      - hashtable

    - 并发容器类

    - - concurrentHashMap

      - - 分段锁
        - size是个估计值
        - 客户端不需要额外加锁
        - 如果需要加锁进行增加功能，那么用         ConcurrentMap

      - CopyOnWriteArrayList

      - - 代替同步list

        - 每次修改会创建并发布一个新的副本

        - - 有性能损耗
          - 避免并发修改的问题

        - 迭代多余修改，才适合使用

      - 阻塞队列

      - - blockingQueue

        - - LinkedBlockingQueue
          - ArrayBlockingQueue
          - PriorityBlockingQueue

        - 双端队列

        - - Deque
          - BlockingDeque

    - 阻塞方法与中断方法

    - 同步工具类

    - - 阻塞队列是一种

      - 闭锁 

      - - 一扇门，门关，任何线程都过不去。门开，都可以过去，门开以后不能关
- 可以确保所有活动都完成后继续执行
        - CountDownLatch
- - 一种灵活的闭锁实现
          - 有一个计数器，非零关门，等于零开门
- FutureTask



## 6、任务执行

Executor框架

```java
public interface Executor {
    void execute(Runnable command);
}
```

线程池

```java
public interface ExecutorService extends Executor {

}
```

### Executors提供了许多静态工厂方法来创建线程池

1、newCachedThreadPool

创建一个可缓存的线程池，若线程数超过处理所需，缓存一段时间后会回收，若线程数不够，则新建线程。

2、newFixedThreadPool

创建一个固定大小的线程池，可控制并发的线程数，超出的线程会在队列中等待。

3、newScheduledThreadPool

创建一个周期性的线程池，支持定时及周期性执行任务。

4、newSingleThreadExecutor

创建一个单线程的线程池，可保证所有任务按照指定顺序(FIFO, LIFO, 优先级)执行。



### 根据**ThreadPoolExecutor**自定义创建线程池

```java
public class ThreadPoolExecutor extends AbstractExecutorService {
	public ThreadPoolExecutor(int corePoolSize,
                              int maximumPoolSize,
                              long keepAliveTime,
                              TimeUnit unit,
                              BlockingQueue<Runnable> workQueue) {
        this(corePoolSize, maximumPoolSize, keepAliveTime, unit, workQueue,
             Executors.defaultThreadFactory(), defaultHandler);
    }
    
...
}
```

coorPoolSize:线程池的基本大小，没有任务执行时线程池的大小，只有工作队列满的情况下才会超出这个线程的数量

maximumPoolSize：最大大小，可同时伙同的线程数量的上限

maximumPoolSize：存活时间，线程数量超过基本大小的时候，超过存活时间会被标记回收

workQueue：

### Executors 和 ThreadPoolExecutor创建用哪种？

阿里巴巴Java手册里面建议用ThreadPoolExecutor，这能让读代码的人明确知道线程池的运行规则，避免资源耗尽。





## 8、线程池的使用

    - - 线程饥饿死锁
      - 线程池的大小
      - 配置ThreadPoolExecutor
    
    - 9、图形用户界面应用程序
    
    -  

  - 结构化并发应用程序
  - 活跃性、性能、测试

java8的stream提供了并行流，但是一般不轻易使用，因为里面的线程池是全局的一个线程池，如果对其大量使用并进行IO操作，那么容易造成阻塞。



## 10、避免活跃性危险

    - 1、死锁
    
      - 锁顺序死锁
    
         - 不同的线程获取不同的几个锁，顺序应该保持一致
    
        - 资源死锁
    
        - -  

## 高级主题