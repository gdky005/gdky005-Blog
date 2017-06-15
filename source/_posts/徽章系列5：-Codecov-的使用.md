title: 徽章系列5： Codecov 的使用
date: 2017-06-15 18:03:18
categories: shields
keywords: 徽章
tags: 徽章
---



根据文中的指示：我们能看到一个开源的 github 项目 [https://github.com/codecov/example-android](https://github.com/codecov/example-android)， 不过看起来点晕晕的，但是还是看下了。

我们之后都直接使用 Trivas CI 构建了。

添加项目就不说了，进入后，点击 project changes, 找到自己的项目：![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-100.png)，等生产报告后，进入该网站就能看见结果。

### 利用 Jacoco 生成报告

Codecov不支持自己生成Android的测试覆盖率报告，它能做的是接收Jacoco生成的报告并进行可视化

1） 在 app 的 build.gradle 文件中 添加依赖

			//Jacoco 生成报告的依赖
	    androidTestCompile('com.android.support.test:runner:0.5', {
	        exclude group: 'com.android.support', module: 'support-annotations'
	    })
	    // Set this dependency to use JUnit 4 rules
	    androidTestCompile('com.android.support.test:rules:0.5', {
	        exclude group: 'com.android.support', module: 'support-annotations'
	    })
	    // Espresso-contrib for DatePicker, RecyclerView, Drawer actions, Accessibility checks, CountingIdlingResource
	    androidTestCompile('com.android.support.test.espresso:espresso-contrib:2.2.2', {
	        exclude group: 'com.android.support', module: 'support-annotations'
	        exclude group: 'com.android.support', module: 'support-v4'
	        exclude group: 'com.android.support', module: 'appcompat-v7'
	        exclude group: 'com.android.support', module: 'design'
	        exclude group: 'com.android.support', module: 'recyclerview-v7'
	    })
	    androidTestCompile('com.android.support.test.espresso:espresso-core:2.2.2', {
	        exclude group: 'com.android.support', module: 'support-annotations'
	    })

2) 在 需要构建测试覆盖率报告的Module  （AndroidBadge 项目中的 app）  的gradle文件中设置。 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-101.png)

	debug{
	        testCoverageEnabled true
	}

3) 可以在尝试在本地生成报告：

	./gradlew :app:createDebugAndroidTestCoverageReport 生成测试报告。  app 就是咱们项目中要测试的 module

测试报告地址：app/build/reports/coverage/debug/index.html。 

### 上报数据给 Codecov
1. 使用Github账号登录 https://codecov.io/， 并提供授权给该应用。
2. 在.travis.yml文件中添加命令将测试覆盖率报告上传给Codecov。

	after_success:
	    - bash <(curl -s https://codecov.io/bash)
	

![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-102.png)



### Codecov 需要用到 单元测试，所以必须要在 配置文件中添加模拟器
因为之前修改过很多次，过程很繁琐，直接给配置文件了，相信大家一眼就能看出来。

完整的配置文件是：

	language: android
	jdk: oraclejdk8
	sudo: false
	
	env:
	  global:
	      - ANDROID_API_LEVEL=25
	      - ANDROID_BUILD_TOOLS_VERSION=25.0.2
	      - ANDROID_ABI=armeabi-v7a
	      - ANDROID_TAG=google_apis
	      - ADB_INSTALL_TIMEOUT=20 # minutes (2 minutes by default)
	
	android:
	  components:
	    - platform-tools
	    - tools # to install Android SDK tools 25.1.x
	    - build-tools-$ANDROID_BUILD_TOOLS_VERSION
	    - android-$ANDROID_API_LEVEL
	    - sys-img-armeabi-v7a-google_apis-$ANDROID_API_LEVEL
	
	  licenses:
	      - android-sdk-license-.+
	      - '.+'
	
	before_install:
	  - chmod +x gradlew
	  - mkdir "$ANDROID_HOME/licenses" || true
	  - echo -e "\n8933bad161af4178b1185d1a37fbf41ea5269c55" > "$ANDROID_HOME/licenses/android-sdk-license"
	  - echo -e "\n84831b9409646a918e30573bab4c9c91346d8abd" > "$ANDROID_HOME/licenses/android-sdk-preview-license"
	
	before_script:
	  # Create and start emulator
	  - echo no | android create avd --force -n test -t "android-"$ANDROID_API_LEVEL --abi $ANDROID_ABI --tag $ANDROID_TAG
	  - emulator -avd test -no-skin -no-window &
	  - android-wait-for-emulator
	  - adb shell input keyevent 82 &
	
	script:
	  - ./gradlew assembleRelease
	  - ./gradlew :app:createDebugAndroidTestCoverageReport --info --stacktrace
	
	after_success:
	  - bash <(curl -s https://codecov.io/bash)


### codecov 总结

Travis-CI 对 android 的单元测试支持不是很好，因为需要开启虚拟机，开启这个过程就得10分钟（我的测试时间），很耗费时间。有时候也连接不上，一次跑下来估计得个 20分钟左右吧。所以，稳定性确实不是很高。如果不做单元测试，而只是做发布之类的，稳定性还是高的。 

单元测试应都会吧， 这里检测的标准就是说 每个类和方法都必须检测到，否则就算没有覆盖。我这里也就简单在项目中写过例子，剩下的你们自己玩吧。 ![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-103.png)

**所以 要不要使用单元测试 还是根据项目决定吧。 **


### 获取 codecov 的徽章

![](http://7xlcno.com1.z0.glb.clouddn.com/gbg/kaiyuan/md/gbg-kaiyuan-md-104.png)

