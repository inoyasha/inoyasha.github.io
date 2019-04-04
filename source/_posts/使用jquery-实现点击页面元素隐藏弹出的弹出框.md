---
title: 使用jquery 实现点击页面元素隐藏弹出的弹出框
tags:
  - jquery
categories:
  - 前端
author: 郭小黑
translate_title: using-jquery-to-hide-pop-up-boxes-by-clicking-on-page-elements
date: 2019-01-23 17:41:00
---

#### 使用jquery 点击页面doucment元素，隐藏弹出框 

```javascript
    $(document).mouseup(
		function(a) {
			if (!$(a.target).isChildAndSelfOf(".dateTimePicker")
					&&!$(a.target).isChildAndSelfOf("#shield")
					&&!$(a.target).isChildAndSelfOf("#alertFram")) {
				$(".timeBox").hide();
			}
			
		});
	// 判断当前元素是否是被筛选元素的子元素或者本身
	jQuery.fn.isChildAndSelfOf = function(b) {
		return (this.closest(b).length > 0);
	};
```
