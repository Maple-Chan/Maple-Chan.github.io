---
layout: post
title:  "Java线程为什么不能重复调用start()"
date:   2020-08-06
excerpt: "Stick to note down what I'v learnt"
tag:
- Java 
---

<center><H2><b>Java线程不能重复调用start()</b></H2></center><br>



### 线程的状态

+ NEW

  线程被创建，但是还没有调用start方法。

+ RUNNABLE

  线程正在运行或者，在**就绪队列**当中等待OS分配CPU片段。

+ BLOCKED

  阻塞状态，等待获得锁。例如，线程试图通过synchronized去获取某个锁，但是其他线程已经独占了，那么当前线程就会处于阻塞状态。

+ WAITING

  等待。线程调用对象的wait方法，或者Thread的join方法而没有运行。例如生产者消费者模式下，需要调用wait()等待生产者线程notify()。

+ TIMED_WAITING

  计时等待，其进入条件和等待状态类似，但是调用的是存在超时条件的方法，比如wait或join等方法的指定超时版本，比如调用sleep方法。

+ TERMINATED

  结束状态。

[Java线程的6种状态及切换(透彻讲解)](https://blog.csdn.net/qq_22771739/article/details/82529874)



> sleep方法，暂停当前线程，把cpu片段让出给其他线程，减缓当前线程的执行。
>
> 但是如果当前线程占有了锁，线程不会释放锁。
>
> sleep过后，不是直接到了运行状态，而是到就绪（可运行）状态。



### 为什么不能重复调用start()

同一个线程对象不能重复调用start()。

调用一次start()之后进入可运行状态，当我们再次调用start方法的时候由于不是NEW这个状态就会抛出**IllegalThreadStateException**这个异常了。

![1596882177407](https://blog.maplestory.work/images/post_image/2020-08-06-Java线程不能重复Start.assets/1596882177407.png)



```java
public class RunTest {

    public static void main(String[] args) {
        Thread thread = new Thread(() -> {
            System.out.println("I'm in the thread!");
        });

        thread.start();
        System.out.println(thread.getState());

        thread.run();

        System.out.println(thread.getState());
        thread.start();
    }

}
/*
输出：
RUNNABLE
I'm in the thread!
I'm in the thread!
TERMINATED
Exception in thread "main" java.lang.IllegalThreadStateException
        at java.base/java.lang.Thread.start(Thread.java:794)
        at javatest.threadpool.RunTest.main(RunTest.java:26)
*/
```

但是一个线程对象可以调用多次run，因为他只是一个对象调用一个方法。但是run()方法并没有启动一个线程，只是在调用方法的线程中执行任务。事实上，应当调用thread.start()方法，因为这会创建一个执行run方法的新线程。

### 线程状态转换

##### JavaThreadState 和 JVMTIThreadState 转换

![1596883974952](https://blog.maplestory.work/images/post_image/2020-08-06-Java线程不能重复Start.assets/1596883974952.png)