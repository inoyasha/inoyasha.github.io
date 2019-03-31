title: ARTS第二周
copyright: true
categories: ARTS
author: guoxiaohei
date: 2019-03-31 19:00:48
tags:
---
### ARTS第二周

>每周完成一个ARTS： 每周至少做一个 leetcode 的算法题、阅读并点评至少一篇英文技术文章、学习至少一个技术技巧、分享一篇有观点和思考的技术文章。（也就是 Algorithm、Review、Tip、Share 简称ARTS

<!--more-->

#### Algorithm

- 题目来自于leecode的整数反转。
- 给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。
- 解答。  
刚开始本来想着将Integer转成String然后在利用String的反转来做。但是还是利用了java的工具类。而且在处理起来也比较繁琐。并不太好处理越界的情况。
我们知道  
```
123%10=3;
123/10=12;
12%10=2;
12/10=1;
1/10=0;
```
我们每次对10取余数都能获取这个数的最后一位，然后在将目标数除以10获取这个数的n-1位。那我们是不是可以利用这个规律`（3*10 + 2）10 +1=321`来获得我们的结果呢。所以for循环重复上面的操作即可。


具体代码如下：

```
while (target != 0) {
      reverse = 10 * reverse + target % 10;
      target /= 10;
    }
    reverse = (reverse > Integer.MAX_VALUE || reverse < Integer.MIN_VALUE) ? 0 : reverse;
    return (int) reverse;
```

#### Review
review看了一个关于git的一篇文章——如何成为一个git专家。[How to become a Git expert](https://medium.freecodecamp.org/how-to-become-a-git-expert-e7c38bf54826)。作者主要讲解了两个问题。

-  当提交代码时，出错怎么处理。  
比如说当你有6个文件进行提交的时候，但是最后你只是提交了5个，虽然可以在后面重新添加到git上并提交到本地，但是这次的提交看着并不是很优美。这个时候我们可以借助`git commit --amend -m “New commit message”` 来修改这次提交。但是请注意**amend命令适合用于本地的修改，如果在远端仓库上使用，可能会导致代码的紊乱。**
-  当提交记录毕竟混乱的时候，应该如何处理。  
我们知道git是一个版本控制工具，大部分的时候都是多人进行提交代码，当你有一个需求，已经确定大概需要10天左右才能完成，而在这个期间会有其他的人提交代码。保持远端和本地库的做法是一个比较好的方式，这能减少代码合并引起的冲突。作者在后续的内容中主要讲解了如何使用`rebase`命令来解决这个问题。具体的提交流程作者使用一个小例子进行了讲解。
- 思考。  
在我看来`rebase`命令其实构建了`git-flow`的一个流程。国内大部分企业使用git的时候都可能在使用这个流程。详细可以看下这篇文章——[git-flow 的工作流程](https://www.git-tower.com/learn/git/ebook/cn/command-line/advanced-topics/git-flow)。rebase能让代码的提交流程看着更加清晰。并且在分支合并的时候可以进行coding的review过程。


#### Tips

使用shell 脚本执行orientdb etl工具.  
orientdb 有自己的etl工具。它是通过sh 脚本来进行数据迁移的。orientdb进行抽取的时候，大部分的使用场景还是全量迁移数据，但是当数据量比较大的时候，全量的迁移就会影响应用系统的正常使用。在每次进行抽取的时候，现将抽取时间放到一个文件中，然后执行orientdb脚本的时候，将时间通过`sed -i "s/xx/xx" filepath`的形式，将对应的json文件中的sql查询进行替换，查询增量数据，执行完抽取后，将最新的时间更新到配置文件中。  
此次使用了sh 脚本读取配置文件的配置
```sh
采用了eval的方式读取
while read line;do
    eval "$line"
done < config
```
`config`是读取文件的路径. 
其他的读取方式请参考——[linux shell 读取配置文件](https://blog.csdn.net/xinfuqizao/article/details/21812003)


#### Share

此次分享说下我在我的Centos7环境上配置maven引发的问题。

- 场景。  
Centos上搭建idea环境，配置maven环境变量。在/etc/profile文件中添加了`MAVEM_HOME=/opt/xxx/maven/` 然后配置到了$PATH上，并执行了`source /etc/profile`命令，让其生效，当时试了一下`mvn -v`，好用了，觉得大功告成了。这为了后面的悲剧做了铺垫。
- 出现的问题。
当我将电脑重启了以后，发现使用我的账户登录以后，使用终端显示`-base-4.1$`这样的显示，切换成root账户登录的时候显示正常。
- 解决。  
后来谷歌查询了一下有多个方式可以解决这个事情。
1、centos在创建新用户的时候，会将/etc/skel/下面的配置复制到新创建用户的配置中取，这个问题的出现是.bash等文件丢失引起的，将文件重新复制回来即可。
2、`vi ~/.bash_profile ` 然后往文件中添加如下内容`export PS1=’[\u@\h \W]\$’ `  

- 出现的原因。  
但是配置maven的时候在maven的配置目录多配置了一个`/`，导致Centos的环境变量出现了紊乱导致。  
重新复制了配置以后，在将环境变量多余的`/`去除掉。问题解决。