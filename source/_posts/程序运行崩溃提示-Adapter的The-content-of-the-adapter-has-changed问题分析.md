title: 程序运行崩溃提示 Adapter的The content of the adapter has changed问题分析
date: 2015-11-12 12:01:59
categories:
keywords: android崩溃异常
tags: android
---

### 程序运行崩溃提示 Adapter的The content of the adapter has changed问题分析
<br>

遇到该问题，程序会直接崩溃

1.具体问题：

	java.lang.IllegalStateException: The content of the adapter has changed but ListView did not receive a notification. Make sure the content of your adapter is not modified from a background thread, but only from the UI thread. Make sure your adapter calls notifyDataSetChanged() when its content changes. [in ListView(2131493253, class android.widget.ListView) with Adapter(class com.kaolafm.a.f)]
	at android.widget.ListView.layoutChildren(ListView.java:1562)
	at android.widget.AbsListView$CheckForTap.run(AbsListView.java:3281)
	at android.os.Handler.handleCallback(Handler.java:739)
	at android.os.Handler.dispatchMessage(Handler.java:95)
	at android.os.Looper.loop(Looper.java:135)
	at android.app.ActivityThread.main(ActivityThread.java:5254)
	at java.lang.reflect.Method.invoke(Native Method)
	at java.lang.reflect.Method.invoke(Method.java:372)
	at com.android.internal.os.ZygoteInit$MethodAndArgsCaller.run(ZygoteInit.java:903)
	at com.android.internal.os.ZygoteInit.main(ZygoteInit.java:698)

2.出现的过程：

程序中聊天功能，当人数太多的时候，聊天室界面会非常的卡顿。做了一次优化：来一条消息后并不会立刻更新 listview 的adapter的数据源 ,而且将数据缓存起来，从第一个消息过来后，等待一秒钟，再去更新adapter的数据源。

具体源码是：
	while (mDataList != null && mDataList.size() > MAX_MESSAGE_NUM) {
	                        mDataList.remove(0);
	                    }
	                    MessageBean data = (MessageBean) msg.obj;
	                    if (!ChatManager.getInstance(getActivity()).handleMessageSended(data)) {
	                        messageBeanList.add(data);
	                    }
	
	                    //调整 在线人数很多，发消息很频繁的bug.
	                    isOnlinePeopleLock = true;
	                    if (isOnlinePeopleLock) {
	                        mHandler.postDelayed(new Runnable() {
	                            @Override
	                            public void run() {
	                                mDataList.addAll(messageBeanList);
	                                messageBeanList.clear();
	                                isOnlinePeopleLock = false;
	                                listViewSelectEnd(mListView, isTouchListView);
	                            }
	                        }, ONE_SECOND);
	                    }

当人数很多的时候会偶然出现：
	java.lang.IllegalStateException

3.分析问题:

**Exception解读：**
**        Adapter的数据内容已经改变，但是ListView却未接收到通知。要确保不在后台线程中修改Adapter的数据内容，而要在UI Thread中修改。确保Adapter的数据内容改变时一定要调用notifyDataSetChanged()方法。**

<br>
从Android源码上看看：

	// Handle the empty set by removing all views that are visible
	// and calling it a day
	if (mItemCount == 0) {
	    resetList();
	    invokeOnItemScrollListener();
	    return;
	} else if (mItemCount != mAdapter.getCount()) {
	    throw new IllegalStateException("The content of the adapter has changed but "
	            + "ListView did not receive a notification. Make sure the content of "
	            + "your adapter is not modified from a background thread, but only "
	            + "from the UI thread. [in ListView(" + getId() + ", " + getClass()
	            + ") with Adapter(" + mAdapter.getClass() + ")]");
	}

#### **当ListView缓存的数据Count和ListView中Adapter.getCount()不等时，会抛出该异常。**

<br>
可以分析出来的是：

当人数过多的时候，可能先走了 mDataList.remove(0); 还没执行 listViewSelectEnd方法 （方法里面包含了 notifyDataSetChanged()）, 并不能保证Adapter的数据更新时，立马调用notifyDataSetChanged()通知ListView，这两个线程之间的时间差引起的数据不同步，导致ListView的layoutChildren()中访问Adapter的getCount()方法时，Adapter内已经是最新数据源，而ListView内的缓存数据Count仍是旧数据的Count，该问题最终原因终于浮出水面。

4.解决办法：

**把addData(List)方法内更新数据的代码挪出来，和notifyDataSetChanged()方法一同放在Handler里，保证数据更新时及时通知ListView。**

5.注意事项：

为了尽量避免该问题，以后编程尽量从如下几个方面检查自己的代码：

- 确保Adapter的数据更新后一定要调用notifyDataSetChanged()方法通知ListView
- 数据更新和notifyDataSetChanged()放在UI线程内，且必须同步顺序执行，不可异步
- 仔细检查确认getCount()方法返回值是否正确

参考：
[http://www.cnblogs.com/monodin/p/3874147.html](http://www.cnblogs.com/monodin/p/3874147.html)