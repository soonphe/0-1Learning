# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android-OkHttp
Android为我们提供了两种HTTP交互的方式：HttpURLConnection和 Apache HTTP Client
OKHttp是一款高效的HTTP库，支持连接同一地址的链接共享同一个socket，通过连接池来减小响应延迟，还有透明的GZIP压缩，请求缓存等

gradle依赖：
```
compile 'com.squareup.okhttp:okhttp:2.4.0'
```

### OkHttp基础请求方式：
```
public static final MediaType JSON = MediaType.get("application/json; charset=utf-8");
OkHttpClient client = new OkHttpClient();
String post(String url, String json) throws IOException {  
    RequestBody body = RequestBody.create(JSON, json);  
    Request request = new Request.Builder()      
                        .url(url)      
                        .post(body)      
                        .build();  
    try (Response response = client.newCall(request).execute()) {    
        return response.body().string();  
    }
}
```

### 常见用法
```
  //得到OKHttpClient对象
  OkHttpClient okHttpClient=new OkHttpClient();
 
  <!-- get请求 -->
  //得到Request对象
  Request request = new Request.Builder()
          .url("https://www.baidu.com/")
          .build();
 
  <!-- post请求 -->
//构建RequestBody对象，调用add()方法构建我们的键值对
RequestBody body=new FormBody.Builder()
        .add("district","%E5%8C%97%E4%BA%AC")
        .build();
//在构建Request对象时，调用post方法，传入RequestBody对象
Request request=new Request.Builder()
        .url("https://www.baidu.com/")
        .post(body)
        .build();
 
  //OkhttpClient #newCall()得到Call对象
  Call call=okHttpClient.newCall(request);
  //Call#execute()同步请求网络
  Response response = call.execute();
  //Call#enqueue()异步请求网络
  call.enqueue(new Callback() {
    @Override
    public void onFailure(Call call, IOException e) {
      Log.e("fail",e.toString());
    }
 
    @Override
    public void onResponse(Call call, Response response) throws IOException{
      Log.e("success",response.body().toString());
    }
  }); 
```

### okhttp缓存设置 
```
    File sdcache =getExternalCacheDir();
    int cacheSize = 10 * 1024 * 1024; //10 MiB
    client.setCache(new Cache(sdcache.getAbsoluteFile(), cacheSize));
    new Thread(new Runnable(){
        @Override
        public void run() {
            try {
                execute();
            } catch(Exception e) {
                e.printStackTrace();
            }
        }
    }).start();
```

但有时候即使在有缓存的情况下我们依然需要去后台请求最新的资源（比如资源更新了）这个时候可以使用强制走网络来要求必须请求网络数据。
```
Request request = new Request.Builder()
.url("http://publicobject.com/helloworld.txt")
.build();
request =request.newBuilder().cacheControl(CacheControl.FORCE_NETWORK).build();        //FORCE_CACHE：强制只使用缓存的数据
Response response =client.newCall(request).execute();
```

### Okhttp连接超时
错误日志
```
2021-09-23 16:18:13.863||[HSFBizProcessor-DEFAULT-8-thread-19]||| OkHttp error url: http://xxx.gateway.com:9966/api/query/es/getOrderListByQuery
java.net.SocketTimeoutException: timeout
```
- 解决方案： 修改超时时长，连接池最大连接数
```
new OkHttpClient.Builder()
.connectTimeout(CONNECT_TIMEOUT,TimeUnit.SECONDS)
.readTimeout(READ_TIMEOUT,TimeUnit.SECONDS)
.connectionPool(newConnectionPool(32,5,TimeUnit.MINUTES))
.build();
```

### java使用okhttp
引入依赖：
```
<dependency>
  <groupId>com.squareup.okhttp3</groupId>
  <artifactId>okhttp</artifactId>
  <version>4.9.1</version>
</dependency>
```

工具类：
```
/**
 * okHttp工具类
 *
 * @author soonphe
 * @since 1.0
 */
public class HttpUtil {

    private static final Logger logger = LoggerFactory.getLogger(HttpUtil.class);

    private static OkHttpClient client;
    /**
     * 默认请求json格式
     */
    private static final String DEFAULT_MEDIA_TYPE = "application/json; charset=utf-8";
    /**
     * 连接超时
     */
    private static final int CONNECT_TIMEOUT = 60;
    /**
     * 读取超时
     */
    private static final int READ_TIMEOUT = 100;
    /**
     * 写入超时
     */
    private static final int WRITE_TIMEOUT = 60;

    private static final String GET = "GET";
    private static final String POST = "POST";

    /**
     * 单例模式  获取类实例
     *
     * @return client
     */
    private static OkHttpClient getInstance() {
        if (client == null) {
            synchronized (OkHttpClient.class) {
                if (client == null) {
                    client = new OkHttpClient.Builder()
                            .connectTimeout(CONNECT_TIMEOUT, TimeUnit.SECONDS)
                            .readTimeout(READ_TIMEOUT, TimeUnit.SECONDS)
                            .writeTimeout(WRITE_TIMEOUT, TimeUnit.SECONDS)
                            .connectionPool(new ConnectionPool(32,5,TimeUnit.MINUTES))
                            .build();
                }
            }
        }
        return client;
    }

    /**
     * Get请求
     *
     * @param url
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static String doGet(String url, String callMethod) throws HttpStatusException {

        try {
            long startTime = System.currentTimeMillis();
            addRequestLog(GET, callMethod, url, null, null);

            Request request = new Request.Builder().url(url).build();
            // 创建一个请求
            Response response = getInstance().newCall(request).execute();
            int httpCode = response.code();
            String result;
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new RuntimeException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }

    /**
     * post 请求
     *
     * @param url 请求路径
     * @param postBody body对象
     * @param mediaType 请求类型，默认为application/json
     * @param callMethod 回调方法
     * @return
     * @throws HttpStatusException
     */
    public static String doPost(String url, String postBody, String mediaType, String callMethod) throws HttpStatusException {
        try {
            long startTime = System.currentTimeMillis();
            addRequestLog(POST, callMethod, url, postBody, null);

            MediaType createMediaType = MediaType.parse(mediaType == null ? DEFAULT_MEDIA_TYPE : mediaType);
            Request request = new Request.Builder()
                    .url(url)
                    .post(RequestBody.create(createMediaType, postBody))
                    .build();

            Response response = getInstance().newCall(request).execute();
            int httpCode = response.code();
            String result;
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new IOException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }

    /**
     * Okhttp异步请求
     *
     * @param url
     * @param postBody
     * @param mediaType
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static void doPostAsyc(String url, String postBody, String mediaType, Callback callMethod) throws HttpStatusException {
        try {
            addRequestLog(POST, callMethod.toString(), url, postBody, null);

            MediaType createMediaType = MediaType.parse(mediaType == null ? DEFAULT_MEDIA_TYPE : mediaType);
            Request request = new Request.Builder()
                .url(url)
                .post(RequestBody.create(createMediaType, postBody))
                .build();

            Call call=getInstance().newCall(request);
            call.enqueue(callMethod);
        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
        }
    }


    /**
     * post请求，form/data数据
     *
     * @param url
     * @param parameterMap
     * @param callMethod
     * @return
     * @throws HttpStatusException
     */
    public static String doPost(String url, Map<String, String> parameterMap, String callMethod) throws HttpStatusException {
        try {
            long startTime = System.currentTimeMillis();
            List<String> parameterList = new ArrayList<>();
            FormBody.Builder builder = new FormBody.Builder();
            if (parameterMap.size() > 0) {
                for (Map.Entry<String, String> entry : parameterMap.entrySet()) {
                    String parameterName = entry.getKey();
                    String value = entry.getValue();
                    builder.add(parameterName, value);
                    parameterList.add(parameterName + ":" + value);
                }
            }

            addRequestLog(POST, callMethod, url, null, Arrays.toString(parameterList.toArray()));

            FormBody formBody = builder.build();
            Request request = new Request.Builder()
                    .url(url)
                    .post(formBody)
                    .build();

            Response response = getInstance().newCall(request).execute();
            String result;
            int httpCode = response.code();
            ResponseBody body = response.body();
            if (body != null) {
                result = body.string();
                addResponseLog(httpCode, result, startTime);
            } else {
                throw new IOException("exception in OkHttpUtil,response body is null");
            }
            return handleHttpResponse(httpCode, result);

        } catch (Exception ex) {
            handleHttpThrowable(ex, url);
            return StringUtils.EMPTY;
        }
    }


    private static void addRequestLog(String method, String callMethod, String url, String body, String formParam) {
        logger.info("===========================request begin================================================");
        logger.info("URI          : {}", url);
        logger.info("Method       : {}", method);
        if (StringUtils.isNotBlank(body)) {
            logger.info("Request body : {}", body);
        }
        if (StringUtils.isNotBlank(formParam)) {
            logger.info("Request param: {}", formParam);
        }
        logger.info("===========================request end================================================");
    }

    private static void addResponseLog(int httpCode, String result, long startTime) {
        logger.info("===========================response begin================================================");
        long endTime = System.currentTimeMillis();
        logger.info("Status       : {}", httpCode);
        logger.info("Response     : {}", result);
        logger.info("Time Cost         : {} ms", endTime - startTime);
        logger.info("===========================response end================================================");
    }

    private static String handleHttpResponse(int httpCode, String result) throws HttpStatusException {
        if (httpCode == HttpStatus.OK.value()) {
            return result;
        }
        HttpStatus httpStatus = HttpStatus.valueOf(httpCode);
        throw new HttpStatusException(httpStatus);
    }

    private static void handleHttpThrowable(Exception ex, String url) throws HttpStatusException {
        if (ex instanceof HttpStatusException) {
            throw (HttpStatusException) ex;
        }
        logger.error("OkHttp error url: " + url, ex);
        if (ex instanceof SocketTimeoutException) {
            throw new RuntimeException("request time out of OkHttp when do url:" + url);
        }
        throw new RuntimeException(ex);
    }

}


/**
 * json解析工具类
 *
 * @author soonphe
 * @since 1.0
 */
public class JsonUtil {

    private JsonUtil() {
    }

    /**
     * 避免指令重排序导致拿到半初始化的对象，保证线程可见性
     */
    private static volatile Gson gson;

    private static Gson getGson() {
        if (gson == null) {
            synchronized (JsonUtil.class) {
                if (gson == null) {
                    gson = new GsonBuilder().setDateFormat("yyyy-MM-dd HH:mm:ss").create();
                }
            }
        }
        return gson;
    }

    public static <T> String toJson(T object) {
        return getGson().toJson(object);
    }


    public static <T> T fromJson(String json, Type type) {
        return getGson().fromJson(json, type);
    }

    public static <T> T fromJson(String json, Class<T> clazz) {
        return getGson().fromJson(json, clazz);
    }
}

/**
 * HttpStatusException
 *
 * @author soonphe
 * @since 1.0
 */
public class HttpStatusException extends Exception {

    private HttpStatus httpStatus;


    private String result;

    public HttpStatusException(HttpStatus httpStatus) {
        this.httpStatus = httpStatus;
    }

    public HttpStatusException(HttpStatus httpStatus, String result) {
        this.httpStatus = httpStatus;

    }

    public HttpStatus getHttpStatus() {
        return httpStatus;
    }

    public String getResult() {
        return result;
    }
}
```
请求示例
```
//同步请求
HttpUtil.doPost(url,JsonUtil.toJson(orderQueryRequest), null, null);

//异步请求
HttpUtil.doPostAsyc(url,
	JsonUtil.toJson(orderQueryRequest), null, new Callback() {
		@Override
		public void onFailure(Call call, IOException e) {
			log.error("fail", e.toString());
		}

		@Override
		public void onResponse(Call call, Response response) throws IOException {
			log.info("success", response.body());
			String result;
			ResponseBody body = response.body();
			if (body != null) {
				result = body.string();
				log.info("result:" + result);
			}
		}
	});
```