title: 升级android 7.1一些经历
date: 2017-03-02 10:56:48
categories: android
keywords: android升级，华为P6
tags: android
---


我一直用的测试机是华为 nexus P6, 7.0出来的时候，刷了一个 7.0 玩，感觉还不错，有些你在 7.0之下的版本绝不会出现的问题，出现了好多。

当7.1 出现的时候，本来不想升级，但是 android 组的 leader 让我把这个 P6 升级为 7.1。为了让测试的性能组测试 vovi 新机器适配。想着刷就刷呗，应该没啥问题。

可悲的事情就来了，因为我的这个 P6升级到 7.0后就 root 了，刷了 twrp 启动方式。结果直接挂代理后，升级 7.1 后就悲剧了，机子死活开不了机。

通过 [http://www.pixcn.cn/thread-23317-1-1.html](http://www.pixcn.cn/thread-23317-1-1.html) ，找到了一些解决方案。因为是我的手机 刷了 rec 启动，所以直接用系统自带的更新包就不行。在官网 [https://developers.google.com/android/images](https://developers.google.com/android/images) 下载了 Factory Images 镜像，只能用线刷。

把 这个镜像通过 rec 连接电脑，用命令行放入 手机SD 卡中，直接刷总是失败，必须用线刷啊，线刷啊，线刷啊。

在折腾的很无奈的时候，灵光一闪，进入 rec 之前，尝试用 adb 连接手机，可是就是 连接不上。 最后直接尝试 fastXXX ,没想到 adb shell 不行，但是 电脑上解压 zip 后， 里面的 fast 命令可直接使用。很顺利的就完成了刷机，进入了 7.1 系统。

在7.0 的时候就一直很郁闷，wifi 连接成功后，在顶部的 wifi 状态标识上面总是有一个 小感叹号。一直没解决，没想到 7.1 直接变成了 小 『x』, 在群里讨论后，一个哥们说可以去掉，我尝试百度了下，用一下命令真就去掉了。 追查原因，说是 google 会去检测 wifi 是否安全，来显示那个 感叹号和小『x』。
 
adb输入命令：

 	adb shell settings put global captive_portal_detection_enabled 0
 	
完成后开启飞行模式即可

