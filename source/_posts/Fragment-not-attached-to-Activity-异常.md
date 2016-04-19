title: Fragment not attached to Activity 异常
date: 2015-11-27 10:48:17
categories:
keywords:
tags: android
---


### 关于Fragment（XXFragment） not attached to Activity 异常


出现该异常，是因为Fragment的还没有Attach到Activity时，调用了如getResource()等，需要上下文Content的函数。解决方法，就是等将调用的代码写在OnStart（）中。网上还有几处这样的参考：[http://stackoverflow.com/questions/10919240/fragment-myfragment-not-attached-to-activity ](http://stackoverflow.com/questions/10919240/fragment-myfragment-not-attached-to-activity%C2%A0) 回答的主要是在调用

	getResources().getString(R.string.app_name); 

之前增加一个判断isAdded(),两外说这个异常解决办法的有
[http://stackoverflow.com/questions/6870325/android-compatibility-package-fragment-not-attached-to-activity](http://stackoverflow.com/questions/6870325/android-compatibility-package-fragment-not-attached-to-activity)

这个是针对另外一种情况下的解决方式。

### 在使用Fragment保存参数的时候，可能是因为需要保存的参数比较大或者比较多，这种情况下页会引起异常


	Bundle b = new Bundle(); 
	b.putParcelable("bitmap", bitmap2); 
	imageRecognitionFragment.setArguments(b); 

设置好参数，并且添加hide(),add(),方法之后，需要commit()，来实现两个Fragment跳转的时候，这种情形下参数需要进行系统保存，但是这个时候你已经实现了跳转，系统参数却没有保存。此时就会报异常:

	java.lang.IllegalStateException: Can not perform this action after onSaveInstanceState

### 分析原因：

你并不需要系统保存的参数，只要你自己设置的参数能够传递过去，在另外一个Fragment里能够顺利接受就行了，现在android里提供了另外一种形式的提交方式commitAllowingStateLoss()，从名字上就能看出，这种提交是允许状态值丢失的。到此问题得到完美解决，值的传递是你自己控制的。

这里也说一下另外一个问题，bitmap 也可以通过Bundle传递的，使用putParacelable就可以了
