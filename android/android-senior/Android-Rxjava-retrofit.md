# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android-Rxjava-retrofit

### RxJava
RxJava ：异步
解释：a library for composing asynchronous and event-based programs using observable sequences for the Java VM"（一个在 Java VM 上使用可观测的序列来组成异步的、基于事件的程序的库）

### RxJava 有四个基本概念：
- Observable (可观察者，即被观察者)、
- Observer (观察者)、
- subscribe (订阅)、
- 事件

基本逻辑是：Observable 和 Observer 通过 subscribe() 方法实现订阅关系，从而 Observable 可以在需要的时候发出事件来通知 Observer。

与传统观察者模式不同， RxJava 的事件回调方法除了普通事件 onNext() （相当于 onClick() / onEvent()）之外，还定义了两个特殊的事件：onCompleted() 和 onError()。
- onCompleted(): 事件队列完结。RxJava 不仅把每个事件单独处理，还会把它们看做一个队列。RxJava 规定，当不会再有新的 onNext() 发出时，需要触发 onCompleted() 方法作为标志。
- onError(): 事件队列异常。在事件处理过程中出异常时，onError() 会被触发，同时队列自动终止，不允许再有事件发出。

注：在一个正确运行的事件序列中, onCompleted() 和 onError() 有且只有一个，并且是事件序列中的最后一个。 
**需要注意的是，onCompleted() 和 onError() 二者也是互斥的，即在队列中调用了其中一个，就不应该再调用另一个。**

### Rxjava提供的方法
- just（）一将一个或多个对象转换成发射这个或这些对象的一个Observable
- from()一将一个lterable,一个Future,或者一个数组转换成一个Observable
- repeat（）一创建一个重复发射指定数据或数据序列的Observable
- repeatWhen（）一创建一个重复发射指定数据或数据序列的Observable，它依赖于另一个Observable发射的数据
- create()一使用一个函数从头创建一个Observable
- defer()一只有当订阅者订阅才创建Observable;为每个订阅创建一个新的Observable
- range（）一创建一个发射指定范围的整数序列的Observable
- interval（）一创建一个按照给定的时间间隔发射整数序列的Observable
- timer（）一创建一个在给定的延时之后发射单个数据的Observable
- empty（）一创建一个什么都不做直接通知完成的Observable
- error（）一创建一个什么都不做直接通知错误的Observable
- never()一创建一个不发射任何数据的Observable

测试Rxjava中的操作符interval()时出现了很奇怪的问题，怎么试都不能执行。

原因是我们的操作不是阻塞的：我们创建了一个每隔一段时间就发射数据的 Observable，然后我们注册了一个 Subscriber 来打印收到的数据。这两个操作都是非阻塞的，而 发射数据的计时器是运行在另外一个线程的，但是这个线程不会阻止 JVM 结束当前的程序，所以 如果没有 System.in.read(); 这个阻塞操作，还没发射数据则程序就已经结束运行了。
解决：
- 添加：Thread.sleep(20000);
- 或使用传参
```
  Observable.interval(1, TimeUnit.SECONDS)
  变更为：
  Observable.interval(1, TimeUnit.SECONDS, Schedulers.trampoline())
```
**Schedulers.computation() 与 Schedulers.trampoline()区别**
- immediate()方法返回的是ImmediateScheduler类的实例，而trampoline()方法返回的是TrampolineScheduler类的实例。
- 从ImmediateScheduler类的官网介绍中可以看出ImmediateScheduler执行在当前main线程。
- 从TrampolineScheduler类的官网介绍中可以看出TrampolineScheduler不会立即执行，当其他排队任务介绍时才执行，当然TrampolineScheduler运行在当前main线程。

### Rxjava与Disposable
Disposable为销毁观察者与被观察者的绑定关系，避免内存泄露

多个响应
```
protected CompositeDisposable mDisposable;
mDisposable = new CompositeDisposable();
加入：
mDisposable.add（网络请求与view回调）
销毁：
if(compositeDisposable!=null){
compositeDisposable.clear();
}
```
单个响应式目标
```
Disposable timeDisposable = null;
加入：
rcodeDisposable = Observable.interval(2 * 60, TimeUnit.SECONDS).subscribe(aLong -> {
   updateQrcode();
});
销毁：
timeDisposable.dispose();
```

Rxjava定时请求：
```
        return Observable.interval(10, TimeUnit.SECONDS)
                .observeOn(AndroidSchedulers.mainThread())
                .takeUntil(aLong -> {
                    return aLong == 0;
                })
                .subscribeWith(new DisposableObserver<Long>() {
                    @Override
                    public void onNext(Long count) {
                        if (count == 0) {
                            timerListener.onComplete();
                        }
                    }

                    @Override
                    public void onError(Throwable e) {

                    }

                    @Override
                    public void onComplete() {

                    }
                });
```
takeUntil操作符:当第二个Observable发射了一项数据或者终止时，丢弃原Observable发射的任何数据。

DisposableObserver是RxJava中的一个观察者实现类，它继承自Observer接口，并且具有一些额外的特性。
与常规观察者相比，DisposableObserver具有以下特点：
- 可以手动取消订阅：DisposableObserver提供了dispose()方法，可以手动取消订阅，避免内存泄漏和资源浪费。
- 自动取消订阅：DisposableObserver在观察者不再需要接收事件时，会自动取消订阅，释放资源。
- 简化代码：DisposableObserver可以通过匿名内部类的方式实现，简化了代码编写过程

**Disposable**
Disposable是rxjava中用于处理资源释放的接口。当我们订阅Observable时，会返回一个Disposable对象，用于手动取消订阅并释放资源。下面是一个示例代码：
在 RxJava 中，在数据流结束后，如果不取消订阅，则可能会导致内存泄露。我们可以通过使用 Disposable 来取消订阅关系。在 RxJava 中，onError 和 onComplete 中，都存在 this::dispose。这也是为什么 onError 和 onComplete 不能同时存在的原因。

有两个方法
```
   // 取消订阅
   void dispose();
   // 判断订阅状态
   boolean isDisposed();
```
示例代码：
```
// 创建一个Disposable对象
Disposable disposable;

// 创建一个Observable对象，发射一组数字
Observable<Integer> observable = Observable.just(1, 2, 3, 4, 5);

// 订阅Observable并获取Disposable对象
disposable = observable.subscribe(
    data -> System.out.println(data), // onNext回调
    error -> System.out.println("Error: " + error), // onError回调
    () -> System.out.println("Complete") // onComplete回调
);

// 销毁Disposable对象
disposable.dispose();
```

**CompositeDisposable**
CompositeDisposable 类是一个存放 Disposable 的 hash 容器，对放入其中的 disposable 会将其解除订阅。如果在添加是，容器内已经被解除，那么新增的会被阻断。
在使用的时候，我们使用容器，调用，add或者 addAll，容器退出时，调用 clear 方法即可将容器内的关系解除。


### RxJava Observable的compose方法
Observable是一个可观察的数据序列，我们可以通过对Observable进行一系列的操作来对数据进行处理和转换。其中，compose方法是非常有用的一个方法，可以将一系列的操作封装为一个操作符，从而提高代码的复用性和可读性。
compose方法的定义如下所示：
```
public final <R> Observable<R> compose(Observable.Transformer<? super T, ? extends R> transformer)
```
其中，Observable.Transformer是一个接口，其定义如下：
```
public interface Transformer<T, R> {
Observable<R> call(Observable<T> observable);
}
```
compose方法的作用是将一个Observable的数据序列转换为另一个Observable的数据序列。
```
Observable<Integer> observable = Observable.just(1, 2, 3, 4, 5)
    .compose(new Observable.Transformer<Integer, Integer>() {
        @Override
        public Observable<Integer> call(Observable<Integer> upstream) {
            return upstream.map(new Func1<Integer, Integer>() {
                @Override
                public Integer call(Integer integer) {
                    return integer * 2;
                }
            });
        }
    });

observable.subscribe(new Action1<Integer>() {
    @Override
    public void call(Integer integer) {
        System.out.println(integer);
    }
});
```
在上面的示例中，我们首先创建了一个包含数字1到5的Observable，然后通过compose方法将原始的Observable转换为一个新的Observable，
新的Observable将原始数据序列中的每个元素都乘以2。
最后，我们通过subscribe方法订阅新的Observable，并打印出每个元素的值。

使用compose方法的好处
- 提高代码复用性：通过将一系列的操作封装为一个操作符，我们可以在多个地方重复使用这个操作符，而不需要重复编写相同的代码。
- 提高可读性：将一系列的操作封装为一个操作符，可以让代码结构更加清晰，易于理解。
- 简化代码逻辑：将复杂的操作拆分为多个小的操作符，可以让代码逻辑更加清晰简洁。

### Rxjava编写
1. 创建Observer 观察者或者Subscriber订阅者（也直接使用Action）
```
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
```
除了 Observer 接口之外，RxJava 还内置了一个实现了 Observer 的抽象类：Subscriber，可以对Observer进行一些拓展，基本使用方式一样

2. 创建 Observable 被订阅者
RxJava 使用 create() 方法来创建一个 Observable ，并为它定义事件触发规则
```
Observable observable = Observable.create(new Observable.OnSubscribe<String>() {        // OnSubscribe 对象作为参数
    @Override
    public void call(Subscriber<? super String> subscriber) {
        subscriber.onNext("Hello");
        subscriber.onNext("Hi");
        subscriber.onNext("Aloha");
        subscriber.onCompleted();
    }
});
//OnSubscribe 会被存储在返回的 Observable 对象中，它的作用相当于一个计划表，当 Observable 被订阅的时候，OnSubscribe 的 call() 方法会自动被调用
```
另：
- just(T...): 将传入的参数依次发送出来
- from(T[]) / from(Iterable<? extends T>) : 将传入的数组或 Iterable 拆分成具体对象后，依次发送出来

3. Subscribe (订阅)
创建了 Observable 和 Observer 之后，再用 subscribe() 方法将它们联结起来，整条链子就可以工作了。代码形式很简单：
```
observable.subscribe(observer);
// 或者：
observable.subscribe(subscriber);
```

还支持不完整定义的回调：
```
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
```

4. 实现异步——线程控制器 —— Scheduler
- Schedulers.immediate(): 直接在当前线程运行，相当于不指定线程。这是默认的 Scheduler。
- Schedulers.newThread(): 总是启用新线程，并在新线程执行操作。
- Schedulers.io(): I/O 操作（读写文件、读写数据库、网络信息交互等）所使用的 Scheduler
- Schedulers.computation(): 计算所使用的 Scheduler
- AndroidSchedulers.mainThread()，它指定的操作将在 Android 主线程运行。

完整代码示例：
```
            observable
            .subscribeOn(Schedulers.io()) // 在 IO 线程中执行串口通信
            .observeOn(AndroidSchedulers.mainThread()) // 在主线程中处理结果
            .subscribe(new Observer<Boolean>() {
                @Override
                public void onSubscribe(Disposable d) {

                }

                @Override
                public void onNext(Boolean o) {
                    
                }

                @Override
                public void onError(Throwable e) {
                   
                }

                @Override
                public void onComplete() {

                }
            });
```

### RxJava操作符 

RxJava操作符之Observable的创建方式
- just：原样发射
- from：将数据转换成为Observables，而不是需要混合使用Observables和其它类型的数据
- repeat：重复执行
```
Observable.just(1, 2, 3, 4)        //just只是简单的原样发射，将数组或Iterable当做单个数据，参数最多为9个
.subscribeOn(Schedulers.io()) // 指定 subscribe() 发生在 IO 线程
.observeOn(AndroidSchedulers.mainThread()) // 指定 Subscriber 的回调发生在主线程
.subscribe(new Action1<Integer>() {
    @Override
    public void call(Integer number) {
        Log.d(tag, "number:" + number);
    }
});
```

#### RxJava操作符之map和flatMap
map和flatMap都是接受一个函数作为参数(Func1)        
- map函数只有一个参数，参数一般是Func1，Func1的<I,O>I,O模版分别为输入和输出值的类型，实现Func1的call方法对I类型进行处理后返回O类型数据
- flatMap函数也只有一个参数，也是Func1,Func1的<I,O>I,O模版分别为输入和输出值的类型，实现Func1的call方法对I类型进行处理后返回O类型数据，不过这里O为Observable类型
- map一般用于对原始的参数进行加工处理，返回值还是基本的类型，可以在subscribe中使用(适用)的类型。
- flatMap一般用于输出一个Observable，而其随后的subscribe中的参数也跟Observable中的参数一样，注意不是Observable，一般用于对原始数据返回一个Observable,这个Observable中数据类型可以是原来的，也可以是其他的

map变换：
```
Observable.just("images/logo.png") // 输入类型 String
.map(new Func1<String, Bitmap>() {        //map(): 事件对象的直接变换
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
```

Map()变换:
```
Student[] students = ...;
Subscriber<String> subscriber = new Subscriber<String>() {
    @Override
    public void onNext(String name) {
        Log.d(tag, name);
    }
    ...
};

Observable.from(students)        //将数据转换成为Observables，而不是需要混合使用Observables和其它类型的数据
.map(new Func1<Student, String>() {
    @Override
    public String call(Student student) {
        return student.getName();        //取学生姓名转换为String
    }
})
.subscribe(subscriber);
```
打印学生所有课程：for循环方式
```
Student[] students = ...;

Subscriber<Student> subscriber = new Subscriber<Student>() {
    @Override
    public void onNext(Student student) {
        List<Course> courses = student.getCourses();        
        for (int i = 0; i < courses.size(); i++) {        //不转换，直接在Subscriber中使用for循环遍历Student
            Course course = courses.get(i);
            Log.d(tag, course.getName());
        }
    }
    ...
};

Observable.from(students)
.subscribe(subscriber);
```

打印学生所有课程：flatMap实现
Subscriber 中不使用 for 循环，而是希望 Subscriber 中直接传入单个的 Course 对象
```
Student[] students = ...;
Subscriber<Course> subscriber = new Subscriber<Course>() {
    @Override
    public void onNext(Course course) {
        Log.d(tag, course.getName());
    }
...
};
Observable.from(students)
.flatMap(new Func1<Student, Observable<Course>>() {        //flatMap() 中返回的是个 Observable 对象， Observable 将初始的对象『铺平』之后通过统一路径分发了下去。而这个『铺平』就是 flatMap() 所谓的 flat
    @Override
    public Observable<Course> call(Student student) {
        return Observable.from(student.getCourses());
    }
})
.subscribe(subscriber);
```

### Retrofit
Retrofit用法解析： 底层对网络的访问默认也是基于okhttp，不过retrofit非常适合于restful url格式的请求，更多使用注解的方式提供功能

```
gradle依赖：compile 'com.squareup.retrofit2:retrofit:2.0.2'
引入Gson支持:compile 'com.squareup.retrofit2:converter-gson:2.0.2'1.创建
```

1.基础用法
```
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("http://localhost:4567/")
        .build();
//创建Retrofit实例时需要通过Retrofit.Builder,并调用baseUrl方法设置URL。baseUlr 必须以 /（斜线） 结束
 ```

2.接口定义
```
public interface BlogService {                
    @GET("blog/{id}")
    Call<ResponseBody> getBlog(@Path("id") int id);
}
```

注解参数
- @Path：所有在网址中的参数（URL的问号前面），如：http://102.10.10.132/api/Accounts/{accountId}
- @Query：URL问号后面的参数，如：http://102.10.10.132/api/Comments?access_token={access_token}
- @QueryMap：相当于多个@Query
- @Field：用于POST请求，提交单个数据
- @Body：相当于多个@Field，以对象的形式提交

这里是interface不是class，所以我们是无法直接调用该方法，我们需要用Retrofit创建一个BlogService的代理对象。
```
BlogService service = retrofit.create(BlogService.class);
```
 
3.接口调用
```
Call<ResponseBody> call = service.getBlog(2);
// 用法和OkHttp的call如出一辙,不同的是如果是Android系统回调方法执行在主线程
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
 ```
 
### 其他完整示例
```
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("http://192.168.31.242:8080/springmvc_users/user/")
        .addConverterFactory(GsonConverterFactory.create(参数gson))        //可以接收自定义的Gson，当然也可以不传
        .build();
```
自定义gson处理（如果需要）：
```
Gson gson = new GsonBuilder()
//配置你的Gson
.setDateFormat("yyyy-MM-dd hh:mm:ss")
.create();
```
定义接口对象
```
public interface IUserBiz
{
    @GET("users")
    Call<List<User>> getUsers();
}
```
创建代理对象执行网络请求：
```
IUserBiz userBiz = retrofit.create(IUserBiz.class);
Call<List<User>> call = userBiz.getUsers();
call.enqueue(new Callback<List<User>>(){
    @Override
    public void onResponse(Call<List<User>> call, Response<List<User>> response)   //这里直接转化为List<User>,<ResponseBody>已经使用泛型
    {
        Log.e(TAG, "normalGet:" + response.body() + "");
    }

    @Override
    public void onFailure(Call<List<User>> call, Throwable t)
    {

    }
});
``` 

### 使用Retrofit注解：
1. HTTP请求方法：
   - GET,POST等7种，另，使用HTTP可以替换以上7种
 
2. 标记类
   - FormUrlEncoded：请求体为form表单
   - Multipart：请求体支持文件上传的from表单
   - Streaming：响应体的数据用流的形式返回
 
3. 参数类
   - Headers：用于添加请求头
   - Body：用于非表单请求体，@Body注解的Blog将会被Gson转换成RequestBody发送到服务器
   - Field：用于表单字段
   - PATH，Query，QueryMap，Url
   - {占位符}和PATH尽量只用在URL的path部分，
   url中的参数使用Query和QueryMap 代替，保证接口定义的简洁

示例:
```
public interface BlogService {
    /**
    * method 表示请求的方法，区分大小写
    * path表示路径
    * hasBody表示是否有请求体
    */
    @HTTP(method = "GET", path = "blog/{id}", hasBody = false)
    Call<ResponseBody> getBlog(@Path("id") int id);
}
```

### okhttp上传图片
示例代码：
```
public void uploadPhoto(String path) {
     File file = new File(path);
     if (!file.exists()) {
         MyLog.e(TAG, "照片不存在或已上传");
         return;
     }
     OkHttpClient httpClient = new OkHttpClient();
     RequestBody body = RequestBody.create(MediaType.parse("application/json; charset=utf-8"), file);
     //构建请求参数体，文件上传同时携带formdata数据，如果需要上传json，添加.addPart(body: RequestBody)
     MultipartBody multipartBody = new MultipartBody.Builder()
             .setType(MultipartBody.FORM)
             .addFormDataPart("file", photoId+ ".jpg", body)
             .addFormDataPart("coachId", trainPhoto.getCoachid())
             .addFormDataPart("studentId", trainPhoto.getStuid())
             .build();
     //请求参数request
     Request request = new Request.Builder()
             .url(AppConfig.HTTP_BASE_URL + "train/trainphoto/uploadImg")
             .addHeader("phone", phone)
             .addHeader("imei", imei)
             .post(multipartBody)
             .build();
     //执行请求
     Call call = httpClient.newCall(request);
     call.enqueue(new Callback() {
         @Override
         public void onFailure(@NotNull Call call, @NotNull IOException e) {
             MyLog.e(TAG, "onFailure: ",e);
         }

         @Override
         public void onResponse(@NotNull Call call, @NotNull Response response) {
             try {
                 JsonObject json = new JsonParser().parse(response.body().string()).getAsJsonObject();
             } catch (Exception e) {
                 MyLog.e(TAG, "照片: 上传失败");
             }
         }
     });
}
```

 
### retrofit上传图片
后台请求路径，使用MultipartFile接收文件
```
//接收单图
@PostMapping("uploads")
public CommonResult uploads(@ApiParam(required = true, value = "file") @RequestParam(value = "file") MultipartFile file) {
//接收多图+其他参数
@PostMapping("uploads")
public UploadResult uploads(@RequestParam(value = "file", required = false) MultipartFile[] files, String file_type) {
```

### android端图片上传
上传接口定义
```
//图片上传，MultipartBody.Part模式（推荐）
@Multipart
@POST("card/upload")
Observable<FileUploadBean> uploadFile(@Part MultipartBody.Part file);

//图片上传+其他参数，RequestBody模式（未验证）,MultipartBody继承自RequestBody
@Multipart
@POST("card/upload")
Call<Response> uploadFile(@Query("des") String description, @Part("uploadFile\"; filename=\"test.jpg\"") RequestBody imgs );

//多图上传
@Multipart
@POST("card/upload")
Call<BaseResponse<String>> uploadFile(@Part() List<MultipartBody.Part> parts);

//多图上传+其他参数
@Multipart
@POST("card/upload")
Observable<GoodsReturnPostEntity> uploadFile(@PartMap Map<String, RequestBody> map, @Part List<MultipartBody.Part>  parts);
```

图片上传，MultipartBody.Part模式
```
// 准备图片文件
File file=new File(s);
// 创建RequestBody
RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), file);
// 将RequestBody转换为MultipartBody.Part对象
MultipartBody.Part photoPart = MultipartBody.Part.createFormData("file", file.getName(), requestBody);
// 创建Retrofit实例
Retrofit retrofit = new Retrofit.Builder()
        .baseUrl("http://your-api-url.com/")
        .addConverterFactory(GsonConverterFactory.create())
        .build();
// 创建服务实例
ApiService service = retrofit.create(ApiService.class);
// 发送请求
Call<ResponseBody> call = service.uploadFile(photoPart).
call.enqueue(new Callback<ResponseBody>() {
    @Override
    public void onResponse(Call<ResponseBody> call, Response<ResponseBody> response) {
        // 处理响应
    }
 
    @Override
    public void onFailure(Call<ResponseBody> call, Throwable t) {
        // 处理错误
    }
});
```
图片上传+其他参数，RequestBody模式（未验证）
```
//同时上传一张图片和字符串
final File file = new File(filename);
RequestBody requestBody = RequestBody.create(MediaType.parse("multipart/form-data"), file);
Call<Response> call = service.updateInfo(msg, requestBody);
```

多图上传
```
   //遍历添加文件
   List<MultipartBody.Part> parts = new ArrayList<>(files.size());
   for (File file : files) {
      RequestBody requestBody = RequestBody.create(MediaType.parse("image/png"), file);
      MultipartBody.Part part = MultipartBody.Part.createFormData("file", file.getName(), requestBody);
      parts.add(part);
   }
   //执行网络请求
   HttpRequestClient.getRetrofitHttpClient().create(GoodsReturnApiService.class)
        .uploadFile(parts)
        .subscribeOn(Schedulers.newThread())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Observer<GoodsReturnPostEntity () {
         ...
         });
```

多图上传+其他参数
```
private void postGoodsPicToServer(){
    Map<String,RequestBody  params = new HashMap< ();
    //以下参数是伪代码，参数需要换成自己服务器支持的
    params.put("type", convertToRequestBody("type"));
    params.put("title",convertToRequestBody("title"));
    params.put("info",convertToRequestBody("info");
    params.put("count",convertToRequestBody("count"));

    //为了构建数据，同样是伪代码
    String path1 = Environment.getExternalStorageDirectory() + File.separator + "test1.jpg";
    String path2 = Environment.getExternalStorageDirectory() + File.separator + "test1.jpg";

    List<File>  fileList = new ArrayList<File> ();

    fileList.add(new File(path1));
    fileList.add(new File(path2));

    List<MultipartBody.Part  partList = filesToMultipartBodyParts(fileList);

    HttpRequestClient.getRetrofitHttpClient().create(GoodsReturnApiService.class)
        .postGoodsReturnPostEntitys(params,partList)
        .subscribeOn(Schedulers.newThread())
        .observeOn(AndroidSchedulers.mainThread())
        .subscribe(new Observer<GoodsReturnPostEntity () {
          @Override
          public void onSubscribe(@NonNull Disposable d) {

          }

          @Override
          public void onNext(@NonNull GoodsReturnPostEntity goodsReturnPostEntity) {

          }

          @Override
          public void onError(@NonNull Throwable e) {

          }

          @Override
          public void onComplete() {

          }
        });
}

private RequestBody convertToRequestBody(String param){
    RequestBody requestBody = RequestBody.create(MediaType.parse("text/plain"), param);
    return requestBody;
  }
```

