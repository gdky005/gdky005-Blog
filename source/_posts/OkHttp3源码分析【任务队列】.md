title: OkHttp3源码分析【任务队列】
date: 2016-06-20 16:34:26
categories:
keywords:
tags:
---

# OkHttp3源码分析【任务队列】

### 本文目录
1. 线程池基础
2. 反向代理模块
3. OkHttp 的任务调度

OkHttp拥有两种运行方式，一种是同步阻塞调用并直接返回的形式，另一种是通过内部线程池分发调度实现非阻塞的一步回调。本文主要分析第二种，即 OkHttp 在多并发网络下的分发调度过程。本文主要分析的是 Dispatcher 对象。


# 线程池基础
 1. 线程池好处有哪些
线程池的关键在于线程复用以减少非核心任务的损耗。以下参考自 IBM 知识库：

多线程技术主要解决 处理器单元时间内多个线程执行的问题，他可以显著减少处理器单元内的闲置时间，增加处理器单元的吞吐能力。但如果对多线程应用不当，会增加对单个任务的处理时间。可以举例：
如果一台服务完成一项任务的时间为 T

	T1 创建线程的时间
	T2 在线程中执行任务的时间，包括线程间同步所需时间
	T3 线程销毁的时间

显然T ＝ T1＋T2＋T3。注意这是一个极度简化的假设。

可以看出 T1 T3 是多线程本身带来的开销，我们渴望减少 T1，T3的时间，从而减少 T 的时间。但一些线程的使用者并没有注意到这一点，多余在程序中 频繁的创建或销毁线程，导致 T1 T3 占的比例更高。显然这是突出了线程的弱点（T1，T3），而不是有点（并发性）。

线程池的技术是关注如何缩短或调整 T1，T3 的时间的技术，从而提高服务器程序性能。
- 通过对线程缓存，减少创建和销毁的时间损失
	- 通过控制线程数据的阈值，减少当线程过少带来的 CPU 闲置（比如说 长时间卡在I/O 上）与线程过多时对 JVM 对对内存与线程切换压力

在 Java 中，我们可以通过 线程池工厂 或者 自定义参数 来创建 线程池。这里就不说了

2. OkHttp 配置的线程池

在 OkHttp 中，使用如下构造了单例线程池
	public synchronized ExecutorService executorService() {
	  if (executorService == null) {
	    executorService = new ThreadPoolExecutor(0, Integer.MAX_VALUE, 60, TimeUnit.SECONDS,
	        new SynchronousQueue<Runnable>(), Util.threadFactory("OkHttp Dispatcher", false));
	  }
	  return executorService;
	}

参数说明：
- int corePoolSize: 最小并发线程数，这里并发同时包括空闲与活动的线程，如果是0的话，空闲一段时间后所有线程将全部被销毁。
- int maximumPoolSize: 最大线程数，当任务进来时可以扩充的线程最大值，当大于了这个值就会根据丢弃*处理机制*来处理
- long keepAliveTime: 当线程数大于corePoolSize时，多余的空闲线程的最大存活时间，类似于HTTP中的Keep-alive
- TimeUnit unit: 时间单位，一般用秒
- BlockingQueue\<Runnable\> workQueue:  工作队列
- ThreadFactory threadFactory: 单个线程的工厂，可以打Log，设置Daemon(即当JVM退出时，线程自动结束)等

可以看出，在 OkHttp 中，构建了一个阈值为【0， Integer.Max\_value】的线程池，她不好留任何最先线程数，随时创建更多的线程数，当线程空闲时只能活 60秒，它使用另一个不存储元素的阻塞工作队列， 一个叫做 "OkHttp Dispatcher" 的线程工厂。

也就是说， 在实际运行中，当收到10个并发请求是，线程池会创建十个线程，当工作完成后，线程池会在60s 后相继关闭所有线程。

> 在RxJava的Schedulers.io()中，也有类似的设计，最小的线程数量控制，不设上限的最大线程，以保证I/O任务中高阻塞低占用的过程中，不会长时间卡在阻塞上，有兴趣的可以分析RxJava中4种不同场景的Schedulers

### 反向代理模型
在 OkHttp 中，使用了与 Nginx 类似的反向代理与分发技术，这是典型的 单生产者多消费者的问题。

我们知道在Nginx中，用户通过HTTP(Socket)访问前置的服务器，服务器会自动转发请求给后端，并返回后端数据给用户。通过将工作分配给多个后台服务器，可以提高服务的负载均衡能力，实现**非阻塞、高并发连接**，避免资源全部放到一台服务器而带来的负载，速度，在线率等影响。
![](http://7xlcno.com1.z0.glb.clouddn.com/okhttp_task_queue_01.png)

而在 OkHttp 中，非常类似上面的场景，它使用 Dispatcher 作为任务的转发器，线程池对应多台后置服务器，用 AsyncCall 对应 Socket 请求，用 Deque\<readyAsyncCalls\>对应 Nginx 的内部缓存

![](http://7xlcno.com1.z0.glb.clouddn.com/okhttp_task_queue_02.png)

具体成员如下：

- maxRequests = 64：最大并发请求数为64
- maxRequestsPerHost = 5：每个主机最大请求数为5
- Dispatcher：分发者，也就是生产者（默认在主线程）
- AsyncCall：队列中需要处理的Runnable（包装了异步回调接口）
- ExecutorService：消费者池（也就是线程池）
- Deque\<readyAsyncCalls\>：缓存（用数组实现，可自动扩容，无大小限制）
- Deque\<runningAsyncCalls\>：正在运行的任务，仅仅是用来引用正在运行的任务以判断并发量，注意它并不是消费者缓存

通过将请求任务分发给多个线程，可以显著减少 I/O 等待时间

### OkHttp 的任务调度
当我们使用 OkHttp 的异步请求时，一般进行如下构造：
	OkHttpClient client = new OkHttpClient.Builder().build();
	Request request = new Request.Builder()
	    .url("http://qq.com").get().build();
	client.newCall(request).enqueue(new Callback() {
	  @Override public void onFailure(Call call, IOException e) {
	
	  }
	
	  @Override public void onResponse(Call call, Response response) throws IOException {
	
	  }
	});

当 HttpClient 的请求入队 时，根据代码，我们可以发现实际上是 Dispatcher 进行了 入队 操作

	synchronized void enqueue(AsyncCall call) {
	  if (runningAsyncCalls.size() < maxRequests && runningCallsForHost(call) < maxRequestsPerHost) {
	      //添加正在运行的请求
	    runningAsyncCalls.add(call);
	       //线程池执行请求
	    executorService().execute(call);
	  } else {
	      //添加到缓存队列
	    readyAsyncCalls.add(call);
	  }
	}

可以发现请求是否进入缓存的条件如下：
	(runningRequests<64 && runningRequestsPerHost<5)

如果满足条件，那么久直接把 AsyncCall 直接加到 runningCalls 的队列中，并在现场中执行（线程池会根据当前负载自动创建，销毁，缓存相应的线程）。反之就放入readyAsyncCalls进行缓存等待。

我们再分析请求元素AsyncCall（本质是实现了Runnable接口），它内部的 execute方法是：
	@Override protected void execute() {
	  boolean signalledCallback = false;
	  try {
	      //执行耗时IO任务
	    Response response = getResponseWithInterceptorChain(forWebSocket);
	    if (canceled) {
	      signalledCallback = true;
	      //回调，注意这里回调是在线程池中，而不是想当然的主线程回调
	      responseCallback.onFailure(RealCall.this, new IOException("Canceled"));
	    } else {
	      signalledCallback = true;
	      //回调，同上
	      responseCallback.onResponse(RealCall.this, response);
	    }
	  } catch (IOException e) {
	    if (signalledCallback) {
	      // Do not signal the callback twice!
	      logger.log(Level.INFO, "Callback failure for " + toLoggableString(), e);
	    } else {
	      responseCallback.onFailure(RealCall.this, e);
	    }
	  } finally {
	      //最关键的代码
	    client.dispatcher().finished(this);
	  }
	}

当任务执行完成后，无是否有 异常，finally 代码段总会被执行，也就是会调用 Dispatcher 的 finished 函数，打开源码，就能发现它将正在运行的任务 Call从 队列 runningAsyncCalls 中移除后，执行 promoteCalls()函数
	private void promoteCalls() {
	    //如果目前是最大负荷运转，接着等
	  if (runningAsyncCalls.size() >= maxRequests) return; // Already running max capacity.
	  //如果缓存等待区是空的，接着等
	  if (readyAsyncCalls.isEmpty()) return; // No ready calls to promote.
	
	  for (Iterator<AsyncCall> i = readyAsyncCalls.iterator(); i.hasNext(); ) {
	    AsyncCall call = i.next();
	
	    if (runningCallsForHost(call) < maxRequestsPerHost) {
	        //将缓存等待区最后一个移动到运行区中，并执行
	      i.remove();
	      runningAsyncCalls.add(call);
	      executorService().execute(call);
	    }
	
	    if (runningAsyncCalls.size() >= maxRequests) return; // Reached max capacity.
	  }
	}

这样，就主动的把缓存队列向前走了一步，而没有使用锁等复杂编码

### Summary
通过上述的分析，我们知道了：
1. OkHttp 采用 Dispatcher 技术，类似于 Nginx, 与线程池配合实现高并发，低阻塞的运行
2. OkHttp 采用 Deque 作为缓存，按照入队的顺序先进先出
3. OkHttp 最出彩的地方就是在 try/finally 中调用了 finished 函数，可以主动控制等待队列的移动，而不是采用锁，极大减少了编码复杂度







摘自：[http://www.jianshu.com/p/6637369d02e7](http://www.jianshu.com/p/6637369d02e7)













