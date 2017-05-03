title: '如何选择 compileSdkVersion, minSdkVersion 和 targetSdkVersion'
date: 2017-05-03 10:34:33
categories: android
keywords: android编译
tags: android
---



![](https://o0dsxp5zq.qnssl.com/wp-content/uploads/2016/01/androidversion.png)

英文原文：[Picking your compileSdkVersion, minSdkVersion, and targetSdkVersion](https://medium.com/google-developers/picking-your-compilesdkversion-minsdkversion-targetsdkversion-a098a0341ebd#.tz5zzucma)

当你发布一个应用之后，(取决于具体的发布时间)可能没过几个月 Android 系统就发布了一个新版本。这对你的应用意味着什么，所有东西都不能用了？

别担心，**向前兼容**是 Android 非常关注的事情。用户在升级到新版 Android 的时候，用以前版本的 SDK 构建的现有应用应该不会出问题。这就是 **compileSdkVersion**, **minSdkVersion** 和 **targetSdkVersion** 的作用：他们分别控制可以使用哪些 API ，要求的 API 级别是什么，以及应用的兼容模式。

### compileSdkVersion

compileSdkVersion 告诉 Gradle 用哪个 Android SDK 版本编译你的应用。使用任何新添加的 API 就需要使用对应 Level 的 Android SDK。

需要强调的是**修改 compileSdkVersion 不会改变运行时的行为**。当你修改了 compileSdkVersion 的时候，可能会出现新的编译警告、编译错误，但新的 compileSdkVersion 不会被包含到 APK 中：它纯粹只是在编译的时候使用。（你真的应该修复这些警告，他们的出现一定是有原因的）

因此我们强烈推荐**总是使用最新的 SDK 进行编译**。在现有代码上使用新的编译检查可以获得很多好处，避免新弃用的 API ，并且为使用新的 API 做好准备。

注意，如果使用 [Support Library](http://developer.android.com/tools/support-library/index.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog)，那么使用最新发布的 Support Library 就需要使用最新的 SDK 编译。例如，要使用 23.1.1 版本的 Support Library ，compileSdkVersion 就必需至少是 23 （大版本号要一致！）。通常，新版的 Support Library 随着新的系统版本而发布，它为系统新增加的 API 和新特性提供兼容性支持。

### minSdkVersion

如果 compileSdkVersion 设置为可用的最新 API，那么 **minSdkVersion 则是应用可以运行的最低要求**。minSdkVersion 是 Google Play 商店用来判断用户设备是否可以安装某个应用的标志之一。

在开发时 minSdkVersion 也起到一个重要角色：[lint](http://developer.android.com/tools/debugging/improving-w-lint.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog) 默认会在项目中运行，它在你使用了高于 minSdkVersion  的 API 时会警告你，帮你避免调用不存在的 API 的运行时问题。如果只在较高版本的系统上才使用某些 API，通常使用[运行时检查系统版本](http://developer.android.com/training/basics/supporting-devices/platforms.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog#version-codes)的方式解决。

请记住，你所使用的库，如 [Support Library](http://developer.android.com/tools/support-library/features.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog) 或 [Google Play services](https://developers.google.com/android/guides/overview?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog)，可能有他们自己的 minSdkVersion 。你的应用设置的 minSdkVersion 必需大于等于这些库的 minSdkVersion 。例如有三个库，它们的 minSdkVersion 分别是 4, 7 和 9 ，那么你的 minSdkVersion  必需至少是 9 才能使用它们。在少数情况下，你仍然想用一个比你应用的 minSdkVersion 还高的库（处理所有的边缘情况，确保它只在较新的平台上使用），你可以使用 [tools:overrideLibrary](http://tools.android.com/tech-docs/new-build-system/user-guide/manifest-merger?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog#TOC-tools:overrideLibrary-marker) 标记，但请做彻底的测试！

当你决定使用什么 minSdkVersion 时候，你应该**参考当前的 [Android 分布统计](http://developer.android.com/about/dashboards/index.html)**，它显示了最近 7 天所有访问 Google Play 的设备信息。他们就是你把应用发布到 Google Play 时的潜在用户。最终这是一个商业决策问题，取决于为了支持额外 3% 的设备，确保最佳体验而付出的开发和测试成本是否值得。

当然，如果某个新的 API 是你整个应用的关键，那么确定 minSdkVersion 的值就比较容易了。不过要记得 14 亿设备中的 0.7％ 也是个不小的数字。

### targetSdkVersion

三个版本号中最有趣的就是 targetSdkVersion 了。 **targetSdkVersion 是 Android 提供向前兼容的主要依据**，在应用的 targetSdkVersion 没有更新之前系统不会应用最新的行为变化。这允许你在适应新的行为变化之前就可以使用新的 API （因为你已经更新了 compileSdkVersion 不是吗？）。

targetSdkVersion 所暗示的许多行为变化都记录在 [VERSION\_CODES](http://developer.android.com/reference/android/os/Build.VERSION_CODES.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog) 文档中了，但是所有恐怖的细节也都列在每次发布的平台亮点中了，在这个 [API Level](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog#ApiLevels) 表中可以方便地找到相应的链接。

例如，[Android 6.0 变化文档](http://developer.android.com/about/versions/marshmallow/android-6.0-changes.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog)中谈了 target 为 API 23 时会如何把你的应用转换到[运行时权限模型](http://android-developers.blogspot.com/2015/08/building-better-apps-with-runtime.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog)上，[Android 4.4 行为](http://developer.android.com/about/versions/android-4.4.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog#Behaviors)变化阐述了 target 为 API 19 及以上时使用 [set()](http://developer.android.com/reference/android/app/AlarmManager.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog#set%28int,%20long,%20android.app.PendingIntent%29) 和 [setRepeating()](http://developer.android.com/reference/android/app/AlarmManager.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog#setRepeating%28int,%20long,%20long,%20android.app.PendingIntent%29) 设置 alarm 会有怎样的行为变化。

由于某些行为的变化对用户是非常明显的（[弃用的 menu 按钮](http://android-developers.blogspot.com/2012/01/say-goodbye-to-menu-button.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog)，运行时权限等），所以**将 target 更新为最新的 SDK 是所有应用都应该优先处理的事情**。但这不意味着你一定要使用所有新引入的功能，也不意味着你可以不做任何测试就盲目地更新 targetSdkVersion ，[请一定在更新 targetSdkVersion 之前做测试！](http://android-developers.blogspot.com/2012/01/say-goodbye-to-menu-button.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog)你的用户会感谢你的。


### Gradle 和 SDK 版本

所以设置正确的 compileSdkVersion, minSdkVersion 和 targetSdkVersion 很重要。如你所想， [Gradle](http://developer.android.com/tools/building/plugin-for-gradle.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog) 和 [Android Studio](http://developer.android.com/tools/studio/index.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog) 都在构建系统中集成了它们。在你的模块的 build.gradle 文件中（也可以在 Android Studio 的项目结构选项中）设置：

	android {
	  compileSdkVersion 23
	  buildToolsVersion "23.0.1"
	
	  defaultConfig {
	    applicationId "com.example.checkyourtargetsdk"
	    minSdkVersion 7
	    targetSdkVersion 23
	    versionCode 1
	    versionName “1.0”
	  }
	}


编译时用到的 compileSdkVersion 是和构建工具版本一起设置的 Android 设置之一。其他两个稍有不同，他们在构建变体([build variant](http://developer.android.com/tools/building/plugin-for-gradle.html?utm_campaign=adp_series_sdkversion_010616&utm_source=medium&utm_medium=blog#buildVariants))的那里声明。defaultConfig 是所有构建变体的基础，也是设置这些默认值的地方。你可以想象在一个更复杂的系统中，应用的某些版本可能会有不同的 minSdkVersion 。

minSdkVersion 和 targetSdkVersion 与 compileSdkVersion 的另一个不同之处是它们会被包含进最终的 APK 文件中，如果你查看生成的 AndroidManifest.xml 文件，你会看到类似下面这样的标签：

	<uses-sdk android:targetSdkVersion="23" android:minSdkVersion="7" />

如果你在 manifest 文件中手工设置，你会发现 Gradle 在构建时会忽略它们（尽管其它构建系统可能会明确依赖它们）。

### 综合来看
如果你按照上面示例那样配置，你会发现这三个值的关系是：

	minSdkVersion <= targetSdkVersion <= compileSdkVersion

这种直觉是合理的，如果 compileSdkVersion 是你的最大值，minSdkVersion 是最小值，那么最大值必需至少和最小值一样大且 target 必需在二者之间。

**理想上**，在稳定状态下三者的关系应该更像这样：

	minSdkVersion (lowest possible) <= 
				targetSdkVersion == compileSdkVersion (latest SDK)

用较低的 minSdkVersion 来覆盖最大的人群，用最新的 SDK 设置 target 和 compile 来获得最好的外观和行为。#BuildBetterApps

### 备注
译者注： Android Development Patterns 系列视频见 YouTube 或 优酷 。
来自： [http://chinagdg.org/2016/01/picking-your-compilesdkversion-minsdkversion-targetsdkversion/](http://chinagdg.org/2016/01/picking-your-compilesdkversion-minsdkversion-targetsdkversion/)

![](https://o0dsxp5zq.qnssl.com/wp-content/uploads/2016/01/androiddeveloperpatterns-1024x317.png)










