---
title: ARTS第一周
copyright: true
categories: ARTS
author: guoxiaohei
translate_title: arts-first-week
date: 2019-03-23 20:07:00
tags:
---
### ARTS第一周

>每周完成一个ARTS： 每周至少做一个 leetcode 的算法题、阅读并点评至少一篇英文技术文章、学习至少一个技术技巧、分享一篇有观点和思考的技术文章。（也就是 Algorithm、Review、Tip、Share 简称ARTS）

<!--more-->

#### Algorithm

- 题目  
给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。
你可以假设每种输入只会对应一个答案。但是，你不能重复利用这个数组中同样的元素。
- 作答  
使用双层for循环，将两个数组对应的元素进行相加，如果结果等于目标值，则获取对应的数组下标。
```code
class Solution {
    public int[] twoSum(int[] nums, int target) {
         int len = nums.length;
        for (int i = 0; i < len; i++) {
          for (int j = 0; j < i + 1; j++) {
            if (nums[i] + nums[j] == target && i != j) {
              return new int[]{i, j};
            }
          }
        }
        return null;
    }
}
```

#### Review

[Spring5.0官方文档](https://docs.spring.io/spring/docs/5.0.5.RELEASE/spring-framework-reference/web.html#mvc)

之前在使用spring时，遇到问题直接百度/googole查询资料，有的时候找到资料的时候就借鉴使用了，也没有深究其中的原理。趁着ARTS的机会，阅读了一下spring5.0的官方文档。

- Spring Web MVC 的名字来源于spring项目中的第一个模块 spring-webmvc,更多被熟知为"Spring MVC"。
- 于Spring Web MVC并行的Spring WebFlux的web框架也是来源于源代码模块中的spring-webflux。
- DispatcherServlet servlet分发器。它和其他的web框架一样，被设计为一个围绕前端控制器的一个主要的servlet，它为请求处理提供了分享算法，但是实际的工作是由委托的组建所完成，它相当于一个枢纽，当请求来了以后告诉这个请求应该谁去处理，然后当请求完成以后，应该交由那个模块进行解析。
- DispatcherServlet的注册方式有两种，一种是使用java代码进行注册，一种是使用web.xml进行注册。

#### Tips

这周在处理问题的时候，需要在将本地服务器的文件上传到其他的服务器上。
使用两种方式来处理文件的上传，一种是带有返回值的处理，二另一种只返回http请求状态码。
        
- 带有返回值的文件上传  



```
  /**
     * 上传文件
     * @param serverUrl 请求地址
     * @param file      上传的文件
     * @param params    参数
     * @return
     */
    public static String uploadFileByPost(String serverUrl, File file,
            Map<String, String> params) throws IOException {
        String respStr = null;
        CloseableHttpClient httpclient = HttpClients.createDefault();
        try {
            HttpPost httppost = new HttpPost(serverUrl);
            // 获取bin文件
            FileBody binFileBody = new FileBody(file);
            MultipartEntityBuilder multipartEntityBuilder = MultipartEntityBuilder
                    .create();
            multipartEntityBuilder
                    .setMode(HttpMultipartMode.BROWSER_COMPATIBLE);
            multipartEntityBuilder.setCharset(Consts.UTF_8);
            multipartEntityBuilder.addPart(file.getName(), binFileBody);
            HttpEntity reqEntity = multipartEntityBuilder.build();
            httppost.setEntity(reqEntity);
            CloseableHttpResponse response = httpclient.execute(httppost);
            HttpEntity resEntity = response.getEntity();
            if (Objects.nonNull(resEntity)) {
                respStr = EntityUtils
                        .toString(resEntity, Charset.forName("UTF-8"));
                EntityUtils.consume(resEntity);
            }
        } catch (ClientProtocolException e) {
            logger.error("上传文件失败,client 协议异常");
            throw e;
        } catch (IOException e) {
            logger.error("上传文件失败,IO异常");
            throw e;
        } catch (NullPointerException ex) {
            logger.error("接口返回异常");
            throw ex;
        } finally {
            try {
                httpclient.close();
            } catch (IOException e) {
                logger.error("关闭httpClient异常", e);
            }
        }
        return respStr;
    }
 ```
 
 只返回状态值的上传
 
 ```
   /**
     * 上传文件使用httppost请求
     * @param file 文件
     * @param params 参数
     * @return
     */
    public static int uploadByPost(String url, File file,
            Map<String, String> params) {
        PostMethod filePost = new PostMethod(url);
        HttpClient client = new HttpClient();
        if (Objects.nonNull(params)) {
            params.forEach((k, v) -> {
                filePost.setParameter(k, v);
            });
        }
        try {
            Part[] parts = { new FilePart(file.getName(), file) };
            filePost.setRequestEntity(
                    new MultipartRequestEntity(parts, filePost.getParams()));
            client.getHttpConnectionManager().getParams()
                    .setConnectionTimeout(5000);
            int status = client.executeMethod(filePost);
            if (status == HttpStatus.SC_OK) {
                logger.debug("上传文件成功!");
            } else {
                logger.debug("上传文件失败!");
            }
            return status;
        } catch (FileNotFoundException e) {
            logger.error("上传文件没有找到!", e);
        } catch (HttpException e) {
            logger.error("网络连接异常!", e);
        } catch (IOException e) {
            logger.error("IO 异常", e);
        } finally {
            filePost.releaseConnection();
        }
        return 0;
    }
``` 
 
 #### share
 
 很久以前的一个需求，需要在ueditor(1.3.6版本上)添加一个一键排版的功能，具体的思路是点击排版按钮的时候ueditor的选取文字按照一个固定的样式进行格式化。其实就是编写ueditor的一个过滤规则。具体可以参考：[ueditor1.3.6 扩展插件(一键排版)](https://www.guoxiaohei.cn/2019/01/23/ueditor1-3-6-%E6%89%A9%E5%B1%95%E6%8F%92%E4%BB%B6-%E4%B8%80%E9%94%AE%E6%8E%92%E7%89%88/)