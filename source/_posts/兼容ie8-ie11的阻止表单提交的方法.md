title: 兼容ie8-ie11的阻止表单提交的方法
tags:
  - javascript
categories:
  - 前端
author: 郭小黑
copyright: true
date: 2019-01-23 19:31:00
---


#### 业务场景，当满足条件的时候，才允许提交from表单。

javascript有两种方法可以阻止表单默认提交。

<!-- more -->

- return false

绑定表单的onclick方法，当条件不满足返回<code>false </code>

```javascript
<form name="loginForm" action="login.aspx" method="post">
  <button type="submit" value="Submit" id="submit">Submit</button>
 </form>
 
 <script>
  var submitBtn = document.getElementById("submit");
 
  submitBtn.onclick = function (event) {
   alert("preventDefault!");
   return false;
  };
 </script>
 ```

 - 使用preventDefalut()方法。

 在标准浏览器中，阻止浏览器默认行为使用<code>event.preventDefalt()</code>,而在IE6~8，使用
 <code>returnValue</code>属性来实现。

```javascript
<form name="loginForm" action="login.aspx" method="post">
 <button type="submit" value="Submit" id="submit">Submit</button>
</form>

<script>
 var submitBtn = document.getElementById("submit");

 submitBtn.onclick = function (event) {
  window.event.returnValue = false; // 兼容IE6~8
  // 需要判断浏览器是否支持默认行为，兼容谷歌等 ie9-11的浏览器
  if (window.event.preventDefault) {
     window.event.preventDefault();
  }
 };
</script>
```


