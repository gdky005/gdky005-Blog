title: Flutter 如何在切换页面的时候，把参数传到下一个页面?
date: 2019-09-10 17:27:24
categories: Flutter
keywords: Flutter
tags: Flutter
---


## 导语

2019年09月10日 Google 刚把 Flutter 1.9 版本发布，相信 2.0 应该不远了。这次更新将 Flutter 的 web 代码合并到主 repo 了，但是 web 的还处于测试版本，2.0 应该是包含 web 的，让我们期待吧。

![DraggedImage.png](https://raw.githubusercontent.com/gdky005/PictureResource/master/flutter_routes/DraggedImage.png)


看到升级提示，立马升级本地 Flutter SDK。


![DraggedImage-1.png](https://raw.githubusercontent.com/gdky005/PictureResource/master/flutter_routes/DraggedImage-1.png)


# 本期讲解Flutter 路由传递

这是一个大的概述图。


![DraggedImage-2.png](https://raw.githubusercontent.com/gdky005/PictureResource/master/flutter_routes/DraggedImage-2.png)


当 app 的页面变多的时候，就需要考虑页面传值的问题，在第一个页面如何把数据传递到 另外一个页面？最最基本的方法是在打开新页面，传递参数过去。但当 app 变得很大或者功能变多，你会发现传值是一件费劲的事情。例如前期设计的时候，只需要一个参数，但后面发现业务可能需要三个参数，如果再追加两个参数也不是不可以，就是不太优雅，而且可能要修改很多地方。

## 跳转到一个界面

![device-2019-09-09-184344 (1).gif](https://raw.githubusercontent.com/gdky005/PictureResource/master/flutter_routes/device-2019-09-09-184344.gif)


先简单解释一下，下面会使用到， App 启动一个主界面，然后点击中间按钮，会打开第二个界面。点击第二个界面的右上角，会返回到之前的界面。具体代码如下：

	import 'package:flutter/material.dart';
	import 'package:flutter/widgets.dart';
	
	class Test extends StatefulWidget {
	  @override
	  _TestState createState() => _TestState();
	}
	
	class _TestState extends State<Test> {
	  @override
	  Widget build(BuildContext context) {
	    return Scaffold(
	      appBar: AppBar(
	        title: Text('路由'),
	      ),
	      body: Center(
	        child: FloatingActionButton(
	          shape: BeveledRectangleBorder(),
	          child: Text('按钮'),
	          onPressed: () {
	            //使用路由打开 第二个界面
	            Navigator.push(
	                context, MaterialPageRoute(builder: (context) => TwoPage()));
	          },
	        ),
	      ),
	    );
	  }
	}
	
	class TwoPage extends StatefulWidget {
	  @override
	  _TwoPageState createState() => _TwoPageState();
	}
	
	class _TwoPageState extends State<TwoPage> {
	  @override
	  Widget build(BuildContext context) {
	    return Scaffold(
	      appBar: AppBar(
	        title: Text('第二页'),
	      ),
	      body: Column(
	        children: <Widget>[
	          SizedBox(height: 100),
	          Text('传递过来的值：\n'),
	          SizedBox(height: 100),
	          Center(
	            child: FloatingActionButton(
	              shape: BeveledRectangleBorder(),
	              child: Text('返回'),
	              onPressed: () {
	                // 返回到上一个界面
	                Navigator.pop(context);
	              },
	            ),
	          )
	        ],
	      ),
	    );
	  }
	}

## 使用构造传参

使用构造参数把参数传递过去，在 TwoPage 中接受参数可直接用 widget.x , x 表示 \_TwoPageState 传递过来的 widget 中包含值。

 
	class _TestState extends State<Test> {
	        ...
	    onPressed: () {
	        String name = "Wang";
	        String age = "99";
	
	        Navigator.push(context, MaterialPageRoute(builder: (context) => TwoPage(name, age)));
	      },
	    ...
	}
	
	class TwoPage extends StatefulWidget {
	  String name;
	  String age;
	
	  TwoPage(this.name, this.age);
	    ...
	    Text('传递过来的值：\n ${widget.name}_${widget.age}'),
	    ...
	}

### List（数组）传参
这里演示了 使用构造传递一个 List 类型的数, 使用 widget.data 的列表脚本获取数据。
  
	class _TestState extends State<Test> {
	        ...
	
	        dynamic data = ['name1', 'age'];
	    Navigator.push(context, MaterialPageRoute(builder: (context) => TwoPage(data)));
	
	    ...
	}
	
	class TwoPage extends StatefulWidget {
	    ...
	    dynamic data;
	    TwoPage(this.data);
	    ...
	}
	
	class _TwoPageState extends State<TwoPage> {
	    ...
	    Text('传递过来的值：\n ${widget.data[0]}_${widget.data[1]}'),
	    ...
	}
 

### Map 传参
这里演示了 使用构造传递一个 map 类型的数据。
  
	class _TestState extends State<Test> {
	        ...
	
	        dynamic map = {
	              'name': "Wang_Map",
	              'age': "99",
	            };
	    Navigator.push(context, MaterialPageRoute(builder: (context) => TwoPage(map)));
	
	    ...
	}
	
	class TwoPage extends StatefulWidget {
	    ...
	    dynamic map;
	    TwoPage(this.map);
	    ...
	}
	
	class _TwoPageState extends State<TwoPage> {
	    ...
	    Text('传递过来的值：\n ${widget.map["name"]}_${widget.map["age"]}'),
	    ...
	}
 


## 使用带名字的路由传参
很多时候我们项目比较大，为了统一管理，会使用带名字的路由概念，下面的路由字符串常量可以写成变量，这样后期不管怎么修改里面的值，依赖的地方都是不需要修改的。

## 传递一个参数
这里传递了一个 name 值，演示了一下 使用带名字的路由传递值。
 
	void main() => runApp(MyApp());
	
	class MyApp extends StatelessWidget {
	  @override
	  Widget build(BuildContext context) {
	    return MaterialApp(
	      initialRoute: "/",
	      routes: {
	        "/": (context) => OnePage(),
	        "/TwoPage": (context) => TwoPage(),
	      },
	    );
	  }
	}
	
	
	class OnePage extends StatefulWidget {
	  @override
	  _OnePageState createState() => _OnePageState();
	}
	
	class _OnePageState extends State<OnePage> {
	  @override
	  Widget build(BuildContext context) {
	    return Scaffold(
	      appBar: AppBar(
	        title: Text('路由页面'),
	      ),
	      body: Center(
	        child: FloatingActionButton(
	          shape: BeveledRectangleBorder(),
	          child: Text('按钮'),
	          onPressed: () {
	            String name = "Wang";
	
	            Navigator.pushNamed(context, '/TwoPage', arguments: name);
	
	          },
	        ),
	      ),
	    );
	  }
	}
	
	class TwoPage extends StatefulWidget {
	
	  @override
	  _TwoPageState createState() => _TwoPageState();
	}
	
	class _TwoPageState extends State<TwoPage> {
	  @override
	  Widget build(BuildContext context) {
	    dynamic name = ModalRoute.of(context).settings.arguments;
	
	    return Scaffold(
	      appBar: AppBar(
	        title: Text('第二页'),
	      ),
	      body: Column(
	        children: <Widget>[
	          SizedBox(height: 100),
	          Text('传递过来的值：\n $name'),
	          SizedBox(height: 100),
	          Center(
	            child: FloatingActionButton(
	              shape: BeveledRectangleBorder(),
	              child: Text('返回'),
	              onPressed: () {
	                Navigator.pop(context);
	              },
	            ),
	          )
	        ],
	      ),
	    );
	  }
	}


## 传递 List（数组）
我们这里传递的是 List，可以根据角标进行获取对应值，前提是对值了解，一般这里会传递一个列表展示或者一个 List 包含多个不同的值，方便从上一次获取。

	class _OnePageState extends State<OnePage> {
	    ...
	    onPressed: () {
	        dynamic listData = ['name1', 'age'];
	        Navigator.pushNamed(context, '/TwoPage', arguments: listData);
	    }
	    ...
	}
	
	class _TwoPageState extends State<TwoPage> {
	  @override
	  Widget build(BuildContext context) {
	    dynamic listData = ModalRoute.of(context).settings.arguments;
	    String name = listData[0];
	    String age = listData[1];
	
	        ...
	        SizedBox(height: 100),
	        Text('传递过来的值：\n $name _ $age'),
	        SizedBox(height: 100),
	        ...
	  }
	}



## 传递 Map

因为我们传递的是 map，所以在接收的时候需要做一次判断，用 is 判断，预防外界传入其他类型，造成我们程序红屏。这里可以从上一次获取多个不同的参数，使用不同名称获取，这里最好对接收到的值做判断，非空校验等。

	class _OnePageState extends State<OnePage> {
	    ...
	    onPressed: () {
	        dynamic listData = ['name1', 'age'];
	        Navigator.pushNamed(context, '/TwoPage', arguments: listData);
	    }
	    ...
	}
	
	class _TwoPageState extends State<TwoPage> {
	  @override
	  Widget build(BuildContext context) {
	    String name;
	    String age;
	
	    dynamic mapData = ModalRoute.of(context).settings.arguments;
	    // 可以做一次校验数据安全，防止类型不匹配
	    if (mapData is Map) {
	      Map data = mapData;
	      name = data['name'];
	      age = data['age'];
	    }
	
	        ...
	        SizedBox(height: 100),
	        Text('传递过来的值：\n $name _ $age'),
	        SizedBox(height: 100),
	        ...
	  }
	}


## 普通路由带参
和带路由名字的方式基本一样，不过写的方式略有不同，需要把参数放入 settings 后面。

	Navigator.of(context).push(new MaterialPageRoute(
	                    builder: (context) {
	                      return NewRouteWidget(); //写上要跳转到的页面
	                    },
	                    settings:
	                        RouteSettings(arguments: {'name': 'postbird'}), // 传参
	                    fullscreenDialog: true));


## 带参数从二级页面返回上一级

返回上一级：

	Navigator.pop(context);

带参数返回上一级:

	Navigator.pop(context, '返回的文本数据');

带类型的参数返回：

	Navigator.pop<Map>(context, mapData);
	Navigator.pop<List>(context, listData);
	Navigator.pop<T>(context, data);

在上一级接受返回的值：
这里使用 then 接受，也可以使用 await, 只是这种方法更容易理解和接受，逻辑更强，代码简洁。

	Navigator.pushNamed(context, '/TwoPage', arguments: mapData)
	                .then((dynamic data) {
	              print("返回的值是：$data");
	            });

<hr/>

### 往期推荐

[Flutter 学习脑图笔记，可方便查找与搜索！](http://mp.weixin.qq.com/s?__biz=MzI5MTM3Mzk4Mg==&mid=2247483767&idx=1&sn=d06bcf7edce0d6cd4f6101afb4dd968b&chksm=ec10d786db675e90b7cda7553d8a318f5da920310be138d189f8f66011a9ec5482e257749da5&scene=21#wechat_redirect) 

[在云盘至少以 1MB/S 的速度下载 『Flutter 入门到精通全套视频』](http://mp.weixin.qq.com/s?__biz=MzI5MTM3Mzk4Mg==&mid=2247483756&idx=1&sn=722b424055086420a063135679cf974a&chksm=ec10d79ddb675e8bbe4d4726e525aaa57fb840e365e6f087d18eed8dbcb6e3344398a3d64ae6&scene=21#wechat_redirect)

[Flutter 也能开发 Web? （文末提供 Flutter 学习视频地址哦）](http://mp.weixin.qq.com/s?__biz=MzI5MTM3Mzk4Mg==&mid=2247483723&idx=1&sn=4d3977575f5d9364c794f8dc451e64bc&chksm=ec10d7badb675eac1177a8a083cd04472a58ae16bf00608466a98fd3219210b5159e975f56b4&scene=21#wechat_redirect)

[马云说要 669，而我却整 Flutter 环境踩坑花了一整天 （附加：Flutter快速起步 短视频）](http://mp.weixin.qq.com/s?__biz=MzI5MTM3Mzk4Mg==&mid=2247483731&idx=1&sn=8b8e2618fe71b58270b5a3b9c777041f&chksm=ec10d7a2db675eb449eaa2bdb13dbcfc13c2c6462bd2b6c2dfb9f147fd476bf503306450164e&scene=21#wechat_redirect)

![image](https://upload-images.jianshu.io/upload_images/720880-019451fc09c241a7?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)


