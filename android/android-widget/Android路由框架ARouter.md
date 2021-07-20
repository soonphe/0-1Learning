1.添加依赖
android {     defaultConfig {         ...         javaCompileOptions {             annotationProcessorOptions {                 arguments = [AROUTER_MODULE_NAME: project.getName()]             }         }     } }
dependencies {     // Replace with the latest version     compile 'com.alibaba:arouter-api:?'     annotationProcessor 'com.alibaba:arouter-compiler:?'     ... }
 
2.Activity添加注解，至少为两级结构
@Route(path = "/test/activity") public class YourActivity extend Activity {     ... }
 
3.初始化SDK
//此配置必须在init之前
if (isDebug()) {                ARouter.openLog();          ARouter.openDebug();  
} ARouter.init(mApplication); 
 
4.初始化路由
// 1. 简单跳转
ARouter.getInstance().build("/test/activity").navigation();
// 2. 携带参数跳转 ARouter.getInstance().build("/test/1")             .withLong("key1", 666L)             .withString("key3", "888")             .withObject("key4", new Test("Jack", "Rose"))             .navigation();
 
6.使用 Gradle 插件实现路由表的自动加载 (可选)
apply plugin: 'com.alibaba.arouter'
buildscript {    repositories {        jcenter()    }
dependencies {        classpath "com.alibaba:arouter-register:?"    }}
7.参数解析
@Route(path = "/test/activity")public class Test1Activity extends Activity {    @Autowired    public String name;    @Autowired    int age;
8.声明拦截器(拦截跳转过程，面向切面编程)
// 比较经典的应用就是在跳转过程中处理登陆事件，这样就不需要在目标页重复做登陆检查// 拦截器会在跳转之间执行，多个拦截器会按优先级顺序依次执行@Interceptor(priority = 8, name = "测试用拦截器")public class TestInterceptor implements IInterceptor {
    @Override    public void process(Postcard postcard, InterceptorCallback callback) {    ...    callback.onContinue(postcard);  // 处理完成，交还控制权    // callback.onInterrupt(new RuntimeException("我觉得有点异常"));      // 觉得有问题，中断路由流程
// 以上两种至少需要调用其中一种，否则不会继续路由    }
@Override    public void init(Context context) {    // 拦截器的初始化，会在sdk初始化的时候调用该方法，仅会调用一次    }
9.处理跳转结果
// 使用两个参数的navigation方法，可以获取单次跳转的结果ARouter.getInstance().build("/test/1").navigation(this, new NavigationCallback() {    @Override    public void onFound(Postcard postcard) {    ...    }
@Override    public void onLost(Postcard postcard) {    ...    }});
10.自定义全局降级策略
// 实现DegradeService接口，并加上一个Path内容任意的注解即可@Route(path = "/xxx/xxx")public class DegradeServiceImpl implements DegradeService {
 
 
 
 
 
 
 
 
 
 
 
 
 
 
