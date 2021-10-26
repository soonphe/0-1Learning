# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## 活动Activity

### 2.1 Activity是什么：
活动（Activity）是一种可以包含用户界面的组件，
相当于一个页面，可以在Activity中添加Button、CheckBox 等控件， 主要用于和用户进行交互，
一个应用程序中可以包含零个或多个活动。


### 2.2 Activity的基本用法
到现在为止，你还没有手动创建过活动呢，因为上一章中的 HelloWorldActivity 是 ADT 帮我们自动创建的。
手动创建活动可以加深我们的理解，因此现在是时候应该自己动手了。

首先，你需要再新建一个 Android 项目，项目名可以叫做 ActivityTest，包名我们就使用 默认值 com.example.activitytest。
新建项目的步骤你已经在上一章学习过了，不过图 1.12 中 的那一步需要稍做修改，我们不再勾选 Empty Activity 这个选项，而是选择Add No Activity，因为这次准备手动创建活动
点击 Finish， 等待Gradle构建完成，项目就创建完成了。

#### 手动创建Activity
右键包名：com.example.activitytest，选择New→Activity→Empty Activity，对话框中命名FirstActivity即可
代码如下：
``````
package com.example.myapplication
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
class MainActivity : AppCompatActivity() {
    //任何Activity需要重写父类的onCreate方法
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
    }
}
``````

#### 创建和加载布局
Android程序的设计讲究逻辑和程序分离
创建布局操作步骤：
Res目录->New->Directory->创建Layout目录->New->Layout Resourcce file->将布局文件命名为first_layout。
点击右上角按钮切换为Design可视化布局或XML编辑布局。
代码示例如下：（这里用按钮button举例）
``````
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:orientation="vertical"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
    <Button
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:id="@+id/button1"
        android:text="Button 1"></Button>
</LinearLayout>
``````
android: id 是唯一标识符，不允许重复
android:layout_width 指定了当前元素 的宽度，这里使用 match_parent 表示让当前元素和父元素一样宽。
android:layout_height 指定 了当前元素的高度，这里使用 wrap_content，表示当前元素的高度只要能刚好包含里面的内 容就行。
android:text 指定了元素中显示的文字内容。


随后在Activity中加载这个布局，使用setcontentView给当前加载一个布局，一般方法是传入布局文件的id。
项目添加的任何资源都会在R文件中生成一个布局文件的id，因此上述xml文件的id已经添加进入了，只需要引用即可。

````
package com.example.myapplication
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
class MainActivity : AppCompatActivity() {
    //任何Activity需要重写父类的onCreate方法
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.first_layout)
    }
}
````

### 在AndroidManifest.xml中注册
注：所有的活动都要在 AndroidManifest.xml 中进行注册才能生效，
Actvity的注册声明要放在application标签内，通过<Activity>对Activity进行注册，
android:name用于指定哪一个Activity，这里采用了缩写，目前没有配置主程序Activity，因此仍然无法运行。
``````

<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapplication">
     
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity"></activity>
    </application>
 
</manifest>
``````

android中配置主Activity的方案是内部加入<intent-filter>标签，然后在此标签中加入两句必要的声明，android:label指定了标题内容。
修改后的代码如下所示：
``````
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.myapplication">
 
    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity
            android:name=".MainActivity"
            android:label="This is Activity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />
                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>
</manifest>
``````

这个我在前面也已经解释过了，如果你想让 FirstActivity 作为我们这个程序的 主活动，即点击桌面应用程序图标时首先打开的就是这个活动，那就一定要加入这两句声明。
另外需要注意，如果你的应用程序中没有声明任何一个活动作为主活动，这个程序仍然是可 以正常安装的，只是你无法在启动器中看到或者打开这个程序。


### 在Activity中使用Toast
Toast是一种非常好的提醒方式，实现如下：
``````
package com.example.myapplication
 
import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.widget.Button
import android.widget.Toast
import kotlinx.android.synthetic.main.first_layout.*
 
class MainActivity : AppCompatActivity() {
    //任何Activity需要重写父类的onCreate方法
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.first_layout)
        //findViewById获取布局文件中定义的元素，R.id.button1得到按键实例，返回一个继承自View的泛型变量
        //因为无法自动推导，所以显式声明为Button类型。
        val button1: Button = findViewById(R.id.button1)
        //得到按键实例后，我们调用setOnClickListener注册监听器，点击按钮会执行onclick方法
        button1.setOnClickListener {
            //静态方法makeText创建一个Toast对象，共三个参数，一个是上下文，Activity本身就是context对象；第二个是文本内容；第三个是Toast显示时长
            Toast.makeText(this, "you clicked button 1", Toast.LENGTH_SHORT).show()
        }
    }
}
``````
如果十个控件，Java需要十个findViewById才行，需要借助ButterKnife才能解决；
在Kotlin中Android项目的gradle文件中默认引入Kotlin-android-extensions插件，会根据布局文件定义的控件id自动生成一个具有相同名称的变量，直接使用这一变量，删除findViewById这一行。推荐使用后者。

### 在Activity中引入Menu
       Menu是目录，不占用任何屏幕控件，创建如下：res目录->右击选New->Directory->输入文件名menu->右击选New->Menu resource file->输入文件名main。

``````
<?xml version="1.0" encoding="utf-8"?>
<menu xmlns:android="http://schemas.android.com/apk/res/android">
<!-- item用于创建某一个菜单项，指明唯一标识符和名称-->
    <item
        android:id="@+id/add_item"
        android:title="Add" />
    <item
        android:id="@+id/remove_item"
        android:title="remove" />
</menu>
``````

再回到mainActivity，重写onCreateOptionsMenu方法，编写如下方法：
``````
    override fun onCreateOptionsMenu(menu: Menu?): Boolean {
        //menuInflater调用了父类的getmenuInflater方法，在调用它的inflate方法就可以创建菜单了，
        // 两个参数：一个是哪一个资源文件的id；另一个是添加到哪一个Menu对象。
        menuInflater.inflate(R.menu.main, menu)
        //返回true显示出来，返回false则不显示
        return true
}
``````

插一点语法糖概念，自动将下面的代码转换为setPages方法和getPages方法。非常简单的Java类和调用的语法糖。
``````
package com.example.myapplication;
 
public class Book {
    private int pages;
 
    public int getPages() {
        return pages;
    }
 
    public void setPages(int pages) {
        this.pages = pages;
    }
}
 
val book = Book()
book.pages = 500
val bookPages = book.pages
``````

另外，仅显示还不够，必须要有相应的点击事件。复写onOptionsItemSelected即可。
``````
    override fun onOptionsItemSelected(item: MenuItem?): Boolean {
        //语法糖的小应用，调用了item的getItemId方法
        when (item?.itemId) {
            //语法逻辑:匹配值->{执行逻辑}
            R.id.add_item -> Toast.makeText(this, "You clicked Add", Toast.LENGTH_SHORT).show()
            R.id.remove_item -> Toast.makeText(this, "You clicked Remove", Toast.LENGTH_SHORT)
                .show()
        }
        return true
}
``````

#### 销毁一个Activity
        Back键或者使用finish()方法。
        
### 2.3 使用Intent在Activity之间穿梭
#### 显式Intent
       创建第二个Activity，并在其中加入一个按钮。Intent是Android各组件进行交互的一种重要方式，可以不同组件传递数据、指明要执行动作。譬如：启动Activity、启动Service、发送广播等。Intent有两种，一种是显式，一种是隐式。下面介绍前者。
``````
 button1.setOnClickListener {
            //Intent接受两个参数：第一个参数context提供了启动Activity的上下文；第二个参数是要启动的目标Activity
            //this是当前上下文，第二个SecondActivity::class.java相当于Java的SecondActivity.class
            val intent = Intent(this, SecondActivity::class.java)
            //启动Activity，接受一个Intent参数
            startActivity(intent)
        }
``````


#### 隐式Intent
隐式Intent通过指定一系列更为抽象的action和category等信息去决定启动哪一个Activity。首先在AndroidManifest.xml中的intent-filter中声明action和category。如下所示：

``````
   <activity android:name=".SecondActivity">
            <intent-filter>
                <action android:name="com.example.myapp.ACTION_START"/>
                <category android:name="android.intent.category.DEFAULT"/>
            </intent-filter>
   </activity>
``````

action标明做啥动作，category是附加信息。只有两者内容同时匹配Intent的指定时，才能响应Intent。响应代码如下所示：

``````
  button1.setOnClickListener {
            val intent = Intent("com.example.myapp.ACTION_START")
            startActivity(intent)
  }
``````
       
刚不是说两个同时匹配么？这是因为android.intent.category.DEFAULT是默认的category，会将其自动添加进去。每个Intent有一个action和多个category。
``````
     button1.setOnClickListener {
            val intent = Intent("com.example.myapp.ACTION_START")
            intent.addCategory("com.example.myapp.my_category")
            startActivity(intent)
     }
``````

Intent中添加了category，但xml中的<intent-filter>没有，需要再添加一个category的声明。在xml中声明。
``````
        <activity android:name=".SecondActivity">
            <intent-filter>
                <action android:name="com.example.myapp.ACTION_START" />
                <category android:name="android.intent.category.DEFAULT" />
                <category android:name="com.example.myapp.my_category" />
            </intent-filter>
        </activity>
``````
#### 更多隐式Intent的用法
       隐式Intent不仅启动程序内的Activity，还可以启动其他程序的Activity。举例来讲，应用程序内展示一个网页百度。

````
        button1.setOnClickListener {
            //action是Intent.ACTION_VIEW，安卓内置动作android.intent.action.VIEW
            val intent = Intent(Intent.ACTION_VIEW)
            //Uri.parse将字符串解析成Uri对象，然后再使用setData传入，这里使用了语法糖，使得看起来是给Intent的data赋值
            intent.data = Uri.parse("https://www.baidu.com")
            startActivity(intent)
        }
````
       另外，<data>标签用于指定数据协议之类的，书中讲的比较粗略，除了https协议，仍有geo地理位置、tel打电话等。譬如，打电话给10086。
````
        button1.setOnClickListener {
            //内置动作，拨号
            val intent = Intent(Intent.ACTION_DIAL)
            //data部分指定了协议是tel，号码是10086
            intent.data = Uri.parse("tel:10086")
            startActivity(intent)
        }
````

#### 向下一个Activity传递数据

       Intent在启动Activity过程中可以传递数据。Intent中提供了putExtra方法进行重载，举例来讲：将字符串从一个Activity中传递至第二个Activity中。
````
        button1.setOnClickListener {
            val data = "Hello SecondActivity"
            val intent = Intent(this, SecondActivity::class.java)
            //putExtra接受的是键值对，第一个参数是键，用于后面取值；第二个是真正要传递的数据
            intent.putExtra("extra_data", data)
            startActivity(intent)
        }
````
      在第二个Activity中将传递的数据拿出，并将其打印出来。
````
class SecondActivity : AppCompatActivity() {
 
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.layout_second)
        //intent调用的是getIntent方法,会获取用于启动SecondActivity的Intent,getStringExtra获取到传递的数据
        //getIntExtra拿到的是整形；getBooleanExtra拿到的是布尔类型
        val extradata = intent.getStringExtra("extra_data")
        Log.d("SecondActivity", "extra data is $extradata")
    }
}
````
#### 返回数据给上一个Activity
       既可以传递数据给下一个Activity，那么将数据返回给一个Activity也是可行的。
````
        button1.setOnClickListener {
            val intent = Intent(this, SecondActivity::class.java)
            startActivityForResult(intent, 1)
        }
````

       startActivityForResult方法接受两个参数：第一个参数是Intent，第二个参数是请求码。用于在之后的回调中判断数据来源。
````
class SecondActivity : AppCompatActivity() {
 
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.layout_second)
        button2.setOnClickListener {
            //Intent传递数据，没有任何意图，只需要将数据存放进去
            val intent = Intent()
            intent.putExtra("data_return", "Hello FirstActivity")
            //setResult接受两个参数：第一个是用于向上一个Activity返回处理结果，一般是RESULT_OK或者RESULT_CANCELED
            //第二个是带有数据的intent传递过去。最后调用finish销毁。
            setResult(Activity.RESULT_OK, intent)
            finish()
        }
    }
}
````
       最后需要在第一个Activity重写方法得到返回的数据。
````
  //第一个参数requestCode是启动Activity时传入的请求码；第二个参数resultcode是返回数据传入的处理结果
    //第三个参数是data数据，携带intent。
    override fun onActivityResult(requestCode: Int, resultCode: Int, data: Intent?) {
        super.onActivityResult(requestCode, resultCode, data)
        //requestcode是判断数据来源
        when (requestCode) {
            //resultcode是判断处理结果是否成功与否。
            1 -> if (resultCode == Activity.RESULT_OK) {
                val returnedData = data?.getStringExtra("data_return")
                //将data值打印出来
                Log.d("MainActivity", "returned data is $returnedData")
            }
        }
}
````
       那么如果用户不是通过点击事件返回，而是点击Back键回到第一个Activity，如何处理？通过在第二个Activity中重写onBackPressed方法来解决这个问题。
````
    override fun onBackPressed() {
        val intent = Intent()
        intent.putExtra("data_return", "Hello FirstActivity")
        setResult(Activity.RESULT_OK, intent)
        finish()
}
````

### 2.4 活动生命周期：

#### 返回栈
        栈后进先出，这个栈在Android中被称为返回栈。默认情况，启动Activity入栈并处于栈顶位置，back键或者finish方法时，栈顶将会被移除。
#### Activity状态
        Activity有四种状态：
        1.运行状态：当Activity处于栈顶时；
        2.暂停状态：不再处于栈顶但可见，并不是每一个Activity都会占满屏幕。eg：对话框形式的Activity占用屏幕中间部分区域，下面的Activity是暂停的，一般不会回收；
        3.停止状态：不再处于栈顶且完全不可见。当其他地方需要内存时，该状态的Activity可能会被回收；
        4.销毁状态：从返回栈中移除，系统会回收该部分。
        
        
#### Activity生存期——七个回调方法
（1）onCreate:create表示创建，这是Activity生命周期的第一个方法，也是我们在android开发中接触的最多的生命周期方法。它本身的作用是进行Activity的一些初始化工作，比如使用setContentView加载布局，对一些控件和变量进行初始化等。但也有很多人将很多与初始化无关的代码放在这，其实这是不规范的。此时Activity还在后台，不可见。所以动画不应该在这里初始化，因为看不到……

（2）onStart:start表示启动，这是Activity生命周期的第二个方法。此时Activity已经可见了，但是还没出现在前台，我们还看不到，无法与Activity交互。其实将Activity的初始化工作放在这也没有什么问题，放在onCreate中是由于官方推荐的以及我们开发的习惯。

（3）onResume:resume表示继续、重新开始，这名字和它的职责也相同。此时Activity经过前两个阶段的初始化已经蓄势待发。Activity在这个阶段已经出现在前台并且可见了。这个阶段可以打开独占设备

（4）onPause:pause表示暂停，当Activity要跳到另一个Activity或应用正常退出时都会执行这个方法。此时Activity在前台并可见，我们可以进行一些轻量级的存储数据和去初始化的工作，不能太耗时，因为在跳转Activity时只有当一个Activity执行完了onPause方法后另一个Activity才会启动，而且android中指定如果onPause在500ms即0.5秒内没有执行完毕的话就会强制关闭Activity。从生命周期图中发现可以在这快速重启，但这种情况其实很罕见，比如用户切到下一个Activity的途中按back键快速得切回来。

（5）onStop：stop表示停止，此时Activity已经不可见了，但是Activity对象还在内存中，没有被销毁。这个阶段的主要工作也是做一些资源的回收工作。

（6）onDestroy：destroy表示毁灭，这个阶段Activity被销毁，不可见，我们可以将还没释放的资源释放，以及进行一些回收工作。

（7）onRestart：restart表示重新开始，Activity在这时可见，当用户按Home键切换到桌面后又切回来或者从后一个Activity切回前一个Activity就会触发这个方法。这里一般不做什么操作。


#### Activity被回收了怎么办？

        考虑一个问题，Activity进入停止状态是有可能被回收的，假设可能被回收的Activity有用户输入的文字之类的，系统回收之后没了，很影响用户体验，在这里我们使用onSavedInstance方法来保证Activity被回收之前一定会被调用。
       onSavedInstance方法携带一个Bundle类型的数据，Bundle提供了保存数据的一系列方法，putString保存字符串，putInt保存整数，这方法有两个参数：键和内容。
````
    override fun onSaveInstanceState(outState: Bundle?, outPersistentState: PersistableBundle?) {
        super.onSaveInstanceState(outState, outPersistentState)
        val tempData = "Something u just typed"
        outState?.putString("data_key", tempData)
}
````
        onCreate方法中调用、恢复：
````
        if (savedInstanceState != null) {
            val tempData = savedInstanceState.getString("data_key")
            Log.d(tag, tempData)
        }
````
       举一反三:（1）Intent也可以结合Bundle一起传递数据。Bundle对象置于intent当中进行传递；
       （2）横竖屏旋转也使得Activity经历重建过程，可以通过onSavedInstance保存数据，当然也有更优的方法。

### 2.5 活动的启动模式
Standard 模式 : standard 模式是android 的默认启动模式，在这种模式下，activity可以有多个实例，每次启动Activity，无论任务栈中是否已经存在这个activity的实例，系统都会创建一个新的activity实例。

SingleTop 模式： 栈顶模式，当一个singleTop模式的activity 已经位于栈顶时，再去启动它时，不在创建实例，如果不在栈顶，就会创建实例。

SingleTask 模式 ： 单任务模式，如果启动的activity 已经存在于 任务栈中，则会将activity移动到栈顶，并将上面的activity出栈，否则创建新的实例

SingleInstance 模式 ：单实例模式，一个activity 一个栈。




