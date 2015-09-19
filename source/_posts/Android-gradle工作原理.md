title: Android_gradle工作原理
date: 2015-09-13 12:47:49
tags: android gradle工作原理
---

在最新的Android Studio 里面，Android 的标准工作方式是使用 gradle 构建的，目前也是非常流行的（*不过也存在一些 eclipse 目录方式的，不太好，能用gradle 构建最好，可以使用依赖等快速开发模式*）。


### gradle 构建流程：

下图是google官方的构建流程图

![andoroid gradle工作原理图][1]

### 简单可以分为以下几类：

1. AndroidManifest.xml 合并
2. Resource (包含应用的 string, style, drawable, .png, .jpg, .xml)合并
3. Asserts 合并

**以上是常用的，当然还有其他合并，不过对于一般用户都没有问题。剩下的，基本和 Android 的构建基本一致。**






---
[1]: http://i1.tietuku.com/4fa0701d09e5704a.png