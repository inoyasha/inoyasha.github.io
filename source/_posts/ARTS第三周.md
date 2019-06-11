title: ARTS第三周
copyright: true
author: 郭小黑
tags: []
translate_title: arts-three-week
date: 2019-04-06 13:58:00
categories: ARTS
---
### ARTS第三周

> 每周完成一个ARTS： 每周至少做一个 leetcode 的算法题、阅读并点评至少一篇英文技术文章、学习至少一个技术技巧、分享一篇有观点和思考的技术文章。（也就是 Algorithm、Review、Tip、Share 简称ARTS）

![](/images//common/program.jpg)

<!--more-->

#### Algorithm

来自leecode的一个简单的算法题——[回文数](https://leetcode-cn.com/problems/palindrome-number/)。

思路：看到题目的时候，想将找一个数据进行按位数进行拆分成为一个数字，然后对于数组的元素来达到目的。  
```code
  eg:121
  [1,2,1] 数组的长度为3，则比较数组中索引为0/2的数字是否相等。如果是
  数组的长度为5，则需要索引为0/4.1/3的数字是否分别相等。如果为偶数，则仅仅当数组中所有的元素都相同的时候，这个数为回文数。
  
  拆分的代码如下:
  
   public static List<Integer> splitNum(int target) {
        List<Integer> split = new ArrayList<>();
        int temp = target;
        while (temp != 0) {
            int number = temp % 10;
            split.add(number);
            temp = temp / 10;
        }
        return split;
    }
```
在继续的过程中，发现其实可以参考下上周做的算法题——[整数反转](http://localhost:4000/2019/03/31/arts-second-week.html)的思路然后变通下。  
我们发现当整数为负数时，这个数是不可能为回文数的。那么仅仅考虑正整数的情况。当目标数为正整数时，我们可以采用整数反转的形式，将数字反转过来，如果反转过来的数字和目标数字相同，那么这个数就是我们要的回文数。代码如下：


```java

public static boolean isPlindrome(int target) {
        if (target > 0) {
            int reverse = reverse(target);
            return reverse == target;
        }
        return false;
    }

 public static int reverse(int target) {
        long reverse = 0;
        while (target != 0) {
            reverse = 10 * reverse + target % 10;
            target /= 10;
        }
        reverse = (reverse > Integer.MAX_VALUE || reverse < Integer.MIN_VALUE) ? 0 : reverse;
        return (int) reverse;
    }
```

#### Review

这周`review`的内容来自于[Medium](https://medium.com/)的一篇文章——[怎么使用html、css和js简单的构建桌面应用](https://medium.freecodecamp.org/how-to-easily-build-desktop-apps-with-html-css-and-javascript-d3e3f03f95a5)

之前也见到过其他的项目使用nodejs构建桌面的客户端。也一直没有详细的了解过，文中作者一个前端框架——[Electron](https://electronjs.org/),这个框架使用html、css和js构建桌面项目并能够在多个平台上使用。Elactorn分为两个进程进行工作。一个是主进程，一个是渲染进程，主进程只有一个，二渲染进程是存在多个的，并且相互不会影响。

![](/images/ARTS/Elactorn.png)

- 安装npm依赖包。  
`npm install -g electron-forge`
- 初始化应用。初始化的时间比较长，请耐心等待，我就卡在install npm Dependencies 好久.   
`electron-forge init simple-desktop-app-electronjs`

- 启动应用  
```npm
cd simple-desktop-app-electronjs
npm start
```

- 应用打包

 `npm run package -- --platform=<platform> arch=<architecture>`

其他的可以参考Elactron官网了解。——[Elactron](https://electronjs.org/)

#### Tips

Springboot使用多数据源

碰到了一个迁移数据的功能。使用springboot创建多数据源。配置数据源基本信息。

![](/images/ARTS/springboot-milt-data-source-001.png)

` 读取配置信息

```java
import lombok.Data;

@Data
public class DbBean {

    private String driverClassName;

    private String dataUsername;

    private String dataPassword;

    private String url;
}
```

```java
@Component
@ConfigurationProperties(prefix = "cms.content-migrate")
@Data
public class CmsMigrateTables {

    private List<String> tables;
}
```

- 创建多数据源

```java
@Configuration
public class DataSourceBeans {

    private DataSourceConfiguration dataSourceConfiguration;

    @Autowired
    public DataSourceBeans(DataSourceConfiguration dataSourceConfiguration) {
        this.dataSourceConfiguration = dataSourceConfiguration;
    }

    @Bean("sourceDataSource")
    public DataSource sourceDataSource() {
        DbBean sourceDb = dataSourceConfiguration.getSource();
        DataSourceBuilder<?> dataSourceBuilder = DataSourceBuilder.create();
        dataSourceBuilder.username(sourceDb.getDataUsername());
        dataSourceBuilder.password(sourceDb.getDataPassword());
        dataSourceBuilder.driverClassName(sourceDb.getDriverClassName());
        dataSourceBuilder.url(sourceDb.getUrl());
        return dataSourceBuilder.build();
    }


    @Bean("targetDataSource")
    public DataSource targetDataSource() {
        DbBean targetDb = dataSourceConfiguration.getSource();
        DataSourceBuilder<?> dataSourceBuilder = DataSourceBuilder.create();
        dataSourceBuilder.username(targetDb.getDataUsername());
        dataSourceBuilder.password(targetDb.getDataPassword());
        dataSourceBuilder.driverClassName(targetDb.getDriverClassName());
        dataSourceBuilder.url(targetDb.getUrl());
        return dataSourceBuilder.build();
    }
```

获取数据源以后，可以使用jdbcTemplate或者spring-jpa进行数据库查询了。
以上配置基于springboot2.1.4配置。

参考文章:[Spring Boot多数据源配置与使用](http://blog.didispace.com/springbootmultidatasource/)

#### share

分享一个画拓扑图的工具吧。近期有一个画拓扑图的需求。使用图表可以使用echart或者jtopo等工具。后面比较了了一下，根据go.js画拓扑图是比较专业的。go-js的[官网](https://gojs.net/latest/learn/index.html)，如有需要的小伙伴可以参考下。

#### 写到最后的话
这已经我打卡ARTS的第三周了，从接触ARTS到不断学习的过程，内心还是有些愉悦的，但是我做的还不是特别的好，希望在日后我也可以写出高质量的文章，并能够坚持下去，不断前行。加油！