---
layout: post
title:  "Fabric-LSCC生命周期系统合约"
date:   2020-07-18
excerpt: "Stick to note down what I'v learnt"
tag:
- java 
- Hyperledger Fabric
---

<center><H2><b>Fabric-LSCC生命周期系统合约</b></H2></center><br>

#### 概念

**LSCC属于系统链码**，系统chaincode跟普通chaincode有着相同的编程模型，除了在peer内运行而不是像普通chaincode在隔离的容器中运行。因此，系统chaincode内置为peer的可执行文件中，不用遵循上述的生命周期。特别地，安装、实例化、以及升级不适用于系统chaincode。



**启动链码的过程**

[Hyperledger Fabric v1.1中的系统链码](https://blockchain-fabric.blogspot.com/2018/03/system-chaincodes-in-hyperledger-fabric.html)

[System chaincode概念](https://hyperledger-fabric.readthedocs.io/en/release-1.4/smartcontract/smartcontract.html#system-chaincode)

> 调用deploy功能以实例化给定通道上的链码。它可以接受五个参数，其中前两个参数是必需的，即通道名称和链码部署规范。而其他三个参数，即背书策略，背书人系统链码的名称和验证者系统链码的名称是可选的。

**LSCC负责管理链码的生命周期**，也就是说链码容器的启动应该是LSCC触发的。

##### 



#### 实践（1.4.4版本）

> 通过查看LSCC调用链码过程知其原理。

在这篇[Fabric的区块信息（结合SDK-java）](https://blog.maplestory.work/Fabric的区块信息/)中，我们通过SDK查看了blockWalker中供查看区块的内容。

在安装了实例化一个新的链码之后查看**区块中实例化的交易**内容如下：

```bash
   # 下文中乱码，调用fabric-sdk-java中集成测试的例子就是如此，还未找到原因
   Transaction action 1 has 6 chaincode input arguments
     Transaction action 1 has chaincode input argument 0 is: deploy
     Transaction action 1 has chaincode input argument 1 is: mychannel
     Transaction action 1 has chaincode input argument 2 is: ?!??????fabcargo??1.0????initLedger
     Transaction action 1 has chaincode input argument 3 is: ????????????????????Org1MSP????????Org2MSP??
     Transaction action 1 has chaincode input argument 4 is: escc
     Transaction action 1 has chaincode input argument 5 is: vscc
   Transaction action 1 proposal response status: 200
   Transaction action 1 proposal response payload: ??fabcargo??1.0??escc"?vscc*,????????????????????Org1MSP????????...
   Transaction action 1 proposal chaincodeIDName: lscc, chaincodeIDVersion: 1.4.4,  chaincodeIDPath:  
   Transaction action 1 has 1 name space read write sets
     Namespace lscc read set 0 key fabcargo  version [0:0]
     Namespace lscc write set 0 key fabcargo has value '??fabcargo??1.0??escc"?vscc*,????????????????????Org1MSP????????...' 
```

上述输出可以看出概念中LSCC调用所需要的参数。

- 函数名：deploy
- 通道：mychannel
- 链码+版本：fabcargo + 1.0
- 背书策略：Org1MSP与Org2MSP
- Endorsement system chaincode (ESCC) ，需要背书系统链码依赖。参见：[System chaincode概念](https://hyperledger-fabric.readthedocs.io/en/release-1.4/smartcontract/smartcontract.html#system-chaincode)
- Validation system chaincode (VSCC)，需要验证链码依赖

