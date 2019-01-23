---
title: jQuery.ajax向后台传递数组问题
date: 2019-01-23 18:16:10
tags: ajax
categories: 前端
---


#### jQuery.ajax向后台传递数组问题

今天重温了一个问题，jQuery.ajax向后台传递一个数组，而在后台接收不到该值 
前台js方法部分代码如下：

<!-- more -->


```javascript
 //创建一个测试数组
    var boxIds = new Array();
    boxIds.push(12182);
    boxIds.push(12183);
    boxIds.push(12184);
    //向后台交互
    $.ajax({
        url: "/xxx",
        type: "GET",
        data: {
            "boxIds": boxIds,
            "boxType": 0,
            "time": new Date().getTime()
        },
        success: function(data) {
            //do sth...
        }
    });
```

##### 后台controller代码（SpringMVC）    

```java
    @ResponseBody
    @RequestMapping(value = "/box/changeLock")
    public String changeLock(final Long[] boxIds, final int boxType) {
        return locker_ChangeLockService.changeLock(boxIds, boxType);
    }
```

##### 观察浏览器中发送的请求，可以发现参数如下

```javascript
    boxIds[]:12182
    boxIds[]:12183
    boxIds[]:12184
    boxType：0
    time：xxx
```

**可以看到，参数的名字为boxIds[]而不是我们所定义的boxIds，也就是说，当传递数组时，会在我们的数组名称后自动加上”[]”，所以后台接不到。**


##### 解决办法：设置jQuery.ajax的tradional属性

```javascript
  $.ajax({
        url: "/xxx",
        type: "GET",
        data: {
            "boxIds": boxIds,
            "boxType": 0,
            "time": new Date().getTime()
        },
        traditional: true,//这里设置为true
        success: function(data) {
            //do sth...
        }
    });
```

**修改之后重新发出请求，观察浏览器**

```javascript
        boxIds:12182
        boxIds:12183
        boxIds:12184
        boxType：0
        time：xxx
```


这次参数不带”[]”了，并且后台可以成功接收到该数组。





