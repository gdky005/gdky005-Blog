title: >-
  android 6.0 权限崩溃分析：android.system.ErrnoException: statvfs failed: ENOENT (No
  such file or directory)
date: 2017-05-03 11:36:44
categories: android
keywords: android
tags: android
---



### 问题复现
在某些 6.0 设备的设备上 程序因为使用了 app 的外置 SD 卡的私有目录，在没有 存储权限的情况下，会崩溃，出现： 

	05-03 09:53:02.337 W/System.err: java.lang.IllegalArgumentException: Invalid path: /storage/emulated/0/Android/data/gdky005.testexception/files
	05-03 09:53:02.356 W/System.err:     at android.os.StatFs.doStat(StatFs.java:46)
	05-03 09:53:02.370 W/System.err:     at android.os.StatFs.<init>(StatFs.java:39)
	05-03 09:53:02.386 W/System.err:     at gdky005.testexception.SDCardUtil.getSDAvailableBlocks(SDCardUtil.java:166)
	05-03 09:53:02.401 W/System.err:     at gdky005.testexception.MainActivity.testSDCard(MainActivity.java:69)
	05-03 09:53:02.410 W/System.err:     at gdky005.testexception.MainActivity$1.onClick(MainActivity.java:26)
	05-03 09:53:02.412 D/DhcpClient: Received packet: f4:5c:89:c2:6d:7b NAK, reason (none)
	05-03 09:53:02.417 W/System.err:     at android.view.View.performClick(View.java:5637)
	05-03 09:53:02.425 W/System.err:     at android.view.View$PerformClick.run(View.java:22429)
	05-03 09:53:02.431 W/System.err:     at android.os.Handler.handleCallback(Handler.java:751)
	05-03 09:53:02.435 W/System.err:     at android.os.Handler.dispatchMessage(Handler.java:95)
	05-03 09:53:02.439 W/System.err:     at android.os.Looper.loop(Looper.java:154)
	05-03 09:53:02.442 W/System.err:     at android.app.ActivityThread.main(ActivityThread.java:6119)
	05-03 09:53:02.445 W/System.err:     at java.lang.reflect.Method.invoke(Native Method)
	05-03 09:53:02.449 W/System.err:     at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:886)
	05-03 09:53:02.452 W/System.err:     at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:776)
	05-03 09:53:02.461 W/System.err: Caused by: android.system.ErrnoException: statvfs failed: ENOENT (No such file or directory)
	05-03 09:53:02.463 W/System.err:     at libcore.io.Posix.statvfs(Native Method)
	05-03 09:53:02.466 W/System.err:     at libcore.io.BlockGuardOs.statvfs(BlockGuardOs.java:304)
	05-03 09:53:02.469 W/System.err:     at android.system.Os.statvfs(Os.java:506)
	05-03 09:53:02.471 W/System.err:     at android.os.StatFs.doStat(StatFs.java:44)
	05-03 09:53:02.472 W/System.err: 	... 13 more

具体崩溃路径是：

	Invalid path: /storage/emulated/0/Android/data/gdky005.testexception/files

代码中调用：

	context.getExternalFilesDir(null);  //获取 外置 SD 卡 私有目录

### 具体原因
在主 app 中 因为使用了 targetSdkVersion 低于 android 6.0（23）的 19， 意味着 app 支持的最高目标版本是 android 4.4 （19）。 targetSdkVersion 是 Android 提供向前兼容的主要依据。 因此系统检查到 这个低于 android 6.0（23）， 默认给 app 自动开启了所有的权限（这也是之前一直疑惑的点）。当在 手机的系统大约等于 android 6.0（23），默认拥有了所有的权限，因此 app 可以正常运行。

但是根据 听云 的崩溃手机信息来看，确实是崩溃了。

	java.lang.IllegalArgumentException: Invalid path: /storage/emulated/0/Android/data/

经过大量尝试以及测试，终于发现问题是处在  targetSdkVersion 这里。 如果 tartgetSdkVersion 大约23，即使关闭 SD 卡的存储权限， 也可以通过  context.getExternalFilesDir(null);  拿到 SD 卡的位置 app 私有存储目录。

但是，当 tartgetSdkVersion \< 23, 关闭 SD 卡的存储权限后， 通过 context.getExternalFilesDir(null);  不可以拿到 SD 卡的外置 app 私有目录，会提示 「Invalid path: /storage/emulated/0/Android/data/」。

因为在 6.0 以上的手机，检测到 app 的 tartgetSdkVersion \< 23，默认给了所有的权限，但是当 关闭 存储权限后，意味着 SD 卡的读写权限都没有了。当调用到 context.getExternalFilesDir(null);  的时候，系统认为 当前 app 的最高 运行版本是 低于 23 的，所有不会给 app 在 SD 卡创建 app 的私有目录。所有尝试获取 SD 卡的 app 私有目录的时候，就会获取不到路径，因为没有 SD 卡的读写权限啊。

这里需要说明一下：当 tartgetSdkVersion \> 23，调用 context.getExternalFilesDir(null); ，系统会给 app 在 SD 卡的 Android/data/ + 包名 + files , 以这样的形式 创建一个 app 的私有文件夹，这也是 6.0 以后的新特性。例如：

	/storage/emulated/0/Android/data/gdky005.testexception/files

这个目录其实就等同于 Linux 里面的一个 app 的私有账户目录，只有当前 app 能访问，其他 app 直接访问不了，所以也被称之为 app 的外置私有目录。 app 的 tartgetSdkVersion \< 23， 因为系统没有给 它创建 私有目录，而且他没有 SD 卡的读写权限，所以 也不可以自行创建 app 的私有目录。

### 总结

由于 app 的 targetSdkVersion \< 23，默认 app 拥有所有的权限，但是当 用户手动禁用 SD 卡的存储权限，或者 Rom, 第三方服务 禁用，那么就会造成很严重的问题。无法使用外置app的私有目录，就会报错。

1. 不过在 原生的 android 系统里面会提示用户： ![](http://7xlcno.com1.z0.glb.clouddn.com/blog/sd_%20competence.png)
2. 如果第三方服务或者 rom 自动禁用的话，也许看不到警告。 
3. 例如三星等系统，在 app 安装好以后，会自动弹出权限框，让用户选择权限。

处理措施：
1. try catch
2. targetSdkVersion \>= 23 参考：[http://chinagdg.org/2016/01/picking-your-compilesdkversion-minsdkversion-targetsdkversion/](http://chinagdg.org/2016/01/picking-your-compilesdkversion-minsdkversion-targetsdkversion/)
3. 尝试 自动创建文件 检测是否成功，并验证，如果失败，立即使用 app 的内置私有目录














