---
title: SpringBoot 配置详解
date: 2019-01-23 15:19:46
tags: springboot
categories: java web
---

- 自定义属性配置
- 自定义文件配置
- 多环境化配置
- 外部命令引导
- 总结
- 说点什么

<!--more-->

> SpringBoot 是为了简化 Spring 应用的创建、运行、调试、部署等一系列问题而诞生的产物，自动装配的特性让我们可以更好的关注业务本身而不是外部的XML配置，我们只需遵循规范，引入相关的依赖就可以轻易的搭建出一个 WEB 工程。

> 为了让 Spring Boot 更好的生成配置元数据文件，我们需要添加如下依赖（该依赖可以不添加，但是在 IDEA 和 STS 中不会有属性提示，没有提示的配置就跟你用记事本写代码一样苦逼，出个问题弄哭你去），该依赖只会在编译时调用，所以不用担心会对生产造成影响…

    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-configuration-processor</artifactId>
        <optional>true</optional>
    </dependency>
    
##### 自定义属性配置

    demo:
      name:  1111
      age: 1111
      
      
      
##### Myproperties实现

注意:在使用没有properties时要注意读取配置文件属性的类，需要有getter和setter方法，否则读取不到配置文件中的属性值。<code>原因：属性类要生成getter和setter函数，否则映射不成功。</code>

使用可以参考：https://blog.csdn.net/yingxiake/article/details/51263071

     package com.example.demo.properties;

    import org.springframework.boot.context.properties.ConfigurationProperties;
    import org.springframework.stereotype.Component;
    
    /**
     * Description:
     *
     * @author guoyupeng [2018/11/22]
     */
    @Component
    @ConfigurationProperties(prefix = "demo")
    public class MyProperties {
    
    
        private int age;
    
        private String name;
    
        @Override
        public String toString() {
            return "name" + this.name +"\n" + "age"+age
                    ;
        }
    
        public int getAge() {
            return age;
        }
    
        public void setAge(int age) {
            this.age = age;
        }
    
        public String getName() {
            return name;
        }
    
        public void setName(String name) {
            this.name = name;
        }
    }


Controller调用

    package com.example.demo.controller;
    
    import com.example.demo.properties.MyProperties;
    import org.slf4j.Logger;
    import org.slf4j.LoggerFactory;
    import org.springframework.beans.factory.annotation.Autowired;
    import org.springframework.web.bind.annotation.GetMapping;
    import org.springframework.web.bind.annotation.RequestMapping;
    import org.springframework.web.bind.annotation.RestController;
    
    /**
     * Description:
     *
     * @author guoyupeng [2018/11/22]
     */
    @RequestMapping("properties")
    @RestController
    public class MyPropertiesController {
    
    
        private Logger logger = LoggerFactory.getLogger(getClass());
    
        private MyProperties myProperties;
    
        @Autowired
        public void setMyProperties(MyProperties myProperties) {
            this.myProperties = myProperties;
        }
    
        @GetMapping("/1")
        public MyProperties myProperties(){
            logger.info("===========================");
            logger.info(myProperties.toString());
            logger.info("============================");
            return myProperties;
        }
    }
    
##### 自定义文件配置
在读取配置文件的类中添加如下注解

    @PropertySource("classpath:my2.properties")
    
    
##### 多环境化配置

在真实的应用中，常常会有多个环境（**如：开发，测试，生产等**），不同的环境数据库连接都不一样，这个时候就需要用到<code>spring.profile.active</code> 的强大功能了，它的格式为 <code>application-{profile}.properties</code>，这里的 <code>application</code> 为前缀不能改，<code>{profile}</code> 是我们自己定义的。

> application-dev.properties

    server.servlet.context-path=/dev
>application-test.properties

    server.servlet.context-path=/test
    
>application-prod.properties

    server.servlet.context-path=/prod



    
[唐亚峰博客](http://blog.battcn.com/2018/04/22/springboot/v2-config-properties/)

[springboot配置例子](https://www.mkyong.com/spring-boot/spring-boot-configurationproperties-example/)