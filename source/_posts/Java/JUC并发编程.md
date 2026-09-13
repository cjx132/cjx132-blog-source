---
title: "JUC并发编程"
date: 2020-11-13T00:02:47+08:00
categories:
  - java
tags:
  - java基础
---

wait和sleep的区别

1.  来自不同的类
    
    wait==>Object
    
    sleep==>Thread
    
2.  关于锁的释放
    
    **wait会释放锁**，sleep不会释放锁
    
3.  使用范围不同
    
    **wait必须在同步代码块中使用**
    
    sleep可以在任何地方使用
    

**并发：多线程操作同一个资源类，把资源丢入线程**

> 代码举例
> 
> ```java
> package com.cjx.demo1;
> 
> import java.util.concurrent.locks.ReentrantLock;
> 
> /**
>  * @author changjx
>  * @date 2020/11/13 10:39
>  */
> 
> 
> public class MultiThreadForTicketLock  {
> 
> 
>     public static void main(String[] args) {
> 
>         //并发：多个线程操作同一个资源，把资源丢入线程
>         Ticket2 t = new Ticket2();
> 
>         new Thread(()->{ for (int i = 0; i < 40; i++) t.sale(); }, "A").start();
>         new Thread(()->{ for (int i = 0; i < 40; i++) t.sale(); }, "B").start();
>         new Thread(()->{ for (int i = 0; i < 40; i++) t.sale(); }, "C").start();
>     }
> }
> 
> //资源类
> class Ticket2 {
>     private int ticket = 50;
>     //一般设置成private final,也要保证锁的唯一性(写在资源类的属性位置,保证全局的唯一性，若写在方法中，将失效)
>     private final ReentrantLock lock = new ReentrantLock();
> 
>     public void sale() {
> 
>         lock.lock();
>         try {
>             if (ticket>0) {
>                 System.out.println(Thread.currentThread().getName() + "----->" + "卖出了第" + ticket-- + "张票，剩余："+ticket);
>             }
>         } catch (Exception e) {
>             e.printStackTrace();
>         } finally {
>             lock.unlock();
>         }
>     }
> }
> ```

# Lock锁（重点）

## 实现类

ReentrantLock （常用）， ReentrantReadWriteLock.ReadLock ， ReentrantReadWriteLock.WriteLock

## 构造方法

```java
public ReentrantLock() {
    sync = new NonfairSync();
}

public ReentrantLock(boolean fair) {
    sync = fair ? new FairSync() : new NonfairSync();
}
```

由构造方法可知，默认为非公平锁；

-   公平锁：按照先后顺序
-   非公平锁：交由CPU调度

## 使用

**并发：多线程操作同一个资源类，把资源丢入线程**

Lock一般设置成private final,也要保证锁的唯一性(**写在资源类的属性位置,保证全局的唯一性，若写在方法中，将失效；即写在和临界资源的同一位置)**

```java
//A为一个资源类
class A{
    //一般设置成private final,也要保证锁的唯一性(写在资源类的属性位置,保证全局的唯一性，若写在方法中，将失效)
    private final ReentrantLock lock=new ReentrantLock();
    
    
    public  void m() {
        lock.lock();//开启锁
        try{
            //保证线程安全的代码
        }finally {
            lock.unlock();//关闭锁
            //如果同步代码有异常，要将unlock()写入finally语句块
        }
    }
}
```

## Synchronized与Lock的区别

1.  Synchronized内置的Java关键字，Lock 是一个java类
2.  Synchronized 无法判断获取锁的状态，Lock可以判断是否获取到了锁
3.  Synchronized 会自动释放锁；lock必须要手动释放锁，如果不释放锁，死锁
4.  Synchronized 线程1 ( 获得锁，阻塞)、线程2(等待，傻傻的等) ；Lock锁就不一定会等待下去
5.  Synchronized 可重入锁，可以中断的，非公平；Lock ，可重入锁，可以判断锁，非公平(可以自己设置)
6.  Synchronized 适合锁少量的代码同步问题，Lock适合锁大的同步代码!

# 生产者消费者问题（同步问题，线程间的通信，涉及临界资源的访问，并发问题）

面试的：单例模式，排序算法，生产者和消费者，死锁

**生产者消费者问题：代码编写口诀**

> 1.  并发：多线程操作同一个资源类，把资源丢入线程
> 2.  同步方法的编写步骤：循环等待–>业务–>唤醒

**注意的问题：**

> 1.  如果使用if会造成**虚假唤醒**（**要使用while**）；等待应该总是要出现在循环中
> 2.  如果使用notify可能会造成死锁（**要使用notifyAll**）

```java
public class A {
    public static void main(String[] args) {
        Cache cache = new Cache();//资源对象

        //并发：多个线程操作同一资源，把资源放进线程
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    cache.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    cache.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    cache.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    cache.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"D").start();
    }
}

//步骤：循环等待-->业务-->唤醒
//资源类
class Cache{
    private int num=0;

    public synchronized void increment() throws InterruptedException {
        while (num!=0){//如果使用if会造成虚假唤醒
            this.wait();
        }
        num++;
        System.out.println(Thread.currentThread().getName()+"==>"+num);
        this.notifyAll();//如果使用notify可能会造成死锁
    }

    public synchronized void decrement() throws InterruptedException {
        while (num==0){
            this.wait();
        }
        num--;
        System.out.println(Thread.currentThread().getName()+"==>"+num);
        this.notifyAll();
    }
}
```

## 关于上例中虚假唤醒的一些思考

### 虚假唤醒何时出现：

1.  在上个例子中，使用if进行了条件判断
2.  当存在多个线程执行increment的方法

### 为什么产生虚假唤醒：

1.  首先要解释一下wait()方法，调用wait()方法时：（1）把当前线程阻塞；（2）还会释放锁；（3）这个阻塞的线程被唤醒后，从之前代码的位置继续执行
    
2.  这里以上述代码为例，假如我们把increment的方法中的while判断改为if，其他代码不变，在这个条件下进行分析。
    
    > 分析如下：（这里的锁都是同一把锁，也就是`Cache cache = new Cache()`这个唯一的cache对象）
    > 
    > 第一步：当一个线程走到increment方法中，发现这个if条件成立，调用wait方法，该线程被阻塞，同时释放锁
    > 
    > 第二步：另外一个也是执行increment方法的线程拿到锁，也进入这个if中发现条件成立，调用wait方法，这个线程也被阻塞，释放锁
    > 
    > 第三步：此时执行decrement方法的线程拿到锁，循环判断条件发现不成立，继续执行，调用notifyAll唤醒所有线程，上面两个阻塞的线程被唤醒
    > 
    > 第四步：第一个被阻塞的线程，回到之前的代码位置，由于是if，那么不用再判断了，接着执行下面的代码，num+1，调用notifyAll唤醒所有线程。
    > 
    > 第五步：假如这时，第二步中的线程拿到锁，回到之前的代码位置。由于是if，那么不用再判断了，接着执行下面的代码，num+1，这时num就变成了2，也就产生了错误（即上面所说的虚假唤醒）
    

### 总结：

-   在if块中使用wait方法，是非常危险的，因为一旦线程被唤醒，并得到锁，就不会再判断if条件，而执行if语句块外的代码，所以建议，凡是先要做条件判断，再wait的地方，都使用while循环来做。

## 使用JUC实现生产者，消费者

1.  synchronized==>lock
2.  wait==>await
3.  notify==>signalAll

```java
public class B {
    public static void main(String[] args) {
        Cache1 cache1 = new Cache1();//资源对象

        //并发：多个线程操作同一资源，把资源放进线程
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    cache1.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"A").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    cache1.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"B").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    cache1.increment();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"C").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                try {
                    cache1.decrement();
                } catch (InterruptedException e) {
                    e.printStackTrace();
                }
            }
        },"D").start();
    }
}

//步骤：循环等待-->业务-->唤醒
//资源类
class Cache1{
    private int num=0;
    ReentrantLock lock = new ReentrantLock();
    Condition condition = lock.newCondition();

    public void increment() throws InterruptedException {
        lock.lock();
        try {
            while (num>0){//如果使用if会造成虚假唤醒
                condition.await();
            }
            num++;
            System.out.println(Thread.currentThread().getName()+"==>"+num);
            condition.signalAll();//如果使用notify可能会造成死锁
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void decrement() throws InterruptedException {
        lock.lock();
        try {
            while (num<=0){
                condition.await();
            }
            num--;
            System.out.println(Thread.currentThread().getName()+"==>"+num);
            condition.signalAll();
        } catch (InterruptedException e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

## Condition精准通知和唤醒线程

```java
//Condition精准通知和唤醒线程
public class ConditionTest {
    public static void main(String[] args) {
        Data data = new Data();//资源对象

        //并发：多个线程操作同一资源，把资源放进线程
        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data.printA();
            }
        },"线程1").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data.printB();
            }
        },"线程2").start();

        new Thread(()->{
            for (int i = 0; i < 10; i++) {
                data.printC();
            }
        },"线程3").start();
    }
}

//步骤：循环等待-->业务-->唤醒
//资源类
class Data{
    ReentrantLock lock = new ReentrantLock();//锁的唯一性

    //1A 2B 3C
    Condition condition1 = lock.newCondition();
    Condition condition2 = lock.newCondition();
    Condition condition3 = lock.newCondition();
    private int num=1;//临界资源

    public void printA(){
        lock.lock();
        try {
            //循环判断
            while(num!=1){//如果使用if会造成虚假唤醒
                condition1.await();
            }
            //业务
            System.out.println(Thread.currentThread().getName() + "=>AAA");
            num=2;
            //唤醒2
            condition2.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void printB(){
        lock.lock();
        try {
            while(num!=2){
                condition2.await();
            }
            System.out.println(Thread.currentThread().getName() + "=>BBB");
            num=3;
            //唤醒3
            condition3.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }

    public void printC(){
        lock.lock();
        try {
            while(num!=3){
                condition3.await();
            }
            System.out.println(Thread.currentThread().getName() + "=>CCC");
            //唤醒1
            num=1;
            condition1.signal();
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            lock.unlock();
        }
    }
}
```

# 8锁现象

就是关于锁的8个问题

关于synchronized锁的一些思考：

1.  synchronized锁的是方法的调用者
2.  如果是同一把锁，任何时刻，谁先拿到谁执行，不能同时执行
3.  如果不是同一把锁，或者一个方法根本没有锁，那么就无所谓了，可以同时执行

关于代码举例，可以查看JUC文件夹下的lock8包

# 集合类不安全

## List不安全：

并发情况下，执行`ArrayList`的写方法会报`java.util.ConcurrentModificationException`并发修改异常

解决方法：使用以下几种方法替代

```java
/*
1.List<String> list = new Vector<>();
2.List<String> list = Collections.synchronizedList((new ArrayList<>()));
3.List<String> list = new CopyOnWriteArrayList<>();
*/
```

关于CopyOnWrite

CopyOnWrite写时复制

1.  写方法使用lock锁，相比vector使用synchronized性能更高
    
2.  可用于读写分离
    
    > -   读取方法不加锁，所有线程可以同时访问;
    > -   写入时有可能存在读取的线程正在读，为防止读取坏数据，先复制一份，在复制的一份上修改，之后再赋值回去  
    >     此时读取到的就是之前旧的数据
    
3.  缺点：1.复制导致内存消耗较大。2.读取的值是旧值
    

## Set不安全：

底层是hashMap，线程不安全的解决方法和思路和`ArrayList`相似

```java
/*
1.Set<String> set = new CopyOnWriteArraySet<>();
2.Set<String> set = Collections.synchronizedSet(new HashSet<>());
*/
```

## Map不安全：

```java
//默认等价于new HashMap<>(16,0.75)
//Map<String, String> map = new HashMap<>();
```

解决方法如下

```java
//        Map<String, String> map = new ConcurrentHashMap<>();
//        Map<String, String> map = Collections.synchronizedMap(new HashMap<>());
```

关于HashMap两篇非常详尽的解释：[原理1](https://blog.csdn.net/refuse_debug/article/details/104623902)，[原理2](https://blog.csdn.net/refuse_debug/article/details/104623908)

# Callable

与Runable相比

1.  可以抛出异常
2.  可以有返回值
3.  方法不同，run(),call()

使用：

```java
public class CallableTest {
    public static void main(String[] args) throws ExecutionException, InterruptedException {

        FutureTask<String> task = new FutureTask<>(() -> {
            System.out.println("call");//会打印几个call
            //耗时的操作
            return "hello";
        });//适配类，将Callable对象转为Runnable对象

        new Thread(task,"A").start();
        new Thread(task,"B").start();//结果会被缓存，效率高
        String s = task.get();//这个get方法可能会产生阻塞!把他放在最后
        //或者使用异步通信来处理
        System.out.println(s);

    }
}
```

细节：

1.  有缓存
2.  结果可能需要等待，会阻塞

# 常用的辅助类

## CountDownLatch

```java
//计数器
public class CountDownLatchDemo {
    public static void main(String[] args) throws InterruptedException {
        //总数是6
        CountDownLatch count = new CountDownLatch(6);

        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "Go Out");
                count.countDown();//数量-1
            },String.valueOf(i)).start();
        }

        count.await();//等待计数器归零，然后再向下执行

        //综上，CountDownLatch的作用就是等待上面6个线程全部执行完毕，再回到主线程继续执行

        System.out.println("关门");
    }
}
```

原理：

`count.countDown();`//数量-1

`count.await();`//等待计数器归零，然后再向下执行（计数器不为0就阻塞，为0会被唤醒）

每次有线程调用countDown()数量-1，假设计数器变为0，count.await();就会被唤醒，继续执行

## CyclicBarrier

```java
/*
集齐七颗龙珠召唤神龙
 */
public class CyclicBarrierDemo {
    public static void main(String[] args) {
        //召唤龙珠的线程
        CyclicBarrier cyclicBarrier = new CyclicBarrier(7,()->{
            System.out.println("召唤神龙成功");
        });

        for (int i = 0; i < 7; i++) {
            int temp=i;
            //lambda不能操作到i,作用域的原因
            new Thread(()->{
                System.out.println(Thread.currentThread().getName() + "收集" + temp + "个龙珠");
                try {
                    cyclicBarrier.await();//等待
                } catch (InterruptedException e) {
                    e.printStackTrace();
                } catch (BrokenBarrierException e) {
                    e.printStackTrace();
                }
            },String.valueOf(i)).start();
        }
    }
}
```

## Semaphore

```java
public class SemaphoreDemo {
    public static void main(String[] args) {
        Semaphore semaphore = new Semaphore(3);

        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                try {
                    semaphore.acquire();//p操作
                    System.out.println(Thread.currentThread().getName() + "抢到车位");
                    TimeUnit.SECONDS.sleep(2);
                    System.out.println(Thread.currentThread().getName() + "离开车位");

                } catch (InterruptedException e) {
                    e.printStackTrace();
                }finally {
                    semaphore.release();//v操作
                }

            },String.valueOf(i)).start();
        }
    }
}
```

原理：

`semaphore.acquire();//p操作` 值不为0，直接获得资源；为0时，该线程阻塞（方法会执行-1操作）

`semaphore.release();//v操作` 方法会执行+1操作，唤醒等待的线程

作用：多个共享资源互斥的使用！并发限流，控制最大的线程数

# 读写锁

ReadWriteLock

-   独占锁（写锁）一次只能被一个线程占有
-   共享锁（读锁）多个线程可以同时占有
-   读-读 可以共存
-   读-写 不能共存
-   写-写 不能共存

```java
public class ReadWriteLockDemo {
    public static void main(String[] args) {
        Cache cache = new Cache();

        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                cache.read();
            },String.valueOf(i)).start();
        }
        for (int i = 0; i < 6; i++) {
            new Thread(()->{
                cache.write();
            },String.valueOf(i)).start();
        }

    }
}

class Cache{
    //读写锁
    private final ReentrantReadWriteLock readWriteLock = new ReentrantReadWriteLock();

    //单线程写
    public void write(){
        readWriteLock.writeLock().lock();//写锁
        try {
            System.out.println(Thread.currentThread().getName() + "开始写入");
            System.out.println(Thread.currentThread().getName() + "写入OK");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.writeLock().unlock();
        }
    }

    //多线程读
    public void read(){
        readWriteLock.readLock().lock();//读锁
        try {
            System.out.println(Thread.currentThread().getName() + "开始读取");
            System.out.println(Thread.currentThread().getName() + "读取OK");
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            readWriteLock.readLock().unlock();
        }
    }
}
```

# 阻塞队列BlockingQueue

可看做是生产者消费者的实现

使用场景：多线程并发处理，线程池

何时阻塞（超时等待）：队列满时，加入操作阻塞（超时等待）；队列空时，读取阻塞（超时等待）

# 同步队列SynchronousQueue

相当于大小为1的阻塞队列

# 线程池（重点）

## 好处

1.  降低资源消耗
2.  提高响应速度
3.  方便管理

线程复用、可以控制最大并发数、管理线程

## 3大方法

是指创建线程池的三大方法

```java
ExecutorService threadPool = Executors.newSingleThreadExecutor();//单个线程
ExecutorService threadPool = Executors.newFixedThreadPool(5);//固定的线程池的大小
ExecutorService threadPool = Executors.newCachedThreadPool();//可伸缩的
```

## 7大参数

线程池的最大承载=阻塞队列的大小+最大线程池大小的设定

```java
public class Demo01 {
    public static void main(String[] args) {
//        ExecutorService threadPool = Executors.newSingleThreadExecutor();//单个线程
//        ExecutorService threadPool = Executors.newFixedThreadPool(5);//固定的线程池的大小
//        ExecutorService threadPool = Executors.newCachedThreadPool();//可伸缩的

        //创建线程池
        ExecutorService threadPool = new ThreadPoolExecutor(
                3,
                5,
                10,
                TimeUnit.SECONDS,
                new LinkedBlockingDeque<Runnable>(5),
                Executors.defaultThreadFactory(),
                new ThreadPoolExecutor.AbortPolicy()
        );

        try {
            for (int i = 0; i < 16; i++) {
                //线程启动
                threadPool.execute(() -> {
                    System.out.println(Thread.currentThread().getName());
                });
            }
        } catch (Exception e) {
            e.printStackTrace();
        } finally {
            //关闭线程池
            threadPool.shutdown();
        }

    }
}
```

## 4种拒绝策略

是指当线程池达到最大承载时，对于仍然请求的任务采取的拒绝策略

![4种拒绝策略](https://cjx132.github.io/picture-bed/img/image-20210202230556682.png)

```java
/*
new ThreadPoolExecutor.AbortPolicy()//不处理并抛出异常
new ThreadPoolExecutor.CallerRunsPolicy()//哪里来的去哪里
new ThreadPoolExecutor.DiscardPolicy()//不处理不抛出异常
new ThreadPoolExecutor.DiscardOldestPolicy()//尝试去和最早的竞争，不抛出异常
 */
```

## CPU密集型和IO密集型情况下，线程池中最大线程的定义

CPU密集型：设置为服务器的最大核数

IO密集型情况：设定为>十分耗IO的线程数量

## 阻塞队列

**无界队列**

队列大小无限制，常用的为无界的LinkedBlockingQueue，使用该队列做为阻塞队列时要尤其当心，当任务耗时较长时可能会导致大量新任务在队列中堆积最终导致OOM。阅读代码发现，Executors.newFixedThreadPool 采用就是 LinkedBlockingQueue，而楼主踩到的就是这个坑，当QPS很高，发送数据很大，大量的任务被添加到这个无界LinkedBlockingQueue 中，导致cpu和内存飙升服务器挂掉。

**有界队列**

常用的有两类，一类是遵循FIFO原则的队列如ArrayBlockingQueue，另一类是优先级队列如PriorityBlockingQueue。PriorityBlockingQueue中的优先级由任务的Comparator决定。  
使用有界队列时队列大小需和线程池大小互相配合，线程池较小有界队列较大时可减少内存消耗，降低cpu使用率和上下文切换，但是可能会限制系统吞吐量。

在我们的修复方案中，选择的就是这个类型的队列，虽然会有部分任务被丢失，但是我们线上是排序日志搜集任务，所以对部分对丢失是可以容忍的。

**同步移交队列**

如果不希望任务在队列中等待而是希望将任务直接移交给工作线程，可使用SynchronousQueue作为等待队列。SynchronousQueue不是一个真正的队列，而是一种线程之间移交的机制。要将一个元素放入SynchronousQueue中，必须有另一个线程正在等待接收这个元素。只有在使用无界线程池或者有饱和策略时才建议使用该队列。

# 四大函数式接口

函数式接口定义：接口中只有一个方法

## Function函数型接口

1.  有一个输入参数，有一个输出参数
2.  apply方法

```java
public class Demo01 {
    public static void main(String[] args) {
//        Function function = new Function<String,String>() {
//            @Override
//            public String apply(String str) {
//                return str;
//            }
//        };

        Function<String, String> function = (str) -> {
            return str;
        };
        System.out.println(function.apply("a"));
    }
}
```

## 断定型Predicate接口

1.  有一个输入参数，返回值只能是布尔值
    
2.  test方法
    

```java
public class Demo02 {
    public static void main(String[] args) {
//        Predicate<String> predicate = new Predicate<String>() {
//            @Override
//            public boolean test(String s) {
//                return s.isEmpty();
//            }
//        };

        Predicate<String> predicate = (str) -> {
            return str.isEmpty();
        };
        System.out.println(predicate.test(""));
    }
}
```

## 消费型Consumer接口

1.  只有输入没有返回值
2.  accept方法

```java
public class Demo03 {
    public static void main(String[] args) {
//        Consumer<String> consumer = new Consumer<String>() {
//            @Override
//            public void accept(String str) {
//                System.out.println(str);
//            }
//        };

        Consumer<String> consumer = (str) -> {
            System.out.println(str);
        };
        consumer.accept("as");
    }
}
```

## 供给型Supplier接口

1.  没有参数，只有返回值
2.  get方法

```java
public class Demo04 {
    public static void main(String[] args) {
//        Supplier <Integer>supplier = new Supplier<Integer>() {
//            @Override
//            public Integer get() {
//                System.out.println("a");
//                return 1024;
//            }
//        };

        Supplier<Integer> supplier = () -> {
            return 1024;
        };
        System.out.println(supplier.get());
    }
}
```

# Stream流式计算

Stream流计算体现了Java8全部的新特性

![image-20210203001125785](https://cjx132.github.io/picture-bed/img/image-20210203001125785.png)

# ForkJoin

并行执行任务，提高效率，大数据量

> 大任务拆分成小任务，多线程计算小任务，之后进行归并，分治的思想

工作窃取：维护一个双端队列

# 异步回调

Future设计的初衷：对将来的某个事件的结果进行建模

异步调用：CompletableFuture

> 异步执行
> 
> 成功回调
> 
> 失败回调

# JMM

1.  **请你谈谈你对Volatile的理解**
    
    Volatile是JVM提供的**轻量级的同步机制**
    
    1、保证可见性==>先介绍JMM，多线程操作时，一个线程对主存的修改可以使得其他线程可见
    
    2、不保证原子性==>num++不是原子操作，可以通过synchronized(悲观锁)或者**原子类（和操作系统相关，在内存中修改值–>讲CAS（乐观锁）–>讲自旋锁–>讲CAS缺点–>讲ABA问题**）保证原子性
    
    3、禁止指令重排==>内存屏障：CPU指令。作用：1、保证特定的操作的执行顺序（禁止指令重排）2、可以保证某些变量的内存可见性（保证可见性）
    
2.  什么是JMM
    
    JMM：java内存模型，不存在的东西，概念！约定！
    
3.  **关于JMM的一些同步的约定**
    
    1、线程解锁前，必须把共享变量立刻刷回主存
    
    2、线程加锁前，必须读取主存中的最新值到工作内存中
    
    3、加锁和解锁是同一把锁
    

![JMM](https://cjx132.github.io/picture-bed/img/image-20210203015625170.png)

**Java内存模型定义了8种操作来完成，虚拟机实现必须保证每一种操作都是原子的、不可再拆分的（double和long类型例外）。**

-   lock（锁定）：作用于主内存的变量，它把一个变量标识为一条线程独占的状态。
-   unlock（解锁）：作用于主内存的变量，它把一个处于锁定状态的变量释放出来，释放后的变量才可以被其他线程锁定。
-   read（读取）：作用于主内存的变量，它把一个变量的值从主内存传输到线程的工作内存中，以便随后的load动作使用。
-   load（载入）：作用于工作内存的变量，它把read操作从主内存中得到的变量值放入工作内存的变量副本中。
-   use（使用）：作用于工作内存的变量，它把工作内存中一个变量的值传递给执行引擎，每当虚拟机遇到一个需要使用到变量的值的字节码指令时将会执行这个操作。
-   assign（赋值）：作用于工作内存的变量，它把一个从执行引擎接收到的值赋给工作内存的变量，每当虚拟机遇到一个给变量赋值的字节码指令时执行这个操作。
-   store（存储）：作用于工作内存的变量，它把工作内存中一个变量的值传送到主内存中，以便随后的write操作使用。
-   write（写入）：作用于主内存的变量，它把store操作从工作内存中得到的变量的值放入主内存的变量中。

**Java内存模型还规定了在执行上述8中基本操作时必须满足如下规则。**

-   不允许read和load、store和write操作之一单独出现，即不允许一个变量从主内存读取了但工作内存不接受，或者从工作内存发起回写了但主内存不接受的情况出现。
-   不允许一个线程丢弃它的最近的assign操作，即变量在工作内存中改变了之后必须把该变化同步回主内存。
-   不允许一个线程无原因地（没有发生过任何assign操作）把数据从线程的工作内存同步回主内存。
-   一个新的变量只能在主内存中“诞生”，不允许在工作内存中直接使用一个未被初始化（load或assign）的变量，换句话说，就是对一个变量实施use、store操作之前，必须先执行过了assign和load操作。
-   一个变量在同一时刻只允许一条线程对其进行lock操作，但lock操作可以被同一条线程重复执行多次，多次执行lock后，只有执行相同次数的unlock操作，变量才会被解锁。
-   如果对一个变量执行lock操作，那将会清空工作内存中此变量的值，在执行引擎使用这个变量前，需要重新执行load或assign操作初始化变量的值。
-   如果一个变量事先没有被lock操作锁定，那就不允许对它执行unlock操作，也不允许去unlock一个被其他线程锁定住的变量。
-   对一个变量执行unlock操作之前，必须先把此变量同步回主内存中（执行store、write操作）。

4.  指令重排
    
    什么是指令重排：你写的程序，计算机并不是按照你写的那样去执行的
    
    源代码–>编译器优化的重排–>指令并行也可能会重排–>内存系统也会重排–>执行
    
    处理器在进行指令重排的时候，考虑：数据之间的依赖性
    

# 彻底玩转单例模式

## 饿汉式

可能会浪费空间

```java
public class Hungry {

    private Hungry() {

    }

    private static final Hungry HUNGRY = new Hungry();

    public static Hungry getInstance() {
        return HUNGRY;
    }
}
```

## DCL懒汉式

**外面的判断用于提高性能；里面的判断用于避免违背单例**

**volatile在这里用于防止指令重排**：lazyMan = new LazyMan();语句不是原子性操作，通过反编译可以看出。

> 1.  分配内存空间
> 2.  执行构造方法，初始化对象
> 3.  把这个对象指向这个空间
> 
> 指令重排可能存在1,3,2的顺序，此时第二个线程发现不为空，直接将未初始化的对象返回

仍然有可能使用序列化反序列化以及反射破坏单例模式

```java
public class LazyMan {

    private LazyMan() {

    }

    private volatile static LazyMan lazyMan;

    public static LazyMan getInstance() {
        if (lazyMan == null) {
            synchronized (LazyMan.class) {
                if (lazyMan == null) {
                    lazyMan = new LazyMan();
                }
            }
        }

        return lazyMan;
    }
}
```

## 枚举类

最优雅的实现方式是使用枚举，其代码精简，没有线程安全问题，且 Enum 类内部防止反射和反序列化时破坏单例。

```java
public enum Enum {
    INSTANCE;
}
```

# CAS

CAS 的思想很简单：**三个参数，一个当前内存值 V、旧的预期值 A、即将更新的值 B，当且仅当预期值 A 和内存值 V 相同时，将内存值修改为 B 并返回 true，否则什么都不做，并返回 false**。

源码分析：

```java
public final int getAndAddInt(Object var1, long var2, int var4) {
  int var5;
  do {
    var5 = this.getIntVolatile(var1, var2);
  } while(!this.compareAndSwapInt(var1, var2, var5, var5 + var4));// 自旋
  return var5;
}
```

通过var1, var2可以取出内存中的值，var5是期望值，var5 + var4是更新值

那么 CAS 核心重点来了，compareAndSwapInt 就是实现 CAS 的核心方法，其原理是如果从var1, var2取出的 value 值和 var5 相等，就证明没有其他线程改变过这个变量，那么就把 value 值更新为 `var5 + var4`，其中 var4 是更新的增量值；反之，那么 CAS 就一直采用自旋的方式继续进行操作（其实就是个 while 循环），这一步也是一个原子操作。

**举例：**

1.  设定 AtomicInteger 的 value 原始值为 A，从 Java 内存模型得知，线程 1 和线程 2 各自持有一份 value 的副本，值都是 A。
2.  线程 1 通过`getIntVolatile(var1, var2)`拿到 value 值 A，这时线程 1 被挂起。
3.  线程 2 也通过`getIntVolatile(var1, var2)`方法获取到 value 值 A，并执行`compareAndSwapInt`方法比较内存值也为 A，成功修改内存值为 B。
4.  这时线程 1 恢复执行`compareAndSwapInt`方法比较，发现自己手里的值 A 和内存的值 B 不一致，说明该值已经被其它线程提前修改过了。
5.  线程 1 重新执行`getIntVolatile(var1, var2)`再次获取 value 值，因为变量 value 被 volatile 修饰，所以其它线程对它的修改，线程 A 总是能够看到，线程A继续执行`compareAndSwapInt`进行比较替换，直到成功。

## CAS的缺点

1.  自旋问题
    
    > 自旋锁，是一种乐观锁机制，一般来说都会给一个限定的自选次数，防止进入死循环。
    > 
    > 自旋锁的优点是不需要休眠当前线程，因为自旋锁使用者一般保持锁时间非常短，因此选择自旋而不是休眠当前线程是提高并发性能的关键点，这是因为减少了很多不必要的线程上下文切换开销。
    > 
    > 但是，如果 CAS 一直操作不成功，会造成长时间原地自旋，会给 CPU 带来非常大的执行开销。
    
2.  只能保证一个共享变量的原子操作。
    
3.  ABA问题。
    

### ABA问题

-   含义解释
    
    > 例如线程 1 从内存位置 V 取出 A，这时候线程 2 也从内存位置 V 取出 A，此时线程 1 处于挂起状态，线程 2 将位置 V 的值改成 B，最后再改成 A，这时候线程 1 再执行，发现位置 V 的值没有变化，尽管线程 1 也更改成功了，但是不代表这个过程就是没有问题的。
    
-   举例分析
    

> 现有一个用单向链表实现的栈，栈顶元素为 A，A.next 为 B，期望用 CAS 将栈顶替换成 B。有线程 1 获取了元素 A，此时线程 1 被挂起，线程 2 也获取了元素 A，并将 A、B 出栈，再 push D、C、A，这时线程 1 恢复执行 CAS，因为此时栈顶元素依然为 A，线程 1 执行成功，栈顶元素变成了 B，但 B.next 为 null，这就会导致 C、D 被丢掉了。

-   解决方法
    
    > 通常，我们的乐观锁实现中都会带一个 version 字段来记录更改的版本，避免并发操作带来的问题。在 Java 中，AtomicStampedReference 也实现了这个处理方式。如果要更改内存中的值，不但要值相同，还要版本号相同。
    

# 各种锁

## 公平锁、非公平锁

公平锁：先来后到。也叫作非抢占式

公平锁：CPU调度，可以插队（默认都是非公平）也叫作抢占式

## 可重入锁（递归锁）

即表示可重新反复进入的锁，但仅限于当前线程

拿到外面的锁之后，可以自动获得里面的锁

## 自旋锁

CAS

## 死锁

1.  **死锁代码（重要）**

```java
package com.cjx.deadlock;

import java.util.concurrent.TimeUnit;

/**
 * @author changjx
 * @date 2021/2/4 2:06
 */
public class Demo01 {
    public static void main(String[] args) {
        String lockA = "lockA";
        String lockB = "lockB";

        new Thread(new MyThread(lockA, lockB)).start();
        new Thread(new MyThread(lockB, lockA)).start();
    }
}

class MyThread implements Runnable {

    private String lockA;
    private String lockB;

    public MyThread(String lockA, String lockB) {
        this.lockA = lockA;
        this.lockB = lockB;
    }

    @Override
    public void run() {
        synchronized (lockA) {
            System.out.println(Thread.currentThread().getName() + "lock:" + lockA + "=>get" + lockB);

            try {
                TimeUnit.SECONDS.sleep(2);
            } catch (InterruptedException e) {
                e.printStackTrace();
            }

            synchronized (lockB) {
                System.out.println(Thread.currentThread().getName() + "lock:" + lockB + "=>get" + lockA);
            }
        }
    }
}
```

2.  死锁排查
    
    jps -l定位进程号
    
    使用jstack 进程号找到死锁问题
    

# 面试代码题

1.  手写生产者消费者
    
    ```java
    package test_20210327;
    
    import java.util.concurrent.locks.Condition;
    import java.util.concurrent.locks.ReentrantLock;
    
    /**
     * @author changjx
     * @date 2021/4/6 17:22
     */
    public class TestD {
        public static void main(String[] args) {
            Cache cache = new Cache(3);
            for (int i = 0; i < 10; i++) {
                new Thread(() -> {
                    try {
                        cache.inc();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }, "A").start();
            }
    
            for (int i = 0; i < 20; i++) {
                new Thread(() -> {
                    try {
                        cache.dec();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }, "B").start();
            }
            for (int i = 0; i < 10; i++) {
                new Thread(() -> {
                    try {
                        cache.inc();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }, "A").start();
            }
        }
    }
    
    class Cache {
    
        private final ReentrantLock reentrantLock = new ReentrantLock();
        Condition condition = reentrantLock.newCondition();
        private int num = 0;
        private int cap = 0;//默认cache容量
    
        Cache(int cap) {
            this.cap = cap;
        }
    
        public void inc() throws InterruptedException {
    
            reentrantLock.lock();
            while (num == cap) {
                condition.await();
            }
            num++;
            System.out.println(Thread.currentThread().getName() + "生产，现存产品：" + num);
            condition.signalAll();
            reentrantLock.unlock();
        }
    
        public void dec() throws InterruptedException {
            reentrantLock.lock();
            while (num == 0) {
                condition.await();
            }
            num--;
            System.out.println(Thread.currentThread().getName() + "消费：剩余产品：" + num);
            condition.signalAll();
            reentrantLock.unlock();
        }
    }
    ```
    ```java
    package test_20210327;
    
    import java.util.concurrent.locks.Condition;
    import java.util.concurrent.locks.ReentrantLock;
    
    /**
     * @author changjx
     * @date 2021/4/6 17:22
     */
    public class TestD {
        public static void main(String[] args) {
            Cache cache = new Cache(3);
            for (int i = 0; i < 10; i++) {
                new Thread(() -> {
                    try {
                        cache.inc();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }, "A").start();
            }
    
            for (int i = 0; i < 20; i++) {
                new Thread(() -> {
                    try {
                        cache.dec();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }, "B").start();
            }
            for (int i = 0; i < 10; i++) {
                new Thread(() -> {
                    try {
                        cache.inc();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }, "A").start();
            }
        }
    }
    
    class Cache {
    
        private int num = 0;
        private int cap = 0;//默认cache容量
    
        Cache(int cap) {
            this.cap = cap;
        }
    
        public synchronized void inc() throws InterruptedException {
    
            while (num == cap) {
                this.wait();
            }
            num++;
            System.out.println(Thread.currentThread().getName() + "生产，现存产品：" + num);
            this.notifyAll();
        }
    
        public synchronized void dec() throws InterruptedException {
            while (num == 0) {
                this.wait();
            }
            num--;
            System.out.println(Thread.currentThread().getName() + "消费：剩余产品：" + num);
            this.notifyAll();
        }
    }
    ```
    
2.  新建的两个线程执行完之后执行main线程
    
    ```java
    package test_20210327;
    
    import java.util.concurrent.CountDownLatch;
    
    
    /**
     * @author changjx
     * @date 2021/4/4 14:51
     */
    public class testC {
        public static void main(String[] args) throws InterruptedException {
    
            CountDownLatch countDownLatch = new CountDownLatch(2);
    
    
            Thread t1 = new Thread(() -> {
                System.out.println("a");
    
                countDownLatch.countDown();
            });
    
            Thread t2 = new Thread(() -> {
                System.out.println("b");
                countDownLatch.countDown();
    
            });
    
            t1.start();
            t2.start();
    
    
            countDownLatch.await();
            System.out.println("s");
        }
    
    }
    ```
    
    3.  两个线程分别打印 1- 100，A 打印偶数， B打印奇数。
    
    ```java
    package test_20210327;
    
    /**
     * @author changjx
     * @date 2021/4/6 18:09
     */
    public class TestE {
        public static void main(String[] args) {
            Print print = new Print(100);
            new Thread(() -> {
                try {
                    while (print.printEven()) ;
                } catch (InterruptedException e) {
    
                }
            }).start();
    
            new Thread(() -> {
                try {
                    while (print.printOld()) ;
                } catch (InterruptedException e) {
    
    
                }
            }).start();
        }
    
    }
    
    class Print {
        int i = 1;
        int max = 0;
    
        public Print(int max) {
            this.max = max;
        }
    
        synchronized boolean printOld() throws InterruptedException {
            if (i >= max) return false;
            if ((i & 1) == 0) this.wait();
            System.out.println(("printOld" + " " + i++));
            this.notify();
            return true;
        }
    
        synchronized boolean printEven() throws InterruptedException {
            if (i >= max) return false;
            if ((i & 1) == 1) this.wait();
            System.out.println(("printEven" + " " + i++));
            this.notify();
            return true;
        }
    }
    ```
