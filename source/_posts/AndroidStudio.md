title: AndroidStudio
date: 2015-09-19 17:02:01
tags: AndroidStudio
---

Android Studio 是可以在线增量更新的，但是可能连不上服务器更新，解决办法如下：

- 1.修改系统hosts文件，添加如下2行

		203.208.46.146   dl-ssl.google.com
		203.208.46.146   dl.google.com
		
- 2.修改Android Studio\bin目录下的studio.vmoptions (32位系统) 或者 studio64.vmoptions (64位系统)文件，添加如下3行

		-Djava.net.preferIPv4Stack=true  
		-Didea.updates.url=http://dl.google.com/android/studio/patches/updates.xml  
		-Didea.patches.url=http://dl.google.com/android/studio/patches/
		
	**重启Android Studio应该就可以更新了，更新时应使用管理员权限打开Android Studio。
**

- 3.如果仍然无效，将url里的修改http为https，然后重启点击Check Update试试。
- 4.android studio首次安装运行时卡在更新处理方法
	
	01.这是在检查你的 Android SDK 。有人会在这里卡上很长时间，很大的原因就是：网络连接有问题。可以通过配置hosts 的方式来解决。如果检查需要更新，则需要你进行安装 。
	
	02.如果想跳过这一步，可以进行如下操作：在Android Studio安装目录下的 bin 目录下，找到 idea.properties 文件，在文件最后追加
		
		disable.android.first.run=true 


本方法适用于在某些网络下无法直接更新的问题


---

