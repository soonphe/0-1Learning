
project根目录下配置：
android {  
    defaultConfig {  
        // Enabling multidex support.  
        multiDexEnabled true  
    }  
}  
 
app下配置：
dependencies {  compile 'com.google.android:multidex:0.1'}  
 
 
如果你的工程中已经含有Application类,那么让它继承android.support.multidex.MultiDexApplication类,
     如果你的Application已经继承了其他类并且不想做改动，那么还有另外一种使用方式,覆写attachBaseContext()方法:
 
public class MyApplication extends FooApplication {  
    @Override  
    protected void attachBaseContext(Context base) {  
        super.attachBaseContext(base);  
        MultiDex.install(this);  
    }  
}  