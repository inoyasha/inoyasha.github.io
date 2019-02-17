title: SourceTree 免登录跳过初始设置（转载）
copyright: false
author: 郭小黑
tags: []
categories:
  - git
date: 2019-02-16 08:24:00
---
使用git的时候，有很多的界面管理工具，下面推荐一个比较好用git管理工具——SourceTree.

<!--more-->

#### 下载地址

[SourceTree官网](https://www.sourcetreeapp.com/)

#### SourceTree免登陆的方法

![](/images/git/sourceTree.png)

SourceTree 安装之后需要使用账号登陆以授权，以前是可以不登陆的，但是现在是强制登陆。

虽然是免费授权，但是碰上不可抗力因素，登陆不是很方便，这里记录一下跳过这个初始化的步骤。

##### 新建accounts.json文件

`%LocalAppData%\Atlassian\SourceTree\`

找到以上目录，查看是否存在`accounts.json`文件，如果不存在则新建。文件的内容如下

```json
[
  {
    "$id": "1",
    "$type": "SourceTree.Api.Host.Identity.Model.IdentityAccount, SourceTree.Api.Host.Identity",
    "Authenticate": true,
    "HostInstance": {
      "$id": "2",
      "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountInstance, SourceTree.Host.AtlassianAccount",
      "Host": {
        "$id": "3",
        "$type": "SourceTree.Host.Atlassianaccount.AtlassianAccountHost, SourceTree.Host.AtlassianAccount",
        "Id": "atlassian account"
      },
      "BaseUrl": "https://id.atlassian.com/"
    },
    "Credentials": {
      "$id": "4",
      "$type": "SourceTree.Model.BasicAuthCredentials, SourceTree.Api.Account",
      "Username": "",
      "Email": null
    },
    "IsDefault": false
  }
]
```
文章来源:[SourceTree 免登录跳过初始设置](https://www.cnblogs.com/xiofee/p/sourcetree_pass_initialization_setup.html)