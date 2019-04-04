---
title: mybatis 拦截器的使用
tags:
  - mybatis
categories:
  - java web
author: 郭小黑
translate_title: use-of-mybatis-interceptor
date: 2019-01-24 17:36:00
---


#### 引言

拦截器的一个作用就是我们可以拦截某些方法的调用，我们可以选择在这些被拦截的方法执行前后加上某些逻辑，也可以在执行这些被拦截的方法时执行自己的逻辑而不再执行被拦截的方法。Mybatis拦截器设计的一个初衷就是为了供用户在某些时候可以实现自己的逻辑而不必去动Mybatis固有的逻辑。打个比方，对于Executor，Mybatis中有几种实现：BatchExecutor、ReuseExecutor、SimpleExecutor和CachingExecutor。这个时候如果你觉得这几种实现对于Executor接口的query方法都不能满足你的要求，那怎么办呢？是要去改源码吗？当然不。我们可以建立一个Mybatis拦截器用于拦截Executor接口的query方法，在拦截之后实现自己的query方法逻辑，之后可以选择是否继续执行原来的query方法。

<!-- more -->

#### 定义拦截器

对于拦截器Mybatis为我们提供了一个Interceptor接口，通过实现该接口就可以定义我们自己的拦截器。我们先来看一下这个接口的定义：

```java
package org.apache.ibatis.plugin;
	 
	import java.util.Properties;
	 
	public interface Interceptor {
	 
	  Object intercept(Invocation invocation) throws Throwable;
	 
	  Object plugin(Object target);
	 
	  void setProperties(Properties properties);
	 
	}
```

上面的接口我们可以看到有三个方法。<code>intercept</code>,<code>plugin</code>,<code>setProperties</code>。


- plugin。拦截器用于封装目标对象的，通过该方法我们可以返回目标对象本身，也可以返回一个它的代理。
- intercept。实际拦截时执行的方法。
- setProperties。为拦截器配置属性。

对于<code>plugin</code>方法而言，其实Mybatis已经为我们提供了一个实现。Mybatis中有一个叫做Plugin的类，里面有一个静态方法<code>wrap(Object target,Interceptor interceptor)</code>，通过该方法可以决定要返回的对象是目标对象还是对应的代理。

```java
package org.apache.ibatis.plugin;
 
import java.lang.reflect.InvocationHandler;
import java.lang.reflect.Method;
import java.lang.reflect.Proxy;
import java.util.HashMap;
import java.util.HashSet;
import java.util.Map;
import java.util.Set;
 
import org.apache.ibatis.reflection.ExceptionUtil;
 
public class Plugin implements InvocationHandler {
 
  private Object target;
  private Interceptor interceptor;
  private Map<Class<?>, Set<Method>> signatureMap;
 
  private Plugin(Object target, Interceptor interceptor, Map<Class<?>, Set<Method>> signatureMap) {
    this.target = target;
    this.interceptor = interceptor;
    this.signatureMap = signatureMap;
  }

  public static Object wrap(Object target, Interceptor interceptor) {
    Map<Class<?>, Set<Method>> signatureMap = getSignatureMap(interceptor);
    Class<?> type = target.getClass();
    Class<?>[] interfaces = getAllInterfaces(type, signatureMap);
    if (interfaces.length > 0) {
      return Proxy.newProxyInstance(
          type.getClassLoader(),
          interfaces,
          new Plugin(target, interceptor, signatureMap));
    }
    return target;
  }
 
  public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
    try {
      Set<Method> methods = signatureMap.get(method.getDeclaringClass());
      if (methods != null && methods.contains(method)) {
        return interceptor.intercept(new Invocation(target, method, args));
      }
      return method.invoke(target, args);
    } catch (Exception e) {
      throw ExceptionUtil.unwrapThrowable(e);
    }
  }
 
  　private static Map<Class<?>, Set<Method>> getSignatureMap(Interceptor interceptor) {
    Intercepts interceptsAnnotation = interceptor.getClass().getAnnotation(Intercepts.class);
    if (interceptsAnnotation == null) { // issue #251
      throw new PluginException("No @Intercepts annotation was found in interceptor " + interceptor.getClass().getName());     
    }
    Signature[] sigs = interceptsAnnotation.value();
    Map<Class<?>, Set<Method>> signatureMap = new HashMap<Class<?>, Set<Method>>();
    for (Signature sig : sigs) {
      Set<Method> methods = signatureMap.get(sig.type());
      if (methods == null) {
        methods = new HashSet<Method>();
        signatureMap.put(sig.type(), methods);
      }
      try {
        Method method = sig.type().getMethod(sig.method(), sig.args());
        methods.add(method);
      } catch (NoSuchMethodException e) {
        throw new PluginException("Could not find method on " + sig.type() + " named " + sig.method() + ". Cause: " + e, e);
      }
    }
    return signatureMap;
  }
 
  private static Class<?>[] getAllInterfaces(Class<?> type, Map<Class<?>, Set<Method>> signatureMap) {
    Set<Class<?>> interfaces = new HashSet<Class<?>>();
    while (type != null) {
      for (Class<?> c : type.getInterfaces()) {
        if (signatureMap.containsKey(c)) {
          interfaces.add(c);
        }
      }
      type = type.getSuperclass();
    }
    return interfaces.toArray(new Class<?>[interfaces.size()]);
  }
 
}
```

##### Plugin的wrap方法。
它根据当前的Interceptor上面的注解定义哪些接口需要拦截，然后判断当前目标对象是否有实现对应需要拦截的接口，如果没有则返回目标对象本身，如果有则返回一个代理对象。而这个代理对象的InvocationHandler正是一个Plugin。所以当目标对象在执行接口方法时，如果是通过代理对象执行的，则会调用对应InvocationHandler的invoke方法，也就是Plugin的invoke方法。所以接着我们来看一下该invoke方法的内容。这里invoke方法的逻辑是：如果当前执行的方法是定义好的需要拦截的方法，则把目标对象、要执行的方法以及方法参数封装成一个Invocation对象，再把封装好的Invocation作为参数传递给当前拦截器的intercept方法。如果不需要拦截，则直接调用当前的方法。Invocation中定义了定义了一个proceed方法，其逻辑就是调用当前方法，所以如果在intercept中需要继续调用当前方法的话可以调用invocation的procced方法。


这就是Mybatis中实现Interceptor拦截的一个思想，如果用户觉得这个思想有问题或者不能完全满足你的要求的话可以通过实现自己的Plugin来决定什么时候需要代理什么时候需要拦截。以下讲解的内容都是基于Mybatis的默认实现即通过Plugin来管理Interceptor来讲解的。

对于实现自己的Interceptor而言有两个很重要的注解，一个是@Intercepts，其值是一个@Signature数组。@Intercepts用于表明当前的对象是一个Interceptor，而@Signature则表明要拦截的接口、方法以及对应的参数类型。来看一个自定义的简单Interceptor：

```java
package com.tiantian.mybatis.interceptor;
 
import java.sql.Connection;
import java.util.Properties;
 
import org.apache.ibatis.executor.Executor;
import org.apache.ibatis.executor.statement.StatementHandler;
import org.apache.ibatis.mapping.MappedStatement;
import org.apache.ibatis.plugin.Interceptor;
import org.apache.ibatis.plugin.Intercepts;
import org.apache.ibatis.plugin.Invocation;
import org.apache.ibatis.plugin.Plugin;
import org.apache.ibatis.plugin.Signature;
import org.apache.ibatis.session.ResultHandler;
import org.apache.ibatis.session.RowBounds;
 
@Intercepts( {
   @Signature(method = "query", type = Executor.class, args = {
          MappedStatement.class, Object.class, RowBounds.class,
          ResultHandler.class }),
   @Signature(method = "prepare", type = StatementHandler.class, args = { Connection.class }) })
public class MyInterceptor implements Interceptor {

public Object intercept(Invocation invocation) throws Throwable {
   Object result = invocation.proceed();
   System.out.println("Invocation.proceed()");
   return result;
}

public Object plugin(Object target) {
   return Plugin.wrap(target, this);
}

public void setProperties(Properties properties) {
   String prop1 = properties.getProperty("prop1");
   String prop2 = properties.getProperty("prop2");
   System.out.println(prop1 + "------" + prop2);
}
```

###### setProperties方法。

这个方法在Configuration初始化当前的Interceptor时就会执行，这里只是简单的取两个属性进行打印。

##### 看plugin方法中我们是用的Plugin的逻辑来实现Mybatis的逻辑的。

MyInterceptor类上我们用@Intercepts标记了这是一个Interceptor，然后在@Intercepts中定义了两个@Signature，即两个拦截点。第一个@Signature我们定义了该Interceptor将拦截Executor接口中参数类型为MappedStatement、Object、RowBounds和ResultHandler的query方法；第二个@Signature我们定义了该Interceptor将拦截StatementHandler中参数类型为Connection的prepare方法。

###### intercept方法。

这里我们只是简单的打印了一句话，然后调用invocation的proceed方法，使当前方法正常的调用。

对于这个拦截器，Mybatis在注册该拦截器的时候就会利用定义好的n个property作为参数调用该拦截器的setProperties方法。之后在新建可拦截对象的时候会调用该拦截器的plugin方法来决定是返回目标对象本身还是代理对象。对于这个拦截器而言，当Mybatis是要Executor或StatementHandler对象的时候就会返回一个代理对象，其他都是原目标对象本身。然后当Executor代理对象在执行参数类型为MappedStatement、Object、RowBounds和ResultHandler的query方法或StatementHandler代理对象在执行参数类型为Connection的prepare方法时就会触发当前的拦截器的intercept方法进行拦截，而执行这两个接口对象的其他方法时都只是做一个简单的代理。


注册拦截器是通过在Mybatis配置文件中plugins元素下的plugin元素来进行的。一个plugin对应着一个拦截器，在plugin元素下面我们可以指定若干个property子元素。Mybatis在注册定义的拦截器时会先把对应拦截器下面的所有property通过Interceptor的setProperties方法注入给对应的拦截器。所以，我们可以这样来注册我们在前面定义的MyInterceptor：


```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
  PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
  "http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<properties resource="config/jdbc.properties"></properties>
	<typeAliases>
	   <package name="com.tiantian.mybatis.model"/>
	</typeAliases>
	<plugins>
	   <plugin interceptor="com.tiantian.mybatis.interceptor.MyInterceptor">
	       <property name="prop1" value="prop1"/>
	       <property name="prop2" value="prop2"/>
	   </plugin>
	</plugins>
	<environments default="development">
	   <environment id="development">
	       <transactionManager type="JDBC" />
	       <dataSource type="POOLED">
	          <property name="driver" value="${jdbc.driver}" />
	          <property name="url" value="${jdbc.url}" />
	          <property name="username" value="${jdbc.username}" />
	          <property name="password" value="${jdbc.password}" />
	       </dataSource>
	   </environment>
	</environments>
	<mappers>
	   <mapper resource="com/tiantian/mybatis/mapper/UserMapper.xml"/>
	</mappers>
</configuration>
```

Mybatis拦截器有四种类型的接口:

- Executor
- StatementHandler
- ParameterHandler
- ResultSetHandler

以上四种类型的接口是在Mybatis的Configuration中写死了的，如果要支持拦截其他接口就需要我们重写Mybatis的Configuration。Mybatis可以对这四个接口中所有的方法进行拦截。

#### 拦截器应用


##### 分页场景

 我们知道要利用JDBC对数据库进行操作就必须要有一个对应的Statement对象。

##### 实现思路

 Mybatis在执行Sql语句前也会产生一个包含Sql语句的Statement对象，而且对应的Sql语句是在Statement之前产生的，所以我们就可以在它成Statement之前对用来生成Statement的Sql语句下手。在Mybatis中Statement语句是通过RoutingStatementHandler对象的prepare方法生成的。所以利用拦截器实现Mybatis分页的一个思路就是拦截StatementHandler接口的prepare方法，然后在拦截器方法中把Sql语句改成对应的分页查询Sql语句，之后再调用StatementHandler对象的prepare方法，即调用invocation.proceed()。更改Sql语句这个看起来很简单，而事实上来说的话就没那么直观，因为包括sql等其他属性在内的多个属性都没有对应的方法可以直接取到，它们对外部都是封闭的，是对象的私有属性，所以这里就需要引入反射机制来获取或者更改对象的私有属性的值了。对于分页而言，在拦截器里面我们常常还需要做的一个操作就是统计满足当前条件的记录一共有多少，这是通过获取到了原始的Sql语句后，把它改为对应的统计语句再利用Mybatis封装好的参数和设置参数的功能把Sql语句中的参数进行替换，之后再执行查询记录数的Sql语句进行总记录数的统计。

##### 具体实现

 先来看一个我们对分页操作封装的一个实体类Page。

```java
import java.util.HashMap;
import java.util.List;
import java.util.Map;
 
/**
 * 对分页的基本数据进行一个简单的封装
 */
public class Page<T> {
 
    private int pageNo = 1;//页码，默认是第一页
    private int pageSize = 15;//每页显示的记录数，默认是15
    private int totalRecord;//总记录数
    private int totalPage;//总页数
    private List<T> results;//对应的当前页记录
    private Map<String, Object> params = new HashMap<String, Object>();//其他的参数我们把它分装成一个Map对象
 
    public int getPageNo() {
       return pageNo;
    }
 
    public void setPageNo(int pageNo) {
       this.pageNo = pageNo;
    }
 
    public int getPageSize() {
       return pageSize;
    }
 
    public void setPageSize(int pageSize) {
       this.pageSize = pageSize;
    }
 
    public int getTotalRecord() {
       return totalRecord;
    }
 
    public void setTotalRecord(int totalRecord) {
       this.totalRecord = totalRecord;
       //在设置总页数的时候计算出对应的总页数，在下面的三目运算中加法拥有更高的优先级，所以最后可以不加括号。
       int totalPage = totalRecord%pageSize==0 ? totalRecord/pageSize : totalRecord/pageSize + 1;
       this.setTotalPage(totalPage);
    }
 
    public int getTotalPage() {
       return totalPage;
    }
 
    public void setTotalPage(int totalPage) {
       this.totalPage = totalPage;
    }
 
    public List<T> getResults() {
       return results;
    }
 
    public void setResults(List<T> results) {
       this.results = results;
    }
   
    public Map<String, Object> getParams() {
       return params;
    }
   
    public void setParams(Map<String, Object> params) {
       this.params = params;
    }
 
    @Override
    public String toString() {
       StringBuilder builder = new StringBuilder();
       builder.append("Page [pageNo=").append(pageNo).append(", pageSize=")
              .append(pageSize).append(", results=").append(results).append(
                     ", totalPage=").append(totalPage).append(
                     ", totalRecord=").append(totalRecord).append("]");
       return builder.toString();
    }
 
}
```


 对于需要进行分页的Mapper映射，我们会给它传一个Page对象作为参数，我们可以看到Page对象里面包括了一些分页的基本信息，这些信息我们可以在拦截器里面用到，然后我们把除分页的基本信息以外的其他参数用一个Map对象进行包装，这样在Mapper映射语句中的其他参数就可以从Map中取值。


 接着来看一下我们的PageInterceptor的定义。

```java
	package com.tiantian.mybatis.interceptor;
	 
	import java.lang.reflect.Field;
	import java.sql.Connection;
	import java.sql.PreparedStatement;
	import java.sql.ResultSet;
	import java.sql.SQLException;
	import java.util.List;
	import java.util.Properties;
	 
	import org.apache.ibatis.executor.parameter.ParameterHandler;
	import org.apache.ibatis.executor.statement.RoutingStatementHandler;
	import org.apache.ibatis.executor.statement.StatementHandler;
	import org.apache.ibatis.mapping.BoundSql;
	import org.apache.ibatis.mapping.MappedStatement;
	import org.apache.ibatis.mapping.ParameterMapping;
	import org.apache.ibatis.plugin.Interceptor;
	import org.apache.ibatis.plugin.Intercepts;
	import org.apache.ibatis.plugin.Invocation;
	import org.apache.ibatis.plugin.Plugin;
	import org.apache.ibatis.plugin.Signature;
	import org.apache.ibatis.scripting.defaults.DefaultParameterHandler;
	 
	import com.tiantian.mybatis.model.Page;
	 
	/**
	 *
	 * 分页拦截器，用于拦截需要进行分页查询的操作，然后对其进行分页处理。
	 * 利用拦截器实现Mybatis分页的原理：
	 * 要利用JDBC对数据库进行操作就必须要有一个对应的Statement对象，Mybatis在执行Sql语句前就会产生一个包含Sql语句的Statement对象，而且对应的Sql语句
	 * 是在Statement之前产生的，所以我们就可以在它生成Statement之前对用来生成Statement的Sql语句下手。在Mybatis中Statement语句是通过RoutingStatementHandler对象的
	 * prepare方法生成的。所以利用拦截器实现Mybatis分页的一个思路就是拦截StatementHandler接口的prepare方法，然后在拦截器方法中把Sql语句改成对应的分页查询Sql语句，之后再调用
	 * StatementHandler对象的prepare方法，即调用invocation.proceed()。
	 * 对于分页而言，在拦截器里面我们还需要做的一个操作就是统计满足当前条件的记录一共有多少，这是通过获取到了原始的Sql语句后，把它改为对应的统计语句再利用Mybatis封装好的参数和设
	 * 置参数的功能把Sql语句中的参数进行替换，之后再执行查询记录数的Sql语句进行总记录数的统计。
	 *
	 */
	@Intercepts( {
       @Signature(method = "prepare", type = StatementHandler.class, args = {Connection.class}) })
	public class PageInterceptor implements Interceptor {
 
    private String databaseType;//数据库类型，不同的数据库有不同的分页方法
   
    /**
     * 拦截后要执行的方法
     */
    public Object intercept(Invocation invocation) throws Throwable {
       //对于StatementHandler其实只有两个实现类，一个是RoutingStatementHandler，另一个是抽象类BaseStatementHandler，
       //BaseStatementHandler有三个子类，分别是SimpleStatementHandler，PreparedStatementHandler和CallableStatementHandler，
       //SimpleStatementHandler是用于处理Statement的，PreparedStatementHandler是处理PreparedStatement的，而CallableStatementHandler是
       //处理CallableStatement的。Mybatis在进行Sql语句处理的时候都是建立的RoutingStatementHandler，而在RoutingStatementHandler里面拥有一个
       //StatementHandler类型的delegate属性，RoutingStatementHandler会依据Statement的不同建立对应的BaseStatementHandler，即SimpleStatementHandler、
       //PreparedStatementHandler或CallableStatementHandler，在RoutingStatementHandler里面所有StatementHandler接口方法的实现都是调用的delegate对应的方法。
       //我们在PageInterceptor类上已经用@Signature标记了该Interceptor只拦截StatementHandler接口的prepare方法，又因为Mybatis只有在建立RoutingStatementHandler的时候
       //是通过Interceptor的plugin方法进行包裹的，所以我们这里拦截到的目标对象肯定是RoutingStatementHandler对象。
       RoutingStatementHandler handler = (RoutingStatementHandler) invocation.getTarget();
       //通过反射获取到当前RoutingStatementHandler对象的delegate属性
       StatementHandler delegate = (StatementHandler)ReflectUtil.getFieldValue(handler, "delegate");
       //获取到当前StatementHandler的 boundSql，这里不管是调用handler.getBoundSql()还是直接调用delegate.getBoundSql()结果是一样的，因为之前已经说过了
       //RoutingStatementHandler实现的所有StatementHandler接口方法里面都是调用的delegate对应的方法。
       BoundSql boundSql = delegate.getBoundSql();
       //拿到当前绑定Sql的参数对象，就是我们在调用对应的Mapper映射语句时所传入的参数对象
       Object obj = boundSql.getParameterObject();
       //这里我们简单的通过传入的是Page对象就认定它是需要进行分页操作的。
       if (obj instanceof Page<?>) {
           Page<?> page = (Page<?>) obj;
           //通过反射获取delegate父类BaseStatementHandler的mappedStatement属性
           MappedStatement mappedStatement = (MappedStatement)ReflectUtil.getFieldValue(delegate, "mappedStatement");
           //拦截到的prepare方法参数是一个Connection对象
           Connection connection = (Connection)invocation.getArgs()[0];
           //获取当前要执行的Sql语句，也就是我们直接在Mapper映射语句中写的Sql语句
           String sql = boundSql.getSql();
           //给当前的page参数对象设置总记录数
           this.setTotalRecord(page,
                  mappedStatement, connection);
           //获取分页Sql语句
           String pageSql = this.getPageSql(page, sql);
           //利用反射设置当前BoundSql对应的sql属性为我们建立好的分页Sql语句
           ReflectUtil.setFieldValue(boundSql, "sql", pageSql);
       }
       return invocation.proceed();
    }
 
 
    /**
     * 拦截器对应的封装原始对象的方法
     */
    public Object plugin(Object target) {
       return Plugin.wrap(target, this);
    }
 
    /**
     * 设置注册拦截器时设定的属性
     */
    public void setProperties(Properties properties) {
       this.databaseType = properties.getProperty("databaseType");
    }
   
    /**
     * 根据page对象获取对应的分页查询Sql语句，这里只做了两种数据库类型，Mysql和Oracle
     * 其它的数据库都 没有进行分页
     *
     * @param page 分页对象
     * @param sql 原sql语句
     * @return
     */
    private String getPageSql(Page<?> page, String sql) {
       StringBuffer sqlBuffer = new StringBuffer(sql);
       if ("mysql".equalsIgnoreCase(databaseType)) {
           return getMysqlPageSql(page, sqlBuffer);
       } else if ("oracle".equalsIgnoreCase(databaseType)) {
           return getOraclePageSql(page, sqlBuffer);
       }
       return sqlBuffer.toString();
    }
   
    /**
     * 获取Mysql数据库的分页查询语句
     * @param page 分页对象
     * @param sqlBuffer 包含原sql语句的StringBuffer对象
     * @return Mysql数据库分页语句
     */
    private String getMysqlPageSql(Page<?> page, StringBuffer sqlBuffer) {
       //计算第一条记录的位置，Mysql中记录的位置是从0开始的。
       int offset = (page.getPageNo() - 1) * page.getPageSize();
       sqlBuffer.append(" limit ").append(offset).append(",").append(page.getPageSize());
       return sqlBuffer.toString();
    }
   
    /**
     * 获取Oracle数据库的分页查询语句
     * @param page 分页对象
     * @param sqlBuffer 包含原sql语句的StringBuffer对象
     * @return Oracle数据库的分页查询语句
     */
    private String getOraclePageSql(Page<?> page, StringBuffer sqlBuffer) {
       //计算第一条记录的位置，Oracle分页是通过rownum进行的，而rownum是从1开始的
       int offset = (page.getPageNo() - 1) * page.getPageSize() + 1;
       sqlBuffer.insert(0, "select u.*, rownum r from (").append(") u where rownum < ").append(offset + page.getPageSize());
       sqlBuffer.insert(0, "select * from (").append(") where r >= ").append(offset);
       //上面的Sql语句拼接之后大概是这个样子：
       //select * from (select u.*, rownum r from (select * from t_user) u where rownum < 31) where r >= 16
       return sqlBuffer.toString();
    }
   
    /**
     * 给当前的参数对象page设置总记录数
     *
     * @param page Mapper映射语句对应的参数对象
     * @param mappedStatement Mapper映射语句
     * @param connection 当前的数据库连接
     */
    private void setTotalRecord(Page<?> page,
           MappedStatement mappedStatement, Connection connection) {
       //获取对应的BoundSql，这个BoundSql其实跟我们利用StatementHandler获取到的BoundSql是同一个对象。
       //delegate里面的boundSql也是通过mappedStatement.getBoundSql(paramObj)方法获取到的。
       BoundSql boundSql = mappedStatement.getBoundSql(page);
       //获取到我们自己写在Mapper映射语句中对应的Sql语句
       String sql = boundSql.getSql();
       //通过查询Sql语句获取到对应的计算总记录数的sql语句
       String countSql = this.getCountSql(sql);
       //通过BoundSql获取对应的参数映射
       List<ParameterMapping> parameterMappings = boundSql.getParameterMappings();
       //利用Configuration、查询记录数的Sql语句countSql、参数映射关系parameterMappings和参数对象page建立查询记录数对应的BoundSql对象。
       BoundSql countBoundSql = new BoundSql(mappedStatement.getConfiguration(), countSql, parameterMappings, page);
       //通过mappedStatement、参数对象page和BoundSql对象countBoundSql建立一个用于设定参数的ParameterHandler对象
       ParameterHandler parameterHandler = new DefaultParameterHandler(mappedStatement, page, countBoundSql);
       //通过connection建立一个countSql对应的PreparedStatement对象。
       PreparedStatement pstmt = null;
       ResultSet rs = null;
       try {
           pstmt = connection.prepareStatement(countSql);
           //通过parameterHandler给PreparedStatement对象设置参数
           parameterHandler.setParameters(pstmt);
           //之后就是执行获取总记录数的Sql语句和获取结果了。
           rs = pstmt.executeQuery();
           if (rs.next()) {
              int totalRecord = rs.getInt(1);
              //给当前的参数page对象设置总记录数
              page.setTotalRecord(totalRecord);
           }
       } catch (SQLException e) {
           e.printStackTrace();
       } finally {
           try {
              if (rs != null)
                  rs.close();
               if (pstmt != null)
                  pstmt.close();
           } catch (SQLException e) {
              e.printStackTrace();
           }
       }
    }
   
    /**
     * 根据原Sql语句获取对应的查询总记录数的Sql语句
     * @param sql
     * @return
     */
    private String getCountSql(String sql) {
       int index = sql.indexOf("from");
       return "select count(*) " + sql.substring(index);
    }
   
    /**
     * 利用反射进行操作的一个工具类
     *
     */
    private static class ReflectUtil {
       /**
        * 利用反射获取指定对象的指定属性
        * @param obj 目标对象
        * @param fieldName 目标属性
        * @return 目标属性的值
        */
       public static Object getFieldValue(Object obj, String fieldName) {
           Object result = null;
           Field field = ReflectUtil.getField(obj, fieldName);
           if (field != null) {
              field.setAccessible(true);
              try {
                  result = field.get(obj);
              } catch (IllegalArgumentException e) {
                  // TODO Auto-generated catch block
                  e.printStackTrace();
              } catch (IllegalAccessException e) {
                  // TODO Auto-generated catch block
                  e.printStackTrace();
              }
           }
           return result;
       }
      
       /**
        * 利用反射获取指定对象里面的指定属性
        * @param obj 目标对象
        * @param fieldName 目标属性
        * @return 目标字段
        */
       private static Field getField(Object obj, String fieldName) {
           Field field = null;
          for (Class<?> clazz=obj.getClass(); clazz != Object.class; clazz=clazz.getSuperclass()) {
              try {
                  field = clazz.getDeclaredField(fieldName);
                  break;
              } catch (NoSuchFieldException e) {
                  //这里不用做处理，子类没有该字段可能对应的父类有，都没有就返回null。
              }
           }
           return field;
       }
 
       /**
        * 利用反射设置指定对象的指定属性为指定的值
        * @param obj 目标对象
        * @param fieldName 目标属性
         * @param fieldValue 目标值
        */
       public static void setFieldValue(Object obj, String fieldName,
              String fieldValue) {
           Field field = ReflectUtil.getField(obj, fieldName);
           if (field != null) {
              try {
                  field.setAccessible(true);
                  field.set(obj, fieldValue);
              } catch (IllegalArgumentException e) {
                  // TODO Auto-generated catch block
                  e.printStackTrace();
              } catch (IllegalAccessException e) {
                  // TODO Auto-generated catch block
                  e.printStackTrace();
              }
           }
        }
    }
 
}
```

##### mybatis 配置拦截器

```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE configuration
PUBLIC "-//mybatis.org//DTD Config 3.0//EN"
"http://mybatis.org/dtd/mybatis-3-config.dtd">
<configuration>
	<properties resource="config/jdbc.properties"></properties>
	<typeAliases>
	   <package name="com.tiantian.mybatis.model"/>
	</typeAliases>
	<plugins>
	   <plugin interceptor="com.tiantian.mybatis.interceptor.PageInterceptor">
	       <property name="databaseType" value="Oracle"/>
	   </plugin>
	</plugins>
	<environments default="development">
	   <environment id="development">
	       <transactionManager type="JDBC" />
	       <dataSource type="POOLED">
	          <property name="driver" value="${jdbc.driver}" />
	          <property name="url" value="${jdbc.url}" />
	           <property name="username" value="${jdbc.username}" />
	          <property name="password" value="${jdbc.password}" />
	       </dataSource>
	   </environment>
	</environments>
	<mappers>
	   <mapper resource="com/tiantian/mybatis/mapper/UserMapper.xml"/>
	</mappers>
</configuration>
```

##### 测试代码

```java
SqlSession sqlSession = sqlSessionFactory.openSession();
try {
   UserMapper userMapper = sqlSession.getMapper(UserMapper.class);
   Page<User> page = new Page<User>();
   page.setPageNo(2);
   List<User> users = userMapper.findPage(page);
   page.setResults(users);
   System.out.println(page);
} finally {
   sqlSession.close();
}
```

参考:

[MyBatis拦截器动态修改SQL语句及参数值（场景：查询中字段值中特殊字符自动转义）](https://blog.csdn.net/fencer911/article/details/39478877)

[mybatise自定义插件或者叫mybatise拦截器，动态修改sql语句](https://blog.csdn.net/zhongbaolin/article/details/46119583)

[MyBatis源码剖析 - MyBatis 插件之拦截器（Interceptor）实现原理](https://blog.csdn.net/top_code/article/details/55657776)
