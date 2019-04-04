---
title: 自定义alert弹窗
tags:
  - javascript
categories:
  - 前端
author: 郭小黑
translate_title: custom-alert-bullet-window
date: 2019-01-23 19:23:00
---

#### 使用javascript自定义alert弹窗

当我们退出的时候需要弹出一个弹出框，询问用户是否退出。如果使用javascript的confirm弹窗，样式不太美观，所以采用了下面的形式更改了系统默认的弹窗。


<!-- more -->

下面是具体的实现

```javascript
 <!DOCTYPE html>
    <html lang="zh-cn">
    <head>
    <meta charset="UTF-8">
    <title>教你如何更改网页的默认alert弹窗</title>
    </head>
    <body>
    <!-- 更改系统默认弹窗 -->
    <script type="text/javascript">
    window.alert = function(str)
    {
        var shield = document.createElement("DIV");
        shield.id = "shield";
        shield.style.position = "absolute";
        shield.style.left = "50%";
        shield.style.top = "50%";
        shield.style.width = "280px";
        shield.style.height = "150px";
        shield.style.marginLeft = "-140px";
        shield.style.marginTop = "-110px";
        shield.style.zIndex = "25";
        var alertFram = document.createElement("DIV");
        alertFram.id="alertFram";
        alertFram.style.position = "absolute";
        alertFram.style.width = "280px";
        alertFram.style.height = "150px";
        alertFram.style.left = "50%";
        alertFram.style.top = "50%";
        alertFram.style.marginLeft = "-140px";
        alertFram.style.marginTop = "-110px";
        alertFram.style.textAlign = "center";
        alertFram.style.lineHeight = "150px";
        alertFram.style.zIndex = "300";
        strHtml = "<ul style=\"list-style:none;margin:0px;padding:0px;width:100%\">\n";
        strHtml += " <li style=\"background:#2f5b7b;text-align:left;padding-left:20px;font-size:14px;font-weight:bold;height:40px;line-height:40px;border:1px solid #2f5b7b;color:white\">[自定义alert]</li>\n";
        strHtml += " <li style=\"background:#183042;text-align:center;font-size:15px;height:95px;line-height:95px;border-left:1px solid #183042;border-right:1px solid #183042;color:#ffffff\">"+str+"</li>\n";
        strHtml += " <li style=\"background:#183042;text-align:center;font-weight:bold;height:40px;line-height:40px; border:1px solid #183042;\"><input type=\"button\" value=\"确 定\" onclick=\"doOk()\" style=\"width:80px;height:28px;background:#3ea1fa;color:white;border:1px solid #3ea1fa;font-size:14px;line-height:27px;outline:none;\"/></li>\n";
        strHtml += "</ul>\n";
        alertFram.innerHTML = strHtml;
        document.body.appendChild(alertFram);
        document.body.appendChild(shield);
        this.doOk = function(){
            alertFram.style.display = "none";
            shield.style.display = "none";
        }
        alertFram.focus();
        document.body.onselectstart = function(){return false;};
    }
    </script>
    <script>
    // 测试 alert
    alert('自定义弹窗');
    </script>
    <button onclick="javascript:alert('dddd')">弹出</button>
    </body>
    </html>
```