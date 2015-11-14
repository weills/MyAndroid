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

##CacheDispatcher##

        mCacheDispatcher = new CacheDispatcher(mCacheQueue, mNetworkQueue, mCache, mDelivery);
如果使用缓存直接就将Request放入缓存队列mCacheQueue中了，使用mCacheQueue的位置就是CacheDispatcher，CacheDispatcher的构造函数中传入了缓存队列mCacheQueue、网络队列mNetworkQueue、缓存对象mCache及结果派发器mDelivery。

CacheDispatcher继承自Thread，当被start后就执行它的run方法
每个Request都可以从中得到CacheKey，看对应的CacheKey在缓存mCache中是否存在
如果缓存不存在就加到网络队列mNetworkQueue中继续取下一个请求
如果缓存存在，判断是否过期


##NetworkDispatcher##

    NetworkDispatcher networkDispatcher = new NetworkDispatcher(mNetworkQueue, mNetwork, mCache, mDelivery);

 NetworkDispatcher的工作方法同CacheDispatcher一样，继承自Thread，当被start后，不停地从mNetworkQueue取请求，然后通过Network接口请求网络。

当拿到请求结果后，如果服务器返回304（自上次请求后结果无变化）并且结果已经通过缓存派发了（即这次是读了缓存后的Refresh），那么什么也不做，否则调用Request的parseNetworkResponse解析请求结果，如果需要进行缓存，并派发结果


##ResponseDelivery##

派发请求结果的接口，有一个子类ExecutorDelivery执行实际操作，构造ExecutorDelivery的对象时需要一个Handler对象，当向ExecutorDelivery请求派发结果时会向这个Handler post消息。


##StringRequest 源码##
            
    /**
        * A canned request for retrieving the response body at a given URL as a String.
       */
    public class StringRequest extends Request<String> {
    private final Listener<String> mListener;

    /**
     * Creates a new request with the given method.
     *
     * @param method the request {@link Method} to use
     * @param url URL to fetch the string at
     * @param listener Listener to receive the String response
     * @param errorListener Error listener, or null to ignore errors
     */
    public StringRequest(int method, String url, Listener<String> listener,
            ErrorListener errorListener) {
        super(method, url, errorListener);
        mListener = listener;
    }

    /**
     * Creates a new GET request.
     *
     * @param url URL to fetch the string at
     * @param listener Listener to receive the String response
     * @param errorListener Error listener, or null to ignore errors
     */
    public StringRequest(String url, Listener<String> listener, ErrorListener errorListener) {
        this(Method.GET, url, listener, errorListener);
    }

    @Override
    protected void deliverResponse(String response) {
        mListener.onResponse(response);
    }

    @Override
    protected Response<String> parseNetworkResponse(NetworkResponse response) {
        String parsed;
        try {
            parsed = new String(response.data, HttpHeaderParser.parseCharset(response.headers));
        } catch (UnsupportedEncodingException e) {
            parsed = new String(response.data);
        }
        return Response.success(parsed, HttpHeaderParser.parseCacheHeaders(response));
    }
    }
首先StringRequest是继承自Request类的，Request可以指定一个泛型类，这里指定的当然就是String了，接下来StringRequest中提供了两个有参的构造函数，参数包括请求类型，请求地址，以及响应回调等，由于我们已经很熟悉StringRequest的用法了，相信这几个参数的作用都不用再解释了吧。但需要注意的是，在构造函数中一定要调用super()方法将这几个参数传给父类，因为HTTP的请求和响应都是在父类中自动处理的。

另外，由于Request类中的deliverResponse()和parseNetworkResponse()是两个抽象方法，因此StringRequest中需要对这两个方法进行实现。deliverResponse()方法中的实现很简单，仅仅是调用了mListener中的onResponse()方法，并将response内容传入即可，这样就可以将服务器响应的数据进行回调了。parseNetworkResponse()方法中则应该对服务器响应的数据进行解析，其中数据是以字节的形式存放在NetworkResponse的data变量中的，这里将数据取出然后组装成一个String，并传入Response的success()方法中即可。
参照可写出自己的自定义Request，很是简单，


