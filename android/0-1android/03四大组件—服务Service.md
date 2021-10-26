# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## 服务Service

### 3.1 服务是什么

服务（Service）是Android 中实现程序后台运行的解决方案，它非常适合用于去执行那些不需要和用户交互而且还要求长期运行的任务。
服务的运行不依赖于任何用户界面，即使当程序被切换到后台，或者用户打开了另外一个应用程序，服务仍然能够保持正常运行。
不过需要注意的是，服务并不是运行在一个独立的进程当中的，而是依赖于创建服务时所在的应用程序进程。当某个应用程序进程被杀掉时，所有依赖于该进程的服务也会停止运行。

另外，也不要被服务的后台概念所迷惑，实际上服务并不会自动开启线程，所有的代码都是默认运行在主线程当中的。
也就是说，我们需要在服务的内部手动创建子线程，并在这里执行具体的任务，否则就有可能出现主线程被阻塞住的情况。

### 3.2 android多线程编程

#### 线程的基本用法
Android 多线程编程其实并不比Java 多线程编程特珠，基本都是使用相同的语法。
继承Thread或者实现runnable接口都可以


#### 在子线程中更新UI
和许多其他的GUI 库一样，Android 的UI 也是线程不安全的。也就是说，如果想要更新应用程序里的UI 元素，则必须在主线程中进行，否则就会出现异常。
例：直接使用Thread或者实现runnable更新会报错

但是有些时候，我们必须在子线程里去执行一些耗时任务，然后根据任务的执行结果来更新相应的UI 控件，
对于这种情况，Android 提供了一套异步消息处理机制，完美地解决了在子线程中进行UI 操作的问题。
```
1.使用handler更新UI
public class MainActivity extends Activity implements OnClickListener {
    public static final int UPDATE_TEXT = 1;
    private TextView text;
    private Button changeText;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        text = (TextView) findViewById(R.id.text);
        changeText = (Button) findViewById(R.id.change_text);
        changeText.setOnClickListener(this);
    }
    //定义handler
    private Handler handler = new Handler() {
        public void handleMessage(Message msg) {
            switch (msg.what) {
                case UPDATE_TEXT:
                    // 在这里可以进行UI操作
                    text.setText("Nice to meet you");
                    break;
                default:
                    break;
            }
        }
    };
    ……    

    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            case R.id.change_text:
                new Thread(new Runnable() {
                    @Override
                    public void run() {
                        //text.setText("Nice to meet you");   //不能直接更新UI
                        Message message = new Message();
                        message.what = UPDATE_TEXT;
                        handler.sendMessage(message); // 将Message对象发送出去
                    }
                }).start();
                break;
            default:
                break;
        }
    }
}
```
#### 解析异步消息处理机制
Android 中的异步消息处理主要由四个部分组成，Message、Handler、MessageQueue 和Looper。
1. Message
Message 是在线程之间传递的消息，它可以在内部携带少量的信息，用于在不同线程之间交换数据。
上一小节中我们使用到了Message 的what 字段，除此之外还可以使
用arg1 和arg2 字段来携带一些整型数据，使用obj 字段携带一个Object 对象。
2. Handler
Handler 顾名思义也就是处理者的意思，它主要是用于发送和处理消息的。
发送消息一般是使用Handler 的sendMessage()方法，而发出的消息经过一系列地辗转处理后，
最终会传递到Handler 的handleMessage()方法中。
3. MessageQueue
MessageQueue 是消息队列的意思，它主要用于存放所有通过Handler 发送的消息。
这部分消息会一直存在于消息队列中，等待被处理。
每个线程中只会有一个MessageQueue对象。
4. Looper
Looper 是每个线程中的MessageQueue 的管家，调用Looper 的loop()方法后，就会
进入到一个无限循环当中，然后每当发现MessageQueue 中存在一条消息，就会将它取
出，并传递到Handler 的handleMessage()方法中。每个线程中也只会有一个Looper 对象。


#### 使用AsyncTask
首先来看一下AsyncTask 的基本用法，由于AsyncTask 是一个抽象类，所以如果我们想使用它，就必须要创建一个子类去继承它。
在继承时我们可以为AsyncTask 类指定三个泛型参数，这三个参数的用途如下。
1. Params
在执行AsyncTask 时需要传入的参数，可用于在后台任务中使用。
2. Progress
后台任务执行时，如果需要在界面上显示当前的进度，则使用这里指定的泛型作为进度单位。
3. Result
当任务执行完毕后，如果需要对结果进行返回，则使用这里指定的泛型作为返回值类型。

```
因此，一个最简单的自定义AsyncTask 就可以写成如下方式：
class DownloadTask extends AsyncTask<Void, Integer, Boolean> {
……
}
```

我们还需要去重写AsyncTask 中的几个方法才能完成对任务的定制。经常需要去重写的方法有以下四个。
1. onPreExecute()
这个方法会在后台任务开始执行之前调用，用于进行一些界面上的初始化操作，比如显示一个进度条对话框等。

2. doInBackground(Params...)
这个方法中的所有代码都会在子线程中运行，我们应该在这里去处理所有的耗时任务。
任务一旦完成就可以通过return 语句来将任务的执行结果返回，如果AsyncTask 的
第三个泛型参数指定的是Void，就可以不返回任务执行结果。
注意，在这个方法中是不可以进行UI 操作的，如果需要更新UI 元素，比如说反馈当前任务的执行进度，可以调
用publishProgress(Progress...)方法来完成。

3. onProgressUpdate(Progress...)
当在后台任务中调用了publishProgress(Progress...)方法后，这个方法就会很快被调用，方法中携带的参数就是在后台任务中传递过来的。
在这个方法中可以对UI 进行操作，利用参数中的数值就可以对界面元素进行相应地更新。

4. onPostExecute(Result)
当后台任务执行完毕并通过return 语句进行返回时，这个方法就很快会被调用。
返回的数据会作为参数传递到此方法中，可以利用返回的数据来进行一些UI 操作，比如说提醒任务执行的结果，以及关闭掉进度条对话框等。

```
class DownloadTask extends AsyncTask<Void, Integer, Boolean> {
    @Override
    protected void onPreExecute() {
        progressDialog.show(); // 显示进度对话框
    }

    @Override
    protected Boolean doInBackground(Void... params) {
        try {
            while (true) {
                int downloadPercent = doDownload(); // 这是一个虚构的方法
                publishProgress(downloadPercent);
                if (downloadPercent >= 100) {
                    break;
                }
            }
        } catch (Exception e) {
            return false;
        }
        return true;
    }

    @Override
    protected void onProgressUpdate(Integer... values) {
        // 在这里更新下载进度
        progressDialog.setMessage("Downloaded " + values[0] + "%");
    }

    @Override
    protected void onPostExecute(Boolean result) {
        progressDialog.dismiss(); // 关闭进度对话框
        // 在这里提示下载结果
        if (result) {
            Toast.makeText(context, "Download succeeded",
            Toast.LENGTH_SHORT).show();
        } else {
            Toast.makeText(context, " Download failed",
            Toast.LENGTH_SHORT).show();
        }
    }
}
```
简单来说，使用AsyncTask 的诀窍就是，
在doInBackground()方法中去执行具体的耗时任务，
在onProgressUpdate()方法中进行UI 操作，
在onPostExecute()方法中执行一些任务的收尾工作。

如果想要启动这个任务，只需编写以下代码即可：
new DownloadTask().execute();


### 详解AsyncTask
首先从Android3.0开始，系统要求网络访问必须在子线程中进行，否则网络访问将会失败并抛出NetworkOnMainThreadException这个异常，这样做是为了避免主线程由于耗时操作所阻塞从而出现ANR现象。
AsyncTask封装了线程池和Handler。
AsyncTask有两个线程池：SerialExecutor（用于任务的排队，默认是串行的线程池）和THREAD_POOL_EXECUTOR（用于真正的执行任务）。

AsyncTask还有一个Handler，叫InternalHandler，用于将执行环境从线程池切换到主线程。AsyncTask内部就是通过InternalHandler来发送任务执行的进度以及执行结束等消息。

AsyncTask排队执行过程：系统先把参数Params封装为FutureTask对象，它相当于Runnable，接着FutureTask交给SerialExcutor的execute方法，它先把FutureTask插入到任务队列tasks中，如果这个时候没有正在活动的AsyncTask任务，那么就会执行下一个AsyncTask任务，同时当一个AsyncTask任务执行完毕之后，AsyncTask会继续执行其他任务直到所有任务都被执行为止。


关于线程池，AsyncTask对应的线程池ThreadPoolExecutor都是进程范围内共享的，都是static的，所以是AsyncTask控制着进程范围内所有的子类实例。
由于这个限制的存在，当使用默认线程池时，如果线程数超过线程池的最大容量，线程池就会爆掉(3.0默认串行执行，不会出现这个问题)。
针对这种情况。可以尝试自定义线程池，配合AsyncTask使用。


### 服务的基本用法
定义一个Server
项目内Server包 右键 --> New --> Service --> Service 或者直接创建Class类，继承Service并重写IBinder方法
```
public class MyService extends Service{

	@Override
	public IBinder onBind(Intent intent) {
		return null;
	}
	
	@Override
	public void onCreate() {
		super.onCreate();
	}
	
	@Override
	public int onStartCommand(Intent intent, int flags, int startId) {
		return super.onStartCommand(intent, flags, startId);
	}
	
	@Override
	public void onDestroy() {
		// TODO Auto-generated method stub
		super.onDestroy();
	}
}

```
重写Service的 onCreate()、onStartCommand()和onDestory()方法。
其中 onCreate() 方法在服务创建的时候调用、onStartCommand() 方法会在每次服务启动的时候调用、onDestory() 方法会在服务销毁的时候调用。
其实onCreate()方法是在服务第一次创建的时候调用的，而onStartCommand()方法则在每次启动服务的时候都会调用

通常情况下，如果我们希望服务一旦启动就立刻去执行任务，就可以将逻辑卸载onStartCommand() 方法里。
另外需要注意的是，每个服务都需要在Androidmanifest.xml 中进行注册才能生效：
```
<application
	....>
	...
	<service
		android:name=".MyService"
		android:enabled="true"
		android:exported="true">
	</service>
</application>
```

#### 启动和停止服务
```
启动服务：
Intent startIntent = new Intent(this, MyService.class);
startService(startIntent); //启动服务

停止服务：
Intent stopIntent = new Intent(this, MyService.class);
stopService(stopIntent); //停止服务
```

#### 活动和服务进行通信
实现这个功能的思路是创建一个专门的Binder 对象来对下载功能进行管理，修改MyService 中的代码，如下所示：
```
public class MyService extends Service {

    private DownloadBinder mBinder = new DownloadBinder();
    class DownloadBinder extends Binder {
        public void startDownload() {
            Log.d("MyService", "startDownload executed");
        }
        public int getProgress() {
            Log.d("MyService", "getProgress executed");
            return 0;
        }
    }
    @Override
    public IBinder onBind(Intent intent) {
        return mBinder;
    }
    ……
}
```
这两个按钮分别是用于绑定服务和取消绑定服务的，那到底谁需要去和服务绑定呢？当然就是活动了。
当一个活动和服务绑定了之后，就可以调用该服务里的Binder 提供的方法了。
修改MainActivity 中的代码，如下所示：
```
public class MainActivity extends Activity implements OnClickListener {
    private Button startService;
    private Button stopService;
    private Button bindService;
    private Button unbindService;
    private MyService.DownloadBinder downloadBinder;
    
    private ServiceConnection connection = new ServiceConnection() {
        @Override
        public void onServiceDisconnected(ComponentName name) {
        }
        @Override
        public void onServiceConnected(ComponentName name, IBinder service) {
            downloadBinder = (MyService.DownloadBinder) service;
            downloadBinder.startDownload();
            downloadBinder.getProgress();
        }
    };
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ……
        bindService = (Button) findViewById(R.id.bind_service);
        unbindService = (Button) findViewById(R.id.unbind_service);
        bindService.setOnClickListener(this);
        unbindService.setOnClickListener(this);
    }
    @Override
    public void onClick(View v) {
        switch (v.getId()) {
            ……
            case R.id.bind_service:
                Intent bindIntent = new Intent(this, MyService.class);
                bindService(bindIntent, connection, BIND_AUTO_CREATE); // 绑定服务
                break;
            case R.id.unbind_service:
                unbindService(connection); // 解绑服务
                break;
            default:
                break;
        }
    }
}

```

### 服务的生命周期
之前章节我们学习过了活动以及碎片的生命周期。类似地，服务也有自己的生命周期，
前面我们使用到的onCreate()、onStartCommand()、onBind()和onDestroy()等方法都是在服务的生命周期内可能回调的方法。

一旦在项目的任何位置调用了Context 的startService()方法，相应的服务就会启动起来，并回调onStartCommand()方法。
如果这个服务之前还没有创建过，onCreate()方法会先于onStartCommand()方法执行。
服务启动了之后会一直保持运行状态，直到stopService()或stopSelf()方法被调用。
注意虽然每调用一次startService()方法，onStartCommand()就会执行一次，但实际上每个服务都只会存在一个实例。
所以不管你调用了多少次startService()方法，只需调用一次stopService()或stopSelf()方法，服务就会停止下来了。

另外，还可以调用Context 的bindService()来获取一个服务的持久连接，这时就会回调服务中的onBind()方法。
类似地，如果这个服务之前还没有创建过，onCreate()方法会先于onBind()方法执行。
之后，调用方可以获取到onBind()方法里返回的IBinder 对象的实例，这样就能自由地和服务进行通信了。只要调用方和服务之间的连接没有断开，服务就会一直保持运行状态。

当调用了startService()方法后，又去调用stopService()方法，这时服务中的onDestroy()
方法就会执行，表示服务已经销毁了。类似地，当调用了bindService()方法后，又去调用
unbindService()方法，onDestroy()方法也会执行，这两种情况都很好理解。
但是需要注意，
我们是完全有可能对一个服务既调用了startService()方法，又调用了bindService()方法的，
这种情况下该如何才能让服务销毁掉呢？根据Android 系统的机制，一个服务只要被启动或
者被绑定了之后，就会一直处于运行状态，必须要让以上两种条件同时不满足，服务才能被销毁。
所以，这种情况下要同时调用stopService()和unbindService()方法，onDestroy()方法才会执行。
这样你就已经把服务的生命周期完整地走了一遍。



### 服务的更多技巧
#### 使用前台服务
前台服务与普通服务的最大区别在于，它会一直有一个正在运行的图标在系统的状态栏中，下拉状态栏后可以看到更加详细的内容，非常类似于通知的效果。
```
public class MyService extends Service{
	Intent intent = new Intent(this, MainActivity.class);
	PendingIntent pi = PendingIntent.getActivity(this, 0 , intent, 0);

	Notification notification  = new NotificationCompat.Builder(this)
		.setContentTitle(" this is content titile")
		.setContentText("this is content text")
		.setWhen(System.currentTimeMillis())
		.setSmallIcon(R.mipmap.ic_launcher);
		.setLargeIcon(BitmapFactory.decodeResource(getResource(),
			R.mipmap.ic_launcher))
		.setContentIntent(pi)
		.build();
	startForeground(1,notification);
}
```
构造一个Notification对象后并没有使用NotificationManager 来讲通知显示出来，而是调用了startForeground()方法，该方法会将MyService变成一个前台服务，并在系统状态栏中显示出来。

#### 使用IntentService
服务中的代码都默认运行在主线程中，如果直接在服务中执行耗时操作很容易出现ANR（Application not Responding）
所以这个时候需要用到Android多线程编程技术，我们应该在服务的每个具体的方法里启动一个子线程，然后在这里去处理那些耗时的操作：
```
public class MyService extends Service{
	...
	@Override
	public int onStartCommand(Intent intent , int flags, int startId){
		new Thread(new Runnable(){
			public void run(){
				//处理具体的逻辑
			}
		}).start();
		return super.onStartCommand(intent, flags, startId);
	}
}
```

但是，这种服务一旦启动之后，就会一直处于运行状态，必须调用stopService()或者stopSelf()方法才能让服务停止下来，所以，如果想要实现让一个服务在执行完毕后自动停止的功能，就可以这样写：
```
public class MySerivce extends Servcie{
	...
	@Override
	public int onStartCommand(Intent intent, int flats , int startId){
		new Thread(new Runnable(){
			public void run(){
				//处理具体的逻辑
				stopSelf();
			}
		});
	}
}
```

虽说这样的写法并不复杂，但是总会有一些程序员忘记开启线程或者忘记调用stopSelf() 方法。为了简单创建一个异步、会自动停止的服务。Android专门提供了一个IntentService类
```
public class MyIntentService extends IntentService{
	public MyIntentService(){
		super("MyIntentService");  //调用父类的有参构造方法
	}
	@Override
	protected void onHandleIntent(Intent intent){	
		//打印当前的线程ID
		Log.e("mylog","Thread id is” + Thread.cuttentThread().getId();
	}
	@Override
	public void onDestory(){
		super.onDestory();
		Log.e("mylog","on Destory executed");
	}
}
```

首先这里提供一个无参的构造方法，并且必须在其内部调用父类的有参构造方法。然后要在子类中去实现onHandleIntent() 这个抽象方法，在这个方法中可以去处理一些逻辑，而且不用担心ANR，因为这个方法已经是在子线程中运行了。
IntentService线程的调用：
```
Intent intent = new Intent(this, MyIntentService.class);
startServcie(intent);
```

如此，线程就会自动启动并执行逻辑，执行完毕后自动关闭。这就是IntentService 的好处，能够自动开启和关闭；

### android中的定时任务
Android 中的定时任务一般有两种实现方式，
一种是使用Java API 里提供的Timer 类，
一种是使用Android 的Alarm 机制。

但Timer有一个明显的短板，它并不太适用于那些需要长期在后台运行的定时任务。
我们都知道了能让电池更加耐用，每种手机都会有自己的休眠策略，Android 手机就会在长时间不操作
的情况下自动让CPU 进入到睡眠状态，这就有可能导致Timer 中的定时任务无法正常运行。
而Alarm 机制则不存在这种情况，它具有唤醒CPU 的功能，即可以保证每次需要执行定时任务的时候CPU 都能正常工作。
需要注意，这里唤醒CPU 和唤醒屏幕完全不是同一个概念，千万不要产生混淆。
```
AlarmManager manager = (AlarmManager) getSystemService(Context.ALARM_SERVICE);
接下来调用AlarmManager 的set()方法就可以设置一个定时任务了，
比如说想要设定一个任务在10 秒钟后执行，就可以写成：
long triggerAtTime = SystemClock.elapsedRealtime() + 10 * 1000;
manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP, triggerAtTime, pendingIntent);
```

第一个参数是一个整型参数，用于指定AlarmManager 的工作类型，有四种值可选，分别是ELAPSED_REALTIME、ELAPSED_REALTIME_WAKEUP、RTC 和RTC_WAKEUP。
ELAPSED_REALTIME 表示让定时任务的触发时间从系统开机开始算起，但不会唤醒CPU。
ELAPSED_REALTIME_WAKEUP 同样表示让定时任务的触发时间从系统开机开始算起，但会唤醒CPU。
RTC 表示让定时任务的触发时间从1970 年1月1 日0 点开始算起，但不会唤醒CPU。
RTC_WAKEUP 同样表示让定时任务的触发时间从1970 年1 月1 日0 点开始算起，但会唤醒CPU。
使用SystemClock.elapsedRealtime()方法可以获取到系统开机至今所经历时间的毫秒数，
使用System.currentTimeMillis()方法可以获取到1970 年1 月1 日0 点至今所经历时间的毫秒数。

第二个参数，这个参数就好理解多了，就是定时任务触发的时间，以毫秒为单位。
如果第一个参数使用的是ELAPSED_REALTIME 或ELAPSED_REALTIME_WAKEUP，则这里传入开机至今的时间再加上延迟执行的时间。
如果第一个参数使用的是RTC 或RTC_WAKEUP，则这里传入1970 年1 月1 日0 点至今的时间再加上延迟执行的时间。

第三个参数是一个PendingIntent，对于它你应该已经不会陌生了吧。这里我们一般会调
用getBroadcast()方法来获取一个能够执行广播的PendingIntent。这样当定时任务被触发的时
候，广播接收器的onReceive()方法就可以得到执行
```
@Override
public int onStartCommand(Intent intent, int flags, int startId) {
    new Thread(new Runnable() {
        @Override
        public void run() {
            Log.d("LongRunningService", "executed at " + new Date().toString());
        }
    }).start();
    AlarmManager manager = (AlarmManager) getSystemService(ALARM_SERVICE);
    int anHour = 60 * 60 * 1000; // 这是一小时的毫秒数
    long triggerAtTime = SystemClock.elapsedRealtime() + anHour;

    Intent i = new Intent(this, AlarmReceiver.class);
    PendingIntent pi = PendingIntent.getBroadcast(this, 0, i, 0);
    manager.set(AlarmManager.ELAPSED_REALTIME_WAKEUP, triggerAtTime, pi);
    return super.onStartCommand(intent, flags, startId);
}
```

当然，如果你要求Alarm 任务的执行时间必须准备无误，Android 仍然提供了解决方案。
使用AlarmManager 的setExact()方法来替代set()方法，就可以保证任务准时执行了。


### handler实现定时任务
```
1. 定义一个Handler类

Handler handler=new Handler();  
Runnable runnable=new Runnable() {  
    @Override  
    public void run() {  
        // TODO Auto-generated method stub  
        //要做的事情  
        handler.postDelayed(this, 2000);  
    }  
};  
2. 启动计时器
handler.postDelayed(runnable, 2000);//每两秒执行一次runnable.  
3. 停止计时器
handler.removeCallbacks(runnable);   
```

### timer定时任务（不能保证准备执行）
```
timer = new Timer();
        //游戏倒计时
        timer.schedule(new TimerTask() {
            @Override
            public void run() {

            }
        }, 1, 1000);
```
