---
layout: post
title:  "使用Transaction注解遇上的问题"
date:   2020-08-20
excerpt: "Stick to note down what I'v learnt"
tag:
- Spring Boot
---

<center><H2><b>@Transaction</b></H2></center><br>

在最近项目中遇上了需要读取用户信息，但是又不能在客户端获取用户密码。我们组的包工采用了加注解`@Transaction(readOnly = true)`的方式，对读到的数据中的密码、盐粒数据进行擦除。

我在另一个需要用到事务的方法中，调用了上面描述的方法。我这个方法需要对数据的数据进行修改，因此就遇上了，在调用我实现的方法之后，将用户的密码等信息清空了。也就是包工写的事务不生效了。



### 解决方案

将读用户信息的方法拎出去，在一个其他类方法中执行。将需要的数据作为参数，传入另一个事务当中，避免事务的嵌套，将事务分为独立的两步。



### 原因

> 产生问题的原因是：spring是通过代理代管理事务的，当在第一个方法insertUser1内直接调用insertUser2的时候 ，insertUser2上的事务是不会起作用的（也就是insertUser2是没有开启事务）



> 参考：
>
> [Spring事务与非事务方法相互调用](https://cloud.tencent.com/developer/article/1633511)