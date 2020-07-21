---
layout: post
title:  "重写equals还要重写hashcode"
date:   2020-07-18
excerpt: "Stick to note down what I'v learnt"
tag:
- Java 
- Coding
---

<center><H2><b>重写equals还要重写hashcode</b></H2></center><br>

参考：

> [为什么要重写hashcode和equals方法？你能说清楚了吗](<https://blog.csdn.net/Y0Q2T57s/article/details/107421148>)



主要是因为自定义变量在存入HashMap的时候会用到ha'shCode();

#### HashMap

在HashMap中如果要比较key是否相等，要同时使用这两个函数。

**比较过程：**

put的时候需要比较是否有相同的Key。

> <u>先求出Key的hashCode()，比较两个值是否相等，若相等再比较equals()，若相等则认为这两个Key是同一个。若hashCode()相等，但equals()不相等，则也认为他们不相等。</u>

+ 如果只重写equals()，则对于同一个实体（变量完全一样的对象），也无法获取相同的value，获得的可能是null。
+ 如果只重写hashCode()，则当比较equals()时，只是看这两个变量是不是同一个对象，代表同一实体的两个对象返回的是false

因此，如果需要将自定义的变量作为HashMap的key存入，则必须重写两个函数。同时从面向对象来看，如果你希望代表同一个实体的两个对象的hashCode()也是一样的，那么也必须重写hashCode();



**HashMap，比较的依据**

> hashCode()的规则
>
> - 两个对象相等，hashCode()一定相等。
>
> - 两个对象不等，hashCode()不一定不等。
>
> - hashCode()相等，两个对象不一定相同。
>
> - hashCode()不等，两个对象一定不相同。



#### 测试例子

```java
public class JavaEqual {
    public static void main(String[] args) {

        /*********************************
         * equals与hashcode测试
         *********************************/
        out("equals与hashcode测试");
        Student aStudent = new Student("me");
        Student bStudent = new Student("me");

        if (aStudent.equals(bStudent)) {
            System.out.println("aStudent == bStudent (using customed equal function)");
            System.out.println("aStudent.hashcode " + aStudent.hashCode());
            System.out.println("bStudent.hashcode " + bStudent.hashCode());
        }

        HashMap<Student, String> map = new HashMap<>(3);

        map.put(aStudent, "a");

        String getBstudent = map.get(bStudent);
        /* 为什么需要用bStudent来获取map中的数据呢，因为这两个数据都是一样的
        （或者说代表同一个现实对象中的实体），应该对应同一个实体，
        所以从map中取数据应该也是一样的。*/
        
        /* 如果不重写hashCode()，那么上面这句话会返回null。*/
        
        /* 如果重写了hashCode()，迫使两个对象hashCode相同，但是equals()不同，
           也无法获得出同一个对象。（原因：hashCode相等，不一定是同一个对象）;*/
        
        System.out.println("using bStudent get aStudent's value:" + getBstudent);
        outEnd();

        /**************** END **************/
    }
    public static void out(String message) {
        System.err.flush();
        System.out.flush();
        System.out.println("/*********************************\n" + " * " + message + "\n"
                + " *********************************/");
        System.err.flush();
        System.out.flush();
    }
    public static void outEnd() {
        System.err.flush();
        System.out.flush();
        System.out.println("/**************** END **************/\n\n");
        System.err.flush();
        System.out.flush();
    }
}
```



