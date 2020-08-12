# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 活动Activity
Activity
1、概念：
android 中，Activity 相当于一个页面，可以在Activity中添加Button、CheckBox 等控件，一个android 程序有多个Activity组成。

2、生命周期：


3、四中启动模式
Standard 模式 : standard 模式是android 的默认启动模式，在这种模式下，activity可以有多个实例，每次启动Activity，无论任务栈中是否已经存在这个activity的实例，系统都会创建一个新的activity实例。

SingleTop 模式： 栈顶模式，当一个singleTop模式的activity 已经位于栈顶时，再去启动它时，不在创建实例，如果不在栈顶，就会创建实例。

SingleTask 模式 ： 单任务模式，如果启动的activity 已经存在于 任务栈中，则会将activity移动到栈顶，并将上面的activity出栈，否则创建新的实例

SingleInstance 模式 ：单实例模式，一个activity 一个栈。



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