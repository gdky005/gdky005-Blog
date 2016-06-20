title: Okhttp3源码分析【DiskLruCache】
date: 2016-06-20 15:57:46
categories: okhttp3
keywords: okhttp3
tags: okhttp3
---
# Okhttp3源码分析【DiskLruCache】

### 本文目录
- Cache 的简介
- LinkedHashMap 原理
- OkHttp 的文件系统

本文对 put/get 过程进行分析，注意缓存的判断依据不是本文， 而是 **缓存策略**

### 1.Cache 的简介
缓存，顾名思义，也就是方便用户快速的获取值的一种储存方式。小到与CPU同频的昂贵的缓存颗粒，内存，硬盘，网络，CDN反代缓存，DNS递归查询，OS页面置换，都可以看作缓存。它有如下的特点：
1. 缓存载体与持久载体总是相对的，容量远远小于持久容量，成本高于持久容量，速度高于持久容量。比如硬盘与网络，目前主流的SSD硬盘可以达到500MB/S，而很多地区网速却只有4M，将网络中的文件存到硬盘中，硬盘就相当于缓存；再比如内存与硬盘，主流的DDR3内存的速度可以达到10GB/S，而硬盘相对的慢了很多数量级别，将硬盘的游戏加载到内存，内存就相对于硬盘是一种缓存。
2. 需要实现 *排序依据*， 子啊 java 中，可以使用 Comparable\<T\>作为排序的接口。
3. 需要一种 *页面置换算法* 将旧页面取代去掉 换成新页面，如 最久未使用算法（LRU）、先进先出（FIFO）、最近最少使用算法（LFU）、非最近使用算法（NMRU）等
4. 如果缓存中没有，就需要从原始地址获取，这个步骤叫做『回源』，CDN厂商会标注“回源率”作为卖点

在 OkHttp 中，使用 FileSystem 作为缓存载体（磁盘相对于网络的缓存），使用 LRU 作为 页面置换算法 （封装了 LinkedHashMap）。

> 1.Comparable\<T\>是java用来排序的接口，推荐参考阅读《Java Software Structures Designing and Using Data Structures》
> 2.页面置换算法可以参考阅读《现代操作系统》的中译本

### 2.LinkedHashMap 原理
1. 源码概述分析
在学之前，应该先了解下 LinkedHashMap。 LinkedHashMap 继承于 HashMap.

在 HashMap 中，维护了一个 Node\<K,V\>[] table,当 put操作时，将元素按照计算后的 hash 值 放入到 数组相应位置 table[has] 中，最后迭代式，从 table[0] 开始向后迭代，具体的顺序取决于元素的 HashCode, 所以我们常说 HashMap 的元素迭代是不可预测的。

而在 LinkedHashMap 中，除了 Node\<K,V\>[](#) table， 还维护着 Entry\<K,V\> head,tail。 当 put 元素后，调用以下回调函数 对链表 将元素移动到链尾 已经清理旧元素
	// move node to last
	void afterNodeAccess(Node<K,V> e)
	// possibly remove eldest
	void afterNodeInsertion(boolean evict)

当 get 元素时，如果设置 accessOrder 为 true 时，通过调用如下回调 元素到链尾， 这里强调 移动，如果元素本身已经在 链表中，那它只会移动，而不是新建
	// move node to last
	void afterNodeAccess(Node<K,V> e)

综上，当你返回对元素进行 get/put 操作时，经常使用的元素会被移动到 tail 中，而长期不用的元素 会被移动到 head

最后 迭代时，迭代是从旧元素 迭代到新元素，这就是 LRU 的实现
	head <--> .... <--> tail
	
	旧元素 <-----------> 反复使用的新元素

在 Okhttp 中，使用了 DiskLruCache 对 LinkedHashMap 进行封装实现了 LRU， 如图进行初始化
	//按照访问顺序排序的Map，设置accessOrder为true
	map = new LinkedHashMap<>(0, 0.75f, true);

### 3.OkHttp 的文件系统
OkHttp 中的关键对象如下：
- FileSystem: 使用 Okio 对 File 的封装，简化了 IO 操作
- DiskLruCache.Editor: 添加了同步锁，并对 FileSystem 进行高度封装
- DiskLruCache.Entry: 维护着 key 对应的多个文件
- Cache.Entry: Response java 对象 与 Okio 流 的序列化/反序列化类
- DiskLruCache: 维护着文件的创建， 清理，读取。 内部有线程池，LinkedHashMap（也是 LruCache）
- Cache: 被上级代码调用，提供透明的 put/get 操作，封装了缓存检查条件与 DiskLruCache, 开发者只用配置大小即可，不需要手动管理
- Response/Request: OkHttp 的请求与回应

1. 文件初级封装（FileSystem）
众所周知，文件读写是流操作，是一堆 try catch 操作，在 OkHttp 中设计了 FileSystem.SYSTEM 作为文件层的管理。通过用 Okio 库中的 source/sink对 File 进行包装，而不用更为头痛 的 Inputstream 这类东西，使用上层调用与管道操作一样简单。
	File(低级操作，步骤繁琐) -> Okio(封装) －> FileSystem(友好工具类)

Okio 很不错，可以去[这里](https://github.com/square/okio)查看。

2. 文件高级封装（DiskLruCache.Entry/Editor/Snapshot）
本部分进行了如下操作，进行了实际的 put/get 操作
	FileSystem <-- DiskLruCache.Entry/Editor --> source/sink(更少参数)

DiskLruCache.Entry 针对每个请求的 url 对应文件进行维护（而没有进行创建/读取等操作）， 它内部维护了2个 File数组，一般来说 每个 url 对应对应2~4个文件。 文件名的规则是{md5(url) + {0,1}}, 后面的 0 或者 1 ，分别表示 ENTRY\_METADATA 与 ENTRY\_BODY。

比如在缓存的路径下执行 ls,结果如下：
	$ ls
	5716ab0f06c49bc7cf602397c51d5677.0
	5716ab0f06c49bc7cf602397c51d5677.1
	5b2f52377611dc6201a1871bdb997466.0
	5b2f52377611dc6201a1871bdb997466.1
	journal
	.....

DiskLruCache.Editor 对工具类 FileSystem 进行进一步的封装， 它以 DiskLruCache.Entry 作为构造参数，通过操控 Entry 中 维护的数组，对外暴露 source/sink ,为上层 的 java对象与文件的转换提供基于 okio 的流操作，我们可以通过对它 的两个方法进行 FindUsage 查询获得 OkHttp 关于文件读写的全部场景

- 写入场景：第一个位置是写入元信息，也就是写入末尾是0的文件中，是序列化的过程；第二个位置是写入 body,也就是写入末位是1的文件中，是存二进制的过程。
![](DraggedImage.png)
- 读取场景：读取时，需要获取快照，通过调用链分析
![](DraggedImage-1.png)

3. 序列化与反序列化（Cache.Entry）
文本的存储本质上也是序列化与反序列化的过程。本部分提供了下图的转变
> Resonse(java对象) \<--- Cache.Entry ---\> source/sink(文件io)

可以通过 find usage 位置相同，概括如下：
如果信息本身是二进制，就直接写入到文件中；如果文本是信息，就按照预设的格式写入即可。
	至于序列化后的东西到底是什么，可以直接在shell下运行cat命令或者打开文本编辑器进行输出查看。
	
	注意这里的Cache.Entry与上面的DiskLruCache.Entry是两个完全不同的对象

4. 缓存的自动清理
在 DiskLruCache 初始化时，将简历线程池，最少0个线程池，最大一个线程，线程空闲可活60s, 线程名叫做【OkHttp DiskLruCache】,当 JVM 退出时，线程自动结束。

	new ThreadPoolExecutor(0, 1, 60L, TimeUnit.SECONDS,
	        new LinkedBlockingQueue<Runnable>(), Util.threadFactory("OkHttp DiskLruCache", true))

当需要清理时，执行清理任务，它将在每次 get/set 后调用

	private final Runnable cleanupRunnable = new Runnable() {
	  public void run() {
	    synchronized (DiskLruCache.this) {
	      if (!initialized | closed) {
	        return; // Nothing to do
	      }
	      try {
	        //遍历LRU缓存(从旧到新进行遍历map),并删除文件
	        //直到小于MaxSize为止
	        trimToSize();
	        if (journalRebuildRequired()) {
	          rebuildJournal();
	          redundantOpCount = 0;
	        }
	      } catch (IOException e) {
	        throw new RuntimeException(e);
	      }
	    }
	  }
	};

### 总结
1. OkHttp 通过对文件进行了多次封装，实现了简单的I/O 操作
2. OKHttp 通过对请求 url进行 md5 实现了与文件的映射，实现写入，删除的操作
3. OkHttp 内部维护着清理线程池，实现对缓存文件的自动清理





























