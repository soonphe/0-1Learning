# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Andorid进程间通信AIDL

AIDL（Android接口声明语言）和你可能用过的IDL类似。它允许定义客户端和服务端都同意为了彼此之间的通信使用进程间通信（IPC）的编程接口。在Android系统中，通常一个进程不能访问其它进程的内存。所以说，它们需要分解为操作系统可以理解的原语，并且为你分配跨越边界的对象。编写这种代码很麻烦，因此Android使用AIDL为你控制它。

带着问题去学习

AIDL,两个android应用间的互相调用方法？
AIDL的全称是什么？如何工作？能处理哪些类型的数据
注意：仅仅当你允许不同应用程序的客户端使用IPC访问你的service并且想要在你的service中处理多线程时才必须使用AIDL。如果你不需要同时执行不同应用程序的IPC，你应该通过实现一个Binder创建你的接口，或者如果你想执行IPC，但不需要处理多线程，使用Messenger实现你的接口。无论如何，实现AIDL之前确保你理解了绑定Service。


在开始设计AIDL接口时，要知道调用AIDL接口是直接调用方法。你不应该猜测调用发生在哪一个线程。因为发生的情况依赖于是从本地进程中一个线程调用还是远程进程中的一个线程调用。尤其是：

从本地进程中调用会在调用的同一线程中执行。如果这是主UI线程，该线程会在AIDL接口中继续执行。如果是其它线程，它就是在service中执行代码的一个线程。因此，如果只有本地线程访问service，你完全可以控制它在哪一个线程中执行（但如果是这种情况，你不应该使用AIDl，而是应该使用实现Binder创建接口）。
从远程中调用会从平台维护的线程池中取出一个执行而不是你自己的进程。你必须准备好来自未知线程的调用，和同时发生多个调用。换句话说，就是AIDL接口的实现必须完全是线程安全的。

oneway关键词修改了远程调用的行为。当使用时，远程调用不会阻塞；它只是发送数据并立即返回。接口的实现最终会和正常的远程调用一样收到从Binder线程池调用规则。如果oneway用于本地调用，没有什么影响并且调用还是同步的。

定义AIDL接口
你必须使用java编程语言语法在.aidl文件中定义AIDL接口，然后把它存放在service的主应用程序和其它要绑定到service的应用程序的源码目录（src/目录）。
当你编译包含.aidl文件应用程序时，Android SDK 工具会基于.aidl文件生成IBinder接口并把它保存在工程的gen/目录下。service必须适当的实现IBinder接口。客户端应用程序可以绑定到service并从IBinder调用方法执行IPC。

使用AIDL创建绑定的service，需要下列步骤：

1.创建.aidl文件

这个文件定义了带方法签名的编程接口。

2.实现接口

Android SDK基于.aidl文件生成了Java编程语言的接口。这个接口有一个继承了Binder名为Stub的内部抽象类并从AIDL接口中实现方法。你必须继承Stub类并实现方法。

3.对客户端暴露接口
实现Service并重写onBind()返回Stub类的实现。

注意： 在你对AIDL文件做了修改之后，为了避免使用你service的其它应用崩溃，第一次发布必须保持向后兼容。就是说，.aidl文件必须复制到其它应用程序为了可以访问service的接口，你必须支持原来的接口。

1.创建.AIDL文件
AIDL使用简单的语法让你使用一个或多个方法声明接口，可以获取参数和返回值。参数和返回值可以是任意类型，甚至其它AIDL生成的接口。

你必须使用Java编程语言构造.aidl文件。每个.aidl文件必须定义一个接口并且只需要接口声明和方法签名。

默认情况下，AIDL支持下列数据类型：

Java编程语言中所有的基本类型（例如int, long, char, boolean等等）
String
CharSequence
List
List 中的所有元素必须是在这个列表中支持的数据类型之一或其它AIDL接口生成的接口之一或你声明的parcelables。List可以用作常规类（例如，List<String>）。对方接收到的具体类总是ArrayList，尽管方法是使用List接口生成的。

Map
Map 中的所有元素必须是在这个列表中支持的数据类型之一或其它AIDL接口生成的接口之一或你声明的parcelables。常规的maps，例如Map<String,Integer>这种形式是不支持的。对方接收到的具体类总是HashMap，尽管方法是使用Map接口生成的。
当你使用的类型不在上面列表中时你必须包含import声明，即使它们和你的接口定义在同一个包中。

当你定义service接口时，需要注意的有：

方法可以有0个或多个参数，可以返回值或void。

所有非基本参数要求定向标记指示数据去哪个方向。in，out，或 inout（看下面的例子）。
基本参数默认为in，不能是其它的。

注意： 你应该限制真正需要的方向，因为分配参数消耗是很昂贵的。

.aidl文件中所有的代码注释包含在生成的IBinder接口中（除了import和package声明之前的注释）。

只支持暴露方法，不能在AIDL中暴露静态域。

这有一个.aidl文件示例：

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
在工程的src/目录下保存.aidl文件并编译应用，SDK工具会在gen/目录下生成IBinder接口文件。生成的文件名和.aidl文件名匹配，但扩展名为.java（例如IRemoteService.aidl会生成IRemoteService.java）。

如果你使用Eclipse，会尽可能快的增量编译生成binder类。如果你不用Eclipse，Ant工具会在你下次编译工程时生成binder类——当你完成编写.aidl文件就应该尽快使用ant debug (或 ant release) 编译你的工程，使代码可以链接到生成的类。

2.实现接口
当你编译你的应用，在命名.aidl文件后Android SDK 工具生成一个.java接口文件。生成的接口包含一个名为Stub子类，它是父接口的抽象实现（例如，YourInterface.Stub）并从.aidl文件中声明所有的方法。

注意： Stub也定义了几个帮助方法，特别是asInterface()，拿到一个IBinder（通常传递到客户端的onServiceConnected()回调方法）并返回stub接口的实例。关于这个强转的更多细节请查看 调用IPC方法 部分.

实现.aidl生成的接口，继承生成的Binder接口（例如，YourInterface.Stub）并实现从.aidl继承的方法。

这有一个使用匿名实例名为IRemoteService（上面IRemoteService.aidl例子定义的）接口的实现的例子：


private final IRemoteService.Stub mBinder = new IRemoteService.Stub() {
    public int getPid(){
        return Process.myPid();
    }
    public void basicTypes(int anInt, long aLong, boolean aBoolean,
        float aFloat, double aDouble, String aString) {
        // Does nothing
    }
};
现在mBinder是Stub类（Binder）的实例，为service定义了RPC接口。在下一步 ，这个实现暴露给客户端，因此它们可以和service交互。

当你实现AIDL接口时有几条需要注意的规则：

调用无法保证在主线程中执行，所以你要考虑多线程启动并合适的构建线程安全的service。
默认情况下，RPC调用是同步的。如果你知道该服务需要超过几毫秒的时间才能完成的请求，不应该从activity的主线程中调用，因为它可能阻塞应用程序（Android可能显示“应用程序无响应”对话框）——通常在客户端你应该从单独的线程中调用它。
你抛出的异常不会发送给调用者。
3.对客户端暴露接口
一旦你为service实现了接口，你需要把它暴露给客户端为了它们可以绑定到它。对于service暴露接口，继承Service并实现onBind()返回实现生成的Stub类（在前面的部分讨论过）的实例。这有一个给客户端暴露IRemoteService接口的service示例。


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
现在，当一个客户端（例如activity）调用bindService()连接到这个service，客户端的onServiceConnected()回调接收到由service的onBind()返回的mBinder实例。

客户端也必须访问接口类，因此如果客户端和service在不同的应用程序，那客户端也必须复制一份.aidl文件到它的src/目录下（生成android.os.Binder接口——提供客户端访问AIDL的方法）。

当客户端在onServiceConnected()回调接收到IBinder，它必须调用YourServiceInterface.Stub.asInterface(service)强转返回的参数为YourServiceInterface类型。例如：


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
更多示例代码请在 ApiDemos的 RemoteService.java 中查看。

传递IPC对象
如果你有一个类想要通过IPC接口从一个进程发送到另一个进程，这个可以实现。但是，你必须确保你的类的代码对于IPC的另外一面是可用的并且你的类必须支持Parcelable接口。支持Parcelable接口很重要，因为它允许Android系统分解对象为可以被分配成跨进程的原语。

对于创建支持Parcelable协议的类，下面是你必须要做的：

1.实现Parcelable接口。
2.实现writeToParcel，带着当前对象的声明并把它写到Parcel。
3.添加一个名为CREATOR实现Parcelable.Creator接口的对象的静态域。
4.最后，创建.aidl文件声明parcelable类（就像下面的Rect.aidl文件）。

如果你使用了自定义的构建处理，不要把.aidl文件添加到你的构建。类似于C语言的头文件，.aidl文件不会被编译。

AIDL使用代码中的这些方法和字段生成编组和解组的对象。

例如，这有一个Rect.aidl文件创建了一个parcelable的Rect类：

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

对象引用跨进程计数。
你可以发送匿名对象作为方法参数。
关于绑定到service的更多信息，请查看Bound Services文档。

Android开发指南——绑定Service

这有一个示例代码说明了调用AIDL创建的service，来自ApiDemos工程Remote Service示例。

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