title: ARTS第四周
copyright: true
author: 郭小黑
tags: []
translate_title: arts-fourth-week
categories: ARTS
date: 2019-04-14 23:00:39
---
### ARTS第四周
> 每周完成一个ARTS： 每周至少做一个 leetcode 的算法题、阅读并点评至少一篇英文技术文章、学习至少一个技术技巧、分享一篇有观点和思考的技术文章。（也就是 Algorithm、Review、Tip、Share 简称ARTS）


![](/images/common/program.jpg)

<!--more-->

#### Algorithm

来自leecode的一个简单的算法题——[罗马数转整数](https://leetcode-cn.com/problems/roman-to-integer/)。

- 解题思路  
先用罗马数字的字母作为key值放到一个map中，然后将目标罗马数按照`""`分解成一个字符串数组。遍历字符串数组，根据key获取对应的值进行相加，当满足一下条件时:
![](/images/ARTS/arts-ranma-to-integer.png)
对其减去其相差值的2倍即可。

**以上的做法，需要逻辑判断6次。代码看着不是很优雅。后面看了下评论，发现其实是解题没有充分思考。**

>通常情况下，罗马数字中小的数字在大的数字的右边。除了以上几种情况，如果当前字符代表的值不小于其右边，就加上该值；否则就减去该值。

下面给出两种的代码，具体请看我的[github](https://github.com/inoyasha/leecode/blob/master/roman-to-integer/src/com/guoxiaohei/RomanToInteger.java).

#### Review

This week costs a lot of time to read a article that introduct some new frameworks to follow in 2019 and expression a lot of review to this.  
- react.js
- vue.js
- jquery
- kotlin
- Elixir and Phoenix.
- GraphQL

In fact,i understand this technology a smaller part.Because of useing these less.but i approval view of author.
>As a developer, it is important to stay up to date with current technological developments as much as possible. 

憋了半天，还是词穷。我们在工作之外还是要了解一些更新在我们身边的技术，了解其利弊，对于日后的工作可能也会有帮助，它能开阔我们的眼界，当我们遇到技术难题时，也可能帮我们更好的扩展思路。但是其实主要的还是基础知识的把握，因为无论是哪种前端还是后端的技术，都离不开基础的东西。比如说vue.js 我觉得还是依赖于j原生的javascript。所以我们了解新技术，但是也要补充自己的基础知识，这样才能在以后的竞争中站的更稳。

#### Tips

最近在看极客时间的视频课程——玩转Git三剑客。里面有一些小技巧。  

- 配置最小配置。  
```code
git config --global user.emal xxx@xxx.com
git config --glabal user.name xxx
```  
- git 重命名  
```
git rename xx xxx.text
```

- git 将新文件提交到暂存区  
```
git add 新文件
```

- git 将修改的文件,删除的文件添加到暂存区
```code
 git add -u 
```
- git 有三个对象  
1、commit  （每次提交会生成一个commit对象。是一个uuid）  
2、tree  （类似文件目录树）  
3、blob  （具体的文件存储）




#### Share

分享下最近糟糕的经历。最近leader让我负责一个项目，并把控项目的进度，说实话这个是我第一次把控项目进度。我先使用project(第一次使用)做了一下
简单的工期计划，在我预想中，这个工期是可以完成的。但是后续发生了一些不可控的问题。其中一个人没怎么写过页面，但是考虑到页面比较简单，我把这项工作交给他，做到后面的效果和实际的效果差距较大。并且没有及时和领导反馈。导致后面的工作要加班来完成。现在对于这个事情还是比较迷茫，感觉没有抓住重点，所以先记录下并总结几点。

- 当遇到问题的时候，及时和领导反馈，让其协调其他人员。不要让不可控因素持续下去。
- 做好规划，并尽量详尽的给出可能的
