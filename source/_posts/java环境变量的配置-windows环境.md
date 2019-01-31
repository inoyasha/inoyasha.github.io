title: java环境变量的配置(windows环境)
author: 郭小黑
tags:
  - 配置
categories:
  - java
date: 2019-01-25 12:56:00
---
![](/images/common/program.jpg)

#### 引言

这里介绍如果在windows环境配置java环境变量。

<!-- more -->

#### windows配置java环境变量

- 官网安装java jdk

- 配置环境变量

  1.新建系统环境变量<code>JAVA_HOME</code>。 我的电脑-属性-高级系统设置-环境变量-新建系统变量。
  
  ```code
  JAVA_HOME=JDK安装路径
  ```
  
  2、新建系统环境变量。<code>CLASSPATH </code>
  
  ```code
  // 配置类库文件的位置
  CLASSPATH = .;%JAVA_HOME%\lib;%JAVA_HOME%\jre\lib;
  ```
  3、编辑PATH变量。在PATH的最前面配置添加下面一句话。
  
  ```code
  //JDK 命令文件的位置
  %JAVA_HOME%\bin;%JAVA_HOME%\jre\bin;
  ```
  
- 检验配置是否成功。

&nbsp;&nbsp;&nbsp;&nbsp;使用以下命令检验配置是否成功。

```command
javac
java -version
```