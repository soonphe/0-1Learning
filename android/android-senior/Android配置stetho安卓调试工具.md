
stetho是facebook开发的Android调试工具。
它可以通过chrome的开发者工具来辅助安卓开发。
 
总的来说，提供了以下几个功能：
 
 
通过Elements标签查看界面的视图结构。
通过Network标签观察网络请求。
 
通过Resources标签查看本地数据，比如sqlite数据库，sharepreference等等。
同时可以在这里执行sql语句。
 
通过Console标签，在这里执行js语句，可以在APP上弹出一个Toast。
 
dumpapp 是linux/mac上使用的命令行工具，可以修改app内部资源，暂时未详细了解。
 
 
 
1、gradle依赖
compile 'com.facebook.stetho:stetho:1.2.0'
 
底层的网络请求可以通过两种方式来实现。分别是okhttp和urlconnection。
使用okhttp进行网络请求：
 
 
 
compile 'com.facebook.stetho:stetho-okhttp:1.2.0'
 
使用urlconnection：
 
 
compile 'com.facebook.stetho:stetho-urlconnection:1.2.0'
 
 
 
2.在Application添加 Stetho.initializeWithDefaults(this);
public class MyApplication extends Application { 
        
public void onCreate() { 
        
super.onCreate(); 
       
Stetho.initializeWithDefaults(this); 
        
}
    
}
 
————————————————————————————————————————————————————
第一次打开有遇到空白的情况，这时只要翻个墙就好了（20160817更新）
————————————————————————————————————————————————————
 
 
3.观察视图结构
在chrome的地址栏输入chrome://inspect, 可以看到当前连接的设备，然后点击inspect按钮。
 
4.网络请求
观察网络请求
 
之前进行网络调试的时候，都是通过断点查看数据，或者通过设置代理，然后用Fiddler抓包来观察。
Stetho也提供了一种观察网络请求的方法。
 
首先要调用
mOkHttpClient.networkInterceptors().add(new StethoInterceptor());
来监听网络请求。然后开始调用接口。
 
 
    OkHttpClient provideOkHttpClient() {
        OkHttpClient.Builder builder = new OkHttpClient.Builder().connectTimeout(10, TimeUnit.SECONDS)
                .readTimeout(10, TimeUnit.SECONDS)
                .writeTimeout(10, TimeUnit.SECONDS)
                .retryOnConnectionFailure(true);
        HttpLoggingInterceptor logging = new HttpLoggingInterceptor();
        logging.setLevel(HttpLoggingInterceptor.Level.BODY);
        builder.addNetworkInterceptor(new StethoInterceptor());        //添加Stetho Interceptor
        builder.addInterceptor(logging);                //添加控制台日志打印
        return builder.build();
 
 
5.本地数据
以前想要观察手机上的sqlite数据库，都是通过命令行使用adb shell来操作，或者把数据库拷贝到电脑上然后再通过sqlite工具打开，非常不方便。现在可以直接通过stetho的Resources标签查看。
