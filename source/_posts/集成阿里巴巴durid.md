---
title: 集成阿里巴巴durid
tags:
  - durid
categories:
  - java web
author: 郭小黑
translate_title: integrating-alibaba-durid
date: 2019-01-24 16:35:00
---

#### 引言

durid是阿里巴巴公司开发的数据库驱动，集成此驱动可以监控java web项目的运行状态和sql执行状态。

<!-- more -->

#### 集成过程

- 引入durid pom

```xml 
<dependency>
    <groupId>com.alibaba</groupId>
    <artifactId>druid</artifactId>
    <version>1.1.9</version>
</dependency>
```

- spring-context中配置数据源

```xml
<bean id="dataSource" class="com.alibaba.druid.pool.DruidDataSource" init-method="init" destroy-method="close">
	<property name="url" value="${jdbc.url}" />
	<property name="username" value="${jdbc.username}" />
	<property name="password" value="${jdbc.password}" />
	<!-- 配置监控统计拦截的filters 这个表示此数据源是否加入监控-->
	<property name="initialSize" value="${cpool.minPoolSize}"/>
	<property name="maxActive" value="${cpool.maxPoolSize}"/>
	<property name="minIdle" value="${cpool.minPoolSize}"/>
	<!--  如果不配置这一项，后台不会显示监控的sql-->
	<property name="filters" value="stat" />  
	<!-- 配置获取连接等待超时的时间 --> 
    <property name="maxWait" value="${cpool.checkoutTimeout}" />
	<!-- 配置一个连接在池中最小生存的时间，单位是毫秒 -->
	<property name="timeBetweenEvictionRunsMillis" value="60000" />
	<property name="minEvictableIdleTimeMillis" value="300000" />
	<property name="validationQuery" value="SELECT 'x'" />
	<property name="testWhileIdle" value="true" />
	<property name="testOnBorrow" value="false" />
	<property name="testOnReturn" value="false" />
</bean>
```

- web.xml中配置拦截器

```xml
<filter>
    <filter-name>DruidWebStatFilter</filter-name>
    <filter-class>com.alibaba.druid.support.http.WebStatFilter</filter-class>
    <init-param>
        <param-name>exclusions</param-name>
        <param-value>*.js,*.gif,*.jpg,*.png,*.css,*.ico,/druid/*</param-value>
    </init-param>
  </filter>
  <filter-mapping>
    <filter-name>DruidWebStatFilter</filter-name>
    <url-pattern>/*</url-pattern>
  </filter-mapping>
  
   <servlet> <servlet-name>DruidStatView</servlet-name>
    <servlet-class>com.alibaba.druid.support.http.StatViewServlet</servlet-class>    </servlet>
      
  <servlet-mapping>
      <servlet-name>DruidStatView</servlet-name>
      <url-pattern>/druid/*</url-pattern>
  </servlet-mapping>
 ```

 **ps：** 其他durid配置请查询durid驱动文档说明。自行google。

