title: springboot2.0版本构建单元测试
author: 郭小黑
tags:
  - springboot
  - 单元测试
categories: []
date: 2019-02-02 17:48:00
---

![](/images/spring.jpg)
#### 前言

 我们在coding的时候，都要对自己的coding进行测试，而对于开发有效的测试不外乎是单元测试了。以往的单元测试我们通过加载配置文件进行测试，但是这样相当于启动了整个项目，于是出现了mock的测试方式。对于mock测试优缺点，可以参考这篇文章——[
Mock测试-优缺点分析](https://blog.csdn.net/weixin_42343424/article/details/80564365)。下面我来介绍下springboot2.0采用mock测试的方法。

<!--more-->

#### 使用mock的形式测试controller

- 带参数的接口  

以下为demo代码

```java
package com.guoyupeng.restapi;

import com.guoyupeng.response.ResponseObject;
import io.swagger.annotations.Api;
import io.swagger.annotations.ApiImplicitParam;
import io.swagger.annotations.ApiImplicitParams;
import io.swagger.annotations.ApiOperation;
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

/**
 * Description: restful controller
 *
 * @author guoyupeng [2018/11/29]
 */
@RestController
@RequestMapping("/api")
@Api(tags = "rest api demo")
public class RestApi {

    /**
     * api demo
     *
     * @return
     */
    @ApiOperation("rest api demo")
    @ApiImplicitParams({@ApiImplicitParam(value = "params", name = "params", required = false)})
    @GetMapping("v1/demo")
    public ResponseObject apiDemo(@RequestParam(name = "params", required = false) String params) {
        log.debug("rest api demo");
        return new ResponseObject("rest api demo");
    }

    /**
     * rest api log
     */
    private Logger log = LoggerFactory.getLogger(RestApi.class);

}
```

- 带参数的接口  

下面编写两个方法分别测试传入参数testApiDemoWithParams和没有参数apiDemoNoParam的测试

```java
package com.guoyupeng.restapi;

import org.junit.Assert;
import org.junit.Before;
import org.junit.Test;
import org.junit.runner.RunWith;
import org.springframework.boot.test.context.SpringBootTest;
import org.springframework.mock.web.MockServletContext;
import org.springframework.test.context.junit4.SpringJUnit4ClassRunner;
import org.springframework.test.context.web.WebAppConfiguration;
import org.springframework.test.web.servlet.MockMvc;
import org.springframework.test.web.servlet.MvcResult;
import org.springframework.test.web.servlet.RequestBuilder;
import org.springframework.test.web.servlet.ResultActions;
import org.springframework.test.web.servlet.request.MockHttpServletRequestBuilder;
import org.springframework.test.web.servlet.request.MockMvcRequestBuilders;
import org.springframework.test.web.servlet.result.MockMvcResultMatchers;
import org.springframework.test.web.servlet.setup.MockMvcBuilders;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;

/**
 * Description: rest api demo test
 *
 * @author guoyupeng [2018/11/29]
 */
@RunWith(SpringJUnit4ClassRunner.class)
@SpringBootTest(classes = MockServletContext.class)
@WebAppConfiguration
public class RestApiTest {

    private MockMvc mockMvc;

    /**
     * 单元测试启动初始化
     */
    @Before
    public void setUp() {
        mockMvc = MockMvcBuilders.standaloneSetup(new RestApi()).build();
    }

    /**
     * 测试api demo 不传入参数的形式
     *
     * @throws Exception
     */
    @Test
    public void apiDemoNoParam() throws Exception {
        //第一种无参的方式
        RequestBuilder requestBuilder = null;
        requestBuilder = MockMvcRequestBuilders.get("/api/v1/demo");
        mockMvc.perform(requestBuilder).andExpect(MockMvcResultMatchers.status().isOk())
                .andExpect(MockMvcResultMatchers.content().json("{\"message\":\"rest api demo\"}"));

        // 第二种无参的方式
        MvcResult mvcResult = mockMvc.perform(requestBuilder).andReturn();
        int status = mvcResult.getResponse().getStatus();
        String resultContent = mvcResult.getResponse().getContentAsString();
        Assert.assertEquals(status, 200);
        Assert.assertEquals(resultContent, "{\"message\":\"rest api demo\"}");
    }

    /**
     * 传入参数的demo
     */
    @Test
    public void testApiDemoWithParams() throws Exception {

        MockHttpServletRequestBuilder requestBuilder = null;
        requestBuilder = get("/api/v1/demo").param("param","111");
        MvcResult result = mockMvc.perform(requestBuilder).andReturn();
        int status = result.getResponse().getStatus();
        String resultJson = result.getResponse().getContentAsString();
        Assert.assertEquals(status, 200);
        Assert.assertEquals(resultJson, "{\"message\":\"rest api demo\"}");
    }

}
```

**ps** 以上代码是我实际测试过的，欢迎大家积极吐槽。


