---
title: java集成mybatis
tags:
  - mybatis
categories:
  - java
author: 郭小黑
translate_title: java-integration-mybatis
date: 2019-01-24 16:55:00
---


#### 引言

MyBatis 是一款优秀的持久层框架，它支持定制化 SQL、存储过程以及高级映射。MyBatis 避免了几乎所有的 JDBC 代码和手动设置参数以及获取结果集。MyBatis 可以使用简单的 XML 或注解来配置和映射原生信息，将接口和 Java 的 POJOs(Plain Old Java Objects,普通的 Java对象)映射成数据库中的记录。其他的ORM框架还有hibernate、SpringJdbcTemplate等。他们直接的区别可以参考[Mybaits-Java ORM来源于历史、Hibernate和MyBatis比较](https://www.jianshu.com/p/38c1b019b85f)。

<!-- more -->

#### 集成mybatis


- 添加pom依赖

```xml
<dependency>
	<groupId>postgresql</groupId>
	<artifactId>postgresql</artifactId>
	<version>9.2-1002.jdbc4</version>
</dependency>
<dependency>
	<groupId>org.mybatis</groupId>
	<artifactId>mybatis</artifactId>
	<version>3.2.3</version>
</dependency>
```

- 添加mybatis配置

在src目录下新建mybatis-config.xml文件,文件内容如下：

```xml
<?xml version="1.0" encoding="UTF-8"?>   
<!DOCTYPE configuration   
PUBLIC "-//mybatis.org//DTD Config 3.0//EN" "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
<settings>
	<!-- changes from the defaults for testing -->
	<setting name="cacheEnabled" value="false" />
	<setting name="useGeneratedKeys" value="true" />
	<setting name="defaultExecutorType" value="REUSE" />
</settings>

<typeAliases>
	<typeAlias alias="User" type="com.thunisoft.bean.User" />
</typeAliases>
<!-- postgresql -->
<environments default="development">
	<environment id="development">
		<transactionManager type="jdbc" />
		<dataSource type="POOLED">
			<property name="driver" value="org.postgresql.Driver" />
			<property name="url" value="jdbc:postgresql://localhost:5432/test?charSet=utf-8" />
			<property name="username" value="数据库用户名" />
			<property name="password" value="数据库密码" />
		</dataSource>
	</environment>
</environments>
<!-- 配置mapper.xml 路径-->
<mappers>
	<mapper resource="com/thunisoft/mapper/UserMapper.xml" />
</mappers>
</configuration>
```

- 编写mapper.xml和mapper.java文件

以下只列出mapper.xml文件,mapper.java是接口 使用@mapper进行声明。


```xml
<?xml version="1.0" encoding="UTF-8" ?>   
<!DOCTYPE mapper   
PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"   
"http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.thunisoft.mapper.UserMapper">
<!-- 这里namespace必须是UserMapper接口的路径” -->
<insert id="insertUser" parameterType="User">
	insert into test.t_user(name,age) values(#{name},#{age})
	<!-- 这里sql结尾不能加分号，否则报“ORA-00911”的错误 -->
</insert>

<!-- 这里的id必须和UserMapper接口中的接口方法名相同 -->
<select id="getUser" resultType="User" parameterType="java.lang.String">
	select *
	from test.t_user where name=#{name}
</select>
<!-- 查询所有的用户 -->
<select id="getAllUsers" resultType="User">
	select * from test.t_user
</select>
</mapper>
```

#### 使用mapper

获取一个sqlSessionFactory对象

```java
    String resource = "mybatis-config.xml";
    Reader reader = null;
    try {
        reader = Resources.getResourceAsReader(resource);
    } catch (IOException e) {
        logger.error(e.getMessage());
    }
    sqlSessionFactory = new SqlSessionFactoryBuilder().build(reader);
```

使用sqlSesstionFactory对象生成sqlSession
```java
SqlSession sqlSession = sqlSessionFactory.openSession();
    try {
        UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
        User user = new User("lisi", new Integer(25));
        userMapper.insertUser(user);
        // 这里一定要提交，不然数据进不去数据库中 执行事务
        sqlSession.commit();
    } finally {
        sqlSession.close();
    }
```


**ps：** mysql数据库集成mybatis和postgresql集成类似。配置如下:

```xml
<!-- mysql -->
<environments default="development">
	<environment id="development">
		<transactionManager type="jdbc" />
		<dataSource type="POOLED">
			<property name="driver" value="com.mysql.jdbc.Driver" />
			<property name="url"
				value="jdbc:mysql://localhost:3306/mybatis_test" />
			<property name="username" value="root" />
			<property name="password" value="root" />
		</dataSource>
	</environment>
</environments>
```

mysql的pom文件

```xml
<!-- mysql -->
<dependency>
	<groupId>mysql</groupId>
	<artifactId>mysql-connector-java</artifactId>
	<version>5.1.26</version>
</dependency>
```

Mybatis有个代码生成工具，生成的代码里面有mapper.xml文件，mapper.xml中的sql语句会用parameterType这个属性， 而这个值可能是我们自定义的对象， 此时，如果没有typealiases，我们就需要为parameterType指定全路径：如果没有别名需要指定的全路径太麻烦 所以可以采用以下的方式来指定报名。

```xml
<typeAliases>  
  <!--  
  通过package, 可以直接指定package的名字， mybatis会自动扫描你指定包下面的javabean,  
  并且默认设置一个别名，默认的名字为： javabean 的首字母小写的非限定类名来作为它的别名。  
  也可在javabean 加上注解@Alias 来自定义别名， 例如： @Alias(user)   
  <package name="com.dy.entity"/>  
   -->  
  <typeAlias alias="UserEntity" type="com.dy.entity.User"/>  
 </typeAliases>
 ```

 参考文章:
[MyBatis中typealiases的使用](https://blog.csdn.net/lelewenzibin/article/details/42713585)
[Mybatis3简单使用（PostgreSQL)](https://blog.csdn.net/gaojinshan/article/details/40656757)
