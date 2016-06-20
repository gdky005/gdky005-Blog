title: 使用 Android Studio 内存分析工具
date: 2016-06-20 18:33:33
categories: AndroidStudio
keywords: AndroidStudio
tags: AndroidStudio
---


### App的内存使用可以通过以下三个工具分析： 
- Heap Viewer
- Memory Monitor
- Allocation Tracker
并且，这个三个是互补的可视化内存分析工具。

1. 使用 Memory Monitor 可以查找非正常的 GC 导致的性能问题。
2. 运行 Heap Viewer可以确认出 object 类型是不是 不必要的，或者分配的内存超出我们的预期估计，可能是持续增长，我们预期是在不需要的时候，可以回收内存。
3. 使用 Allocation Tracker 可以确定出你的代码中存在的问题。

### Memory Monitor
![](http://7xlcno.com1.z0.glb.clouddn.com/AS_memory_01.png)
- 显示你的 App 当前某一时刻的内存状态（包括可用内存，和占用内存）的曲线图，用电压跌落 的方式展示 Garbage Collection (GC) 的现象。
- 可以方便的看出 app 的卡顿是否是由于频繁 GC（内存 频繁回收，一个 app 在手机上分配的内存是一定的，但手机厂商决定手机内存的大小，因此当我们的 app 的内存达到一定的峰值的时候，系统就会回收内存，保证 App的正常运行） 导致。
- 提供一种快速看出App 是否是由于 运行内存不足 而导致的 crash(崩溃)的方法。
- 当前运行的app ，每一秒更新占用内存的状况。
- 有助于识别潜在的内存泄漏。
- 有助于识别 App 的 GC模式,并确定它们是否正常和你所希望的样子。
- 它很容易使用，并且很容易看明白
- 然而，Memory Monitor 不能告诉你哪些对象是有问题的，或者给你指出代码中存在的问题

### Heap Viewer
![Allocation Tracker](http://7xlcno.com1.z0.glb.clouddn.com/AS_memory_02.png)
- 根据类型 分配许多对象 的快照图。
- 每一次的样本数据 是自动采集或者你手动触发。
- 帮助确定哪些对象类型可能是内存泄漏。
- 然而，你 **必须自己寻找** 在一段时间内曲线图的变化发送了什么。 （重点，需要**采集两次以上**的数据进行**对比**，才能明确，出问题的地方在哪里）
### Allocation Tracker
[![](http://7xlcno.com1.z0.glb.clouddn.com/AS_memory_03.png)](# "Allocation Tracker")
- 显示你的代码在在一定的时间内分配的 object 类型，Object 的大小，分配的线程和堆栈的大小
- 帮助识别 内存的改变是通过 循环分配或重新分配。
- 可以结合使用 Heap Viewer 来跟踪内存泄露，例如，如果你看到了一个 bitmap 对象 在 heap 上分配的大小。你可以使用 Allocation Tracker 找到它分配的位置。
- 然而，它需要时间和经验来学习理解这个工具的使用。

