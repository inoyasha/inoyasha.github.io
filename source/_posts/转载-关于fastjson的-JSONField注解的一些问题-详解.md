---
title: '转载:关于fastjson的@JSONField注解的一些问题(详解)'
author: 郭小黑
tags: []
categories:
  - java web
copyright: false
translate_title: 'reprint:-some-questions-about-the-@jsonfield-annotation-of-fastjson-(detailed)'
date: 2019-02-02 19:32:00
---
#### 引言

在`java web`项目中，我们接受的前台参数和后台定义的参数不一致的时候，需要我们将字段进行转换，`fastjson`中有一个`@JsonField`注解，能够将字段属性和数据库列进行转换。下面说明一下关于`fastjson`中`@JSONFiled`注解的一些问题。

<!--more-->

#### 关于fastjson的@JSONField注解的一些问题(详解)

`@JSONField` 看源码它可以作用于字段和方法上。

#####  作用Field

`@JSONField`作用在`Field`时，其`name`不仅定义了输入key的名称，同时也定义了输出的名称。

但是我在使用中，发现并不如上所说。
```java
@JSONField(name="project_id") private Long ProjectID
```
发现`bean` 转`json`的时候并是`"project_id":xxx`的形式，`json`转`bean`的时候也不会把`"project_id":xx`的内容设置到`ProjectID`的里面。对应的fastjson的版本是1.1.15。

##### 作用在setter和getter方法上 这种方式倒是在使用的过程当中符合期望。

```java
/**bean 转json 时会把bean中的ProjectID转换为project_id */
  @JSONField(name="project_id")
  public Long getProjectID() {
    return ProjectID;
  }


/**json 转bean 时会把json中的project_id值赋值给projectID*/
  @JSONField(name="project_id")
  public void setProjectID(Long projectID) {
    ProjectID = projectID;
  }
```
##### @JSONField其它用法

查看@JSONField注解的源码，除了name可用之外，还有format，serialize，deserialize，serialzeFeatures，parseFeatures可用。

- format，貌似用在Date类型的字段来格式化时间格式比较有用。
- serialize和deserialize是布尔类型的，用法为

```java
@JSONField(serialize=false) 
private Long ProjectID
```

就是在序列化的时候就不包含这个字段了。deserialize与之相反。但是有一点需要注意，我看其它地方说，当字段为final的时候注解放在字段上是不起作用的，这时候应该放在get
或set方法上。

- serialzeFeatures.  
我用到这个属性，fastjson默认的序列化规则是当你的字段的值为null的时候，它是不会给你序列化这个字段的，例如我有一个这样的需求。

```json
{"fieldName":"project_id","operator":"is not","value":null}
```

一个对象序列化成这样，我的代码如下

```java
	CriteriaVO criteriaVO = new CriteriaVO();
    criteriaVO.setFieldName("project_id");
    criteriaVO.setOperator("is not");
    criteriaVO.setValue(null);
```

默认的它只会序列化为如下结果

```json
{"fieldName":"project_id","operator":"is not"}
```

当然fastjson还是允许你控制一下序列化的规则的。

这就用到了SerializerFeature，这个一个枚举，里面有好几个值 ，具体的含义大家有兴趣的可以了解一下,我只是用到了其中一个。

```java
@JSONField(serialzeFeatures=SerializerFeature.WriteMapNullValue)
private String value;
```

这样当value的值为null的时候，依然会把它的值序列化出来。也就是下面的样子，这就是我想要的结果

```json
{"fieldName":"project_id","operator":"is not","value":null}`
```

又遇到了另一个问题，当字段类型为int类型时，如

```java
private int start;
private int limit;
```

我如果不set值的时候，会序列化为下面这样

```java
"limit":0,"start":0
```

默认为都是0了，而我的目标是如果不设置值的时候，它们不会出现。

我是简单地通过把他们的类型改为Integer了。应该有其它通过自定义序列化行为的方式来解决，暂不研究。

原文转载: [关于fastjson的@JSONField注解的一些问题(详解)](https://www.jb51.net/article/105906.htm)






