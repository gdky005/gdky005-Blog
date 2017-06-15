title: 徽章系列1： Top 30 android 开源项目徽章
date: 2017-06-15 18:05:56
categories: shields
keywords: 徽章
tags: 徽章
---


我们尝试在 github 里面搜索 以 android  关键字 开发语言为 java 的开源项目。
![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-1.png)
统计情况如下：

1. Retrofit 0
2. okhttp 0
3. Butter Knife 0
4. MPAndroidChart 4 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-2.png)
5. Android-Universal-Image-Loader 2 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-3.png)
6. glide 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-4.png)
7. leakcanary 0 
8. EventBus 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-5.png)
9. picasso 0 
10. zxing 3 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-6.png)
11. iosched 0
12. Fresco 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-7.png)
13. lottie-android 0
14. RxAndroid 3 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-8.png)
15. libgdx 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-9.png)
16. SlidingMenu 0
17. PhotoView 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-10.png)
18. android-async-http 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-11.png)
19. material-dialogs 5 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-12.png)
20. AndroidUtilCode 0
21. androidannotations 3 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-13.png)
22. Material-Animations 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-14.png)
23. fastjson 5 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-15.png)
24. ViewPagerIndicator 0 
25. plaid 0 
26. PocketHub 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-16.png)
27. tinker 4 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-17.png)
28. Android-CleanArchitecture 2  ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-18.png) ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-19.png)
29. Android-PullToRefresh 0 
30. MaterialDesignLibrary 1 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-20.png)

我们是筛选容器就是 30个，统计日期：2017年04月27日（随着时间推移，可能略微有变动）。使用标签的有 18个，未使用的有12个。 使用概率大约是：60%。如果筛选容器的范围再大一点可能更多，没有使用徽章的12个项目，可能由于历史原因，或者个人原因未使用，但是不管怎么说，使用徽章的人会越来越多。



### 为什么要使用徽章？
徽章 [shields](https://github.com/badges/shields)

徽章的使用不仅仅是为了装 B，而是为了让开源想更高效。进入项目主页一眼能看出需要的东西，例如该项目能否编译通过，当前最新的版本是什么等。

徽章能突出视野，github 默认给我们展示的是黑白世界，但是通过 徽章，将会得到改变。重要的信息可以一目了然。

### 最常用徽章有哪些？
一份不太靠谱的标准，不过你应该掌握:
![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-21.png)
（PS: 上图中的图标顺序对应下面的顺序）

1. jitpack 徽章：[JitPack](https://jitpack.io) 是一个仓库，类似 maven，binary, 主要是帮我们生产 android 项目的 aar or jar 的平台。基于 GitHub，可以很方便将 library 发布到远程，然后可以用 gradle 依赖到任何一个项目中。
2. travis-ci 徽章：[Travis-CI](https://travis-ci.org) 是一个线托管的CI服务，不需要自己搭服务器，在网页上点几下就好，用起来更方便。最重要的是，它对开源项目是免费的。
3. circle-ci 徽章：[Cricle-CI](https://circleci.com/) 是也一个线托管的CI服务，和上面相同，不过这个相对来说比较好用一些，可以 SSH 到测试容器，方便在出问题的时候上去调试找原因，界面相对好看一些。
4. codecov 徽章：[Codecov](https://codecov.io) 是开源的测试结果展示平台，将测试结果可视化。Github上许多开源项目都使用了Codecov来展示单测结果。
5. api level 徽章：[Api-Leavel](https://android-arsenal.com/api) 是 android-arsenal 网站提供的 android api 展示的徽章。可以在项目主页中直接使用 badge 的内容。 
6. codacy 徽章：编程代码自动审查服务平台。帮助开发者及时发现代码中的 bug，提升软件运行质量，主要包括代码质量、语法规范、功能可用性方面的检查。
7. 个人专属 徽章：[shiedls](https://shields.io/) 根据自己的需求可以定制很多样式的徽章，全凭个人 爱好，怎么开心怎么玩。


