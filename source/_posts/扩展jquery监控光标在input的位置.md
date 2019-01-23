---
title: 扩展jquery监控光标在input的位置
date: 2019-01-23 19:44:02
tags: jquery
categories: 前端
---

#### 扩展jquery监控光标在input的位置

- 业务场景 。

当需要监控光标所在的位置的时候

- jquery实现。

```javascript
//监控光标在input框的位置
(function ($, undefined) {  
        $.fn.getCursorPosition = function () {  
            var el = $(this).get(0);  
            var pos = 0;  
            if ('selectionStart' in el) {  
                pos = el.selectionStart;  
            } else if ('selection' in document) {  
                el.focus();  
                var Sel = document.selection.createRange();  
                var SelLength = document.selection.createRange().text.length;  
                Sel.moveStart('character', -el.value.length);  
                pos = Sel.text.length - SelLength;  
            }  
            return pos;  
        }  
 })(jQuery);
```
