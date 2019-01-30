title: tomcat6优化配置
author: ginuyasha
tags:
  - tomcat
categories:
  - 中间件
date: 2019-01-30 08:32:00
---

![](/images/tomcat.jpg)

#### 引言

为什么要对tomcat进行优化？tomcat下载的时候的默认配置并不能满足生产环境的要求，这个时候就需要我们根据生产环境的配置对tomcat进行相应的优化。下面介绍的只是对tomcat6优化的配置，如果有问题的地方，请指出，谢谢。

<!--more-->


#### 优化的方向


- JVM内存优化

  1.备份tomcat/bin/catalina.bat文件
  2.修改catalina.bat文件，在文件的上方添加一下内容
  
  ```xml
  set JAVA_OPTS=%JAVA_OPTS% -server -Xms1024m -Xmx1024m -XX:PermSize=64M -XX:MaxPermSize=128m -XX:+PrintGCDateStamps -XX:+PrintGCDetails -Xloggc:D:\Tomcat\apache-tomcat-6.0.29\logs\gc.log
  ```
  
  如果想要生成堆栈日志需要添加一下内容。
  
  ```xml
  JAVA_OPTS="%JAVA_OPTS% -XX:+HeapDumpOnOutOfMemoryError -XX:HeapDumpPath=日志路径"
JAVA_OPTS="%JAVA_OPTS% -XX:ErrorFile=日志路径/hs_err_pid%p.log"
  ```
  
**说明**

`-Xms1024m -Xmx1024m` 的数值要保持一致。具体的内存值根据服务器的配置进行修改。  
`-Xloggc:`配置的是jvm虚拟机gc的日志。  
`-XX:PermSize=128M` 一般配置成初始物理内存的1/64  
`-XX:MaxPermSize=256M`  一般配置成物理内存的1/4


- tomcat配置优化   
  打开tomcat/conf目录 将server.xml文件进行备份,修改server.xml文件，添加如下内容。
  
  ```xml
<Executor name="tomcatThreadPool"     namePrefix="catalina-exec-" 
      maxSpareThreads="75" minSpareThreads="25"/>
```
找到`Connector`标签 修改`Connector`的内容  

```xml
//上面添加的Executor节点
<Connector executor="tomcatThreadPool" port="80" protocol="HTTP/1.1" 
           connectionTimeout="20000" 
           acceptCount="700" 
           maxThreads="600"
		   enableLookups="false" 
           redirectPort="8443" />
```
`acceptCount`容许的最大连接数   
`minSpareThreads`Tomcat初始化时创建的 socket线程数  
`maxSpareThreads`Tomcat连接器的最大空闲socket 线程数  
`maxThreads`客户请求最大线程数

找到如下节点：
```xml
<Host name="localhost"  appBase="webapps"
        unpackWARs="true" autoDeploy="true"
        xmlValidation="false" xmlNamespaceAware="false">
```

将标签中的`autoDeploy="true"`改成` false`


