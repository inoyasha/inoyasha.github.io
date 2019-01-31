title: 使用java工具keytool生成ssl秘钥文件keystore
tags:
  - tomcat
categories:
  - 中间件
author: 郭小黑
date: 2019-01-24 15:54:00
---


#### 引言

最近在做Mina的开发，通信的时候需要数据加密，而且mina本身支持SSLFilter过滤器，所以可以采用SSL加密的方式对数据进行加密。

在进行加密之前，我们需要使用keytool(这个存在于C:\Program Files\Java\jdk1.7.0_65\bin目录下)产生创建keystore和truststore文件。接下来我会为你介绍生成密钥的方法。

<!-- more -->

#### 生成keystore.jsk文件

```code
keytool -genkeypair -alias certificatekey -keyalg RSA -validity 7 -keystore keystore.jks
```

#### 导出凭证文件

```code
keytool -export -alias certificatekey -keystore keystore.jks -rfc -file selfcert.cer
```

#### 将凭证文件导入到truststore文件

```code
keytool -import -alias certificatekey -file selfcert.cer -keystore truststore.jks
```

#### 找到keystore.jks和truststore.jks文件


#### 启用tomcat ssh功能

找到<code>tomcat/config/server.xml </code>文件，打开找到8443 相关<code>Connector</code>

配置如下

```xml
<Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
               maxThreads="150" scheme="https" secure="true"
               keystoreFile="E:\Tomcat\apache-tomcat-6.0.35\conf\cert\keystore.jks" keystorePass="123456"
               truststoreFile="E:\Tomcat\apache-tomcat-6.0.35\conf\cert\truststore.jks" truststorePass="123456"
               clientAuth="true" sslProtocol="TLS" truststoreType="JKS" 
			   ciphers="TLS_ECDHE_ECDSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_ECDSA_WITH_AES_256_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA,TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA,TLS_RSA_WITH_AES_128_CBC_SHA,TLS_RSA_WITH_AES_256_CBC_SHA,SSL_RSA_WITH_3DES_EDE_CBC_SHA"/>
```

**ps：** 此方法我在tomcat6上试过可用，其他tomcat版本没有试过。


转载地址：[使用keytool生成ssl密钥文件keystore和truststore](https://blog.csdn.net/u010049692/article/details/38686659)



