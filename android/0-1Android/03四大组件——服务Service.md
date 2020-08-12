# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 服务Service

定义一个Server
项目内Server包 右键 --> New --> Service --> Service 或者直接创建Class类，继承Service并重写IBinder方法

public class MyService extends Service{
	
	public MyService(){
		
	}

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

重写Service的 onCreate()、onStartCommand()和onDestory()方法。其中 onCreate() 方法在服务创建的时候调用、onStartCommand() 方法会在每次服务启动的时候调用、onDestory() 方法会在服务销毁的时候调用。
通常情况下，如果我们希望服务一旦启动就立刻去执行任务，就可以将逻辑卸载onStartCommand() 方法里。
另外需要注意的是，每个服务都需要在Androidmanifest.xml 中进行注册才能生效：

<application
	....>
	...
	<service
		android:name=".MyService"
		android:enabled="true"
		android:exported="true">
	</service>
</application>
1
2
3
4
5
6
7
8
9
启动和停止服务
启动服务：

Intent startIntent = new Intent(this, MyService.class);
startService(startIntent); //启动服务
1
2
停止服务：

Intent stopIntent = new Intent(this, MyService.class);
stopService(stopIntent); //停止服务
1
2
使用前台服务
前台服务与普通服务的最大区别在于，它会一直有一个正在运行的图标在系统的状态栏中，下拉状态栏后可以看到更加详细的内容，非常类似于通知的效果。

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
1
2
3
4
5
6
7
8
9
10
11
12
13
14
构造一个Notification对象后并没有使用NotificationManager 来讲通知显示出来，而是调用了startForeground()方法，该方法会将MyService变成一个前台服务，并在系统状态栏中显示出来。

使用IntentService
服务中的代码都默认运行在主线程中，如果直接在服务中执行耗时操作很容易出现ANR（Application not Responding）
所以这个时候需要用到Android多线程编程技术，我们应该在服务的每个具体的方法里启动一个子线程，然后在这里去处理那些耗时的操作：

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
1
2
3
4
5
6
7
8
9
10
11
12
但是，这种服务一旦启动之后，就会一直处于运行状态，必须调用stopService()或者stopSelf()方法才能让服务停止下来，所以，如果想要实现让一个服务在执行完毕后自动停止的功能，就可以这样写：

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
1
2
3
4
5
6
7
8
9
10
11
12
虽说这样的写法并不复杂，但是总会有一些程序员忘记开启线程或者忘记调用stopSelf() 方法。为了简单创建一个异步、会自动停止的服务。Android专门提供了一个IntentService类

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
1
2
3
4
5
6
7
8
9
10
11
12
13
14
15
首先这里提供一个无参的构造方法，并且必须在其内部调用父类的有参构造方法。然后要在子类中去实现onHandleIntent() 这个抽象方法，在这个方法中可以去处理一些逻辑，而且不用担心ANR，因为这个方法已经是在子线程中运行了。
IntentService线程的调用：

Intent intent = new Intent(this, MyIntentService.class);
startServcie(intent);
1
2
如此，线程就会自动启动并执行逻辑，执行完毕后自动关闭。这就是IntentService 的好处，能够自动开启和关闭；

