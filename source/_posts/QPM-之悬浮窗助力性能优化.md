title: QPM 之悬浮窗助力性能优化
date: 2019-01-03 15:21:18
categories: QPM
keywords: QPM, 性能优化, 组件
tags: QPM, 性能优化, 组件
---
QPM 开源地址：[https://github.com/ZhuoKeTeam/QPM](https://github.com/ZhuoKeTeam/QPM)


让我们来尝试做一些可以可视化的悬浮窗功能吧，里面可以展示一些基础的性能指标数据。

启动 App 后便可以看到一些数据，解决未 root 手机无法获取数据的疑难杂症。

![](https://raw.githubusercontent.com/gdky005/PictureResource/master/qpm/qpm_1.jpg)

### 包名
一个 apk 会有一个固定的包名，但是在某些特殊场景下，却会展示多个包名，例如：测试包，正式包，变种包，推送测试包等等，给 QPM 展示当前应用的包名，在某些时候可以方便我们定位问题。举个例子：我们之前一直在测试推送包，有时候需要切换到正式包，在两个包中切换各种RD，ST环境，最后我都不记得我用的什么包，只能卸载了，重新安装。QPM 的悬浮窗可以直接展示当前应用的包名，看一眼就知道了，其实也可以把当前进程+线程号打印出来，方便开发同学分析问题。

### 当前 Activity 的名字

试想，做了5年的项目，交给新来你接手？或者同事离职，丢下一堆坑，需要你来填坑。根据代码梳理流程后，也不一定能立刻接手，如果根据页面找Activity，一个字————累！

如果能直接展示当前界面的 Activity 名字，是不是更容易一些呢？ 

### CPU 和 内存

界面怎么这么卡啊，快优化下。懵逼的你可能会想这要从哪里入手？先从界面渲染，还是从业务角度? 关键是我们需要知道在页面的什么场景下会出现问题，有一个直观指标就容易判断了。当 CPU 到达 200% 的时候，内存剧增，那肯定有问题，可以用性能工具对该页面详细的分析。 一般先看看在该界面的 CPU 和 内存是否异常，再结合业务逻辑把相关的数据提前或者延迟获取，减少同一时刻并发获取，从而减少主界面卡顿。

### 线程数

这是什么鬼？还记得曾经的老大说要复用线程，别单独搞么。如果你发现 200 多个线程，那你就得考虑下是否需要线程池了。这里可以依据现有逻辑来处理，并非绝对性的。

### Activity 堆栈

还记得刚学 Activity 那会儿么，Activity的 四种 LaunchMode，这里可以记录一个栈里面的 Activity 的顺序。方便你直观了解栈中的情况。

### 流量

我们 App 的请求用了多少流量？ 可能在 3G/4G 关注点比较多，虽然现在绝多数都是 WIFI，但是我们的用户在一定环境下会使用 3G/4G, 所以还是又必须关注下。

网络情况如何？ 比方说我用的是 Wifi, 在某些角落网速很差，甚至没流量数据，我们都希望可以了解。

在某个时刻，页面是空白的？为什么没有数据呢，可以看看尝试看看下载速度。

尤其对现在约来越多的某些小视频，大家可能会关心大约用了多少流量。

### 屏幕录制

基于 Android 5.0 的 API，录制整个屏幕，方便大家复现某些关于操作记录的问题。

### 监控 H5 页面

需要配合相应的设置，我们就可以在 WebView 中对任何一个网页进行异步检测，例如获取当前页面地址，首页白屏加载时间，以及每个资源的请求时间，和请求资源地址。非常容易。

### 自定义的五种对外样式

以下的一个唯一标识，表示一个 item, 如果要添加多个，可以把唯一标示设置为不同的。

- 大文件框样式

QPMManager.getInstance().showBigText(flag, bigText);
第一个参数 flag 是唯一标示, 第二个 bigText 是自定义悬浮窗中显示的所有文本数据。

- 键值对文本样式

QPMManager.getInstance().showKeyValue(flag, key, value);
第一个参数 flag 是唯一标示, 第二个 key 是自定义悬浮窗中显示的 key 值，第三个是 悬浮窗中的 value 值。

- 键图样式

QPMManager.getInstance().showKeyPic(flag, key, picRes);
第一个参数 flag 是唯一标示, 第二个 key 是自定义悬浮窗中显示的 key 值，第三个是 悬浮窗中的 pic Res 中的资源值。

- 图值样式

QPMManager.getInstance().showPicValue(flag, picId, value);
第一个参数 flag 是唯一标示, 第二个 key 是自定义悬浮窗中显示的 key 值，第三个是 悬浮窗中的 pic Res 中的资源值 (可以放到你们的主 App 中)。


- 自定义样式

QPMManager.getInstance().showCustom(flag,QPMTemplateCustomRenderer);
第一个参数 flag 是唯一标示, 第二个 QPMTemplateCustomRenderer 是自定义悬浮窗中你们要自己添加的布局，可以写一个类，继承自QPMTemplateCustomRenderer，实现里面的方法，悬浮窗上就可以显示对应的内容。




