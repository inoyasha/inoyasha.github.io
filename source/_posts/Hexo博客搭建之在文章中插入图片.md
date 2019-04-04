---
title: Hexo博客搭建之在文章中插入图片(转载)
author: 郭小黑
tags:
  - hexo
categories:
  - 静态博客
translate_title: the-hexo-blog-is-built-to-insert-pictures-in-the-article-(reprint)
date: 2019-01-25 13:52:00
---
在写文章时，常常有配图说明的需求。Hexo有多种图片插入方式，可以将图片存放在本地引用或者将图片放在CDN上引用。

<!-- more -->


#### 本地引用

##### 绝对路径

当Hexo项目中只用到少量图片时，可以将图片统一放在source/images文件夹中，通过markdown语法访问它们。

```markdown
![](/images/image.jpg)
```
图片既可以在首页内容中访问到，也可以在文章正文中访问到。

###### 相对路径

图片除了可以放在统一的images文件夹中，还可以放在文章自己的目录中。文章的目录可以通过配置_config.yml来生成。


```markdown
post_asset_folder: true
```
将<code>_config.yml</code>文件中的配置项<code>post_asset_folder</code>设为<code>true</code>后，执行命令<code>$ hexo new post_name</code>，在<code>source/_posts</code>中会生成文章<code>post_name.md</code>和同名文件夹<code>post_name</code>。将图片资源放在<code>post_name</code>中，文章就可以使用相对路径引用图片资源了。

```markdown
![](image.jpg)
```
上述是markdown的引用方式，图片只能在文章中显示，但无法在首页中正常显示。

如果希望图片在文章和首页中同时显示，可以使用标签插件语法。

```hexo
{% asset_img image.jpg This is an image %}
```

#### CDN引用
除了在本地存储图片，还可以将图片上传到一些免费的CDN服务中。比如[Cloudinary]()提供的图片CDN服务，在Cloudinary中上传图片后，会生成对应的url地址，将地址直接拿来引用即可。

原文地址:

[Hexo博客搭建之在文章中插入图片](https://yanyinhong.github.io/2017/05/02/How-to-insert-image-in-hexo-post/)
