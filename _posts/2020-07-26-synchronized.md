---
layout: post
title:  "synchronized"
date:   2020-07-26
excerpt: "Stick to note down what I'v learnt"
tag:
- Java
---

<center><H2><b>synchronized</b></H2></center><br>

参考：

> 文章出处于：[【Java并发编程之深入理解】Synchronized的使用](https://blog.csdn.net/zjy15203167987/article/details/82531772)
>
> 以下内容都经过实验。

​	在并发编程中存在线程安全问题，主要原因有：1.存在共享数据 2.多线程共同操作共享数据。关键字synchronized可以保证在同一时刻，只有一个线程可以执行某个方法或某个代码块，同时synchronized可以保证一个线程的变化可见（可见性），即可以代替volatile。

​	synchronized可以保证方法或者代码块在运行时，同一时刻只有一个方法可以进入到临界区，同时它还可以保证共享变量的内存可见性。

​	可以用于解决Java并发问题，可以确保线程互斥的访问同步代码。

### **synchronized的使用**

Java中每个对象都可以作为锁，这是synchronized实现同步的基础。

使用方法：

> 1. 普通同步方法：锁是当前实例对象。当进入该对象的一个同步方法，则不能同时再进入该对象的另一个同步方法，可以进入非同步方法。
> 2. 静态同步方法：锁是当前类，即class对象，进入同步代码前需要获得当前类锁。
> 3. 同步方法块：所示括号里的对象，对给定对象加锁，进入同步代码块之前要获得该对象的对象锁。

### **实践**


##### **1. 多个线程访问同一个对象的同一个方法**


```java
/**
 * @description: 该类用于synchronized关键词测试
 * @modifyContent:
 * @author: Maple Chan
 * @date: 2020-07-26 21:42:24
 * @version: 0.0.1
 */
public class JavaSynchronizedTest implements Runnable {
    /**
     * 共享资源
     */
    static int i = 0;
    /**
     * synchronized 修饰实例方法
     */
    public synchronized void increaseSync() {
        i++;
    }
    public void increase() {
        i++;
    }
    @Override
    public void run() {
        for (int j = 0; j < 10000; j++) {
            // increase();
            increaseSync();
        }
    }
    public static void main(String[] args) throws InterruptedException {
        JavaSynchronizedTest test = new JavaSynchronizedTest();
        Thread t1 = new Thread(test);
        Thread t2 = new Thread(test);
        t1.start();
        t2.start();
        t1.join();
        t2.join();
        // 等待两个线程执行完，在执行下面输出
        
        System.out.println(i);
    }
}
/*
    如果调用的是increaseSync(); 输出20000
    如果调用的是increase(); 输出不确定，12317（12927），比20000小
*/
```

##### **2. 一个线程获取了该对象的锁之后，其他线程来访问其他synchronized实例方法现象**

一个获取了对象锁后，其他线程就需要等该线程释放该对象锁之后，再由其他线程调用同步方法。

```java
package javatest;

/**
 * @description: 该类用于synchronized关键词测试
 * @modifyContent:
 * @author: Maple Chan
 * @date: 2020-07-26 21:42:24
 * @version: 0.0.1
 */
public class JavaSynchronizedTest implements Runnable {

    public synchronized void methodSync1() {
        System.out.println("Method 1 start");
        try {
            System.out.println("Method 1 execute");
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Method 1 end");
    }

    public synchronized void methodSync2() {
        System.out.println("Method 2 start");
        try {
            System.out.println("Method 2 execute");
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Method 2 end");
    }

    public static void main(String[] args) throws InterruptedException {
        new Thread(test::methodSync1).start();
        new Thread(test::methodSync2).start();
    }
}
/*
输出：
Method 1 start
Method 1 execute
Method 1 end
Method 2 start
Method 2 execute
Method 2 end
*/

```

##### **3.一个线程获取了该对象的锁之后，其他线程来访问其他非synchronized实例方法现象**

一个线程获取对象所进入同步方法后，依旧可以直接调用非同步方法，不需要锁。

```java
/**
 * @description: 该类用于synchronized关键词测试
 * @modifyContent:
 * @author: Maple Chan
 * @date: 2020-07-26 21:42:24
 * @version: 0.0.1
 */
public class JavaSynchronizedTest implements Runnable {
    public synchronized void methodSync1() {
        System.out.println("Method 1 start");
        try {
            System.out.println("Method 1 execute");
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Method 1 end");
    }
    public synchronized void methodSync2() {
        System.out.println("Method 2 start");
        try {
            System.out.println("Method 2 execute");
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Method 2 end");
    }
    public synchronized void methodSync3() {
        System.out.println("Method 3 start");
        try {
            System.out.println("Method 3 execute");
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Method 3 end");
    }
    @Override
    public void run() {
        for (int j = 0; j < 10000; j++) {
            increase();
            // increaseSync();
        }
    }
    public static void main(String[] args) throws InterruptedException {
        JavaSynchronizedTest test = new JavaSynchronizedTest();
        // ---------------------
        new Thread(test::methodSync1).start();
        new Thread(test::methodSync2).start();
        // --------------------
        new Thread(test::methodSync1).start();
        new Thread(test::methodSync3).start();
    }
}
/*
// 可以的出，2必须等1执行完毕才能执行，而3可以不用管1执行到哪儿直接进入执行非同步方法。
Method 1 start
Method 1 execute
Method 3 start
Method 3 execute
Method 3 end
Method 1 end
Method 1 start
Method 1 execute
Method 1 end
Method 2 start
Method 2 execute
Method 2 end
*/



```

##### **4.当多个线程作用于不同的对象**

不同对象的同步方法，互不影响，因为他们各自的对象锁不一样

```java
package javatest;

/**
 * @description: 该类用于synchronized关键词测试
 * @modifyContent:
 * @author: Maple Chan
 * @date: 2020-07-26 21:42:24
 * @version: 0.0.1
 */
public class JavaSynchronizedTest implements Runnable {

    public synchronized void methodSync1() {
        System.out.println("Method 1 start");
        try {
            System.out.println("Method 1 execute");
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Method 1 end");
    }

    public synchronized void methodSync2() {
        System.out.println("Method 2 start");
        try {
            System.out.println("Method 2 execute");
            Thread.sleep(1000);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
        System.out.println("Method 2 end");
    }

    public static void main(String[] args) throws InterruptedException {
        JavaSynchronizedTest test = new JavaSynchronizedTest()
        JavaSynchronizedTest test2 = new JavaSynchronizedTest();
        new Thread(test::methodSync1).start();
        new Thread(test2::methodSync2).start();
    }
}

/*
输出：

Method 1 start
Method 1 execute
Method 2 start
Method 2 execute
Method 2 end
Method 1 end
*/
```

##### **5.synchronized作用于静态方法**

静态方法的锁是类锁，一个线程进入静态方法之后，另一个线程需要等待该线程执行完毕才可以进入。

```java
package javatest;

/**
 * @description: 该类用于synchronized关键词测试
 * @modifyContent:
 * @author: Maple Chan
 * @date: 2020-07-26 21:42:24
 * @version: 0.0.1
 */
public class JavaSynchronizedTest implements Runnable {
    /**
     * 共享资源
     */
    static int i = 0;
    /**
     * synchronized 修饰实例方法
     */
    public synchronized void increaseSync() {
        i++;
    }
    public void increase() {
        i++;
    }
    public static synchronized void increaseStatic(){
        i++;
    }
    @Override
    public void run() {
        for (int j = 0; j < 10000; j++) {
            // increase();
            // increaseSync();
            increaseStatic();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        JavaSynchronizedTest test = new JavaSynchronizedTest();
        JavaSynchronizedTest test2 = new JavaSynchronizedTest();
        Thread t1 = new Thread(test);
        Thread t2 = new Thread(test2);
        t1.start();
        t2.start();
        // t1.join();
        // t2.join();
        // System.out.println(i);

        // 等待2s，等待两个线程执行完
        Thread.sleep(2000);
        System.out.println(i);
    }
}
/*
两个线程实例化两个不同的对象，但是访问的方法是静态的，两个线程发生了互斥（即一个线程访问，另一个线程只能等着），因为静态方法是依附于类而不是对象的，当synchronized修饰静态方法时，锁是class对象。

输出：
20000
*/
```

##### **6. synchronized作用于同步代码块**

在某些情况下，我们编写的方法体可能比较大，同时存在一些比较耗时的操作，而需要同步的代码又只有一小部分，如果直接对整个方法进行同步操作，可能会得不偿失。此时我们可以使用同步代码块的方式对需要同步的代码进行包裹，这样就无需对整个方法进行同步操作了。

```java

/**
 * @description: 该类用于synchronized关键词测试
 * @modifyContent:
 * @author: Maple Chan
 * @date: 2020-07-26 21:42:24
 * @version: 0.0.1
 */
public class JavaSynchronizedTest implements Runnable {
    /**
     * 共享资源
     */
    static int i = 0;
    public void increase() {
        i++;
    }

    @Override
    public void run() {

        synchronized (lock) {
            for (int j = 0; j < 10000; j++) {
                increase();
                // increaseSync();
                // increaseStatic();
            }
        }

    }
	public static void main(String[] args) throws InterruptedException {

        JavaSynchronizedTest test2 = new JavaSynchronizedTest();
        Thread tt1 = new Thread(test2);
        Thread tt2 = new Thread(test2);
        tt1.start();
        tt2.start();
        tt1.join();
        tt2.join();

        System.out.println(i);
    }

}
/*
increase()是非同步方法，加了同步代码块之后，两个线程能同步执行

输出：
20000

*/
```





