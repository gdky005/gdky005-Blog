title: 从 volley 切换到 OkHttp 遇到的坑
date: 2016-06-20 18:17:30
categories: okhttp3
keywords: okhttp3
tags: okhttp3
---

这几天打算把项目的 volley 切换到 okhttp,遇到了一些小问题，现在予以整理。

之前考虑直接将 volley 切换到 okhttp, 底层肯定使用 okhttp, 请求队列也使用 okhttp。但是考虑到代价可能比较大，所以我是基于网上给的解决方案： 上层队列依然使用 volley,但是对于底层发送请求的地方，可以直接切换到 okhttp.

### 代理异常？
切换成功后，遇到的第一个问题就是：代理功能没法使用，我们客户端 是有联通流量包的功能的，因此必须要加 代理功能。

根据 okhttp 里面  issue 的回答，弄好多次都不行，折腾了一两天左右。 最后也懒得管了，先放放，优先解决其他问题。 

之后过了几天，再回来弄这块的时候，就突然好了，兴奋坏了。赶紧查查和之前的有没有什么差异？

经过对比后发现：原来是 之前写 volley 的时候是这样的：

		HttpURLConnection connection;
	        if ("https".equals(url.getProtocol())) {
	            Proxy proxy = new Proxy(Proxy.Type.HTTP,
	                    InetSocketAddress
	                            .createUnresolved(FLOWPACKAGEHOST, FLOWPACKAGETCPPORT));
	            connection = (HttpURLConnection) url
	                    .openConnection(proxy);
	            connection.addRequestProperty("Proxy-Authorization",
	                    "Basic MzAwMDAwNDU4NDo0Mjk5NjREMEI1Q0QyODc6");
	        } else {
	            Proxy proxy = new Proxy(Proxy.Type.HTTP,
	                    InetSocketAddress
	                            .createUnresolved(FLOWPACKAGEHOST, FLOWPACKAGEPORT));
	            connection = (HttpURLConnection) url
	                    .openConnection(proxy);
	            connection.addRequestProperty("Authorization",
	                    "Basic MzAwMDAwNDU4NDo0Mjk5NjREMEI1Q0QyODc6");
	        }
	        connection.addRequestProperty("Proxy-Connection", "Keep-Alive");

主要区分了 https 和 http, 然后里面传入的 key 和 端口号都不一样。

但是在 okhttp 里面貌似是不需要区分的。只需要这样写：

	/**
	     * 设置联通流量 代理功能
	     * @param builder
	     */
	    private void setUnicomProxy(OkHttpClient.Builder builder) {
	        //添加联通代理功能
	        if (TrafficUtil.getUnicomProxyAvailable()) {
	            Authenticator proxyAuthenticator = new Authenticator() {
	                @Override
	                public okhttp3.Request authenticate(Route route, Response response) throws IOException {
	                    return response.request().newBuilder().header("Proxy-Authorization", "Basic " +
	                            "MzAwMDAwNDU4NDo0Mjk5NjREMEI1Q0QyODc6").header("Proxy-Connection",
	                            "Keep-Alive").build();
	                }
	            };
	
	            builder.proxy(new Proxy(Proxy.Type.HTTP, new InetSocketAddress(FLOWPACKAGEHOST,
	                    FLOWPACKAGETCPPORT)));
	            builder.proxyAuthenticator(proxyAuthenticator);
	        }
	    }

就可以了。
` FLOWPACKAGEHOST -> test.proxy.1111.com (这是域名)`
` FLOWPACKAGETCPPORT -> 8143
`
这还是真是一个偶然的机会，歪打正着，否则估计得排除好久。

`备注： 上面 key 我随意修改了几个字符，看看就行，想要直接使用肯定不行的， 哈哈`

### SSL/STL证书 出错？
这是第二个遇到的问题，证书一直没法用，一使用 https 的接口就失败。最后解决办法是：
	 @NonNull
	    private SSLContext getSslContext(InputStream... certificates) {
	        SSLContext sslContext = null;
	
	        try {
	            CertificateFactory certificateFactory = CertificateFactory.getInstance("X.509");
	            KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
	            keyStore.load(null);
	            int index = 0;
	            for (InputStream certificate : certificates) {
	                String certificateAlias = Integer.toString(index++);
	                keyStore.setCertificateEntry(certificateAlias, certificateFactory.generateCertificate
	                        (certificate));
	                try {
	                    if (certificate != null)
	                        certificate.close();
	                } catch (IOException e) {
	                    e.printStackTrace();
	                }
	            }
	
	            sslContext = SSLContext.getInstance("TLS");
	
	            TrustManagerFactory trustManagerFactory =
	                    TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
	
	            trustManagerFactory.init(keyStore);
	            sslContext.init(null, trustManagerFactory.getTrustManagers(),
	                    new SecureRandom());
	
	        } catch (KeyStoreException e) {
	            e.printStackTrace();
	        } catch (IOException e) {
	            e.printStackTrace();
	        } catch (NoSuchAlgorithmException e) {
	            e.printStackTrace();
	        } catch (KeyManagementException e) {
	            e.printStackTrace();
	        } catch (Exception e){
	            e.printStackTrace();
	        }   finally {
	        }
	
	        return sslContext;
	    }

	/**
	     * 启用 OkHttps 域名校验功能
	     * @param builder
	     */
	    private void setOkhttpSSLContext(OkHttpClient.Builder builder) {
	        SSLContext sslContext = getSslContext(KaolaApplication.mContext.getResources().openRawResource(R
	                .raw.kl_magic));
	
	        if (sslContext != null) {
	            builder.hostnameVerifier(new HostnameVerifier() {
	                @Override
	                public boolean verify(String hostname, SSLSession session) {
	                    HostnameVerifier hostnameVerifier = HttpsURLConnection.getDefaultHostnameVerifier();
	                    return hostnameVerifier.verify("xxx.com", session); //启用xxx 域名校验 (这里是一个非常重要的地方，缺少了这一步肯定不行)
	                }
	            });
	            builder.sslSocketFactory(sslContext.getSocketFactory());
	        }
	    }

这句是非常重要的：
`hostnameVerifier.verify("xxx.com", session);  
`主要是进行 https 的域名校验，用证书匹配你的域名，如果匹配成功，那么就可以直接使用，否则 https 握手失败，无法正确发送请求。

之前尝试过使用 使用
	builder.hostnameVerifier(new AllowAllHostnameVerifier());

这样可以忽略证书，默认都允许，也能正常使用，但是 存在安全隐患。

### post 参数不能为空？
这个问题遇到的比较奇葩，原因是，我们的 https  的接口使用了 post 请求，但是 post 里面没有参数，通用参数都放在 url 后面追加了，这就造成 这个 request 没有 body( body 就是对 post 请求的参数 处理下).

但是 okhttp 对于参数为空的请求，直接返回 null, 所以对于这种不规范的 接口定义就报错了。在 [okhttp issue](https://github.com/square/okhttp/issues/751) 里面也有关于这个的讨论，说明这个不符合 http 的标准，所以不能发出请求。解决办法是添加一个空的 参数就可以，但是绝不能 “”:””, 里面必须有值，因此我这边和服务器约定了一下，用 temp 代替，服务器也肯定不会用这个字段取数据。

具体参考这个：

	public void addRequest(int method, final Map<String, String> params, final String baseUrl,
	                           final TypeReference<? extends BaseResponse> type, final JsonResultCallback callback) {
				......
	          if (params.size() == 0 && method == Request.Method.POST)
	            params.put("temp", "temp"); //解决 method POST must have a request body.;
				......
	}

这三个问题解决后，基本就可以放心使用了。