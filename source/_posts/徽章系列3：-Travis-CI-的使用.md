title: 徽章系列3： Travis CI 的使用
date: 2017-06-15 18:04:35
categories: shields
keywords: 徽章
tags: 徽章
---

官网： [http://travis-ci.org/](http://travis-ci.org/)
ps: 这个是公开的，如果需要使用私有的，请使用 .com 域名。
需要提醒的是：每次提交代码后都会重新下载需要的资源文件哦，所以时间很长，耐心等待吧。

### TravisCI 有什么用？
travis-ci 就是 自动化 CI 工具，类似于大公司经常使用的 Jenkins，但是 travis-ci 是在云端的，而是支持 github, 还免费，我们可以 用 travis-ci 做很多的事情，不仅仅是 编译看 项目有没有问题。例如在项目构建结束以后，我们可以打包，并发布 apk 到都 蒲公英， fir，也可以用邮件通知给相关的开发人员和业务任务。 做一些简单处理，轻轻松松。这部分内容到后面尽快整理，在写一篇。

### 添加 github 项目到 travis-ci
1. 进入 Travis 官网后，使用 GitHub 账号 登录，成功后：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-68.png)。
2. 点击 加号 ，能看到你 GitHub 里面的所有：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-69.png)， 如果没有项目或者没有你新建的项目，请点击右上方的   Sync account  按钮。
3. 点击这里：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-70.png)，开启 Travis 构建。点击这个可以配置该项目：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-71.png)
4. 默认是空的：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-72.png)

### 在 项目中添加 travis-ci 需要的 .travis.yml 文件
1. 在项目根目录下直接创建 一个 .travis.yml 的文件。![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-73.png)
2. 直接在文件里面添加代码：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-74.png)

**.travis.yml 文件 源代码**

	language: android
	jdk: oraclejdk8
	sudo: false
	
	android:
	  components:
	    - tools
	    - build-tools-25.0.2
	    - android-25
	    - extra-android-m2repository
	    - extra-android-support
	  licenses:
	      - android-sdk-license-.+
	      - '.+'
	
	before_install:
	  - chmod +x gradlew
	  - mkdir "$ANDROID_HOME/licenses" || true
	  - echo -e "\n8933bad161af4178b1185d1a37fbf41ea5269c55" > "$ANDROID_HOME/licenses/android-sdk-license"
	  - echo -e "\n84831b9409646a918e30573bab4c9c91346d8abd" > "$ANDROID_HOME/licenses/android-sdk-preview-license"
	
	script:
	  - ./gradlew assembleRelease
	

因为该文很长很长，所以暂时就不解释具体含义了，可以看看官方文档。

### push 项目到 github, travis-ci 自动监测构建
1. 提交到代码，并 push 到 github。会自动触发 Travis 的自动构建。![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-75.png)
2. 下面黑色部分是构建的过程：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-76.png)，  黑框上的白色点点点击后会变成绿色，会自动滚动到最下面。![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-77.png)

喝一杯咖啡，慢慢等待吧，最难熬的时候已经过去，此刻是享受的时候了。![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-78.png)

回到首页刷新后，能看到：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-79.png) 说明我们已经构建成功了。

### .travis.yml 需要注意的地方

初次使用  Travis 的试试，总遇到 说 android 的一些协议未接受而构建失败，导致耗费了一两天，曾经一度想放弃，但是最终坚持了下来，通过各种搜索，摸索，猜测，终于搞定。其实最初的项目是这个：[https://github.com/gdky005/TestJitpack](https://github.com/gdky005/TestJitpack) ，里面有很多辛酸史记录，从提交记录能看得出来，有兴趣的可以研究研究，也许能解决你现在的问题。

Travis CI 协议问题解决方法：[http://stackoverflow.com/questions/37615379/travis-ci-build-doesnt-work-with-android-constraint-layout](http://stackoverflow.com/questions/37615379/travis-ci-build-doesnt-work-with-android-constraint-layout)

最重要部分在这里：

	machine:
	  environment:
		  ANDROID_HOME: /usr/local/android-sdk-linux
	
	dependencies:
	  pre:
		- mkdir -p "$ANDROID_HOME/licenses"
		- echo -e "\n8933bad161af4178b1185d1a37fbf41ea5269c55" > "$ANDROID_HOME/licenses/android-sdk-license"
		- echo -e "\n84831b9409646a918e30573bab4c9c91346d8abd" > "$ANDROID_HOME/licenses/android-sdk-preview-license"


### 添加 travis-ci 徽章到 github
激动的时刻再次到来，让我们找找徽章在哪里呢？
![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-80.png)

让我们来选择 markdonw 格式：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-81.png) 并复制上。

同样如上修改 readme.md。 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-82.png)

加空格后，直接贴上去：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-83.png)，这里需要说明的是，如果换成，图标也会换行哦。这样写就能保证所有的图标在一行了。

回到项目首页以后就能发现：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-84.png)

添加 Travis 徽章成功。


Bye the way! 上面是最初级的构建过程，如果遇到单元测试就不行了，还得参考 我的开源项目 [TestJitpack](https://github.com/gdky005/TestJitpack)。 不过有点乱，后面整理下。


### 相关链接：

完整版：
[打造一个高逼格的android开源项目——小白全攻略](http://www.gdky005.com/2017/06/15/%E6%89%93%E9%80%A0%E4%B8%80%E4%B8%AA%E9%AB%98%E9%80%BC%E6%A0%BC%E7%9A%84android%E5%BC%80%E6%BA%90%E9%A1%B9%E7%9B%AE%E2%80%94%E2%80%94%E5%B0%8F%E7%99%BD%E5%85%A8%E6%94%BB%E7%95%A5/)

精简集合版：
[徽章系列1： Top 30 android 开源项目徽章](http://www.gdky005.com/2017/06/15/%E5%BE%BD%E7%AB%A0%E7%B3%BB%E5%88%971%EF%BC%9A-Top-30-android-%E5%BC%80%E6%BA%90%E9%A1%B9%E7%9B%AE%E5%BE%BD%E7%AB%A0/)
[徽章系列2：JitPack 的使用](http://www.gdky005.com/2017/06/15/%E5%BE%BD%E7%AB%A0%E7%B3%BB%E5%88%972%EF%BC%9AJitPack-%E7%9A%84%E4%BD%BF%E7%94%A8/)
[徽章系列3： Travis CI 的使用](http://www.gdky005.com/2017/06/15/%E5%BE%BD%E7%AB%A0%E7%B3%BB%E5%88%973%EF%BC%9A-Travis-CI-%E7%9A%84%E4%BD%BF%E7%94%A8/)
[徽章系列4： Circle CI 的使用](http://www.gdky005.com/2017/06/15/%E5%BE%BD%E7%AB%A0%E7%B3%BB%E5%88%974%EF%BC%9A-Circle-CI-%E7%9A%84%E4%BD%BF%E7%94%A8/)
[徽章系列5： Codecov 的使用](http://www.gdky005.com/2017/06/15/%E5%BE%BD%E7%AB%A0%E7%B3%BB%E5%88%975%EF%BC%9A-Codecov-%E7%9A%84%E4%BD%BF%E7%94%A8/)
[徽章系列6： Api\_Level 的使用](http://www.gdky005.com/2017/06/15/%E5%BE%BD%E7%AB%A0%E7%B3%BB%E5%88%976%EF%BC%9A-Api-Level-%E7%9A%84%E4%BD%BF%E7%94%A8/)
[徽章系列7： codacy 的使用](http://www.gdky005.com/2017/06/15/%E5%BE%BD%E7%AB%A0%E7%B3%BB%E5%88%977%EF%BC%9A-codacy-%E7%9A%84%E4%BD%BF%E7%94%A8/)
[徽章系列8：生成个性徽章](http://www.gdky005.com/2017/06/15/%E5%BE%BD%E7%AB%A0%E7%B3%BB%E5%88%978%EF%BC%9A%E7%94%9F%E6%88%90%E4%B8%AA%E6%80%A7%E5%BE%BD%E7%AB%A0/)

总分类：
[徽章（shields ）系列文章总分类](http://www.gdky005.com/categories/shields/)

github 地址：
[徽章项目 Demo github 地址：](https://github.com/gdky005/AndroidBadge)
 [https://github.com/gdky005/AndroidBadge](https://github.com/gdky005/AndroidBadge)


