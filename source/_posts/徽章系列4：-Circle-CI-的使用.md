title: 徽章系列4： Circle CI 的使用
date: 2017-06-15 18:03:52
categories: shields
keywords: 徽章
tags: 徽章
---


 官网: [https://circleci.com/](https://circleci.com/)

### Circle CI 和 Travis CI 有什么区别？需要一起使用吗？
Circle CI 相对来说比 Travis CI 好一些，至少界面上来说哈。还提供 ssh 的连接，构建过程相对来说 比较透明直观。例如：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-85.png)

Travis CI 的文档资料相对 Circle CI 来说 比较多， Circle CI 资料少之又少。

Travis CI 的使用率还是很高的， 不过 Circle CI 相对来说比较 年轻化，符合主流的科技感，更智能。

说到是否需要一起使用，其实都行，不过我在观察 github 主流项目的时候 有不少项目都是同时使用的，多一个技能总没有坏处吧。其实会了 Travis CI，在加 Circle CI 真是简单不少呢，只是基本语法不太一样。

### 添加项目到 Circle CI
1. 登录主页面：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-86.png)， 在 project 里面自己的账号下搜索刚创建的项目。
2. 一般直接选择 Ubuntu 即可:![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-87.png),点击绿色  Build project.![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-88.png)
3. 能看到：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-89.png)， 但是这次肯定会失败，因为我们还没有添加 Circle 需要的文件呢。

### 在 项目中添加 Circle CI 需要的 circle.yml 文件

1. 在项目的根目录下 添加 circle.yml 文件；
2. 添加 circle 的代码到文件中：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-90.png)

**circle.yml 源代码：**

	machine:
	  java:
	      version: oraclejdk8
	  environment:
	      ANDROID_HOME: /usr/local/android-sdk-linux
	
	dependencies:
	  pre:
	    - mkdir -p "$ANDROID_HOME/licenses"
	    - echo -e "\n8933bad161af4178b1185d1a37fbf41ea5269c55" > "$ANDROID_HOME/licenses/android-sdk-license"
	    - echo -e "\n84831b9409646a918e30573bab4c9c91346d8abd" > "$ANDROID_HOME/licenses/android-sdk-preview-license"
	
	
	  override:
	    - echo y | android update sdk --no-ui --filter "android-25"
	    - echo y | android update sdk --no-ui --filter "build-tools-25.0.2"
	    - echo y | android update sdk --no-ui --filter "extra-android-m2repository"
	    - echo y | android update sdk --no-ui --filter "extra-android-support"
	    - echo y | android update sdk --no-ui --filter "extra-google-m2repositor"
	    - ./gradlew dependencies || true
	
	test:
	  override:
	    - ./gradlew build

### push 项目到 github, Circle CI 自动监测构建

1. 提交代码后，发布到 github，Circle CI 会自动执行。
2. ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-91.png) 点击进来后，会看到：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-92.png)， 说明已经开始 构建了， 下载需要的东西：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-93.png)。
3. 构建中的一些步骤：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-94.png)， 相对 Travis 来说展示更直观。
4. 看到 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-95.png) 说明构建成功。

### 添加 Circle CI 徽章到 github
我们再来把 Circle CI 的徽章找到，并添加到我们的 github 上去吧。![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-96.png)![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-97.png)

我们把 徽章的 markdown 链接拷贝下来放入到我们的 主项目页面的里面。![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-98.png)

回项目主页刷新后：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-99.png) 非常 happy, 已经添加成功了。

