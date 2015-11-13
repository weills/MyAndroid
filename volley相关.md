#Volley相关#
##使用##
1.创建一个RequestQueue对象

         RequestQueue mQueue = Volley.newRequestQueue(context);  
2.创建一个StringRequest（jsonRequest ImageRequest）对象。

         StringRequest stringRequest = new StringRequest("http://www.baidu.com",
						new Response.Listener<String>() {
							@Override
							public void onResponse(String response) {
								Log.d("TAG", response);
							}
						}, new Response.ErrorListener() {
							@Override
							public void onErrorResponse(VolleyError error) {
								Log.e("TAG", error.getMessage(), error);
							}
						});

3将相应request对象添加到RequestQueue里面

         mQueue.add(stringRequest);  

*关于imageRequest有要注意*
（图片相关开源类库：volley imageloader xutils fresco,Picasso glide等）


##关于volley源码部分##
![](https://raw.githubusercontent.com/android-cn/android-open-project-analysis/master/tool-lib/network/volley/image/design.png)
    (图片来自于github)


上面是 Volley 的总体设计图，主要是通过两种Dispatch Thread不断从RequestQueue中取出请求，根据是否已缓存调用Cache或Network这两类数据获取接口之一，从内存缓存或是服务器取得请求的数据，然后交由ResponseDelivery去做结果分发及回调处理。



RequestQueue：表示请求队列，里面包含一个CacheDispatcher(用于处理走缓存请求的调度线程)、NetworkDispatcher数组(用于处理走网络请求的调度线程)，一个ResponseDelivery(返回结果分发接口)，通过 start() 函数启动时会启动CacheDispatcher和NetworkDispatchers。

Cache：缓存请求结果，Volley 默认使用的是基于 sdcard 的DiskBasedCache。NetworkDispatcher得到请求结果后判断是否需要存储在 Cache，CacheDispatcher会从 Cache 中取缓存结果。
##Volley.java##
Volley.java 有两个重载的静态方法。

public static RequestQueue newRequestQueue(Context context)

public static RequestQueue newRequestQueue(Context context, HttpStack stack)
第一个方法的实现调用了第二个方法，传 HttpStack 参数为 null。


第二个方法中，如果 HttpStatck 参数为 null，则如果系统在 Gingerbread 及之后(即 API Level >= 9)，采用基于 HttpURLConnection 的 HurlStack，如果小于 9，采用基于 HttpClient 的 HttpClientStack。

    if (stack == null) {
    if (Build.VERSION.SDK_INT >= 9) {
        stack = new HurlStack();
    } else {
        stack = new HttpClientStack(AndroidHttpClient.newInstance(userAgent));
    }
    }
得到了 HttpStack,然后通过它构造一个代表网络（Network）的具体实现BasicNetwork。
接着构造一个代表缓存（Cache）的基于 Disk 的具体实现DiskBasedCache。
最后将网络（Network）对象和缓存（Cache）对象传入构建一个 RequestQueue，启动这个 RequestQueue，并返回。

    Network network = new BasicNetwork(stack);
     RequestQueue queue = new RequestQueue(new DiskBasedCache(cacheDir), network);
    queue.start();
    return queue;
我们平时大多采用Volly.newRequestQueue(context)的默认实现，构建 RequestQueue。
通过源码可以看出，我们可以抛开 Volley 工具类构建自定义的 RequestQueue，采用自定义的HttpStatck，采用自定义的Network实现，采用自定义的 Cache 实现等来构建RequestQueue

##HttpURLConnection 和 AndroidHttpClient(HttpClient 的封装)如何选择及原因##
在 Froyo(2.2) 之前，HttpURLConnection 有个重大 Bug，调用 close() 函数会影响连接池，导致连接复用失效，所以在 Froyo 之前使用 HttpURLConnection 需要关闭 keepAlive。
另外在 Gingerbread(2.3) HttpURLConnection 默认开启了 gzip 压缩，提高了 HTTPS 的性能，Ice Cream Sandwich(4.0) HttpURLConnection 支持了请求结果缓存。
再加上 HttpURLConnection 本身 API 相对简单，所以对 Android 来说，在 2.3 之后建议使用 HttpURLConnection，之前建议使用 AndroidHttpClient。
            

