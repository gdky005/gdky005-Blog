title: 徽章系列2：JitPack 的使用
date: 2017-06-15 18:05:15
categories: shields
keywords: 徽章
tags: 徽章
---


官网： [https://jitpack.io](https://jitpack.io)

### 创建 Android Library
1. 在 AS 中创建标准的 android 项目：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-22.png)
2. 创建 Library：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-23.png) ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-24.png) ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-25.png)
3. 创建 badge library 成功。
### 配置相关文件
进入 [jitpack android](https://jitpack.io/docs/ANDROID/) 可以看到 jitpack 为我们提供的文档帮助。  ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-26.png)
1. 在项目的根目录下的 build.gradle 文件中添加：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-27.png)
2. 在 library 下的 build.gradle 中添加：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-28.png) ， 对应官网中说的 『group='com.github.YourUsername’』， 其实可以不写，写不写都会自动生成。 即使你写成了别的，最终还是以这样的格式输出。

AndroidBadge 中 的 build.gradle :

	classpath 'com.github.dcendents:android-maven-gradle-plugin:1.5' // Add this line

AndroidBadge 里面的 badge (library) :

	apply plugin: 'com.github.dcendents.android-maven'

### 发布到 Github （这里直接展示 AS 中的界面图形操作，会命令行的同学随意）

1. 登录自己的 github 账号，然后创建一个 项目，在首页右边能看到：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-29.png)
2. 点击 大绿色 按钮，并填写信息：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-30.png)。 那个协议，你们随意，这里只是演示。
3. 创建成功后：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-31.png)，拷贝当前项目地址。
4. 在 AS 的项目中创建 git 仓库：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-32.png)， 点击后，直接 选择 ok。将项目添加到 git 管理![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-33.png)。 项目中的文件都变成绿色后：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-34.png)，耐心等待完成后， 本地项目已经被 git 管理起来了，然后把代码提交到 github 即可。可能会有冲突，自行解决即可。
5. 现在我们给 library 的 badge 项目添加一个工具类：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-35.png)， 并提交到 github。


### Github 打 release or tag
![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-36.png) ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-37.png)
创建第一个 release 分支：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-38.png)
发布成功以后就能看到: ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-39.png)

一般用 release 就可以了， 在适当的时候 用  tag。
### 在 JitPack 上生成 aar
1. 进入 [https://jitpack.io/](https://jitpack.io/)。
2. 将 github 的项目地址： [https://github.com/gdky005/AndroidBadge](https://github.com/gdky005/AndroidBadge)， 直接拷贝 到 jitpack 网页中的文本框中。![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-40.png)
3. 点击 Look Up 后：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-41.png)， 等里面的那个圈圈 转完 以后，出现 绿色的这个：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-42.png) 说明已经发布 aar 成功，那么我们 可以直接使用了。 如果点开这个东西，会看到编译的和发布的过程。![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-43.png) 
4. 如果是红色的，说明有错误，点开查看，修改后重新构建。

### 在 app Demo 中测试是否生效

点击 get 后，能看到： ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-44.png)

1. 给项目根目录下的 build.gradle 添加：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-45.png)
2. 给app 项目里面的 build.gradle 添加：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-46.png)

AndroidBadge 中 的 build.gradle :

	maven { url 'https://jitpack.io' }


AndroidBadge 里面的 app 的 build.gradle :

	compile 'com.github.gdky005:AndroidBadge:v1.0.0'



好的，现在我们已经添加成功了，在 MainActivity 里面是是吧，看能否调到之前在 library 里面的写的 Utils.getVersion()。很高兴的是我们调出来了：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-47.png)
在项目的最底下也能看到： ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-48.png)。 运行 app 项目：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-49.png)


### 问题来了，我们修改library 后也能调用到吗？
让咱们一起试试吧：
1. 修改 badge 项目中的 1.0.0 为  1.0.1 ： ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-50.png)。
2. 提交代码后，push 到 github。 提交成功以后，我们重复上面的打 release 步骤 ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-51.png), ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-52.png), ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-53.png)
3. 然后再去 jitpack 上看看：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-54.png) 多了一个 v1.0.1, 点击 get 吧。![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-55.png) ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-56.png) ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-57.png)
4. 发布成功后，我们去 app 里面试试。 ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-58.png)
5. 同步后，直接运行 app。非常好，我们的修改的东西已经变了：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-59.png)。

至此， jitpack 基本也差不多了，但是貌似缺少了最重要的一点吧。

### jitpack 的徽章怎么弄

还是在刚刚的 jitpack 界面，只是我们把 页面拉倒底部。![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-60.png) 点击后：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-61.png) 是不是看到了熟悉的 md 文档的格式呢？ 看不懂也没关系，下面会专门讲解。
1. 拷贝内容：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-62.png)
2. 打开 github 的 AndroidBridge 项目：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-63.png)，点击该文件。
3. 让我们在线编辑下： ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-64.png)
4. 给 github 中的 这个 readme.md 文件添加点东西吧。![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-65.png)
5. 让我们 保存下。 ![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-66.png)
6. 然后回到项目[首页](https://github.com/gdky005/AndroidBadge)看看：![](https://raw.githubusercontent.com/gdky005/AndroidBadge/master/pic/gbg-kaiyuan-md-67.png) 棒棒的，添加成功。


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


