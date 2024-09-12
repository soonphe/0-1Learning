# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android进程间通信
Android进程间通信的方法有以下几种：
1. Intent：通过发送Intent来实现进程间通信。可以通过隐式或显式Intent来启动其他进程的组件，并通过Intent传递数据。
2. Messenger：通过使用Android提供的Messenger类来实现进程间通信。Messenger基于AIDL（Android接口定义语言）实现了进程间通信的机制。
3. AIDL（Android接口定义语言）：AIDL是Android提供的一种跨进程通信的机制。通过定义接口和方法，可以在不同进程之间进行数据交互。
4. ContentProvider：ContentProvider是Android提供的一种用于实现数据共享和访问的机制。通过ContentProvider，不同应用程序可以共享数据并进行交互。
5. Socket：Socket是一种基于网络的通信方式，可以在不同设备或进程之间进行通信。通过Socket，可以实现进程间的数据交换。
6. BroadcastReceiver：通过发送和接收广播来实现进程间通信。可以通过发送广播的方式来传递数据，并通过注册广播接收器来接收并处理广播。
7. Binder：Binder是Android提供的一种高级机制，用于实现跨进程通信。通过Binder，可以实现进程间的数据交换和方法调用。

### Binder机制
首先Binder是Android系统进程间通信(IPC)方式之一。

Binder使用Client－Server通信方式。Binder框架定义了四个角色：
- Server
- Client,
- ServiceManager以及
- Binder驱动。

其中Server,Client,ServiceManager运行于用户空间，而Binder驱动运行于内核空间。

进程空间分为用户空间和内核空间。
- 用户空间不可以进行数据交互；
- 内核空间可以进行数据交互，
- 所有进程共用 一个内核空间

Binder驱动程序提供设备文件/dev/binder与用户空间交互，Client、Server和Service Manager通过open和ioctl文件操作函数与Binder驱动程序进行通信。

Server创建了Binder实体，为其取一个字符形式，可读易记的名字，将这个Binder连同名字以数据包的形式通过Binder驱动发送给ServiceManager，通知ServiceManager注册一个名字为XX的Binder，它位于Server中。驱动为这个穿过进程边界的Binder创建位于内核中的实体结点以及ServiceManager对实体的引用，将名字以及新建的引用打包给ServiceManager。ServiceManager收数据包后，从中取出名字和引用填入一张查找表中。但是一个Server若向ServiceManager注册自己Binder就必须通过0这个引用和ServiceManager的Binder通信。Server向ServiceManager注册了Binder实体及其名字后，Client就可以通过名字获得该Binder的引用了。Clent也利用保留的0号引用向ServiceManager请求访问某个Binder：我申请名字叫XX的Binder的引用。ServiceManager收到这个连接请求，从请求数据包里获得Binder的名字，在查找表里找到该名字对应的条目，从条目中取出Binder引用，将该引用作为回复发送给发起请求的Client。

当然，不是所有的Binder都需要注册给ServiceManager广而告之的。Server端可以通过已经建立的Binder连接将创建的Binder实体传给Client，当然这条已经建立的Binder连接必须是通过实名Binder实现。由于这个Binder没有向ServiceManager注册名字，所以是匿名Binder。Client将会收到这个匿名Binder的引用，通过这个引用向位于Server中的实体发送请求。匿名Binder为通信双方建立一条私密通道，只要Server没有把匿名Binder发给别的进程，别的进程就无法通过穷举或猜测等任何方式获得该Binder的引用，向该Binder发送请求。


#### 为何新增Binder来作为主要的IPC方式
为何新增Binder来作为主要的IPC方式 ：Android也是基于Linux内核，Linux现有的进程通信手段有管道/消息队列/共享内存/套接字/信号量。

主要是出于以上三个方面的考量：
```
1. 效率：传输效率主要影响因素是内存拷贝的次数，拷贝次数越少，传输速率越高。从Android进程架构角度 分析：对于消息队列、Socket和管道来说，数据先从发送方的缓存区拷贝到内核开辟的缓存区中，再从内核缓存区拷 贝到接收方的缓存区，一共两次拷贝。
一次数据传递需要经历：用户空间 –> 内核缓存区 –> 用户空间，需要2次数据拷贝，这样效率不高。 
而对于Binder来说，数据从发送方的缓存区拷贝到内核的缓存区，而接收方的缓存区与内核的缓存区是映射到同 一块物理地址的，节省了一次数据拷贝的过程 ： 共享内存不需要拷贝，Binder的性能仅次于共享内存。

2、稳定性：上面说到共享内存的性能优于Binder，那为什么不采用共享内存呢，因为共享内存需要处理并发同 步问题，容易出现死锁和资源竞争，稳定性较差。 Binder基于C/S架构 ，Server端与Client端相对独立，稳定性较 好。
3、安全性：传统Linux IPC的接收方无法获得对方进程可靠的UID/PID，从而无法鉴别对方身份；而Binder机制 为每个进程分配了UID/PID，且在Binder通信时会根据UID/PID进行有效性检测。
```
从进程间通信的角度看，Binder 是一种进程间通信的机制；
- 从 Server 进程的角度看，Binder 指的是 Server 中的 Binder 实体对象(Binder类 IBinder)；
- 从 Client 进程的角度看，Binder 指的是对 Binder 代理对象，是 Binder 实体对象的一个远程代理
- 从传输过程的角度看，Binder 是一个可以跨进程传输的对象；Binder 驱动会自动完成代理对象和本地对象之间 的转换。 从Android Framework角度来说，Binder是ServiceManager连接各种Manager和相应ManagerService的桥梁


#### Binder使用方法：
```
android.os.Parcel

android.os.Parcel request = android.os.Parcel.obtain();
android.os.Parcel reply = android.os.Parcel.obtain();
mBinder.transact(BINDER_GET_PREVIEW_SIZE, request, reply, 0);//去拿前路子码流分辨率大小
```
示例代码:1. 直接利用Binder的transact实现：
```
public class IPCService extends Service {
private static final String DESCRIPTOR = "IPCService";
private final String[] names = {"B神","艹神","基神","J神","翔神"};
private MyBinder mBinder = new MyBinder();
private class MyBinder extends Binder {
  @Override
  protected boolean onTransact(int code, Parcel data, Parcel reply, int flags) throws RemoteException {
      switch (code){
          case 0x001: {
              Log.d("TAG", "MyBinder Switch块 -----" + android.os.Process.myPid());
              data.enforceInterface( "IPCService");
              int num = data.readInt();
              int num1 = data.readInt();
              int num2 = data.readInt();
              String test = data.readString();
              reply.writeNoException();
              reply.writeString(names[num] + "  " + android.os.Process.myPid() + "    " + num1 + "   " + num2 + "   " + test);
              reply.writeInt(1);
              reply.writeString("收到");
              return true;
          }
      }
      Log.d("TAG", "MyBinder   OnTransact块 ----- " + android.os.Process.myPid());
      return super.onTransact(code, data, reply, flags);
  }
}
@Override
public IBinder onBind(Intent intent) {
  return mBinder;
}
}
/**
 * AndroidManifest.xml
         <service android:name="net.binderlearning.IPCService"
                 android:process=".myservice"/>
*/

```
client:
```
public class MainActivity extends AppCompatActivity implements View.OnClickListener{
    private EditText edit_num;
    private Button btn_query;
    private TextView txt_result;
    private IBinder mIBinder;
    private ServiceConnection PersonConnection  = new ServiceConnection()
    {
        @Override
        public void onServiceDisconnected(ComponentName name)
        {
            mIBinder = null;
        }
        @Override
        public void onServiceConnected(ComponentName name, IBinder service)
        {
            mIBinder =  service;
            Log.d("TAG", "客户端-----" + android.os.Process.myPid());
        }
    };
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        bindViews();
        //绑定远程Service
        Intent service = new Intent(this,IPCService.class);
        bindService(service, PersonConnection, BIND_AUTO_CREATE);
        btn_query.setOnClickListener(this);
    }
    private void bindViews() {
        edit_num = (EditText) findViewById(R.id.edit_num);
        btn_query = (Button) findViewById(R.id.btn_query);
        txt_result = (TextView) findViewById(R.id.txt_result);
    }
    @Override
    public void onClick(View v) {
        int num = Integer.parseInt(edit_num.getText().toString());
        if (mIBinder == null)
        {
            Toast.makeText(this, "未连接服务端或服务端被异常杀死", Toast.LENGTH_SHORT).show();
        } else {
            android.os.Parcel _data = android.os.Parcel.obtain();
            android.os.Parcel _reply = android.os.Parcel.obtain();
            String _result = null;
            try{
                _data.writeInterfaceToken("IPCService");
                _data.writeInt(num);
                _data.writeInt(4);
                mIBinder.transact(0x001, _data, _reply, 0);
                _reply.readException(); //读取异常
                _result = _reply.readString();
                String test = _reply.readString();
                int test1 = _reply.readInt();
                Toast.makeText(this, "我收到的内容是：" + test + "    " + test1, Toast.LENGTH_SHORT).show();
                txt_result.setText(_result);
                edit_num.setText("");
                Log.d("TAG", "客户端-----" + android.os.Process.myPid());
            }catch (RemoteException e)
            {
                e.printStackTrace();
            } finally
            {
                _reply.recycle();
                _data.recycle();
            }
        }
    }
}
```

#### 为什么Binder只进行了一次数据拷贝？

Linux内核实际上没有从一个用户空间到另一个用户空间直接拷贝的函数，需要先用copy_from_user()拷贝到内核空间，再用copy_to_user()拷贝到另一个用户空间。为了实现用户空间到用户空间的拷贝，mmap()分配的内存除了映射进了接收方进程里，还映射进了内核空间。所以调用copy_from_user()将数据拷贝进内核空间也相当于拷贝进了接收方的用户空间，这就是Binder只需一次拷贝的‘秘密’。

最底层的是Android的ashmen(Anonymous shared memory)机制，它负责辅助实现内存的分配，以及跨进程所需要的内存共享。AIDL(android interface definition language)对Binder的使用进行了封装，可以让开发者方便的进行方法的远程调用，后面会详细介绍。Intent是最高一层的抽象，方便开发者进行常用的跨进程调用。

从英文字面上意思看，Binder具有粘结剂的意思那么它是把什么东西粘接在一起呢？

在Android系统的Binder机制中，由一系统组件组成，分别是Client、Server、Service Manager和Binder驱动，其中Client、Server、Service Manager运行在用户空间，Binder驱动程序运行内核空间。Binder就是一种把这四个组件粘合在一起的粘连剂了，其中，核心组件便是Binder驱动程序了，ServiceManager提供了辅助管理的功能，Client和Server正是Binder驱动和ServiceManager提供的基础设施上，进行Client-Server之间的通信。
1. Client、Server和ServiceManager实现在用户空间中，Binder驱动实现在内核空间中
2. Binder驱动程序和ServiceManager在Android平台中已经实现，开发者只需要在用户空间实现自己的Client和Server
3. Binder驱动程序提供设备文件/dev/binder与用户空间交互，Client、Server和ServiceManager通过open和ioctl文件操作函数与Binder驱动程序进行通信
4. Client和Server之间的进程间通信通过Binder驱动程序间接实现
5. ServiceManager是一个守护进程，用来管理Server，并向Client提供查询Server接口的能力

服务器端：一个Binder服务器就是一个Binder类的对象。当创建一个Binder对象后，内部就会开启一个线程，这个线程用户接收binder驱动发送的消息，收到消息后，会执行相关的服务代码。

Binder驱动：当服务端成功创建一个Binder对象后，Binder驱动也会相应创建一个mRemote对象，该对象的类型也是Binder类，客户就可以借助这个mRemote对象来访问远程服务。

客户端：客户端要想访问Binder的远程服务，就必须获取远程服务的Binder对象在binder驱动层对应的binder驱动层对应的mRemote引用。当获取到mRemote对象的引用后，就可以调用相应Binde对象的服务了。

在这里我们可以看到，客户是通过Binder驱动来调用服务端的相关服务。首先，在服务端创建一个Binder对象，接着客户端通过获取Binder驱动中Binder对象的引用来调用服务端的服务。在Binder机制中正是借着Binder驱动将不同进程间的组件bind(粘连)在一起，实现通信。

mmap将一个文件或者其他对象映射进内存。文件被映射进内存。文件被映射到多个页上，如果文件的大小不是所有页的大小之和，最后一个页不被使用的空间将会凋零。munmap执行相反的操作，删除特定地址区域的对象映射。

当使用mmap映射文件到进程后，就可以直接操作这段虚拟地址进行文件的读写等操作，不必再调用read,write等系统调用。但需注意，直接对该段内存写时不会写入超过当前文件大小的内容。

使用共享内存通信的一个显而易见的好处是效率高，因为进程可以直接读写内存，而不需要任何数据的拷贝。对于像管道和消息队列等通信方式，则需要在内核和用户空间进行四次的数据拷贝，而共享内存则只拷贝两次内存数据：一次从输入文件到共享内存区，另一次从共享内存到输出文件。实际上，进程之间在共享内存时，并不总是读写少量数据后就解除映射，有新的通信时，再重新建立共享内存区域，而是保持共享区域，直到通信完成为止，这样，数据内容一直保存在共享内存中，并没有写回文件。共享内存中的内容往往是在解除内存映射时才写回文件的。因此，采用共享内存的通信方式效率是非常高的。

### AIDL 
AIDL（Android接口声明语言）和你可能用过的IDL类似。 它允许定义客户端和服务端都同意为了彼此之间的通信使用进程间通信（IPC）的编程接口。

在Android系统中，通常一个进程不能访问其它进程的内存。所以说，它们需要分解为操作系统可以理解的原语，并且为你分配跨越边界的对象。编写这种代码很麻烦，因此Android使用AIDL为你控制它。

注意：仅仅当你允许不同应用程序的客户端使用IPC访问你的service并且想要在你的service中处理多线程时才必须使用AIDL。如果你不需要同时执行不同应用程序的IPC，你应该通过实现一个Binder创建你的接口，或者如果你想执行IPC，但不需要处理多线程，使用Messenger实现你的接口。无论如何，实现AIDL之前确保你理解了绑定Service。

aidl主要就帮助了我们完成了包装数据和解包的过程，并调用了transact过程，而用来传递的数据包我们就称为parcel

#### AIDL实现步骤: xxx.aidl -> xxx.java ,注册service
1. 用aidl定义需要被调用方法接口
2. 实现这些方法
3. 调用这些方法

在开始设计AIDL接口时，要知道调用AIDL接口是直接调用方法。你不应该猜测调用发生在哪一个线程。因为发生的情况依赖于是从本地进程中一个线程调用还是远程进程中的一个线程调用。尤其是：

从本地进程中调用会在调用的同一线程中执行。如果这是主UI线程，该线程会在AIDL接口中继续执行。如果是其它线程，它就是在service中执行代码的一个线程。因此，如果只有本地线程访问service，你完全可以控制它在哪一个线程中执行（但如果是这种情况，你不应该使用AIDl，而是应该使用实现Binder创建接口）。

从远程中调用会从平台维护的线程池中取出一个执行而不是你自己的进程。你必须准备好来自未知线程的调用，和同时发生多个调用。换句话说，就是AIDL接口的实现必须完全是线程安全的。

oneway关键词修改了远程调用的行为。当使用时，远程调用不会阻塞；它只是发送数据并立即返回。接口的实现最终会和正常的远程调用一样收到从Binder线程池调用规则。如果oneway用于本地调用，没有什么影响并且调用还是同步的。

#### 定义AIDL接口
你必须使用java编程语言语法在.aidl文件中定义AIDL接口，然后把它存放在service的主应用程序和其它要绑定到service的应用程序的源码目录（src/目录）。
当你编译包含.aidl文件应用程序时，Android SDK 工具会基于.aidl文件生成IBinder接口并把它保存在工程的gen/目录下。service必须适当的实现IBinder接口。客户端应用程序可以绑定到service并从IBinder调用方法执行IPC。

使用AIDL创建绑定的service，需要下列步骤：
1. 创建.aidl文件： 这个文件定义了带方法签名的编程接口。
2. 实现接口 ：Android SDK基于.aidl文件生成了Java编程语言的接口。这个接口有一个继承了Binder名为Stub的内部抽象类并从AIDL接口中实现方法。你必须继承Stub类并实现方法。
3. 对客户端暴露接口 ：实现Service并重写onBind()返回Stub类的实现。

注意： 在你对AIDL文件做了修改之后，为了避免使用你service的其它应用崩溃，第一次发布必须保持向后兼容。就是说，.aidl文件必须复制到其它应用程序为了可以访问service的接口，你必须支持原来的接口。

#### 1.创建.AIDL文件
AIDL使用简单的语法让你使用一个或多个方法声明接口，可以获取参数和返回值。参数和返回值可以是任意类型，甚至其它AIDL生成的接口。

你必须使用Java编程语言构造.aidl文件。每个.aidl文件必须定义一个接口并且只需要接口声明和方法签名。

默认情况下，AIDL支持下列数据类型：
- Java编程语言中所有的基本类型（例如int, long, char, boolean等等）
- String
- CharSequence
- List ：List 中的所有元素必须是在这个列表中支持的数据类型之一或其它AIDL接口生成的接口之一或你声明的parcelables。List可以用作常规类（例如，List<String>）。对方接收到的具体类总是ArrayList，尽管方法是使用List接口生成的。
- Map ：Map 中的所有元素必须是在这个列表中支持的数据类型之一或其它AIDL接口生成的接口之一或你声明的parcelables。常规的maps，例如Map<String,Integer>这种形式是不支持的。对方接收到的具体类总是HashMap，尽管方法是使用Map接口生成的。
当你使用的类型不在上面列表中时你必须包含import声明，即使它们和你的接口定义在同一个包中。

当你定义service接口时，需要注意的有：
- 方法可以有0个或多个参数，可以返回值或void。
- 所有非基本参数要求定向标记指示数据去哪个方向。in，out，或 inout（看下面的例子）。
- 基本参数默认为int，不能是其它的。 注意： 你应该限制真正需要的方向，因为分配参数消耗是很昂贵的。
- .aidl文件中所有的代码注释包含在生成的IBinder接口中（除了import和package声明之前的注释）。
- 只支持暴露方法，不能在AIDL中暴露静态域。

.aidl文件示例：
```
// IRemoteService.aidl
package com.example.android;

// Declare any non-default types here with import statements

/** Example service interface */
interface IRemoteService {
    /** Request the process ID of this service, to do evil things with it. */
    int getPid();

    /** Demonstrates some basic types that you can use as parameters
     * and return values in AIDL.
     */
    void basicTypes(int anInt, long aLong, boolean aBoolean, float aFloat,
            double aDouble, String aString);
}
```
在工程的src/目录下保存.aidl文件并编译应用，SDK工具会在gen/目录下生成IBinder接口文件。生成的文件名和.aidl文件名匹配，但扩展名为.java（例如IRemoteService.aidl会生成IRemoteService.java）。

#### 2.实现接口
当你编译你的应用，在命名.aidl文件后Android SDK 工具生成一个.java接口文件。生成的接口包含一个名为Stub子类，它是父接口的抽象实现（例如，YourInterface.Stub）并从.aidl文件中声明所有的方法。

注意： Stub也定义了几个帮助方法，特别是asInterface()，拿到一个IBinder（通常传递到客户端的onServiceConnected()回调方法）并返回stub接口的实例。关于这个强转的更多细节请查看 调用IPC方法 部分.

实现.aidl生成的接口，继承生成的Binder接口（例如，YourInterface.Stub）并实现从.aidl继承的方法。

这有一个使用匿名实例名为IRemoteService（上面IRemoteService.aidl例子定义的）接口的实现的例子：
```
private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
    public int getPid(){
        return Process.myPid();
    }
    public void basicTypes(int anInt, long aLong, boolean aBoolean,
        float aFloat, double aDouble, String aString) {
        // Does nothing
    }
};
```
现在mBinder是Stub类（Binder）的实例，为service定义了RPC接口。在下一步 ，这个实现暴露给客户端，因此它们可以和service交互。

当你实现AIDL接口时有几条需要注意的规则：
- 调用无法保证在主线程中执行，所以你要考虑多线程启动并合适的构建线程安全的service。
- 默认情况下，RPC调用是同步的。如果你知道该服务需要超过几毫秒的时间才能完成的请求，不应该从activity的主线程中调用，因为它可能阻塞应用程序（Android可能显示“应用程序无响应”对话框）——通常在客户端你应该从单独的线程中调用它。
- 你抛出的异常不会发送给调用者。

#### 3.对客户端暴露接口
一旦你为service实现了接口，你需要把它暴露给客户端为了它们可以绑定到它。对于service暴露接口，继承Service并实现onBind()返回实现生成的Stub类（在前面的部分讨论过）的实例。这有一个给客户端暴露IRemoteService接口的service示例。
```
public class RemoteService extends Service {
    @Override
    public void onCreate() {
        super.onCreate();
    }

    @Override
    public IBinder onBind(Intent intent) {
        // Return the interface
        return mBinder;
    }

    private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
        public int getPid(){
            return Process.myPid();
        }
        public void basicTypes(int anInt, long aLong, boolean aBoolean,
            float aFloat, double aDouble, String aString) {
            // Does nothing
        }
    };
}
```
现在，当一个客户端（例如activity）调用bindService()连接到这个service，客户端的onServiceConnected()回调接收到由service的onBind()返回的mBinder实例。

客户端也必须访问接口类，因此如果客户端和service在不同的应用程序，那客户端也必须复制一份.aidl文件到它的src/目录下（生成android.os.Binder接口——提供客户端访问AIDL的方法）。

当客户端在onServiceConnected()回调接收到IBinder，它必须调用YourServiceInterface.Stub.asInterface(service)强转返回的参数为YourServiceInterface类型。例如：
```
IRemoteService mIRemoteService;
private ServiceConnection mConnection = new ServiceConnection() {
    // Called when the connection with the service is established
    public void onServiceConnected(ComponentName className, IBinder service) {
        // Following the example above for an AIDL interface,
        // this gets an instance of the IRemoteInterface, which we can use to call on the service
        mIRemoteService = IRemoteService.Stub.asInterface(service);
    }

    // Called when the connection with the service disconnects unexpectedly
    public void onServiceDisconnected(ComponentName className) {
        Log.e(TAG, "Service has unexpectedly disconnected");
        mIRemoteService = null;
    }
};
```


#### 传递IPC对象
如果你有一个类想要通过IPC接口从一个进程发送到另一个进程，这个可以实现。但是，你必须确保你的类的代码对于IPC的另外一面是可用的并且你的类必须支持Parcelable接口。支持Parcelable接口很重要，因为它允许Android系统分解对象为可以被分配成跨进程的原语。

对于创建支持Parcelable协议的类，下面是你必须要做的：

1.实现Parcelable接口。
2.实现writeToParcel，带着当前对象的声明并把它写到Parcel。
3.添加一个名为CREATOR实现Parcelable.Creator接口的对象的静态域。
4.最后，创建.aidl文件声明parcelable类（就像下面的Rect.aidl文件）。

如果你使用了自定义的构建处理，不要把.aidl文件添加到你的构建。类似于C语言的头文件，.aidl文件不会被编译。

AIDL使用代码中的这些方法和字段生成编组和解组的对象。

例如，这有一个Rect.aidl文件创建了一个parcelable的Rect类：
```
package android.graphics;

// Declare Rect so AIDL can find it and knows that it implements
// the parcelable protocol.
parcelable Rect;
并且这有个例子说明了Rect类怎样实现Parcelable协议。


import android.os.Parcel;
import android.os.Parcelable;

public final class Rect implements Parcelable {
    public int left;
    public int top;
    public int right;
    public int bottom;

    public static final Parcelable.Creator<Rect> CREATOR = new
Parcelable.Creator<Rect>() {
        public Rect createFromParcel(Parcel in) {
            return new Rect(in);
        }

        public Rect[] newArray(int size) {
            return new Rect[size];
        }
    };

    public Rect() {
    }

    private Rect(Parcel in) {
        readFromParcel(in);
    }

    public void writeToParcel(Parcel out) {
        out.writeInt(left);
        out.writeInt(top);
        out.writeInt(right);
        out.writeInt(bottom);
    }

    public void readFromParcel(Parcel in) {
        left = in.readInt();
        top = in.readInt();
        right = in.readInt();
        bottom = in.readInt();
    }
}
```
Rect类中的编组相当简单。看看Parcel上的其它方法，看看你可以写到Parcel上其它类型的值。

警告： 不要忘记从其他进程接收数据的安全问题。这种情况下，Rect从Parcel读取四个数，但它是由你来确保这些值是在可接受范围之内，无论调用者尝试做什么。关于怎样维护从恶意软件带来的应用的安全问题的更多信息请查看 Security and Permissions。

调用IPC方法
这有一个调用类调用使用AIDL定义的远程接口要做的步骤：
1.在工程的src/目录下包含.aidl文件。
2.声明IBinder接口（基于AIDL生成的）的实例。
3.实现ServiceConnection。
4.调用Context.bindService()，传入ServiceConnection的实现。
5.在你的实现的onServiceConnected()中，你可以接收到IBinder的实例（调用的service）。调用YourInterfaceName.Stub.asInterface((IBinder)service)强转返回的参数到YourInterface类型。
6.调用接口中定义的方法。你应该总是捕获DeadObjectException异常，当连接被中断时抛出，这个是只有远程方法才会抛出的异常。
7.断开连接，使用接口的实例调用Context.unbindService()。

在调用一个IPC服务需要注意几点：

- 对象引用跨进程计数。
- 你可以发送匿名对象作为方法参数。
- 关于绑定到service的更多信息，请查看Bound Services文档。


这有一个示例代码说明了调用AIDL创建的service，来自ApiDemos工程Remote Service示例。
```
public static class Binding extends Activity {
    /** The primary interface we will be calling on the service. */
    IRemoteService mService = null;
    /** Another interface we use on the service. */
    ISecondary mSecondaryService = null;

    Button mKillButton;
    TextView mCallbackText;

    private boolean mIsBound;

    /**
     * Standard initialization of this activity.  Set up the UI, then wait
     * for the user to poke it before doing anything.
     */
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);

        setContentView(R.layout.remote_service_binding);

        // Watch for button clicks.
        Button button = (Button)findViewById(R.id.bind);
        button.setOnClickListener(mBindListener);
        button = (Button)findViewById(R.id.unbind);
        button.setOnClickListener(mUnbindListener);
        mKillButton = (Button)findViewById(R.id.kill);
        mKillButton.setOnClickListener(mKillListener);
        mKillButton.setEnabled(false);

        mCallbackText = (TextView)findViewById(R.id.callback);
        mCallbackText.setText("Not attached.");
    }

    /**
     * Class for interacting with the main interface of the service.
     */
    private ServiceConnection mConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            // This is called when the connection with the service has been
            // established, giving us the service object we can use to
            // interact with the service.  We are communicating with our
            // service through an IDL interface, so get a client-side
            // representation of that from the raw service object.
            mService = IRemoteService.Stub.asInterface(service);
            mKillButton.setEnabled(true);
            mCallbackText.setText("Attached.");

            // We want to monitor the service for as long as we are
            // connected to it.
            try {
                mService.registerCallback(mCallback);
            } catch (RemoteException e) {
                // In this case the service has crashed before we could even
                // do anything with it; we can count on soon being
                // disconnected (and then reconnected if it can be restarted)
                // so there is no need to do anything here.
            }

            // As part of the sample, tell the user what happened.
            Toast.makeText(Binding.this, R.string.remote_service_connected,
                    Toast.LENGTH_SHORT).show();
        }

        public void onServiceDisconnected(ComponentName className) {
            // This is called when the connection with the service has been
            // unexpectedly disconnected -- that is, its process crashed.
            mService = null;
            mKillButton.setEnabled(false);
            mCallbackText.setText("Disconnected.");

            // As part of the sample, tell the user what happened.
            Toast.makeText(Binding.this, R.string.remote_service_disconnected,
                    Toast.LENGTH_SHORT).show();
        }
    };

    /**
     * Class for interacting with the secondary interface of the service.
     */
    private ServiceConnection mSecondaryConnection = new ServiceConnection() {
        public void onServiceConnected(ComponentName className,
                IBinder service) {
            // Connecting to a secondary interface is the same as any
            // other interface.
            mSecondaryService = ISecondary.Stub.asInterface(service);
            mKillButton.setEnabled(true);
        }

        public void onServiceDisconnected(ComponentName className) {
            mSecondaryService = null;
            mKillButton.setEnabled(false);
        }
    };

    private OnClickListener mBindListener = new OnClickListener() {
        public void onClick(View v) {
            // Establish a couple connections with the service, binding
            // by interface names.  This allows other applications to be
            // installed that replace the remote service by implementing
            // the same interface.
            Intent intent = new Intent(Binding.this, RemoteService.class);
            intent.setAction(IRemoteService.class.getName());
            bindService(intent, mConnection, Context.BIND_AUTO_CREATE);
            intent.setAction(ISecondary.class.getName());
            bindService(intent, mSecondaryConnection, Context.BIND_AUTO_CREATE);
            mIsBound = true;
            mCallbackText.setText("Binding.");
        }
    };

    private OnClickListener mUnbindListener = new OnClickListener() {
        public void onClick(View v) {
            if (mIsBound) {
                // If we have received the service, and hence registered with
                // it, then now is the time to unregister.
                if (mService != null) {
                    try {
                        mService.unregisterCallback(mCallback);
                    } catch (RemoteException e) {
                        // There is nothing special we need to do if the service
                        // has crashed.
                    }
                }

                // Detach our existing connection.
                unbindService(mConnection);
                unbindService(mSecondaryConnection);
                mKillButton.setEnabled(false);
                mIsBound = false;
                mCallbackText.setText("Unbinding.");
            }
        }
    };

    private OnClickListener mKillListener = new OnClickListener() {
        public void onClick(View v) {
            // To kill the process hosting our service, we need to know its
            // PID.  Conveniently our service has a call that will return
            // to us that information.
            if (mSecondaryService != null) {
                try {
                    int pid = mSecondaryService.getPid();
                    // Note that, though this API allows us to request to
                    // kill any process based on its PID, the kernel will
                    // still impose standard restrictions on which PIDs you
                    // are actually able to kill.  Typically this means only
                    // the process running your application and any additional
                    // processes created by that app as shown here; packages
                    // sharing a common UID will also be able to kill each
                    // other's processes.
                    Process.killProcess(pid);
                    mCallbackText.setText("Killed service process.");
                } catch (RemoteException ex) {
                    // Recover gracefully from the process hosting the
                    // server dying.
                    // Just for purposes of the sample, put up a notification.
                    Toast.makeText(Binding.this,
                            R.string.remote_call_failed,
                            Toast.LENGTH_SHORT).show();
                }
            }
        }
    };

    // ----------------------------------------------------------------------
    // Code showing how to deal with callbacks.
    // ----------------------------------------------------------------------

    /**
     * This implementation is used to receive callbacks from the remote
     * service.
     */
    private IRemoteServiceCallback mCallback = new IRemoteServiceCallback.Stub() {
        /**
         * This is called by the remote service regularly to tell us about
         * new values.  Note that IPC calls are dispatched through a thread
         * pool running in each process, so the code executing here will
         * NOT be running in our main thread like most other things -- so,
         * to update the UI, we need to use a Handler to hop over there.
         */
        public void valueChanged(int value) {
            mHandler.sendMessage(mHandler.obtainMessage(BUMP_MSG, value, 0));
        }
    };

    private static final int BUMP_MSG = 1;

    private Handler mHandler = new Handler() {
        @Override public void handleMessage(Message msg) {
            switch (msg.what) {
                case BUMP_MSG:
                    mCallbackText.setText("Received from service: " + msg.arg1);
                    break;
                default:
                    super.handleMessage(msg);
            }
        }

    };
}
```


#### AIDL完整示例
1. 创建一个接口，再里面定义方法
```
package com.example.taidl;  
interface ICalcAIDL  
{  
    int add(int x , int y);  
    int min(int x , int y );  
} 
```

build一下gen目录下会生成ICalcAIDL.java文件
```
/*
 * This file is auto-generated.  DO NOT MODIFY.
 * Original file: /Users/dream/Downloads/android/androidProject/TAIDL/src/com/example/taidl/ICalcAIDL.aidl
 */
package com.example.taidl;
public interface ICalcAIDL extends android.os.IInterface
{
/** Local-side IPC implementation stub class. */
public static abstract class Stub extends android.os.Binder implements com.example.taidl.ICalcAIDL
{
private static final java.lang.String DESCRIPTOR = "com.example.taidl.ICalcAIDL";
/** Construct the stub at attach it to the interface. */
public Stub()
{
this.attachInterface(this, DESCRIPTOR);
}
/**
 * Cast an IBinder object into an com.example.taidl.ICalcAIDL interface,
 * generating a proxy if needed.
 */
public static com.example.taidl.ICalcAIDL asInterface(android.os.IBinder obj)
{
if ((obj==null)) {
return null;
}
android.os.IInterface iin = obj.queryLocalInterface(DESCRIPTOR);
if (((iin!=null)&&(iin instanceof com.example.taidl.ICalcAIDL))) {
return ((com.example.taidl.ICalcAIDL)iin);
}
return new com.example.taidl.ICalcAIDL.Stub.Proxy(obj);
}
@Override public android.os.IBinder asBinder()
{
return this;
}
@Override public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException
{
switch (code)
{
case INTERFACE_TRANSACTION:
{
reply.writeString(DESCRIPTOR);
return true;
}
case TRANSACTION_add:
{
data.enforceInterface(DESCRIPTOR);
int _arg0;
_arg0 = data.readInt();
int _arg1;
_arg1 = data.readInt();
int _result = this.add(_arg0, _arg1);
reply.writeNoException();
reply.writeInt(_result);
return true;
}
case TRANSACTION_min:
{
data.enforceInterface(DESCRIPTOR);
int _arg0;
_arg0 = data.readInt();
int _arg1;
_arg1 = data.readInt();
int _result = this.min(_arg0, _arg1);
reply.writeNoException();
reply.writeInt(_result);
return true;
}
}
return super.onTransact(code, data, reply, flags);
}
private static class Proxy implements com.example.taidl.ICalcAIDL
{
private android.os.IBinder mRemote;
Proxy(android.os.IBinder remote)
{
mRemote = remote;
}
@Override public android.os.IBinder asBinder()
{
return mRemote;
}
public java.lang.String getInterfaceDescriptor()
{
return DESCRIPTOR;
}
@Override public int add(int x, int y) throws android.os.RemoteException
{
android.os.Parcel _data = android.os.Parcel.obtain();
android.os.Parcel _reply = android.os.Parcel.obtain();
int _result;
try {
_data.writeInterfaceToken(DESCRIPTOR);
_data.writeInt(x);
_data.writeInt(y);
mRemote.transact(Stub.TRANSACTION_add, _data, _reply, 0);
_reply.readException();
_result = _reply.readInt();
}
finally {
_reply.recycle();
_data.recycle();
}
return _result;
}
@Override public int min(int x, int y) throws android.os.RemoteException
{
android.os.Parcel _data = android.os.Parcel.obtain();
android.os.Parcel _reply = android.os.Parcel.obtain();
int _result;
try {
_data.writeInterfaceToken(DESCRIPTOR);
_data.writeInt(x);
_data.writeInt(y);
mRemote.transact(Stub.TRANSACTION_min, _data, _reply, 0);
_reply.readException();
_result = _reply.readInt();
}
finally {
_reply.recycle();
_data.recycle();
}
return _result;
}
}
static final int TRANSACTION_add = (android.os.IBinder.FIRST_CALL_TRANSACTION + 0);
static final int TRANSACTION_min = (android.os.IBinder.FIRST_CALL_TRANSACTION + 1);
}
public int add(int x, int y) throws android.os.RemoteException;
public int min(int x, int y) throws android.os.RemoteException;
}
```

2. 新建一个Service
```
package com.example.taidl;

import android.app.Service;
import android.content.Intent;
import android.os.IBinder;
import android.os.RemoteException;
import android.util.Log;

public class CalcService extends Service{

	private static final String TAG = "server";  
	
	public void onCreate()  
    {  
        Log.e(TAG, "onCreate");  
    }  
  
    public IBinder onBind(Intent t)  
    {  
        Log.e(TAG, "onBind");  
        return mBinder;  
    }  
  
    public void onDestroy()  
    {  
        Log.e(TAG, "onDestroy");  
        super.onDestroy();  
    }  
  
    public boolean onUnbind(Intent intent)  
    {  
        Log.e(TAG, "onUnbind");  
        return super.onUnbind(intent);  
    }  
  
    public void onRebind(Intent intent)  
    {  
        Log.e(TAG, "onRebind");  
        super.onRebind(intent);  
    }  
	private final ICalcAIDL.Stub mBinder = new ICalcAIDL.Stub() {
		
		@Override
		public int min(int x, int y) throws RemoteException {
			return x + y;
		}
		
		@Override
		public int add(int x, int y) throws RemoteException {
			// TODO Auto-generated method stub
			return x - y;
		}
	};
}

```

创建了一个mBinder对象，并在Service的onBind方法中返回
注册：
```
        <service android:name="com.example.taidl.CalcService">
            <intent-filter>  
               <action android:name="com.example.taidl.calc" />  
  
               <category android:name="android.intent.category.DEFAULT" />  
           </intent-filter>  
        </service>
```

我们一会会在别的应用程序中通过Intent来查找此Service；这个不需要Activity，所以我也就没写Activity，安装完成也看不到安装图标，悄悄在后台运行着。服务端编写完毕。下面开始编写客户端:
```
package com.example.tclient;

import com.example.taidl.ICalcAIDL;

import android.app.Activity;
import android.content.ComponentName;
import android.content.Context;
import android.content.Intent;
import android.content.ServiceConnection;
import android.os.Bundle;
import android.os.IBinder;
import android.util.Log;
import android.view.View;
import android.widget.Toast;

public class MainActivity extends Activity {

	private ICalcAIDL mCalcAidl;

	private ServiceConnection mServiceConn = new ServiceConnection()
	{
		@Override
		public void onServiceDisconnected(ComponentName name)
		{
			Log.e("client", "onServiceDisconnected");
			mCalcAidl = null;
		}

		@Override
		public void onServiceConnected(ComponentName name, IBinder service)
		{
			Log.e("client", "onServiceConnected");
			mCalcAidl = ICalcAIDL.Stub.asInterface(service);
		}
	};

	@Override
	protected void onCreate(Bundle savedInstanceState)
	{
		super.onCreate(savedInstanceState);
		setContentView(R.layout.activity_main);

	}
	
	/**
	 * 点击BindService按钮时调用
	 * @param view
	 */
	public void bindService(View view)
	{
		Intent intent = new Intent();
		intent.setAction("com.example.taidl.calc");
		bindService(intent, mServiceConn, Context.BIND_AUTO_CREATE);
	}
	/**
	 * 点击unBindService按钮时调用
	 * @param view
	 */
	public void unbindService(View view)
	{
		unbindService(mServiceConn);
	}
	/**
	 * 点击12+12按钮时调用
	 * @param view
	 */
	public void addInvoked(View view) throws Exception
	{

		if (mCalcAidl != null)
		{
			int addRes = mCalcAidl.add(12, 12);
			Toast.makeText(this, addRes + "", Toast.LENGTH_SHORT).show();
		} else
		{
			Toast.makeText(this, "服务器被异常杀死，请重新绑定服务端", Toast.LENGTH_SHORT)
					.show();

		}

	}
	/**
	 * 点击50-12按钮时调用
	 * @param view
	 */
	public void minInvoked(View view) throws Exception
	{

		if (mCalcAidl != null)
		{
			int addRes = mCalcAidl.min(50, 12);
			Toast.makeText(this, addRes + "", Toast.LENGTH_SHORT).show();
		} else
		{
			Toast.makeText(this, "服务器未绑定或被异常杀死，请重新绑定服务端", Toast.LENGTH_SHORT)
					.show();

		}

	}
}

```
将服务端的aidl文件完整的复制过来，包名一定要一致。

#### 分析AIDL生成的代码

1. 服务端
```
private final ICalcAIDL.Stub mBinder = new ICalcAIDL.Stub()  
    {  
  
        @Override  
        public int add(int x, int y) throws RemoteException  
        {  
            return x + y;  
        }  
  
        @Override  
        public int min(int x, int y) throws RemoteException  
        {  
            return x - y;  
        }  
  
    };  

```
ICalcAILD.Stub来执行的，让我们来看看Stub这个类的声明：
```
public static abstract class Stub extends android.os.Binder implements com.zhy.calc.aidl.ICalcAIDL  
```

清楚的看到这个类是Binder的子类，是不是符合我们文章开通所说的服务端其实是一个Binder类的实例
接下来看它的onTransact()方法：
```
@Override public boolean onTransact(int code, android.os.Parcel data, android.os.Parcel reply, int flags) throws android.os.RemoteException  
{  
switch (code)  
{  
case INTERFACE_TRANSACTION:  
{  
reply.writeString(DESCRIPTOR);  
return true;  
}  
case TRANSACTION_add:  
{  
data.enforceInterface(DESCRIPTOR);  
int _arg0;  
_arg0 = data.readInt();  
int _arg1;  
_arg1 = data.readInt();  
int _result = this.add(_arg0, _arg1);  
reply.writeNoException();  
reply.writeInt(_result);  
return true;  
}  
case TRANSACTION_min:  
{  
data.enforceInterface(DESCRIPTOR);  
int _arg0;  
_arg0 = data.readInt();  
int _arg1;  
_arg1 = data.readInt();  
int _result = this.min(_arg0, _arg1);  
reply.writeNoException();  
reply.writeInt(_result);  
return true;  
}  
}  
return super.onTransact(code, data, reply, flags);  
}  
```

文章开头也说到服务端的Binder实例会根据客户端依靠Binder驱动发来的消息，执行onTransact方法，然后由其参数决定执行服务端的代码。

可以看到onTransact有四个参数
code ， data ，replay ， flags
* code 是一个整形的唯一标识，用于区分执行哪个方法，客户端会传递此参数，告诉服务端执行哪个方法
* data客户端传递过来的参数
* replay服务器返回回去的值
* flags标明是否有返回值，0为有（双向），1为没有（单向）

我们仔细看case TRANSACTION_min中的代码

data.enforceInterface(DESCRIPTOR);

与客户端的writeInterfaceToken对用，标识远程服务的名称

```
int _arg0;
_arg0 = data.readInt();
int _arg1;
_arg1 = data.readInt();
```

接下来分别读取了客户端传入的两个参数

```
int _result = this.min(_arg0, _arg1);
reply.writeNoException();
reply.writeInt(_result);
```

然后执行this.min，即我们实现的min方法；返回result由reply写回。

add同理，可以看到服务端通过AIDL生成Stub的类，封装了服务端本来需要写的代码。

#### 客户端

客户端主要通过ServiceConnected与服务端连接

```
private ServiceConnection mServiceConn = new ServiceConnection()  
    {  
        @Override  
        public void onServiceDisconnected(ComponentName name)  
        {  
            Log.e("client", "onServiceDisconnected");  
            mCalcAidl = null;  
        }  
  
        @Override  
        public void onServiceConnected(ComponentName name, IBinder service)  
        {  
            Log.e("client", "onServiceConnected");  
            mCalcAidl = ICalcAIDL.Stub.asInterface(service);  
        }  
    };  
```

如果你比较敏锐，应该会猜到这个onServiceConnected中的IBinder实例，其实就是我们文章开通所说的Binder驱动，也是一个Binder实例
在ICalcAIDL.Stub.asInterface中最终调用了：

```
return new com.zhy.calc.aidl.ICalcAIDL.Stub.Proxy(obj);  
```
这个Proxy实例传入了我们的Binder驱动，并且封装了我们调用服务端的代码，文章开头说，客户端会通过Binder驱动的transact()方法调用服务端代码

直接看Proxy中的add方法

```
@Override public int add(int x, int y) throws android.os.RemoteException  
{  
android.os.Parcel _data = android.os.Parcel.obtain();  
android.os.Parcel _reply = android.os.Parcel.obtain();  
int _result;  
try {  
_data.writeInterfaceToken(DESCRIPTOR);  
_data.writeInt(x);  
_data.writeInt(y);  
mRemote.transact(Stub.TRANSACTION_add, _data, _reply, 0);  
_reply.readException();  
_result = _reply.readInt();  
}  
finally {  
_reply.recycle();  
_data.recycle();  
}  
return _result;  
}  
```

首先声明两个Parcel对象，一个用于传递数据，一个用户接收返回的数据

```
_data.writeInterfaceToken(DESCRIPTOR);与服务器端的enforceInterfac对应
_data.writeInt(x);
_data.writeInt(y);写入需要传递的参数
mRemote.transact(Stub.TRANSACTION_add, _data, _reply, 0);
```

终于看到了我们的transact方法，第一个对应服务端的code,_data,_repay分别对应服务端的data，reply，0表示是双向的

```
_reply.readException();
_result = _reply.readInt();
```

最后读出我们服务端返回的数据，然后return。可以看到和服务端的onTransact基本是一行一行对应的。

我们已经通过AIDL生成的代码解释了Android Binder框架的工作原理。Service的作用其实就是为我们创建Binder驱动，即服务端与客户端连接的桥梁。
