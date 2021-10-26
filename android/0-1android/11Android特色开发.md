# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## Android特色开发

### 位置服务简介
基于位置的服务简称LBS，主要的工作原理就是利用无线电通讯网络或GPS 等定位方式来确定出移动设备所在的位置

#### 找到自己的位置
用GPS 定位，精准度比较高，但是非常耗电，室内无法接受卫星信号
使用网络定位，的精准度稍差，但耗电量比较少

android原生定位：国内有墙，无法使用
第三方SDK：高德或者百度，参考官方文档

### 传感器简介
手机中内置的传感器是一种微型的物理设备，它能够探测、感受到外界的信号，并按一定规律转换成我们所需要的信息。
Android 手机通常都会支持多种类型的传感器，如光照传感右器、加速度传感器、地磁传感器、压力传感器、温度传感器等。

### 光照传感器

Android 中每个传感器的用法其实都比较类似，真的可以说是一通百通了。

首先第一步要获取到SensorManager 的实例，方法如下：
```
SensorManager senserManager = (SensorManager)
getSystemService(Context.SENSOR_SERVICE);
```

SensorManager是系统所有传感器的管理器，有了它的实例之后就可以调用getDefaultSensor()方法来得到任意的传感器类型了，如下所示：
```
Sensor sensor = senserManager.getDefaultSensor(Sensor.TYPE_LIGHT);
```
这里使用Sensor.TYPE_LIGHT 常量来指定传感器类型，此时的Sensor 实例就代表着一个光照传感器。Sensor 中还有很多其他传感器类型的常量，。

接下来我们需要对传感器输出的信号进行监听，这就要借助SensorEventListener 来实现了。
SensorEventListener 是一个接口，其中定义了onSensorChanged()和onAccuracyChanged()
这两个方法，如下所示：
```
SensorEventListener listener = new SensorEventListener() {
    @Override
    public void onAccuracyChanged(Sensor sensor, int accuracy) {
    }
    @Override
    public void onSensorChanged(SensorEvent event) {
    }
};
```
当传感器的精度发生变化时就会调用onAccuracyChanged()方法，
当传感器监测到的数值发生变化时就会调用onSensorChanged()方法。
可以看到onSensorChanged()方法中传入了一个SensorEvent 参数，这个参数里又包含了一个values 数组，所有传感器输出的信息都是存放在这里的。

下面我们还需要调用SensorManager 的registerListener()方法来注册SensorEventListener才能使其生效，
registerListener()方法接收三个参数，
第一个参数就是SensorEventListener 的实例，
第二个参数是Sensor 的实例，这两个参数我们在前面都已经成功得到了。
第三个参数是用于表示传感器输出信息的更新速率，共有SENSOR_DELAY_UI、SENSOR_DELAY_NORMAL、SENSOR_DELAY_GAME 和SENSOR_DELAY_FASTEST 这四种值可选，它们的更新速率是依次递增的。
因此，注册一个SensorEventListener 就可以写成：
```
senserManager.registerListener(listener, senser, SensorManager.SENSOR_DELAY_NORMAL);
```

另外始终要记得，当程序退出或传感器使用完毕时，一定要调用unregisterListener ()方法将使用的资源释放掉，如下所示：
```
sensorManager.unregisterListener(listener);
```

### 光照传感器
Sensor sensor = sensorManager.getDefaultSensor(Sensor.TYPE_LIGHT);

### 加速度传感器的用法
Sensor sensor = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);

### 模仿微信摇一摇
```
public class MainActivity extends Activity {
    private SensorManager sensorManager;
    
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        sensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        Sensor sensor = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
        sensorManager.registerListener(listener, sensor, SensorManager.SENSOR_DELAY_NORMAL);
    }
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (sensorManager != null) {
        sensorManager.unregisterListener(listener);
        }
    }
    private SensorEventListener listener = new SensorEventListener() {
        @Override
        public void onSensorChanged(SensorEvent event) {
            // 加速度可能会是负值，所以要取它们的绝对值
            float xValue = Math.abs(event.values[0]);
            float yValue = Math.abs(event.values[1]);
            float zValue = Math.abs(event.values[2]);
            if (xValue > 15 || yValue > 15 || zValue > 15) {
            // 认为用户摇动了手机，触发摇一摇逻辑
            Toast.makeText(MainActivity.this, "摇一摇",
            Toast.LENGTH_SHORT).show();
            }
        }
        @Override
        public void onAccuracyChanged(Sensor sensor, int accuracy) {
        
        }
    };
}

```

### 方向传感器
Sensor sensor = sensorManager.getDefaultSensor(Sensor.TYPE_ORIENTATION);

之后在onSensorChanged()方法中通过SensorEvent 的values 数组，就可以得到传感器输
出的所有值了。方向传感器会记录手机在所有方向上的旋转角度，如图12.3 所示。
其中，values[0]记录着手机围绕Z 轴的旋转角度，values[1] 记录着手机围绕X 轴的旋转角度，values[2] 记录着手机围绕Y 轴的旋转角度。

看起来很美好是吗？但遗憾的是，Android 早就废弃了Sensor.TYPE_ORIENTATION 这种传感器类型，虽然代码还是有效的，但已经不再推荐这么写了。
事实上，Android 获取手机旋转的方向和角度是通过加速度传感器和地磁传感器共同计算得出的，这也是Android 目前推荐使用的方式。

```
首先我们需要分别获取到加速度传感器和地磁传感器的实例，并给它们注册监听器，如下所示：
Sensor accelerometerSensor = sensorManager.getDefaultSensor(Sensor.TYPE_ACCELEROMETER);
Sensor magneticSensor = sensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD);
sensorManager.registerListener(listener, accelerometerSensor,SensorManager.SENSOR_DELAY_GAME);
sensorManager.registerListener(listener, magneticSensor,SensorManager.SENSOR_DELAY_GAME);

由于方向传感器的精确度要求通常都比较高，这里我们把传感器输出信息的更新速率提高了一些，使用的是SENSOR_DELAY_GAME。

接下来在onSensorChanged()方法中可以获取到SensorEvent 的values 数组，
分别记录着加速度传感器和地磁传感器输出的值。然后将这两个值传入到SensorManager 的
getRotationMatrix()方法中就可以得到一个包含旋转矩阵的R 数组，如下所示：

SensorManager.getRotationMatrix(R, null, accelerometerValues, magneticValues);
第一个参数R 是一个长度为9 的float 数组，getRotationMatrix()方法计算出的旋转数据就会赋值到这个数组当中。
第二个参数是一个用于将地磁向量转换成重力坐标的旋转矩阵，通常指定为null 即可。
第三和第四个参数则分别就是加速度传感器和地磁传感器输出的values 值。

得到了R 数组之后，接着就可以调用SensorManager 的getOrientation()方法来计算手机的旋转数据了，如下所示：
SensorManager.getOrientation(R, values);
values 是一个长度为3 的float 数组，手机在各个方向上的旋转数据都会被存放到这个数组当中。
values[0]记录着手机围绕着图12.3 中Z 轴的旋转弧度，
values[1]记录着手机围绕X 轴的旋转弧度，
values[2]记录着手机围绕Y 轴的旋转弧度。

注意这里计算出的数据都是以弧度为单位的，因此如果你想将它们转换成角度还需要调用如下方法：
Math.toDegrees(values[0]);
```

### 制作简易指南针
```
public class MainActivity extends Activity {
    private SensorManager sensorManager;
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        sensorManager = (SensorManager) getSystemService(Context.SENSOR_SERVICE);
        Sensor magneticSensor = sensorManager.getDefaultSensor(Sensor.TYPE_MAGNETIC_FIELD);
        Sensor accelerometerSensor = sensorManager.getDefaultSensor(
        Sensor.TYPE_ACCELEROMETER);
        sensorManager.registerListener(listener, magneticSensor,
        SensorManager.SENSOR_DELAY_GAME);
        sensorManager.registerListener(listener, accelerometerSensor,
        SensorManager.SENSOR_DELAY_GAME);
    }
    @Override
    protected void onDestroy() {
        super.onDestroy();
        if (sensorManager != null) {
        sensorManager.unregisterListener(listener);
        }
    }
    private SensorEventListener listener = new SensorEventListener() {
        float[] accelerometerValues = new float[3];
        float[] magneticValues = new float[3];
        @Override
        public void onSensorChanged(SensorEvent event) {
            // 判断当前是加速度传感器还是地磁传感器
            if (event.sensor.getType() == Sensor.TYPE_ACCELEROMETER) {
                // 注意赋值时要调用clone()方法
                accelerometerValues = event.values.clone();
            } else if (event.sensor.getType() == Sensor.TYPE_MAGNETIC_FIELD) {
                // 注意赋值时要调用clone()方法
                magneticValues = event.values.clone();
            }
            float[] R = new float[9];
            float[] values = new float[3];
            SensorManager.getRotationMatrix(R, null, accelerometerValues,
            magneticValues);
            SensorManager.getOrientation(R, values);
            Log.d("MainActivity", "value[0] is " + Math.toDegrees(values[0]));
        }
        @Override
        public void onAccuracyChanged(Sensor sensor, int accuracy) {
        }
    };
}

```

### 全局获取Context
```
public class MyApplication extends Application {
    private static Context context;
    @Override
    public void onCreate() {
        context = getApplicationContext();
    }
    public static Context getContext() {
        return context;
    }
}
```

### 使用Intent 传递对象

在Intent 中添加一些附加数据，以达到传值的效果，
比如在FirstActivity 中添加如下代码：
```
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
intent.putExtra("string_data", "hello");
intent.putExtra("int_data", 100);
startActivity(intent);
这里调用了Intent 的putExtra()方法来添加要传递的数据，之后在SecondActivity 中就可
以得到这些值了，代码如下所示：
getIntent().getStringExtra("string_data");
getIntent().getIntExtra("int_data", 0);
```

但是不知道你有没有发现，putExtra()方法中所支持的数据类型是有限的，虽然常用的一
些数据类型它都会支持，但是当你想去传递一些自定义对象的时候就会发现无从下手。

Serializable 方式
```
public class Person implements Serializable{
。。。

Person person = new Person();
person.setName("Tom");
person.setAge(20);
Intent intent = new Intent(FirstActivity.this, SecondActivity.class);
intent.putExtra("person_data", person);
startActivity(intent);

获取这个对象也很简单，写法如下：
Person person = (Person) getIntent().getSerializableExtra("person_data");
```

Parcelable 方式
```
public class Person implements Parcelable {
    private String name;
    private int age;
    ……
    @Override
    public int describeContents() {
        return 0;
    }
    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeString(name); // 写出name
        dest.writeInt(age); // 写出age
    }
    public static final Parcelable.Creator<Person> CREATOR = new Parcelable.Creator<Person>() {
        @Override
        public Person createFromParcel(Parcel source) {
            Person person = new Person();
            person.name = source.readString(); // 读取name
            person.age = source.readInt(); // 读取age
            return person;
        }
        @Override
        public Person[] newArray(int size) {
            return new Person[size];
        }
    };
}

获取对象
Person person = (Person) getIntent().getParcelableExtra("person_data");
```