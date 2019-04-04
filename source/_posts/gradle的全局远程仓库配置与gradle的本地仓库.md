---
title: gradle的全局远程仓库配置与gradle的本地仓库
tags:
  - gradle
categories:
  - 构建工具
author: 郭小黑
translate_title: global-remote-warehouse-configuration-of-gradle-and-local-warehouse-of-gradle
date: 2019-01-24 15:44:00
---


#### 引言

构建，软件生命周期中重要的一环，在现代软件开发过程中，起着越来越重要的作用。现在的构建工具有ant、maven、gradle等。Gradle，一个基于Groovy，更灵活更强大的构建系统，能帮助我们构建更复杂的项目。

<!-- more -->


#### 配置远程仓库地址

在C:\Users\用户名.gradle的目录下创建文件init.gradle并添加一下文字，使用阿里云的仓库地址。

```code
allprojects{
  repositories {
    def REPOSITORY_URL = 'http://maven.aliyun.com/nexus/content/groups/public/'
      all { ArtifactRepository repo ->
        if(repo instanceof MavenArtifactRepository){
          def url = repo.url.toString()
          if (url.startsWith('https://repo1.maven.org/maven2') || url.startsWith('https://jcenter.bintray.com/')) {
            project.logger.lifecycle "Repository ${repo.url} replaced by $REPOSITORY_URL."
            remove repo
          }
       }
    }
    maven {
      url REPOSITORY_URL
    }
  }
}
```

#### 配置本地gradle仓库地址

- 设置环境变量：

```code
GRADLE_USER_HOME=已有的且要设为gradle本地仓库的本地目录
```
- 例子

```code
GRADLE_USER_HOME=D:\我的代码\LibRepo\GradleRepo
```

将文件目录<code>D:\我的代码\LibRepo\GradleRepo</code> 射程本地电脑上的gradle默认的本地仓库，然后执行gradle相关命令，相关缓存的文件将会默认存储在这个目录下。





