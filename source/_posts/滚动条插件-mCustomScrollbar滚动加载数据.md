title: 滚动条插件 mCustomScrollbar滚动加载数据
tags:
  - mCustomScrollbar
categories:
  - 前端
author: 郭小黑
date: 2019-01-23 18:26:00
---


#### 滚动条插件 mCustomScrollbar滚动加载数据

 
[mCustomScrollbar滚动条插件地址](http://manos.malihu.gr/jquery-custom-content-scroller/)

##### 引入资源文件

```html
<link rel="stylesheet" href="/path/to/jquery.mCustomScrollbar.css" />
<script src="http://ajax.googleapis.com/ajax/libs/jquery/1.11.1/jquery.min.js"></script>
<script src="/path/to/jquery.mCustomScrollbar.concat.min.js"></script>
```

<!-- more -->

##### html文件调用

```html
<div class="classname mCustomScrollbar" data-mcs-theme="minimal-dark">
</div>
```

其中 classname 是你自己给这个 div 一个样式，比如设置 div 高度等，minimal-dark 是它其中一个样式，可以在 mCustomScrollbar.css 中修改它的样式，比如滚动条的宽度和颜色等。

如果要实现滚动获取数据，就不能直接在 html 里直接调用，需要在<script></script>标签里调用它。

```javascript
<script type="text/javascript">
    $(".classname").mCustomScrollbar({
          theme:"minimal-dark"
     });
</script>
```
这个和上面 html 直接调用效果是一样的。


##### 使用它的一个回调函数 whileScrolling

```javascript
$(".classname").mCustomScrollbar({
　　theme: 'minimal-dark',
　　callbacks: {
　　　　whileScrolling: function(){      　// 只要滚动条滚动，这个函数就会执行
　　　　　　if (this.mcs.topPct >= 90) {    // 这表示当滚动条滚动到这个div的90%(当然这个值是可变的)的时候调用下面的代码，
　　　　　　　　$.ajax({
　　　　　　　　　　// 用ajax去后台获取数据，并把数据添加到这个div里
　　　　　　　　})
　　　　　　}
　　　　}
　　}
})
```

转载：[用 mCustomScrollbar 滚动条插件实现滚动更新添加数据](http://www.cnblogs.com/LY-leo/p/5750059.html)
