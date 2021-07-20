1.编写activity基类AbActivity
@Override
protected void onCreate(@Nullable Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
 
        AppMana.getInstance().addActivity(this);        //添加当前activity到堆栈
        if (NetWorkUtils.isNetWorkAvailable(AbActivity.this)) {        //判断网络状态
            String param =MyBeanUser.getInstance().getRongToken();        //获取token
 
            RetrofitUtils.getInstance().connect(AbActivity.this,param);        //
 
        } else {
            Toast.makeText(AbActivity.this, "当前网络不可用", Toast.LENGTH_SHORT).show();
        }
 
————Handle说明——————————————————————————————————————————————————————————————
 
android handle解析：android主线程不能访问网络，子线程不能更新UI，当子线程操作涉及UI更新时，Handle就出现了
Handler运行在主线程中(UI线程中)，  
它与子线程可以通过Message对象来传递数据， 这个时候，Handler就承担着接受子线程传过来的(子线程用sendMessage()方法传递)Message对象，(里面包含数据)  ， 把这些消息放入主线程队列中，配合主线程进行更新UI
 
————OKHttp——————————————————————————————————————————————————————————————
Android为我们提供了两种HTTP交互的方式：HttpURLConnection和 Apache HTTP Client
 
OKHttp是一款高效的HTTP库，支持连接同一地址的链接共享同一个socket，通过连接池来减小响应延迟，还有透明的GZIP压缩，请求缓存等
okhttp解析：
gradle依赖：compile 'com.squareup.okhttp:okhttp:2.4.0'
 
用法解析：
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
 
⑶ 缓存设置
 
在网络请求中，缓存技术是一项应用比较广泛的技术，需要对请求过的网络资源进行缓存，而okhttp也支持这一技术，也使用十分方便，只需调用OkHttpClient类中的setCache方法即可。
 
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
 
但有时候即使在有缓存的情况下我们依然需要去后台请求最新的资源（比如资源更新了）这个时候可以使用强制走网络来要求必须请求网络数据。
Request request = new Request.Builder()
        .url("http://publicobject.com/helloworld.txt")
        .build();
request =request.newBuilder().cacheControl(CacheControl.FORCE_NETWORK).build();        //FORCE_CACHE：强制只使用缓存的数据
Response response =client.newCall(request).execute();
 
————
Retrofit——————————————————————————————————————————————————————————
Retrofit用法解析：
底层对网络的访问默认也是基于okhttp，不过retrofit非常适合于restful url格式的请求，更多使用注解的方式提供功能
 
gradle依赖：compile 'com.squareup.retrofit2:retrofit:2.0.2'
引入Gson支持:compile 'com.squareup.retrofit2:converter-gson:2.0.2'1.创建
 
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("http://localhost:4567/")
        .build();
//创建Retrofit实例时需要通过Retrofit.Builder,并调用baseUrl方法设置URL。baseUlr 必须以 /（斜线） 结束
 
2.接口定义
public interface BlogService {                
    @GET("blog/{id}")
    Call<ResponseBody> getBlog(@Path("id") int id);
}
//这里是interface不是class，所以我们是无法直接调用该方法，我们需要用Retrofit创建一个BlogService的代理对象。
//BlogService service = retrofit.create(BlogService.class);
 
3.接口调用
Call<ResponseBody> call = service.getBlog(2);
 
// 用法和OkHttp的call如出一辙,
 
// 不同的是如果是Android系统回调方法执行在主线程
 
call.enqueue(new Callback<ResponseBody>() {
    
@Override
    
public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
        
try {
            
System.out.println(response.body().string());
        
} catch (IOException e) {
            
e.printStackTrace();
        
}
    
}
 
    
@Override
    
public void onFailure(Call<ResponseBody> call, Throwable t) {
        
t.printStackTrace();
    
}
 
});
 
 
《！-- 其他完整示例 --》
Gson gson = new GsonBuilder()
      //配置你的Gson
      .setDateFormat("yyyy-MM-dd hh:mm:ss")
      .create();
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("http://192.168.31.242:8080/springmvc_users/user/")
        .addConverterFactory(GsonConverterFactory.create(参数gson))        // //可以接收自定义的Gson，当然也可以不传
        .build();
//定义接口对象
public interface IUserBiz
{
    @GET("users")
    Call<List<User>> getUsers();
}
IUserBiz userBiz = retrofit.create(IUserBiz.class);
Call<List<User>> call = userBiz.getUsers();
        call.enqueue(new Callback<List<User>>()
        {
            @Override
            public void onResponse(Call<List<User>> call, Response<List<User>> response)   //这里直接转化为List<User>,<ResponseBody>已经使用泛型
            {
                Log.e(TAG, "normalGet:" + response.body() + "");
            }
 
            @Override
            public void onFailure(Call<List<User>> call, Throwable t)
            {
 
            }
        });
使用Retrofit注解：
1.HTTP请求方法：
GET,POST等7种，另，使用HTTP可以替换以上7种
 
2.标记类
FormUrlEncoded：请求体为form表单
Multipart：请求体支持文件上传的from表单
Streaming：响应体的数据用流的形式返回
 
3.参数类
Headers：用于添加请求头
Body：用于非表单请求体，@Body注解的Blog将会被Gson转换成RequestBody发送到服务器
Field：用于表单字段
PATH，Query，QueryMap，Url
{占位符}和PATH尽量只用在URL的path部分，
url中的参数使用Query和QueryMap 代替，保证接口定义的简洁
《！--示例--》
public interface BlogService {
    
/**
     
* method 表示请求的方法，区分大小写
     
* path表示路径
     
* hasBody表示是否有请求体
     
 
*/
    
@HTTP(method = "GET", path = "blog/{id}", hasBody = false)
    
Call<ResponseBody> getBlog(@Path("id") int id);
}
 
 
 
————RxJava异步操作————————————————————————————————————————————————————————————
1.RxJava ：异步
解释：a library for composing asynchronous and event-based programs using observable sequences for the Java VM"（一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库）
『同样是做异步，为什么人们用它，而不用现成的 AsyncTask / Handler / XXX / ... ？』
RxJava 有四个基本概念：
Observable (可观察者，即被观察者)、
Observer (观察者)、 
subscribe (订阅)、
事件。
Observable 和 Observer 通过 subscribe() 方法实现订阅关系，从而 Observable 可以在需要的时候发出事件来通知 Observer。
 
与传统观察者模式不同， RxJava 的事件回调方法除了普通事件 onNext() （相当于 onClick() / onEvent()）之外，还定义了两个特殊的事件：onCompleted() 和 onError()。
 
onCompleted(): 事件队列完结。RxJava 不仅把每个事件单独处理，还会把它们看做一个队列。RxJava 规定，当不会再有新的 onNext() 发出时，需要触发 onCompleted() 方法作为标志。
onError(): 事件队列异常。在事件处理过程中出异常时，onError() 会被触发，同时队列自动终止，不允许再有事件发出。
在一个正确运行的事件序列中, onCompleted() 和 onError() 有且只有一个，并且是事件序列中的最后一个。需要注意的是，onCompleted() 和 onError() 二者也是互斥的，即在队列中调用了其中一个，就不应该再调用另一个。
 
1.创建Observer 观察者或者Subscriber订阅者（也直接使用Action）
Observer<String> observer = new Observer<String>() {
    @Override
    public void onNext(String s) {
        Log.d(tag, "Item: " + s);
    }
 
    @Override
    public void onCompleted() {
        Log.d(tag, "Completed!");
    }
 
    @Override
    public void onError(Throwable e) {
        Log.d(tag, "Error!");
    }
};
除了 Observer 接口之外，RxJava 还内置了一个实现了 Observer 的抽象类：Subscriber，可以对Observer进行一些拓展，基本使用方式一样
 
2.创建 Observable 被订阅者
RxJava 使用 create() 方法来创建一个 Observable ，并为它定义事件触发规则
Observable observable = Observable.create(new Observable.OnSubscribe<String>() {        // OnSubscribe 对象作为参数
    @Override
    public void call(Subscriber<? super String> subscriber) {
        subscriber.onNext("Hello");
        subscriber.onNext("Hi");
        subscriber.onNext("Aloha");
        subscriber.onCompleted();
    }
});
//OnSubscribe 会被存储在返回的 Observable 对象中，它的作用相当于一个计划表，当 Observable 被订阅的时候，OnSubscribe 的 call() 方法会自动被调用
 
另：just(T...): 将传入的参数依次发送出来
String[] words = {"Hello", "Hi", "Aloha"};
from(T[]) / from(Iterable<? extends T>) : 将传入的数组或 Iterable 拆分成具体对象后，依次发送出来
 
3. Subscribe (订阅)
 
创建了 Observable 和 Observer 之后，再用 subscribe() 方法将它们联结起来，整条链子就可以工作了。代码形式很简单：
observable.subscribe(observer);
// 或者：
observable.subscribe(subscriber);
 
还支持不完整定义的回调：
Action1<String> onNextAction = new Action1<String>() {
    // onNext()
    @Override
    public void call(String s) {
        Log.d(tag, s);
    }
};
Action1<Throwable> onErrorAction = new Action1<Throwable>() {
    // onError()
    @Override
    public void call(Throwable throwable) {
        // Error handling
    }
};
// 自动创建 Subscriber ，并使用 onNextAction 和 onErrorAction 来定义 onNext() 和 onError()
observable.subscribe(onNextAction, onErrorAction);
 
4.实现异步——线程控制器 —— Scheduler
Schedulers.immediate(): 直接在当前线程运行，相当于不指定线程。这是默认的 Scheduler。
Schedulers.newThread(): 总是启用新线程，并在新线程执行操作。
Schedulers.io(): I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler
Schedulers.computation(): 计算所使用的 Scheduler
AndroidSchedulers.mainThread()，它指定的操作将在 Android 主线程运行。
 
RX操作符之Observable的创建方式一（
just：原样发射
from：将数据转换成为Observables，而不是需要混合使用Observables和其它类型的数据
repeat：重复执行
 
map和flatMap都是接受一个函数作为参数(Func1)        
map函数只有一个参数，参数一般是Func1，Func1的<I,O>I,O模版分别为输入和输出值的类型，实现Func1的call方法对I类型进行处理后返回O类型数据
flatMap函数也只有一个参数，也是Func1,Func1的<I,O>I,O模版分别为输入和输出值的类型，实现Func1的call方法对I类型进行处理后返回O类型数据，不过这里O为Observable类型
 
Map一般用于对原始的参数进行加工处理，返回值还是基本的类型，可以在subscribe中使用(适用)的类型。
flatMap一般用于输出一个Observable，而其随后的subscribe中的参数也跟Observable中的参数一样，注意不是Observable，一般用于对原始数据返回一个Observable,这个Observable中数据类型可以是原来的，也可以是其他的
 
《！-- 示例 --》
Observable.just(1, 2, 3, 4)        //just只是简单的原样发射，将数组或Iterable当做单个数据，参数最多为9个
    .subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
    .observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
    .subscribe(new Action1<Integer>() {
        @Override
        public void call(Integer number) {
            Log.d(tag, "number:" + number);
        }
    });
 
**变换***********************************************
map变换：
Observable.just("images/logo.png") // 输入类型 String
    .map(new Func1<String, Bitmap>() {        //map(): 事件对象的直接变换
//Func1与Action1类似，但Func1 包装的是有返回值的方法，可以由多个不同参数方法
//变换结果： String 对象转换成一个 Bitmap 对象后返回
        @Override
        public Bitmap call(String filePath) { // 参数类型 String
            return getBitmapFromPath(filePath); // 返回类型 Bitmap
        }
    })
    .subscribe(new Action1<Bitmap>() {
        @Override
        public void call(Bitmap bitmap) { // 参数类型 Bitmap
            showBitmap(bitmap);
        }
    });
 
flatMap()变换:
打印学生姓名：
Student[] students = ...;
Subscriber<String> subscriber = new Subscriber<String>() {
    @Override
    public void onNext(String name) {
        Log.d(tag, name);
    }
    ...
};
Observable.from(students)        //将数据转换成为Observables，而不是需要混合使用Observables和其它类型的数据
    .map(new Func1<Student, String>() {
        @Override
        public String call(Student student) {
            return student.getName();        //取学生姓名转换为String
        }
    })
    .subscribe(subscriber);
打印学生所有课程：
Student[] students = ...;
Subscriber<Student> subscriber = new Subscriber<Student>() {
    @Override
    public void onNext(Student student) {
        List<Course> courses = student.getCourses();        
        for (int i = 0; i < courses.size(); i++) {        //不转换，直接在Subscriber中使用for循环遍历Student
            Course course = courses.get(i);
            Log.d(tag, course.getName());
        }
    }
    ...
};
Observable.from(students)
    .subscribe(subscriber);
 
**** Subscriber 中不使用 for 循环，而是希望 Subscriber 中直接传入单个的 Course 对象
flatMap实现
****
 
Student[] students = ...;
Subscriber<Course> subscriber = new Subscriber<Course>() {
    @Override
    public void onNext(Course course) {
        Log.d(tag, course.getName());
    }
    ...
};
Observable.from(students)
    .flatMap(new Func1<Student, Observable<Course>>() {        //flatMap() 中返回的是个 Observable 对象
//新创建的 Observable 将初始的对象『铺平』之后通过统一路径分发了下去。而这个『铺平』就是 flatMap() 所谓的 flat
        @Override
        public Observable<Course> call(Student student) {
            return Observable.from(student.getCourses());
        }
    })
    .subscribe(subscriber);
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
