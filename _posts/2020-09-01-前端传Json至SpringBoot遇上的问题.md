---
layout: post
title:  "前端传Json至SpringBoot的问题"
date:   2020-09-01
excerpt: "Stick to note down what I'v learnt"
tag:
- Spring Boot
---

<center><H2><b>前端传Json至SpringBoot的问题</b></H2></center><br>

### 问题发现

在通过Swagger调接口往后端发起请求的时候，请求中返回错误。

截取部分错误信息如下：

```java
"Invalid JSON input: Unrecognized field \"AbsoluteMaxBytes\" (class com.cgs.fabric.domain.ChannelConfig), not marked as ignorable; nested exception is com.fasterxml.jackson.databind.exc.UnrecognizedPropertyException:
```

### 分析

经过分析是Json进行解析时候的数据问题。当把对应的字段`AbsoluteMaxBytes`，改为`absoluteMaxBytes`的时候，不再报错。那么 一定是关于变量命名的问题。



**其他解决方案** 

其他博客上对该报错有一些解决方案：

>@JsonIgnore注解用来忽略某些字段，可以用在Field或者Getter方法上，用在Setter方法时，和Filed效果一样。这个注解只能用在POJO存在的字段要忽略的情况，不能满足现在需要的情况。
>
>@JsonIgnoreProperties(ignoreUnknown = true)，将这个注解写在类上之后，就会忽略类中不存在的字段，可以满足当前的需要。这个注解还可以指定要忽略的字段。使用方法如下：
>
>@JsonIgnoreProperties({ "internalId", "secretKey" })

但是这不能满足我的要求，因为我们的前端还是需要传值到后端的。如果采用ignore注解，虽然不会报错，但是值无法传递至后端。

### 解决

经过测试是因为Json的属性命名不符合JavaBean的命名规范，导致的该问题。

因此得出结论：

+ 需要在Json传数据的时候符合Jackson、JavaBean的命名规范。
+ 在定义JavaBean的时候也一定要遵守驼峰命名规则。
+ 如果一定要在Json中定义大写开头，不符合驼峰命名的。可以再Java类的属性中加上注解`@JsonProperty("OrdererType")`

通过上述三种方式可以解决这个问题。