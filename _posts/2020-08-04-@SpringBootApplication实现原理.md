---
layout: post
title:  "@SpringBootApplication"
date:   2020-08-4
excerpt: "Stick to note down what I'v learnt"
tag:
- Spring Boot
---

<center><H2><b>@SpringBootApplication</b></H2></center><br>

该注解将`@Configuration`、`@ComponentScan`、`@EnableAutoConfiguration`三个注解组合在了一起。

+ `@ComponentScan`：启动组件扫描，这样写的Web控制器类（`@Controller`）和其他组件才能被自动发现并注册为Spring应用程序上下文里的Bean。

+ `@EnableAutoConfiguration`：开启SpringBoot自动配置。

