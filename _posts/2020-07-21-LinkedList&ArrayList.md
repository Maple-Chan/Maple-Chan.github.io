---
layout: post
title:  "LinkedList&ArrayList"
date:   2020-07-21
excerpt: "Stick to note down what I'v learnt"
tag:
- Java
-Coding
---

<center><H2><b>LinkedList&ArrayList</b></H2></center><br>

### ArrayList

基于动态数组的数据结构，对于随机访问get和set方法，会优于LinkedList。当查询业务比较多的时候用ArrayList会更加合适。

ArrayList实现是基于动态数据，但是在Add的时候也会导致数组下标越界。

> 当ArrayList的size为14的时候，一个线程1执行add()的方法，在执行ensureCapacityInternal( size + 1)时，发现还可以添加一个元素，故数组没有扩容，此时线程1被阻塞。线程2进入，由于线程1没有添加元素，所以Capacity还是15，此时线程2的数据add成功，size==15。然后线程1被释放，可以执行add操作，线程1就在尝试往index ==15存数据，而size就只有 == 15，就导致了越界的情况。

当然，如果在你get的时候传入了>=list.size()的数值也会导致数据下标越界。

### LinkedList

基于链表的数据结构，随机访问较ArrayList慢，因为不能同数组一样直接获取下标数据，需要遍历指针。当时增删比较快，可以在需要频繁增删数据的情况下用LinkedList。尽可能不用get的方式获取数据，用迭代器或者foreach

```java
LinkedList<Integer> linkedList = new LinkedList<>();
for (int i = 0; i < EIGHT; i++) {
    linkedList.add(i);
}
// forEach
linkedList.forEach(i -> System.out.print(" " + i));
// 迭代器
Iterator<Integer> iterator = linkedList.iterator();
while (iterator.hasNext()) {
    System.out.print(iterator.next() + " ");
}
```

### 效率比较

```java
// LinkedList用get获取元素
public static void main(String[] args) {
    // add elements
    int size = 2000000;
    List<String> list = new LinkedList<String>();
    for (int i = 0; i < size; i++) {
        list.add("Just some test data");
    }
 
    long startTime = System.currentTimeMillis();
    for (int i = 0; i < size; i++) {
        list.get(i);
 
        if (i % 10000 == 0) {
            System.out.println("query 10000 elements spend: "
                    + (System.currentTimeMillis() - startTime));
            startTime = System.currentTimeMillis();
        }
    }
}
/*
query 10000 elements spend: 0
query 10000 elements spend: 91
query 10000 elements spend: 244
query 10000 elements spend: 392
query 10000 elements spend: 562
query 10000 elements spend: 714
query 10000 elements spend: 1238
query 10000 elements spend: 1077
query 10000 elements spend: 1147
query 10000 elements spend: 1298
query 10000 elements spend: 1456
query 10000 elements spend: 1652
query 10000 elements spend: 1926
query 10000 elements spend: 2907
query 10000 elements spend: 2966
query 10000 elements spend: 2777
query 10000 elements spend: 3072
query 10000 elements spend: 3308
query 10000 elements spend: 3618
query 10000 elements spend: 4055
query 10000 elements spend: 4481
query 10000 elements spend: 6130
query 10000 elements spend: 5167
.
.
.
*/
```

```java
public static void main(String[] args) {
    // add elements
    int size = 2000000;
    List<String> list = new ArrayList<String>();
    for (int i = 0; i < size; i++) {
        list.add("Just some test data");
    }
 
    long startTime = System.currentTimeMillis();
    for (int i = 0; i < size; i++) {
        list.get(i);
 
        if (i % 10000 == 0) {
            System.out.println("query 10000 elements spend: "
                    + (System.currentTimeMillis() - startTime));
            startTime = System.currentTimeMillis();
        }
    }
}
/*
query 10000 elements spend: 0
query 10000 elements spend: 1
query 10000 elements spend: 1
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 1
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
query 10000 elements spend: 0
.
.
.
*/
```

