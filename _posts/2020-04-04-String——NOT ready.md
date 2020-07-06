---
layout: post
title:  "String"
date:   2020-04-04
excerpt: "Stick to note down what I'v learnt"
tag:
- java 
- coding
---

<center><H2><b>String</b></H2></center><br>



### String.split()

> [Java split()用法](https://www.cnblogs.com/xiaoxiaohui2015/p/5838674.html)

单个符号作为分割：

```java
String[] splitAddress=address.split("\\"); // `\`
String[] splitAddress=address.split("\\|"); // `|`
String[] splitAddress=address.split("\\*"); // `*`;`^`;`:`;`.`;`@`;`,` 同理，前面需要加\\
```

多符号作为分割：

> 如果使用多个分隔符则需要借助 | 符号，但需要转义符的仍然要加上分隔符进行处理

```java
String address="上海^上海市@闵行区#吴中路";
String[] splitAddress=address.split("\\^|@|#"); //分隔符为^ @ #
```