title: Android 和 IOS 应用查找bug 利器
date: 2015-09-26 15:12:50
categories:
keywords:
tags: android
---

[来源: Trinea codeKK][3]


查找bug 的神器： [bugtags][1]



快速反馈 App Bug 的工具 Bugtags，任何人随时随地一键提交 Bug。<br><br><br>


		  Bugtags是国内首款为改善移动产品质量而专门打造的测试平台产品。使用Bugtags平台可以随时随地对移动产品提出准确的改善意见，使得测试更简单，修复问题更轻松，产品用户满意度更高。
		 
		 




![][2]

### 它在移动端是的主要功能包括：

1. 任何地方快速标记 Bug，分配给对应开发
跟他们官网说的一样，测试从未如此简单！

2. 自动记录 App Crash Bug
App Crash 时会自动提交日志到后台，这个原理也是大家熟知的 Thread.UncaughtExceptionHandler。

3. 无网下次重传

4. 摇一摇作为彩蛋
Bugtags 启动模式有三种，除了截图中的悬浮球，还有摇一摇提交 Bug，可以作为 Release 版的彩蛋。




### 传统的 App Bug 发现过程是这样的：

> 测试的妹子们截图传到 PC，再上传到 Bug 管理系统，添加具体描述，分配给相应开发

> 产品经理跑过来“这个这个地方有问题，你改下”

> 老板拿着手机过来，直接扔给你，立即调试这个“老板级”Bug


### 使用方式：
1. 在 [bugtags][1] 里面集成开发对应的SDK，
2. 添加依赖
	`compile 'com.bugtags.library:bugtags-lib:latest.integration'`
3. 在 Application 初始化

```	

public class MyApplication extends Application {
　@Override
　public void onCreate() {
　　super.onCreate();

　　// 在这里初始化
　　Bugtags.start("App key", this, Bugtags.BTGInvocationEventBubble);
　}
}

```

### 添加回调

```
public class BaseActivity extends Activity{
　@Override
　protected void onResume() {
　　super.onResume();
　　// 注：回调 1
　　Bugtags.onResume(this);
　}
　@Override
　protected void onPause() {
　　super.onPause();
　　// 注：回调 2
　　Bugtags.onPause(this);
　}
　@Override
　public boolean dispatchTouchEvent(MotionEvent event) {
　　// 注：回调 3
　　Bugtags.onDispatchTouchEvent(this, event);
　　return super.dispatchTouchEvent(event);
　}
}

```

如果没有 BaseActivity，可新建此类作为所有 Activity 的基类，统一的 BaseActivity 在开发中还是很有必要的。

PS：MIUI 需要手动打开悬浮窗的显示，打开方式：安全中心-> 授权管理->应用权限管理，点击选择应用，勾选“显示悬浮窗”开关。

<br>

<br>

<br>

###### 目前他们也在计划开放 API，方便其他系统接入。





---
[1]: https://bugtags.com
[2]: http://mmbiz.qpic.cn/mmbiz/KfDGLcEiauQjMjV5yhOicj2pQm6m7nJsiarkheNAVo58icUrJzj3y1CojAocibU7jXe0xxlhck079iarhYrZ7Iqg2v7Q/0?wx_fmt=gif&tp=webp&wxfrom=5&wx_lazy=1

[3]: http://mp.weixin.qq.com/s?__biz=MzAxNjI3MDkzOQ==&mid=207368806&idx=1&sn=ab1203e49d15b5a81a30d3c199fdc80d&scene=1&srcid=0926RMXXJe3DfggnLTgzVz8M&key=2877d24f51fa5384040fd32a20a0f93b7082eb623889c7a2011c660a4a0d9f50a45893beadd645cda8dba79b97cead62&ascene=0&uin=MTA5NTgyNjA0MA%3D%3D&devicetype=iMac+MacBookPro12%2C1+OSX+OSX+10.10.5+build(14F27)&version=11020012&pass_ticket=DiIrR6XymKM2K2hxm76gAWEBFZQ9pdsonmtfjw3IgzG9PNaJi00dUtdbVQgTUwir