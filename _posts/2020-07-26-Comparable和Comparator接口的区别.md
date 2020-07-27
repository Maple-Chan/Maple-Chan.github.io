---
layout: post
title:  "Comparable和Comparator接口的区别"
date:   2020-07-26
excerpt: "Stick to note down what I'v learnt"
tag:
- Java
---

<center><H2><b>Comparable和Comparator接口的区别</b></H2></center><br>

##### **Comparable 接口**

该接口只包含一个方法，x.compareTo(y)。实现Comparable接口的类的对象可以用作“有序映射(如TreeMap)”中的键或“有序集合(TreeSet)”中的元素，而不需要指定比较器就可以支持排序。

该方法通过返回负数、0、正数，来表示输入的对象小于，等于，大于调用该方法的对象。x.compareTo(y)==0，说明x与y相等。 x.compareTo(y) < 0，说明x<y;

##### **Comparator接口**

该接口包含compare(x,y)方法。若需要控制某个类的次序，我们可以建立一个“该类的比较器”来进行排序。这个“比较器”只需要实现Comparator接口即可。也就是说，我们可以通过“实现Comparator类来新建一个比较器”，然后通过该比较器对类进行排序。



### 实践

若一个类实现了Comparable接口，就意味着“该类支持排序”。在调用sorts方法的时候就不需要在指定比较器（Comparator）。

```java
package javatest;

import java.util.ArrayList;
import java.util.Collections;
import java.util.Comparator;
import java.util.List;

/**
 * @description:
 * @modifyContent:
 * @author: Maple Chan
 * @date: 2020-07-26 23:24:59
 * @version: 0.0.1
 */
public class JavaComparableComparator {

    public static void main(String[] args) {
        List<House> houses = new ArrayList<>();
        // 报错:No enclosing instance of type JavaComparableComparator is accessible.
        // 原因、解决：
        // 内部类是动态的（无static关键字修饰），而main方法是静态的，
        // 普通的内部类对象隐含地保存了一个引用，指向创建它的外围类对象，
        // 所以要在static方法（类加载时已经初始化）调用内部类的必须先创建外部类。
        House h1 = new JavaComparableComparator().new House(95);
        House h2 = new JavaComparableComparator().new House(110);
        House h3 = new JavaComparableComparator().new House(80);
        House h4 = new JavaComparableComparator().new House(150);
        houses.add(h1);
        houses.add(h2);
        houses.add(h3);
        houses.add(h4);
        
        // Comparator常见使用
        System.out.println("before sort");
        houses.forEach((n)-> System.out.print(n.price + " "));
        houses.sort(new JavaComparableComparator().new HouseComparator());
        System.out.println("\nafter sort");
        houses.forEach((n)-> System.out.print(n.price + " "));
        
        // Collections.sort(houses) , 不指定Comparator会报错
        Collections.sort(houses,new JavaComparableComparator().new HouseComparator());

        // =======================================================================
        // Comparable常见使用
        List<HouseComparable> housesComparables = new ArrayList<>();
        HouseComparable hc1 = new JavaComparableComparator().new HouseComparable(95);
        HouseComparable hc2 = new JavaComparableComparator().new HouseComparable(110);
        HouseComparable hc3 = new JavaComparableComparator().new HouseComparable(80);
        HouseComparable hc4 = new JavaComparableComparator().new HouseComparable(150);
        housesComparables.add(hc1);
        housesComparables.add(hc2);
        housesComparables.add(hc3);
        housesComparables.add(hc4);

        System.out.println("\n\n\n\nbefore sort");
        housesComparables.forEach((n)-> System.out.print(n.price + " "));
        // 无需指定 Comparator，他自己就是可以进行比较的类
        //Collections sort
        Collections.sort(housesComparables); 
        
        System.out.println("\nafter sort");
        housesComparables.forEach((n)-> System.out.print(n.price + " "));


    }

  /**
    *普通House
    */
    class House {
        int price;
        public House(int price) {
            this.price = price;
        }
    }
    /**
    * House比较器
    */
    class HouseComparator implements Comparator<House> {
        @Override
        public int compare(House o1, House o2) {
            if (o1.price > o2.price) {
                return 1;
            } else if (o1.price < o2.price) {
                return -1;
            }
            return 0;
        }

    }

    /**
    * 可排序的House
    */
    class HouseComparable implements Comparable<HouseComparable> {
        int price;

        public HouseComparable(int price) {
            this.price = price;
        }

        @Override
        public int compareTo(HouseComparable o) {

            if (price < o.price) {
                return -1;
            } else if (price > o.price) {
                return 1;
            }

            return 0;
        }

    }

}
```

### Collections调用重写的compareTo

Collections如何调用重写的compareTo方法的

```java
Collections.sort(List<T> list); 
Collections.sort(List<T> list, Comparator<? super T> c)
```

如果待排序的列表中是数字或者字符，可以直接使用Collections.sort(list);当需要排序的集合或数组不是单纯的数字型时，需要自己定义排序规则，实现一个Comparator比较器。



参考：

> [详解Java中Comparable和Comparator接口的区别](<https://blog.csdn.net/u010859650/article/details/85009595>)