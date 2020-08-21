---
layout: post
title:  "MySQL存储byte[]数据"
date:   2020-08-12
excerpt: "Stick to note down what I'v learnt"
tag:
- 数据库
---

<center><H2><b>MySQL存储byte[]数据</b></H2></center><br>

一个需求，需要将数字签名等二进制文件存入MySQL中。经过分析在本次应用中采用下述第一种方式。

#### 1. 将数据转换为String后存储

采用这个方法就需要用考虑字节数组对应的转换成String之后的情况。

例如，在近段时间开发的一个例子中，需要将一个byte[]的签名存入到数据库中，在几个组织同时对某个数据进行签名了之后才能执行正确的业务操作。

在这个例子中，我们讨论需要**通过Base64、URLEncoder编码之后**再进行传入业务数据库。当然再执行完相应的操作之后，将签名置空，避免重复利用这些数据。

```java
// Base64是 java.util.Base64
public static String encoderBase64URLEncoder(byte[] bytes)  {
    String signBase64 = Base64.getUrlEncoder().encodeToString(bytes);
    return signBase64;
}

public static byte[] decoderURLEncoderBase64(String value) {
    byte[] bytes = Base64.getUrlDecoder().decode(value);
    return bytes;
}
```



#### 2. 用Blob字段存

同时mysql还有这些字段

> binary和varbinary，适bai合存储少量的du二进制数据
> blob适合存储大量的数据



**blob 数据类型** 

[官方介绍](https://dev.mysql.com/doc/refman/5.7/en/blob.html)

`TINYBLOB` 每个字段最多255bytes大小。

`BLOB` 最大限制65KB大小

`MEDIUMBLOB`限制16MB

`LONGBLOB`最大可达4GB

> BLOB与TEXT是为了存储极大的字符串而设计的数据类型，采用二进制与字符串方式存储。当BLOB与TEXT的值太大时，InnoDB会使用专门的“外部”存储区域来进行存储，此时每个值在行内会采用1~4个自己存储指针，在外部存储区域存储实际值。

> 在实际使用中应该慎用这两个类型，尤其是会创建临时表的情况下，因为如果临时表大小超过max_heap_table_size或者tmp_table_size，就会将临时表存储在磁盘上，进而导致整体速度下降！



