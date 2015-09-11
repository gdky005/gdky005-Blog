title: hexo使用指南
date: 2015-09-11 10:28:06
tags: hexo使用
---


超快速度

- Node.js 所带来的超快生成速度，让上百个页面在几秒内瞬间完成渲染。

支持 Markdown

- Hexo 支持 GitHub Flavored Markdown 的所有功能，甚至可以整合 Octopress 的大多数插件。

丰富的插件

- Hexo 拥有强大的插件系统，安装插件可以让 Hexo 支持 Jade, CoffeeScript。


```

$ hexo new [layout] <title> #建立新文章，默认在_posts下，layout="draft"时发布的是草稿
$ hexo publish <filename> #将_drafts下的文件放到_posts下，也就是发布草稿
$ hexo generate #生成静态网页
$ hexo server #启动预览服务器，开启-d选项时可以预览草稿
$ hexo deploy #发布到远程服务器，开启--generate选项可以在deploy前自动generate



$ hexo n  # == hexo new
$ hexo p  # == hexo publish 
$ hexo g  # == hexo generate
$ hexo s  # == hexo server
$ hexo d  # == hexo deploy
```


### 写文章

和Octopress完全一样，使用Markdown语法编辑文章。
使用hexo new命令生成文章或者直接在_posts目录下直接创建文件，打开后先编辑文章头部信息，如下所示是本文的头部信息，以---结尾。

title: 使用Hexo搭建个人博客
layout: post
date: 2014-03-03 19:07:43
comments: true
categories: Blog
tags: [Hexo]
keywords: Hexo, Blog

	description: 生命在于折腾，又把博客折腾到Hexo了。给Hexo点赞。
	
接下来使用Markdown编辑文章就可以了。





我想发表一篇文章，怎么做呢？

	hexo new "my new post"
	在H:\hexo\source\_posts中打开这个文件（打开方式用“记事本”即可），配置开头。
	
```
title: my new post #可以改成中文的，如“新文章”
date: 2013-05-29 07:56:29 #发表日期，一般不改动
categories: blog #文章文类
tags: [博客，文章] #文章标签，多于一项时用这种格式
---
```

hexo server，访问localhost:4000预览效果。（退出server用Ctrl+c）
hexo deploy，同步到github。访问网站看看效果。
现在为止，我们已经搭建起博客，进行一些基本配置，并学会了怎么发表文章。后面会陆续介绍一些高级点的个性化设置，不过在此之前，你可以正常发表博客了。后面的文章你可以不看，有意者请跟随。