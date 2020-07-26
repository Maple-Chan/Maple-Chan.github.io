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

