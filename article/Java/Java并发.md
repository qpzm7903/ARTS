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

线程池的作用：
- 避免反复创建线程
- 池化，无需等待，提高响应速度
- 

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


## executor的生命周期
JVM只有所有（非守护）线程全部终止后才会退出。如果无法正确关闭executor，JVM将无法结束。

ExecutorService 扩展了 Executor
```java
public interface ExecutorService extends Executor{
	void shutdown();
	List<Runnable> shuwdownNow();
	boolean isShutdown();
	boolean isTerminated();
	boolean awaitTermination(long timtout, TimeUnit unit);
	...
}
```

shutdown 方法会平滑关闭，不接受新任务，等待所有已提交任务执行完成。





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



# 其他


## 线程怎么用？

第一步创建一个线程。

```java
Thread thread = new Thread();  
thread.run();  
thread.start();
```
那么这线程到底干啥？

所以要告诉它要做什么

所以Thread在初始化的时候提供了构造器，构造器接受一个叫 Runnable的接口。
```java
public Thread(Runnable target) {  
    this(null, target, "Thread-" + nextThreadNum(), 0);  
}
```

那么这个Runnable能干啥

```java
@FunctionalInterface  
public interface Runnable {  
    /**  
     * When an object implementing interface {@code Runnable} is used  
     * to create a thread, starting the thread causes the object's     * {@code run} method to be called in that separately executing  
     * thread.     * <p>  
     * The general contract of the method {@code run} is that it may  
     * take any action whatsoever.     *     * @see     java.lang.Thread#run()  
     */    public abstract void run();  
}
```


原来是个只有一个`run`方法的Interface。

那么事情简单了。

创建Thread的时候，传入一个Runnable的实现类，Thread会运行这个Runnable里面的run方法。

于是最简单的代码变成了下面这样。

```java
Thread thread = new Thread(new Runnable() {  
    @Override  
    public void run() {  
        System.out.println("hi, I am a new thread");  
    }  
});  
thread.run();  
thread.start();
```
那么 Thread 的`run` 和 `start` 方法有啥区别？

看一下 `run` 的文档和代码

```
/**  
 * If this thread was constructed using a separate * {@code Runnable} run object, then that  
 * {@code Runnable} object's {@code run} method is called;  
 * otherwise, this method does nothing and returns. * <p>  
 * Subclasses of {@code Thread} should override this method.  
 * * @see     #start()  
 * @see     #stop()  
 * @see     #Thread(ThreadGroup, Runnable, String)  
 */@Override  
public void run() {  
    if (target != null) {  
        target.run();  
    }  
}
```

所以它啥也没干，就直接调用了 `target.run`， `target` 就是创建Thread时时传入的一个`Runnable`对象。

那么 `start` 方法呢？

```java
/**  
 * Causes this thread to begin execution; the Java Virtual Machine * calls the {@code run} method of this thread.  
 * <p>  
 * The result is that two threads are running concurrently: the  
 * current thread (which returns from the call to the * {@code start} method) and the other thread (which executes its  
 * {@code run} method).  
 * <p>  
 * It is never legal to start a thread more than once.  
 * In particular, a thread may not be restarted once it has completed * execution. * * @throws     IllegalThreadStateException  if the thread was already started.  
 * @see        #run()  
 * @see        #stop()  
 */public synchronized void start() {  
    /**  
     * This method is not invoked for the main method thread or "system"     * group threads created/set up by the VM. Any new functionality added     * to this method in the future may have to also be added to the VM.     *     * A zero status value corresponds to state "NEW".     */    if (threadStatus != 0)  
        throw new IllegalThreadStateException();  
  
    /* Notify the group that this thread is about to be started  
     * so that it can be added to the group's list of threads     * and the group's unstarted count can be decremented. */    group.add(this);  
  
    boolean started = false;  
    try {  
        start0();  
        started = true;  
    } finally {  
        try {  
            if (!started) {  
                group.threadStartFailed(this);  
            }  
        } catch (Throwable ignore) {  
            /* do nothing. If start0 threw a Throwable then  
              it will be passed up the call stack */        }  
    }  
}
```


文档说的很清楚，`start()` 调用的时候，会有两个线程同步运行，一个是调用此`start`方法的线程，一个是新的线程（执行传入的`Runnable`对象）。并且还提到`start`方法不能重复执行。那么重复执行会抛出 `IllegalThreadStateException`;


那么如何观察是否是两个线程呢？
```java
System.out.println("current thread id is " + Thread.currentThread().getId());  
Thread thread = new Thread(new Runnable() {  
    @Override  
    public void run() {  
        System.out.println("current thread id is " + Thread.currentThread().getId());  
    }  
});  
thread.start();
```
通过`Thread`的工具方法获取当前线程的id，输出即可观察。
结果如下
```log
current thread id is 1
current thread id is 24
```


同时有个疑问  `start`方法会阻塞吗？

```java
public class ThreadDemo {  
    public static void main(String[] args) {  
        Thread thread = new Thread(new Runnable() {  
            @Override  
            public void run() {  
                try {  
                    Thread.sleep(1000);  
                } catch (InterruptedException e) {  
                    throw new RuntimeException(e);  
                }  
                printTimeAndThreadId("runnable ");  
            }  
        });  
        thread.start();  
        printTimeAndThreadId("main ");  
  
    }  
  
    private static void printTimeAndThreadId(String name) {  
        System.out.println(name + "thread id is " + Thread.currentThread().getId() +  " at " + System.currentTimeMillis());  
    }  
  
}
```

输出结果
```log
main thread id is 1 at 1662593396951
runnable thread id is 24 at 1662593397952
```
说明不会阻塞

## 如何从线程中获取返回值？

使用[callable](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/Callable.html)

先看看文档
> A task that returns a result and may throw an exception. Implementors define a single method with no arguments called `call`.

> The `Callable` interface is similar to [`Runnable`](https://docs.oracle.com/javase/8/docs/api/java/lang/Runnable.html "interface in java.lang"), in that both are designed for classes whose instances are potentially executed by another thread. A `Runnable`, however, does not return a result and cannot throw a checked exception.

能返回值，和Runnable很像。


Runnable可以给线程执行，那么Callable呢？Thread没有关于Callable的方法。回到文档，文档说可以

>https://docs.oracle.com/javase/tutorial/essential/concurrency/executors.html


