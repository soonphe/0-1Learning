1.gradle依赖
    compile 'com.google.dagger:dagger:2.11'
    annotationProcessor 'com.google.dagger:dagger-compiler:2.11'
 
2.相关注解解析
@Inject: 通常在需要依赖的地方使用这个注解，或者标记用于提供依赖的方法。换句话说，你用它告诉Dagger这个类或者字段需要依赖注入。这样，Dagger就会构造一个这个类的实例并满足他们的依赖。
@Module: Modules类里面的方法专门提供依赖，所以我们定义一个类，用@Module注解，这样Dagger在构造类的实例的时候，就知道从哪里去找到需要的 依赖。modules的一个重要特征是它们设计为分区并组合在一起（比如说，在我们的app中可以有多个组成在一起的modules）。
 
@Provide: 在modules中，我们定义的方法是用这个注解，以此来告诉Dagger我们想要构造对象并提供这些依赖。
 
@Component: Components从根本上来说就是一个注入器，也可以说是@Inject和@Module的桥梁，它的主要作用就是连接这两个部分。 Components可以提供所有定义了的类型的实例，比如：我们必须用@Component注解一个接口然后列出所有的@Modules组成该组件，如 果缺失了任何一块都会在编译的时候报错。所有的组件都可以通过它的modules知道依赖的范围。
——多层依赖：
@Component(dependencies = ApplicationComponent.class, modules = ActivityModule.class)        //除Module，这个Component还依赖ApplicationComponent
 
@Scope: Scopes可是非常的有用，Dagger2可以通过自定义注解限定注解作用域。后面会演示一个例子，这是一个非常强大的特点，因为就如前面说的一样，没 必要让每个对象都去了解如何管理他们的实例。在scope的例子中，我们用自定义的@PerActivity注解一个类，所以这个对象存活时间就和 activity的一样。简单来说就是我们可以定义所有范围的粒度(@PerFragment, @PerUser, 等等)。
 
在Dagger2中，我们可以通过自定义Scope来实现局部单例。（例：两个activity共享一个实例，其他activity获取另外实例）
——首先让我们先来定义一个局部作用域：
@Scope  
@Retention(RetentionPolicy.RUNTIME)  
public @interface UserScope {  
}  
然后就可以使用@UserScope 注解局部作用域
 
    public ActivityComponent getComponent() {
        return DaggerActivityComponent.builder()
                .activityModule(getActivityModule())
                .applicationComponent(getApplicationComponent())
                .build();
    }
 
    protected ApplicationComponent getApplicationComponent() {
        return ((MyApplication) getApplication()).getApplicationComponent();
    }
 
Qualifier: 当类的类型不足以鉴别一个依赖的时候，我们就可以使用这个注解标示。例如：在Android中，我们会需要不同类型的context，所以我们就可以定义 qualifier注解“@ForApplication”和“@ForActivity”，这样当注入一个context的时候，我们就可以告诉 Dagger我们想要哪种类型的context。
 
——————————————————————————————————————————
用法：
dagger依赖注入**********************
①编写module提供依赖
@Module
public class MainModule {
    @Provides
    public Cloth getCloth() {
 
②书写Component接口
@Component(modules=MainModule.class)
public interface MainComponent {
    void inject(MainActivity mainActivity);
}
③调用
@Inject
            AppApi appApi;
 
        MainComponent build = DaggerMainComponent.builder().mainModule(new MainModule()).build();
        build.inject(this);
 
        mMainComponent = DaggerFragmentComponent.builder()
                .applicationComponent(getApplicationComponent())
                .build();
        mMainComponent.inject(this);
 
@Inject
当Component在所拥有的Module类中找不到依赖需求方需要类型的提供方法时,
Dagger2就会检查该需要类型的有没有用@Inject声明的构造方法,有则用该构造方法创建一个.
 
————————————————————————————————————————————————————————————————
Dagger使用示例：
1.@Module和@Provider提供依赖
@Module  
public class UserModule {  
    @Provides  
    @Singleton  
    User providesUser() {  
        return new User();  
    }  
}  
 
2.Component使用modules
配置ActivityComponent，如下：（如果不使用module，那Component也就没有(module=...)，但必须在User类构造器上添加@Inject 以提供依赖）
@Singleton  
@Component(modules = UserModule.class)  
public interface ActivityComponent {  
    void inject(MainActivity activity);  
}  
 
然后MainActivity中初始化的代码也要稍微修改一下下：
DaggerActivityComponent.builder().userModule(new UserModule()).build().inject(this);  
 
——————————————————————————————————————————————————————————————
自定义作用域：
@Scope  
@Retention(RetentionPolicy.RUNTIME)  
public @interface UserScope {  
}  
 
然后在我们的UserModule和ActivityComponent中应用该局部作用域：
@Module  
public class UserModule {  
    @Provides  
    @UserScope  
    User providesUser() {  
        return new User();  
    }  
}  
 
@UserScope  
@Component(modules = UserModule.class)  
public interface ActivityComponent {  
    void inject(MainActivity activity);  
  
    void inject(BActivity activity);  
}  
最后，如果想要这个作用域产生单例效果，需要我们在自定义的Appliation类中来初始化这个Component（304train在activity中提供初始化）
public class MyApplication extends Application {  
    ActivityComponent activityComponent;  
    @Override  
    public void onCreate() {  
        super.onCreate();  
        activityComponent = DaggerActivityComponent.builder().userModule(new UserModule()).build();  
    }  
  
    ActivityComponent getActivityComponent(){  
        return activityComponent;  
    }  
}  
 
MainActivity和BActivity中注入依赖，MainActivity如下：
    @Inject  
    User user;  
    @Inject  
    User user2;  
    private TextView tv;  
    private TextView tv2;  
  
    @Override  
    protected void onCreate(Bundle savedInstanceState) {  
        super.onCreate(savedInstanceState);  
        setContentView(R.layout.activity_main);  
//        DaggerActivityComponent.builder().userModule(new UserModule()).build().inject(this);  
        ((MyApp) getApplication()).getActivityComponent().inject(this); 
 
BActivity如下：
@Inject  
User user;  
  
@Override  
protected void onCreate(Bundle savedInstanceState) {  
    super.onCreate(savedInstanceState);  
    setContentView(R.layout.activity_b);  
    ((MyApp) getApplication()).getActivityComponent().inject(this);   
 
 
其他地方引用新的User
Module和Component省略（不添加@Sington和自定义作用域）
 
@Inject  
User user;  
@Override  
protected void onCreate(Bundle savedInstanceState) {  
    super.onCreate(savedInstanceState);  
    setContentView(R.layout.activity_c);  
    DaggerCActivityComponent.builder().cUserModule(new CUserModule()).build().inject(this);          //这里引用的是一个新的实例
    TextView tv = (TextView) findViewById(R.id.tv);  
    tv.setText(user.toString());  
}  
————————————————————————————————————————————————————————————————————
304train对自定义作用域的初始化：
    /**
     * 注入Injector
     */
    public abstract void initInjector();
 
    public ActivityComponent getComponent() {
        return DaggerActivityComponent.builder()
                .activityModule(getActivityModule())        //这里的.activityModule和.userModule都是系统会自动生成，
                .applicationComponent(getApplicationComponent())
                .build();
    }
 
    protected ApplicationComponent getApplicationComponent() {
        return ((MyApplication) getApplication()).getApplicationComponent();
    }
 
在实现的activity中会调用initInjector
    @Override
    public void initInjector() {
 
        getComponent().inject(this);
    }
 
 
 
 
