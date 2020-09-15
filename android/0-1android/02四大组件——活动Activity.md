# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 活动Activity
Activity


### 活动是什么：
android 中，Activity 相当于一个页面，可以在Activity中添加Button、CheckBox 等控件，一个android 程序有多个Activity组成。

活动（Activity）是最容易吸引到用户的地方了，它是一种可以包含用户界面的组件， 主要用于和用户进行交互。一个应用程序中可以包含零个或多个活动，但不包含任何活动的 应用程序很少见，谁也不想让自己的应用永远无法被用户看到吧？

### 活动的基本用法
到现在为止，你还没有手动创建过活动呢，因为上一章中的 HelloWorldActivity 是 ADT 帮我们自动创建的。手动创建活动可以加深我们的理解，因此现在是时候应该自己动手了。

首先，你需要再新建一个 Android 项目，项目名可以叫做 ActivityTest，包名我们就使用 默认值 com.example.activitytest。新建项目的步骤你已经在上一章学习过了，不过图 1.12 中 的那一步需要稍做修改，我们不再勾选 Create Activity 这个选项，因为这次我们准备手动创 建活动，如图 2.1 所示。

点击 Finish， 项目就创建完成了， 这时候你的 Eclipse 中应该有两个项目， ActivityTest 和 HelloWorld。极度建议你将不相干的项目关闭掉，仅打开当前工作所需要的项目，不然我 保证以后你会在这方面吃亏。最好现在就右击 HelloWorld 项目→Close Project。

### 活动生命周期：

（1）onCreate:create表示创建，这是Activity生命周期的第一个方法，也是我们在android开发中接触的最多的生命周期方法。它本身的作用是进行Activity的一些初始化工作，比如使用setContentView加载布局，对一些控件和变量进行初始化等。但也有很多人将很多与初始化无关的代码放在这，其实这是不规范的。此时Activity还在后台，不可见。所以动画不应该在这里初始化，因为看不到……

（2）onStart:start表示启动，这是Activity生命周期的第二个方法。此时Activity已经可见了，但是还没出现在前台，我们还看不到，无法与Activity交互。其实将Activity的初始化工作放在这也没有什么问题，放在onCreate中是由于官方推荐的以及我们开发的习惯。

（3）onResume:resume表示继续、重新开始，这名字和它的职责也相同。此时Activity经过前两个阶段的初始化已经蓄势待发。Activity在这个阶段已经出现在前台并且可见了。这个阶段可以打开独占设备

（4）onPause:pause表示暂停，当Activity要跳到另一个Activity或应用正常退出时都会执行这个方法。此时Activity在前台并可见，我们可以进行一些轻量级的存储数据和去初始化的工作，不能太耗时，因为在跳转Activity时只有当一个Activity执行完了onPause方法后另一个Activity才会启动，而且android中指定如果onPause在500ms即0.5秒内没有执行完毕的话就会强制关闭Activity。从生命周期图中发现可以在这快速重启，但这种情况其实很罕见，比如用户切到下一个Activity的途中按back键快速得切回来。

（5）onStop：stop表示停止，此时Activity已经不可见了，但是Activity对象还在内存中，没有被销毁。这个阶段的主要工作也是做一些资源的回收工作。

（6）onDestroy：destroy表示毁灭，这个阶段Activity被销毁，不可见，我们可以将还没释放的资源释放，以及进行一些回收工作。

（7）onRestart：restart表示重新开始，Activity在这时可见，当用户按Home键切换到桌面后又切回来或者从后一个Activity切回前一个Activity就会触发这个方法。这里一般不做什么操作。

### 四中启动模式
Standard 模式 : standard 模式是android 的默认启动模式，在这种模式下，activity可以有多个实例，每次启动Activity，无论任务栈中是否已经存在这个activity的实例，系统都会创建一个新的activity实例。

SingleTop 模式： 栈顶模式，当一个singleTop模式的activity 已经位于栈顶时，再去启动它时，不在创建实例，如果不在栈顶，就会创建实例。

SingleTask 模式 ： 单任务模式，如果启动的activity 已经存在于 任务栈中，则会将activity移动到栈顶，并将上面的activity出栈，否则创建新的实例

SingleInstance 模式 ：单实例模式，一个activity 一个栈。


### 活动的跳转方式
4、三种跳转方式
显示启动 ：
Intrent 内部直接声明要启动的activity所对应的的class
~~~~
Intent intent = new Intent(MainActivity.this, SecondActivity.class);
startActivity(intnet);
~~~~

隐式启动
进行三个匹配，一个是activity，一个是category，一个是data，全部或者部分匹配，应用于广播原理

清单文件中 里配置activity属性，activity的名字要和跳转内容一样
~~~~
<activity 
	android:name="com.exanple.android.tst.secondActivity"
	android:label = @string/title>
	<intent=filter>
		<action android:name="com.exanple.android.tst.secondActivity/>
		<category android:name="android.intent.category.DEFAULT"/>
	<intent-filter/>
</activity>
~~~~

在需要跳转的地方
~~~~
Intent intent = new Intent("com.example.android.tst.secondActivity");
startActivity(intnet);
~~~~

跳转后再返回，能获取返回值
~~~~
Intent in = new Intent(MainActivity.this,OtehrActivity.class);
in.putExtra("a",a);
startActivityForResult(in,1000);
~~~~
在OTherActivity中设置返回值
~~~~
Intent int = new Intent();
int.putExtra("c",c);
setResult(1001,int);
finish();
~~~~
在MainActivity中获取返回值
~~~~
@Override
protected void onActivityResult(int requestCode, int resultCode	,Intent data) {
	super.onActivityResult(requestCode,resultCode,data);
	if(requestCode == 1000){
		if(resultCode == 1001){
			int c = data.getExtra("c",0);
		}
	}
~~~~