---
layout: post
title:  "No Such Provider"
date:   2020-07-08
excerpt: "Stick to note down what I'v learnt"
tag:
- Java 
- Security
- Hyperledger Fabric
---

<center><H2><b>No Such Provider</b></H2></center><br>


参考：[No such provider: BC : 问题解决](https://blog.csdn.net/qq_41712834/article/details/102808134)



在Fabric-SDK实例化一个身份的时候需要加载证书，通过java.security存储证书。

在读取的证书String对象，实例化java.security.PrivateKey的时候，报了No Such Provider的错误。

```java
public PrivateKey getPrivateKeyFromBytes(byte[] data) throws IOException {
    final Reader pemReader = new StringReader(new String(data));
    final PrivateKeyInfo pemPair;
    try (PEMParser pemParser = new PEMParser(pemReader)) {
        pemPair = (PrivateKeyInfo) pemParser.readObject();
    }
    PrivateKey privateKey = new JcaPEMKeyConverter().
        // 报错位置 No Such Provider:BC. 而BouncyCastleProvider.PROVIDER_NAME == "BC"
        setProvider(BouncyCastleProvider.PROVIDER_NAME).
        getPrivateKey(pemPair);
    
    return privateKey;
}
```

### 解决方法

#### 法一(欠佳)

1、修改以下两个文件

```
%JDK_Home%\jre\lib\security\java.security

%JRE_Home%\jre\lib\security\java.security
```

%JRE_Home%\jre\lib\security\java.security

​	追加 最后一行

```java
security.provider.1=sun.security.provider.Sun
security.provider.2=sun.security.rsa.SunRsaSign
security.provider.3=sun.security.ec.SunEC
security.provider.4=com.sun.net.ssl.internal.ssl.Provider
security.provider.5=com.sun.crypto.provider.SunJCE
security.provider.6=sun.security.jgss.SunProvider
security.provider.7=com.sun.security.sasl.Provider
security.provider.8=org.jcp.xml.dsig.internal.dom.XMLDSigRI
security.provider.9=sun.security.smartcardio.SunPCSC
security.provider.10=sun.security.mscapi.SunMSCAPI
security.provider.11=org.bouncycastle.jce.provider.BouncyCastleProvider
```

2、将bcprov-ext-jdk16-143.jar 放到

     %JDK_Home%\jre\lib\ext
     %JRE_Home%\jre\lib\ext



上述方法欠佳。



#### 法二（maven导入）

1：maven（版本很关键，不说了，说多了都是泪😢）

```xml
<dependency>
    <groupId>org.bouncycastle</groupId>
    <artifactId>bcprov-ext-jdk15on</artifactId>
    <version>1.64</version>
</dependency>
```

2、在加密类中加入静态块。

```java
 static{
     try{
         Security.addProvider(new BouncyCastleProvider());
     }catch(Exception e){
         e.printStackTrace();
     }
 }
```

