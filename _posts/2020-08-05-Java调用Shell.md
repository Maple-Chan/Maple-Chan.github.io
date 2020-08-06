---
layout: post
title:  "Java调用Shell"
date:   2020-08-05
excerpt: "Stick to note down what I'v learnt"
tag:
- Java 
---

<center><H2><b>Java调用Shell</b></H2></center><br>

ProcessBuilder类是J2SE 1.5在java.lang中新添加的一个新类，此类用于创建操作系统进程，它提供一种启动和管理进程（也就是应用程序）的方法。在J2SE 1.5之前，都是由Process类处理实现进程的控制管理。**每个 ProcessBuilder 实例管理一个进程属性集。它的start() 方法利用这些属性创建一个新的 Process 实例。start() 方法可以从同一实例重复调用，以利用相同的或相关的属性创建新的子进程。**

ProcessBuilder.start() 和 Runtime.exec() 方法都被用来创建一个操作系统**进程**（执行命令行操作），并返回 Process 子类的一个实例，该实例可用来控制进程状态并获得相关信息。
ProcessBuilder.start() 和 Runtime.exec()传递的参数有所不同，Runtime.exec()可接受一个单独的字符串，这个字符串是通过空格来分隔可执行命令程序和参数的；也可以接受字符串数组参数。而ProcessBuilder的构造函数是一个字符串列表或者数组。列表中第一个参数是可执行命令程序，其他的是命令行执行是需要的参数。

### 1. 调用正常脚本/二进制命令

封装如下类，进行调用shell命令。

```java
public class CallShellUtil {
    private Logger logger = LoggerFactory.getLogger(CallShellUtil.class);
    
    public int runCommand(File workDirector, String... cmd) throws  IOException {
        ProcessBuilder processBuilder = new ProcessBuilder(cmd);
        processBuilder.directory(workDirector);

        logger.info("processBuilder.environment:");
        logger.info("env:{}", processBuilder.environment());
        logger.info("工作目录 pwd:{}", processBuilder.directory());

        String resultString;

        //返回值，0为正确执行
        int runningStatus = -1;

        Process shellProcess = processBuilder.start();

        BufferedReader stdInput = new BufferedReader(new InputStreamReader(shellProcess.getInputStream()));
        BufferedReader stdError = new BufferedReader(new InputStreamReader(shellProcess.getErrorStream()));

        try {
            //等待命令执行
            runningStatus = shellProcess.waitFor();
        } catch (InterruptedException e) {
            e.printStackTrace();
        }

        /*显示日志*/
        while (null != (resultString = stdError.readLine())) {
            logger.info(resultString); //问题，fabric的info输出，这里需要getErrorStream获取到
        }
        while (null != (resultString = stdInput.readLine())) {
            logger.info(resultString);
        }

        logger.info("running status:{}", runningStatus);
        //销毁进程
        shellProcess.destroy();
        return runningStatus;
    }
}

```

调用方法

```java
new CallShellUtil().runCommand(
    new File("workspace"),
    new String[]{"command","arg1","arg2"});
```





### 2. 调用带有 `>` 与 `|` 命令

> 参考：<https://www.cnblogs.com/lisperl/archive/2012/06/28/2568494.html>

> 背景：在Fabric-sdk-java的一个应用中，我们需要用到Linux下的configtxgen工具来从configtx.yaml文件转换成一个json文件。

命令如下

```bash
./configtxgen -printOrg Msp-org2 > org3.json
```

按照1.中的方式将命令传入ProcessBuilder，不能正确执行，无法重定向生成org3.json。

```java
String CONFIGTXGEN = System.getProperty("user.dir") + "/files/configtxgen";
String[] cmd = {    CONFIGTXGEN,
                    "-printOrg",
                    orgName,
                    ">",
                    path
               }
ProcessBuilder processBuilder = new ProcessBuilder(cmd);
```

原因是，ProcessBuilder无法识别重定向符号“>”。

#### 尝试1

用如下替代：

```java
String CONFIGTXGEN = System.getProperty("user.dir") + "/files/configtxgen";
String[] cmd = { "sh",
                "-c",
                "\'"+ CONFIGTXGEN + " -printOrg " + orgName + " > " + path + "\'"
               }
// 第一反应这么写的原因，可以用sh -c './configtxgen -printOrg MSP-org2 > org2.json' 来生成命令
```

替换后，报sh: connot find file or directory错误。

据分析原因，传入ProcessBuilder之后，将第三个参数作为一个字符串进行执行了。



#### 经过调整

将命令作为如下数组传入：

```java
String CONFIGTXGEN = System.getProperty("user.dir") + "/files/configtxgen";
String[] cmd = { "sh",
                "-c",
                ""+ CONFIGTXGEN + " -printOrg " + orgName + " > " + path + ""
               }
```



#### 其他解决思路

我们也可以通过仅执行`./configtxgen -printOrg Msp-org2`,然后对输出进行处理，生成org.json文件在Java中使用、保存。