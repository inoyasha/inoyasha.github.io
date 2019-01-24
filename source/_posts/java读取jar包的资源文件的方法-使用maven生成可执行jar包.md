---
title: java读取jar包的资源文件的方法(使用maven生成可执行jar包)
date: 2019-01-24 15:14:23
tags: maven打包问题
categories: javaweb
---

#### 引言

使用ide软件可以读取配置文件的时候采用<code>ClassLoad.getResouce("/").getPath()方法</code>读取配置文件，当将项目通过maven打成可执行包以后，在包里面读取不到配置文件。

<!-- more -->

#### 解决办法

使用<code>getResourceAsStream()方法来直接获取InputStream对象，不通过文件路径获取。获取完inputstream对象，再将其转成BufferReader，否则后面load的时候还出抛出异常。</code>


#### 代码

```java
public Properties loadProperties(){
        InputStream ips = ImportHelper.class.getResourceAsStream("/config.properties");  
        Properties properties = new Properties();
        BufferedReader br = null;
        try {
            br = new BufferedReader(new InputStreamReader(ips, Constant.UNICODE));
            properties.load(br);
        } catch (FileNotFoundException e) {
            logger.error("文件找不到，请检查配置文件config.properties 是否存在!");
        } catch (IOException e) {
            logger.error(e.getMessage());
        }
        return properties;
    }
```
