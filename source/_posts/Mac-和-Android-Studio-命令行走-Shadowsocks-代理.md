title: Mac 和 Android Studio 命令行走 Shadowsocks 代理
date: 2018-03-29 11:58:01
categories: Shadowsocks
keywords: 代理，Shadowsocks
tags: Shadowsocks，代理
---


今天同事说拉了一份 Android 代码，但是在他的电脑上总是构建失败，说下载不下来一些组件。经常查看 log, 发现确实是需要翻墙。
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_0.jpg)

## 使用 Shadowsocks 的 PAC 自动模式始终失败
本地开启自己搭建的 Shadowsocks，使用 PAC 自动模式。代理地址是：`172.0.0.1 ，端口：1089 `

在 Android Studio 中如下设置，未成功。
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_1.jpg)

尝试以下，也没有成功。
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_2.jpg) ![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_3.jpg)

经过 ![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_4.jpg) 的测试，发现，这种设置方法是错误的。不会成功，至少对于 mac 来说是这样的。

## 开启正确的全局代理模式
首先将 Shadowsocks 设置为全局代理模式，通过 mac 的网络 ![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_5.jpg)，![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_6.jpg)
得到代理地址是：
`127.0.0.1, 端口：1086 `

在 AS 中配置：
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_7.jpg)

可以点击图中的 `Check connecction` 按钮，输入`http://www.youtube.com` 来检验是否可以走代理。

正常来说，应该会成功的。如果不行，可能是端口占用，或者一些其他原因，建议重启电脑试试。

使用 Android Studio 构建项目的时候，默认就可以走代理直接访问下载不了的资源了。但是这个期间是看不到具体过程，如果卡在哪一步，我们很难察觉，只能默默的等待。非常的尴尬，我们可能会想如果使用命令该多好。


经过一些测试，发现设置命令的时候，还是有一些技巧的。
简单点的就是直接使用 `export ALL_PROXY=socks5://127.0.0.1:1086 ` 设置当前的窗口生效。 

然后使用 `curl -i http://ip.cn ` 进行测试，看是否走了代理。

以下是我的测试结果：
未使用代理
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_8.jpg)

使用代理：
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_9.jpg)

虽然使用了代理，但是 `ping` 命令还是不通的。
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_10.jpg)
当时总以为失败了，但是经过大姑爷的提醒，用国外的 git clone 命令测试下，就知道了。

## git clone 测试代理
googlesource.com 是 google 的代码开源地址，但是现在几乎停用了，都转到 github。 但是还有部分代码还是 这里，例如: [https://android.googlesource.com/device/asus/deb/](https://android.googlesource.com/device/asus/deb/)

google 和 googlesource.com 默认在国内都是 ping 不通的。所以可以使用这个测试代理是否可用。

所以选定这个命令：`git clone https://android.googlesource.com/device/asus/deb `

不开代理，默认提示失败：
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_11.jpg)

开代理，下载成功：
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_12.jpg)

到这里，命令行已经可用了。


## Android Studio 设置代理
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_13.jpg)

## 更新 Android SDK
### 尝试使用 android sdk 工具更新最新组件
新版本的 Android SDK 不允许直接通过命令行更新 SDK 了，需要配合 Android Studio 一起才能更新，让人有些不爽。

可以在这里下载对应平台的 zip, 解压后直接替换自带的 tools 等目录，就可以和以前一样使用 `android` 命令打开 Android SDK 的 UI 更新界面。
[https://pan.baidu.com/s/1xAOFWhI\_nVNByHyuYJQQog](https://pan.baidu.com/s/1xAOFWhI_nVNByHyuYJQQog)

### 在 Android Studio 中更新 SDK

![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_14.jpg)

![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_15.jpg)


## 常用公开的代理服务器

大连东软信息学院镜像服务器地址:
	http://mirrors.neusoft.edu.cn 端口：80


北京化工大学镜像服务器地址:
	IPv4: http://ubuntu.buct.edu.cn/ 端口：80
	IPv4: http://ubuntu.buct.cn/ 端口：80
	IPv6: http://ubuntu.buct6.edu.cn/ 端口：80

上海GDG镜像服务器地址:
	http://sdk.gdgshanghai.com 端口：8000

参考地址：[https://www.cnblogs.com/maxinliang/p/5083495.html](https://www.cnblogs.com/maxinliang/p/5083495.html)

具体的使用的时候，不需要 http, 直接 域名，即：mirrors.neusoft.edu.cn


## 总结
先开启 SS 的全局模式，查看到代理的 ip 和 port, 然后命令行每次需要设置下 `export ALL_PROXY=socks5://127.0.0.1:1086 `, 只对当前窗口有效。至于 ping 命令不行，而代码库能拉下来的原因是：
SS 不会代理到 ICMP 包 同理也无法测试路由跟踪。
因为ping用的icmp， 代理没代理icmp。

	nc -X 5 -x 127.0.0.1:1086 youtube.com 443 -v
	-x 是当前代理的端口和地址
	
	-X 5 表示 socks5
	-X 4 表示 socks4
	-X connect 表示 https

![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_16.jpg)

测试结果是：
![](https://raw.githubusercontent.com/gdky005/PictureResource/master/mac_doc_shadowsocks/mac_doc_shadowsocks_17.jpg)


