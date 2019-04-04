---
title: 转载：几种copyProperties工具类性能比较
author: 郭小黑
tags:
  - 工具类
  - copyProperties
categories: []
translate_title: 'reprint:-performance-comparison-of-several-copyproperties-tooling-classes'
date: 2019-02-02 18:23:00
---

#### 前言

在`coding`过程中，难免遇到对象的属性复制，往往我们想到的都是使用`apahce`的`BeanUtils`的`corpProperties`来进行复制，但是它的性能并不仅人意。下面我们来看下几种`copyProperties`工具类的性能比较。

<!--more-->

#### 市面上的其他几种属性copy工具

- springframework的BeanUtils
- cglib的BeanCopier
- Apache BeanUtils包的PropertyUtils类

由于apache BeanUtils的性能存在问题

所以使用上面的集中工具来代替

工具名称 | 执行1000次耗时 | 10000次 | 100000次|10000000次
---|---|---|---|---
Apache BeanUtils | 390ms | 854ms| 1763ms | 8408ms
Apache PropertyUtils | 26ms|221ms|352ms|2663ms
spring BeanUtils | 39ms| 315ms|373ms|949ms
Cglib BeanCopier | 64ms | 144ms	|171ms|309ms

#### 结论:

1. Apache BeanUtils的性能最差,不建议使用。
1. Apache PropertyUtils100000次以内性能还能接受,到百万级别性能就比较差了,可酌情考虑。
1. spring BeanUtils和BeanCopier性能较好,如果对性能有特别要求,可使用BeanCopier,不然spring BeanUtils也是可取的。


详细的解析过程请到远文章查看:[几种copyProperties工具类性能比较](https://www.jianshu.com/p/bcbacab3b89e)

