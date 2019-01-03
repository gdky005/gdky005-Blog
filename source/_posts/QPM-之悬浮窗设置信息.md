title: QPM 之悬浮窗设置信息
date: 2019-01-03 15:22:18
categories: QPM
keywords: QPM, 性能优化, 组件
tags: QPM, 性能优化, 组件
---
QPM 开源地址：[https://github.com/ZhuoKeTeam/QPM](https://github.com/ZhuoKeTeam/QPM)

更多实用信息：
1. 手机的基本信息
2. AndroidManifest.xml 信息
3. App 中所有的 SharePreference 信息
4. 可配置的开关
5. 网络接口

## 手机基础信息

1. 再也不用 去手机的复杂界面查看各种数据；
2. 再也不用 下载 辅助性 apk 获取信息；
3. 再也不用 因为某些信息没有，查询半天。

是否 Root, SDK 版本，手机型号，网络，名称，IP，Mac 地址，屏幕分辨率，CPU 架构等等信息。遇到关键的数据，还能复制。


这里获取的数据更全面
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/qpm/qpm_2.png)

## AndroidManifest.xml 信息 

包名，版本号，App 的所有权限，构建 SDK 的版本信息，还有最最重要的注册的四大组件（Activity，Service，Receiver，Provider）。里面的 Activity 可以直接点击后跳转，Service可以查看有多少本地服务，Receiver 可以很明确的知道当前注册了多少广播，Provider 可以查看本地的内容提供者。


## 应用的所有 SP 信息

Root 手机我们直接通过 文件管理器 可以直接查看 SP 文件。

如果没有 Root 呢？ 笨办法，通过调试代码或者 log 打印输出。

包含整个 App 的所有 SP 信息，可以查看单个 SP 里面的信息，最最好的是还能直接修改 对应的 Value。

极大提升程序员们的开发效率。 

## 其他开关

我们提供的了这些基础功能，打开开关后，可以直接在悬浮窗展示相关数据信息。

自我控制聚焦点，只关注需要的信息。

所有的开关，可以打开，关闭，对于某些影响性能的操作，可以关闭其他所有的东西，保留关注的指标。

每一个开关都可以长按开关名称的这一条，上下移动位置，调整开关的顺序。


## 网络接口

获取最近50条网络请求数据，可以查看更多信息：
1. 请求方式；
2. 返回状态码；
3. 请求时长；
4. 请求大小；
5. 返回数据大小 

需要 OkHttp，然后可以获取网络请求的所有数据，包括请求 Request Header，Request Response，Response等数据。


## 精简模式

关注的数据太多会占满屏幕，可以开启精简模式，默认显示开关列表最顶部的两个选项。开关列表可以通过拖动把选项位置移动到想要的前两项。

