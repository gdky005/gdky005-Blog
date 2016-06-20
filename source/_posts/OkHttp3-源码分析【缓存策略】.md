title: OkHttp3 源码分析【缓存策略】
date: 2016-06-20 16:35:10
categories:
keywords:
tags:
---

# OkHttp3 源码分析【缓存策略】
本文专门分析 OkHttp 的缓存策略，是 OkHttp 中最简单的一篇

### Http 缓存基础知识
分析源目前，我们先回顾一下 Http 的缓存 Header 的含义
1. Expires
表示到期时间，一般用在 response 报文中，当超过此事件后相应将被认为是无效的而需要网络连接，反之而是直接使用缓存
	Expires: Thu, 12 Jan 2017 11:01:33 GMT

2. Cache-Control
相对值，单位是秒，指定某个文件被续多少秒的时间，从而避免额外的网络请求。比expired更好的选择，它不用要求服务器与客户端的时间同步，也不用服务器时刻同步修改配置Expired中的绝对时间，而且它的优先级比Expires更高。比如简书静态资源有如下的header，表示可以续31536000秒，也就是一年。

	Cache-Control: max-age=31536000, public

3. 修订文件名（Reving Filenames）
如果我们通过设置 header 保证了客户端可以缓存的，而此时远程服务器更新了文件如何解决呢？这个时候可以通过修改 url 的文件名版本后缀进行缓存，比如下文是又拍云的公共CDN就提供了多个版本的JQuery
	upcdn.b0.upaiyun.com/libs/jquery/jquery-2.0.3.min.js

4. 条件 get 请求 （Conditional GET Requests） 与 304
如果缓存过期或者轻质放弃缓存，在此情况下，缓存策略全部交给服务器判断，客户端只用发送 条件 get 请求 即可，如果缓存是有效的， 则返回 304 not Modifiled, 否则直接返回 body.

请求的方式有两种：
- Last-Modified-Date:
	Last-Modified: Tue, 12 Jan 2016 09:31:27 GMT

客户端再次发送时，通过发送
	If-Modified-Since: Tue, 12 Jan 2016 09:31:27 GMT

交给服务器进行判断，如果任然可以缓存使用，服务器就返回 304.
- ETag
ETag 是对资源文件的一种摘要，客户端并不需要了解实现细节。当客户端第一次请求，服务器返回了
	ETag: "5694c7ef-24dc"

客户端再次请求时，通过发送
	If-None-Match:"5694c7ef-24dc"

交给服务器进行判断，如果还能使用缓存，服务器就返回 304

> 如果 ETag 和 Last-Modified 都有，则必须一次性都发给服务器，它们没有优先级之分，反正这里客户端没有任何判断的逻辑。

- 其他标签
	- no-cache/no-store: 不使用缓存
		- only-if-cached: 只使用缓存
		- Date:The date and time that the message was sent
		- Age： CDN 反代服务器 到原始服务器获取数据延迟的缓存时间

> "only-if-cached"标签非常具有诱导性，它只在请求中使用，表示无论是否有网完全只使用缓存（如果命中还好说，否则返回503错误/网络错误），这个标签比较危险。
> 全部的标签，可以到[这里看](https://en.wikipedia.org/wiki/List_of_HTTP_header_fields)

以上内容是作为一个服务器开发或者客户端的常识。下图是网上找的总结，注意图中的 ETag 和 Last-Modified 可能有优先级的歧义，你只需要记住它们是没有优先级的。

![](http://7xlcno.com1.z0.glb.clouddn.com/okhttp_cache_01.png)

### 源码分析
OkHttp 中使用了 CacheStrategy 实现了上午的流程图，他根据之前的缓存结果与当前将要发送 Request 的 header 进行策略分析，并得出是否要请求的结论。

1. 总体请求流程分析
CacheStrategy 类似一个 mapping 操作，将两个值输入，再将两值输出
Input（request, cacheCandidate） —-》 CacheStrategy(处理，判断 Header 信息) —-》Output(networkRequest, cacheResponse)

Request:
开发者手动编写并在 Interceptor 中递归加工而成的对象（需要调试分析的话，可以使用 logging-interceptor进行log操作），我们只需要知道母亲传入的 Request 没有任何关于 缓存的 Header。

cacheCandildate:
也就是上次与服务器交互缓存的 Response,可能为 null。 这里的缓存全部是基于文件 系统的 map ,key 是请求中url 的 md5, value 是在文件中查询到的缓存，页面置换基于 LRU 算法，我们现在只需要知道他是一个可以读取 缓存 Header 的 Response.

当 CacheStrategy 加工输出后，输出 networkRequest 与 cacheResponse， 根据是否为空执行不同的请求

![](http://7xlcno.com1.z0.glb.clouddn.com/okhttp_cache_02.png)

> 以上是对 networkRequest / cacheResponse进行 find usage 查询获得出的结论

基本上与上文中的图片完全一致，以上就是 OkHttp 的缓存策略。
> 关于此部分的分析，读者可以在HttpEngine对象中通过对userResponse进行findUsage分析得出，源码都是一大堆的if判断

2. CacheStrategy 的加工过程

CacheStrategy 使用 Factory模式进行构造，参数如下

	InternalCache responseCache = Internal.instance.internalCache(client);
	//cacheCandidate从disklurcache中获取
	//request的url被md5序列化为key,进行缓存查询
	Response cacheCandidate = responseCache != null ? responseCache.get(request) : null;
	//请求与缓存
	factory = new CacheStrategy.Factory(now, request, cacheCandidate);
	cacheStrategy = factory.get();
	//输出结果
	networkRequest = cacheStrategy.networkRequest;
	cacheResponse = cacheStrategy.cacheResponse;
	//进行一大堆的if判断，内容同上表格
	.....

可以看出Factory.get()是最关键的缓存策略的判断，我们点入get()方法，可以发现是对getCandidate()的一个封装，我们接着点开getCandidate()，全是if与数学计算，详细代码如下

	private CacheStrategy getCandidate() {
	  //如果缓存没有命中(即null),网络请求也不需要加缓存Header了
	  if (cacheResponse == null) {
	    //`没有缓存的网络请求,查上文的表可知是直接访问
	    return new CacheStrategy(request, null);
	  }
	
	  // 如果缓存的TLS握手信息丢失,返回进行直接连接
	  if (request.isHttps() && cacheResponse.handshake() == null) {
	    //直接访问
	    return new CacheStrategy(request, null);
	  }
	
	  //检测response的状态码,Expired时间,是否有no-cache标签
	  if (!isCacheable(cacheResponse, request)) {
	    //直接访问
	    return new CacheStrategy(request, null);
	  }
	
	  CacheControl requestCaching = request.cacheControl();
	  //如果请求报文使用了`no-cache`标签(这个只可能是开发者故意添加的)
	  //或者有ETag/Since标签(也就是条件GET请求)
	  if (requestCaching.noCache() || hasConditions(request)) {
	    //直接连接,把缓存判断交给服务器
	    return new CacheStrategy(request, null);
	  }
	  //根据RFC协议计算
	  //计算当前age的时间戳
	  //now - sent + age (s)
	  long ageMillis = cacheResponseAge();
	  //大部分情况服务器设置为max-age
	  long freshMillis = computeFreshnessLifetime();
	
	  if (requestCaching.maxAgeSeconds() != -1) {
	    //大部分情况下是取max-age
	    freshMillis = Math.min(freshMillis, SECONDS.toMillis(requestCaching.maxAgeSeconds()));
	  }
	
	  long minFreshMillis = 0;
	  if (requestCaching.minFreshSeconds() != -1) {
	    //大部分情况下设置是0
	    minFreshMillis = SECONDS.toMillis(requestCaching.minFreshSeconds());
	  }
	
	  long maxStaleMillis = 0;
	  //ParseHeader中的缓存控制信息
	  CacheControl responseCaching = cacheResponse.cacheControl();
	  if (!responseCaching.mustRevalidate() && requestCaching.maxStaleSeconds() != -1) {
	    //设置最大过期时间,一般设置为0
	    maxStaleMillis = SECONDS.toMillis(requestCaching.maxStaleSeconds());
	  }
	
	  //缓存在过期时间内,可以使用
	  //大部分情况下是进行如下判断
	  //now - sent + age + 0 < max-age + 0
	  if (!responseCaching.noCache() && ageMillis + minFreshMillis < freshMillis + maxStaleMillis) {
	    //返回上次的缓存
	    Response.Builder builder = cacheResponse.newBuilder();
	    return new CacheStrategy(null, builder.build());
	  }
	
	  //缓存失效, 如果有etag等信息
	  //进行发送`conditional`请求,交给服务器处理
	  Request.Builder conditionalRequestBuilder = request.newBuilder();
	
	  if (etag != null) {
	    conditionalRequestBuilder.header("If-None-Match", etag);
	  } else if (lastModified != null) {
	    conditionalRequestBuilder.header("If-Modified-Since", lastModifiedString);
	  } else if (servedDate != null) {
	    conditionalRequestBuilder.header("If-Modified-Since", servedDateString);
	  }
	  //下面请求实质还说网络请求
	  Request conditionalRequest = conditionalRequestBuilder.build();
	  return hasConditions(conditionalRequest) ? new CacheStrategy(conditionalRequest,
	      cacheResponse) : new CacheStrategy(conditionalRequest, null);
	}

太长不看的话，大多数常见的情况可以用这个估算

	now - sent + age < max-age

> 这里有个技巧，对构造函数进行findUsage查询，就可以看出各个输出是否为空的结果，然后各个击破分析
> ![](http://7xlcno.com1.z0.glb.clouddn.com/okhttp_cache_03.png)

### 结论
根据上面的分析，我们可以发现，okhttp 实现的缓存策略实质上就是大量的 if 判断集合，这些事根据 RFC 标准文件写死的，并没有相当难的技巧。

1. 通过上面的分析，我们可以发现，okhttp实现的缓存策略实质上就是大量的if判断集合，这些是根据RFC标准文档写死的，并没有相当难的技巧。
2. OkHttp 的缓存是自动完成的，玩去由服务器 Header 决定，自己 **没有必要** 进行控制。网上热传的文件中在 Interceptor 中手动天阿基缓存代码控制，它固然有用，但是属于 Hack 式的利用，违反了 RFC 的文档标准，不建议使用，OkHttp 的官方缓存控制在 [注释中](https://github.com/square/okhttp/blob/d662c1a82851800c46ad8ede2d9d10d10427fdad/okhttp/src/main/java/okhttp3/Cache.java#L79)。 如果读者的需求是对象持久化，建议使用文件存储或者 数据库即可（比如 realm）.
3. 充分利用 idea 的 findUsage 的功能，源码的各个跳转条件都能很快分析完成
4. 可以使用 alt + space  快速预览某个函数
![](http://7xlcno.com1.z0.glb.clouddn.com/okhttp_cache_04.png)




摘自：[http://www.jianshu.com/p/9cebbbd0eeab](http://www.jianshu.com/p/9cebbbd0eeab)


























