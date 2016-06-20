title: 网络请求的 UA
date: 2016-06-20 18:27:54
categories: okhttp3
keywords: okhttp3
tags: okhttp3
---

一般我们的客户端请求头里面都会有一个 User Agent 的参数，默认请求下 会直接获取系统里面的一些参数信息，然后发送到服务器。服务器就知道这个设备 大体情况，也可以根据这个来处理，或者禁用某些设备。
一般定义的 UA 应该包含下面这些东西（当前也可以删减或去掉的）：

`appName + versionName + versonCode + 渠道号 + 设备基本信息
`

这里写一些大体的代码，其他可以自行封装：

拼接数据：

`appName: mContext.getString(R.string.appname)
`

`versionName:  DeviceUtil.getVersionName(mContext)
`

`versonCode: DeviceUtil.getVersionCode(mContext)
`

`渠道号：DeviceUtil.getChannelTag(mContext)
`

设备基本信息: 
`build.MODEL 		Nexus 6
`

`build.MANUFACTURER  motorola
`

`build.BRAND			google
`

`build.FINGERPRINT	google/shamu/shamu:6.0.1/MMB29S/2489379:user/release-keys
`

`Build.VERSION.RELEASE 6.0.1
`

`Build.VERSION.SDKINT 23`