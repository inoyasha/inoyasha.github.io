---
title: tomcat配置https启动出现 No Certificate file specified or invalid file format异常
tags:
  - tomcat
categories:
  - 中间件
author: 郭小黑
translate_title: >-
  no-certificate-file-specifier-or-invalid-file-format-exception-occurred-in-tomcat-configuration-https-startup
date: 2019-01-24 16:03:00
---


#### 引言

tomcat配置https启动出现 <code>No Certificate file specified or invalid file format</code>异常,以下是具体的原因和解决办法。

<!-- more -->

#### 具体配置

```xml
<Connector port="8443" protocol="HTTP/1.1" SSLEnabled="true"
	   maxThreads="150" scheme="https" secure="true"
	   clientAuth="false" sslProtocol="TLS"
	   keystoreFile="D:/Tomcat6/server.keystore"
	   keystorePass="changeit" />
```

#### 报错信息

上面为tomcat的SSL配置。 但是启动的时候报"No Certificate file specified or invalid file format"异常，完整的启动信息如下。

```log
2012-4-28 9:17:07 org.apache.catalina.core.AprLifecycleListener init
信息: Loaded APR based Apache Tomcat Native library 1.1.20.
2012-4-28 9:17:07 org.apache.catalina.core.AprLifecycleListener init
信息: APR capabilities: IPv6 [true], sendfile [true], accept filters [false], random [true].
2012-4-28 9:17:07 org.apache.catalina.startup.SetAllPropertiesRule begin
警告: [SetAllPropertiesRule]{Server/Service/Connector} Setting property 'clientAuth' to 'false' did not find a matching property.
2012-4-28 9:17:07 org.apache.catalina.startup.SetAllPropertiesRule begin
警告: [SetAllPropertiesRule]{Server/Service/Connector} Setting property 'keystoreFile' to 'D:/Tomcat6/server.keystore' did not find a matching property.
2012-4-28 9:17:07 org.apache.catalina.startup.SetAllPropertiesRule begin
警告: [SetAllPropertiesRule]{Server/Service/Connector} Setting property 'keystorePass' to 'changeit' did not find a matching property.
2012-4-28 9:17:08 org.apache.coyote.http11.Http11AprProtocol init
严重: Error initializing endpoint
java.lang.Exception: No Certificate file specified or invalid file format
at org.apache.tomcat.jni.SSLContext.setCertificate(Native Method)
at org.apache.tomcat.util.net.AprEndpoint.init(AprEndpoint.java:733)
at org.apache.coyote.http11.Http11AprProtocol.init(Http11AprProtocol.java:107)
at org.apache.catalina.connector.Connector.initialize(Connector.java:1022)
at org.apache.catalina.core.StandardService.initialize(StandardService.java:703)
at org.apache.catalina.core.StandardServer.initialize(StandardServer.java:838)
at org.apache.catalina.startup.Catalina.load(Catalina.java:538)
at org.apache.catalina.startup.Catalina.load(Catalina.java:562)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
at java.lang.reflect.Method.invoke(Method.java:597)
at org.apache.catalina.startup.Bootstrap.load(Bootstrap.java:261)
at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:413)
2012-4-28 9:17:08 org.apache.catalina.core.StandardService initialize
严重: Failed to initialize connector [Connector[HTTP/1.1-8443]]
LifecycleException:  Protocol handler initialization failed: java.lang.Exception: No Certificate file specified or invalid file format
at org.apache.catalina.connector.Connector.initialize(Connector.java:1024)
at org.apache.catalina.core.StandardService.initialize(StandardService.java:703)
at org.apache.catalina.core.StandardServer.initialize(StandardServer.java:838)
at org.apache.catalina.startup.Catalina.load(Catalina.java:538)
at org.apache.catalina.startup.Catalina.load(Catalina.java:562)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
at java.lang.reflect.Method.invoke(Method.java:597)
at org.apache.catalina.startup.Bootstrap.load(Bootstrap.java:261)
at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:413)
2012-4-28 9:17:08 org.apache.coyote.ajp.AjpAprProtocol init
信息: Initializing Coyote AJP/1.3 on ajp-8009
2012-4-28 9:17:08 org.apache.catalina.startup.Catalina load
信息: Initialization processed in 804 ms
2012-4-28 9:17:08 org.apache.catalina.core.StandardService start
信息: Starting service Catalina
2012-4-28 9:17:08 org.apache.catalina.core.StandardEngine start
信息: Starting Servlet Engine: Apache Tomcat/6.0.33
2012-4-28 9:17:08 org.logicalcobwebs.proxool.ProxoolFacade registerConnectionPool
信息: Proxool 0.9.1 (23-Aug-2008 11:10)
2012-4-28 9:17:08 org.logicalcobwebs.proxool.ConnectionPoolDefinition setAnyProperty
警告: Use of proxool.maximum-new-connections is deprecated. Use more descriptive proxool.simultaneous-build-throttle instead.
2012-4-28 9:17:08 org.apache.catalina.startup.HostConfig deployDescriptor
信息: Deploying configuration descriptor host-manager.xml
2012-4-28 9:17:08 org.apache.catalina.startup.HostConfig deployDescriptor
信息: Deploying configuration descriptor manager.xml
2012-4-28 9:17:08 org.apache.catalina.startup.HostConfig deployDirectory
信息: Deploying web application directory docs
2012-4-28 9:17:08 org.apache.catalina.startup.HostConfig deployDirectory
信息: Deploying web application directory examples
2012-4-28 9:17:09 org.apache.coyote.http11.Http11AprProtocol start
严重: Error starting endpoint
java.lang.Exception: Socket bind failed: [730048] ?????????×???(Э?é/???????/???)????í??
at org.apache.tomcat.util.net.AprEndpoint.init(AprEndpoint.java:649)
at org.apache.tomcat.util.net.AprEndpoint.start(AprEndpoint.java:766)
at org.apache.coyote.http11.Http11AprProtocol.start(Http11AprProtocol.java:137)
at org.apache.catalina.connector.Connector.start(Connector.java:1095)
at org.apache.catalina.core.StandardService.start(StandardService.java:540)
at org.apache.catalina.core.StandardServer.start(StandardServer.java:754)
at org.apache.catalina.startup.Catalina.start(Catalina.java:595)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
at java.lang.reflect.Method.invoke(Method.java:597)
at org.apache.catalina.startup.Bootstrap.start(Bootstrap.java:289)
at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:414)
2012-4-28 9:17:09 org.apache.catalina.core.StandardService start
严重: Failed to start connector [Connector[HTTP/1.1-8443]]
LifecycleException:  service.getName(): "Catalina";  Protocol handler start failed: java.lang.Exception: Socket bind failed: [730048] ?????????×???(Э?é/???????/???)????í??
at org.apache.catalina.connector.Connector.start(Connector.java:1102)
at org.apache.catalina.core.StandardService.start(StandardService.java:540)
at org.apache.catalina.core.StandardServer.start(StandardServer.java:754)
at org.apache.catalina.startup.Catalina.start(Catalina.java:595)
at sun.reflect.NativeMethodAccessorImpl.invoke0(Native Method)
at sun.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:39)
at sun.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:25)
at java.lang.reflect.Method.invoke(Method.java:597)
at org.apache.catalina.startup.Bootstrap.start(Bootstrap.java:289)
at org.apache.catalina.startup.Bootstrap.main(Bootstrap.java:414)
2012-4-28 9:17:09 org.apache.coyote.ajp.AjpAprProtocol start
信息: Starting Coyote AJP/1.3 on ajp-8009
2012-4-28 9:17:09 org.apache.catalina.startup.Catalina start
信息: Server startup in 534 ms
```

#### 解决办法

查阅资料，和具体的tomcat版本有关系。<code>tomcat6.0.18</code>在上面的ssl配置不会出现此异常。
<code>6.0.33</code>版本中默认启用了APR（APR是通过JNI访问的可移植库，可以提高Tomcat的性能和伸缩性），所以采用传统的配置方式（如下）会报异常 解决办法是采用下面的配置
将协议修改成<code>org.apache.coyote.http11.Http11Protocol</code>协议即可。


配置如下:

```xml
<Connector port="8443" protocol="org.apache.coyote.http11.Http11Protocol" SSLEnabled="true"
	   maxThreads="150" scheme="https" secure="true"
	   clientAuth="false" sslProtocol="TLS"
	   keystoreFile="D:/Tomcat6/server.keystore"
	   keystorePass="changeit" />
```


转载：[tomcat配置https启动出现"No Certificate file specified or invalid file format"异常](https://blog.csdn.net/hfsu0419/article/details/7536408)

