ProGuard简介
 
因为Java代码是非常容易反编码的，况且Android开发的应用程序是用Java代码写的，为了很好的保护Java源代码，我们需要对编译好后的class文件进行混淆。
ProGuard是一个混淆代码的开源项目，它的主要作用是混淆代码，殊不知ProGuard还包括以下4个功能。
压缩(Shrink)：检测并移除代码中无用的类、字段、方法和特性（Attribute）。
优化(Optimize)：对字节码进行优化，移除无用的指令。
混淆(Obfuscate)：使用a，b，c，d这样简短而无意义的名称，对类、字段和方法进行重命名。
预检(Preveirfy)：在Java平台上对处理后的代码进行预检，确保加载的class文件是可执行的。
 
 
如何编写一个ProGuard文件
 
有个三步走的过程：
基本混淆
针对APP的量身定制
针对第三方jar包的解决方案
 
————————————————————————————————————————————————————————————————————
基本混淆
 
混淆文件的基本配置信息，任何APP都要使用，可以作为模板使用，具体如下。
1，基本指令
复制代码
# 代码混淆压缩比，在0和7之间，默认为5，一般不需要改
-optimizationpasses 5
 
# 混淆时不使用大小写混合，混淆后的类名为小写
-dontusemixedcaseclassnames
 
# 指定不去忽略非公共的库的类
-dontskipnonpubliclibraryclasses
 
# 指定不去忽略非公共的库的类的成员
-dontskipnonpubliclibraryclassmembers
 
# 不做预校验，preverify是proguard的4个步骤之一
# Android不需要preverify，去掉这一步可加快混淆速度
-dontpreverify
 
# 有了verbose这句话，混淆后就会生成映射文件
# 包含有类名->混淆后类名的映射关系
# 然后使用printmapping指定映射文件的名称
-verbose
-printmapping proguardMapping.txt
 
# 指定混淆时采用的算法，后面的参数是一个过滤器
# 这个过滤器是谷歌推荐的算法，一般不改变
-optimizations !code/simplification/arithmetic,!field/*,!class/merging/*
 
# 保护代码中的Annotation不被混淆，这在JSON实体映射时非常重要，比如fastJson
-keepattributes *Annotation*
 
# 避免混淆泛型，这在JSON实体映射时非常重要，比如fastJson
-keepattributes Signature
 
//抛出异常时保留代码行号，在异常分析中可以方便定位
-keepattributes SourceFile,LineNumberTable
 
-dontskipnonpubliclibraryclasses用于告诉ProGuard，不要跳过对非公开类的处理。默认情况下是跳过的，因为程序中不会引用它们，有些情况下人们编写的代码与类库中的类在同一个包下，并且对包中内容加以引用，此时需要加入此条声明。
 
-dontusemixedcaseclassnames，这个是给Microsoft Windows用户的，因为ProGuard假定使用的操作系统是能区分两个只是大小写不同的文件名，但是Microsoft Windows不是这样的操作系统，所以必须为ProGuard指定-dontusemixedcaseclassnames选项
 
2，需要保留的东西
 
# 保留所有的本地native方法不被混淆
-keepclasseswithmembernames class * {
    native <methods>;
}
 
# 保留了继承自Activity、Application这些类的子类
# 因为这些子类，都有可能被外部调用
# 比如说，第一行就保证了所有Activity的子类不要被混淆
-keep public class * extends android.app.Activity
-keep public class * extends android.app.Application
-keep public class * extends android.app.Service
-keep public class * extends android.content.BroadcastReceiver
-keep public class * extends android.content.ContentProvider
-keep public class * extends android.app.backup.BackupAgentHelper
-keep public class * extends android.preference.Preference
-keep public class * extends android.view.View
-keep public class com.android.vending.licensing.ILicensingService
 
# 如果有引用android-support-v4.jar包，可以添加下面这行
-keep public class com.xxxx.app.ui.fragment.** {*;}
 
# 保留在Activity中的方法参数是view的方法，
# 从而我们在layout里面编写onClick就不会被影响
-keepclassmembers class * extends android.app.Activity {
    public void *(android.view.View);
}
 
# 枚举类不能被混淆
-keepclassmembers enum * {
public static **[] values();
public static ** valueOf(java.lang.String);
}
 
# 保留自定义控件（继承自View）不被混淆
-keep public class * extends android.view.View {
    *** get*();
    void set*(***);
    public <init>(android.content.Context);
    public <init>(android.content.Context, android.util.AttributeSet);
    public <init>(android.content.Context, android.util.AttributeSet, int);
}
 
# 保留Parcelable序列化的类不被混淆
-keep class * implements android.os.Parcelable {
    public static final android.os.Parcelable$Creator *;
}
 
# 保留Serializable序列化的类不被混淆
-keepclassmembers class * implements java.io.Serializable {
    static final long serialVersionUID;
    private static final java.io.ObjectStreamField[] serialPersistentFields;
    private void writeObject(java.io.ObjectOutputStream);
    private void readObject(java.io.ObjectInputStream);
    java.lang.Object writeReplace();
    java.lang.Object readResolve();
}
 
# 对于R（资源）下的所有类及其方法，都不能被混淆
-keep class **.R$* {
    *;
}
 
# 对于带有回调函数onXXEvent的，不能被混淆
-keepclassmembers class * {
    void *(**On*Event);
}
—————————————————————————————————————————————————————————————————————————
针对APP的量身定制
 
1,保留实体类和成员被混淆
对于实体，保留它们的set和get方法，对于boolean型get方法，有人喜欢命名isXXX的方式，所以不要遗漏。如下：
# 保留实体类和成员不被混淆
-keep public class com.xxxx.entity.** {
    public void set*(***);
    public *** get*();
    public *** is*();
}
 一种好的做法是把所有实体都放在一个包下进行管理，这样只写一次混淆就够了，避免以后在别的包中新增的实体而忘记保留，代码在混淆后因为找不到相应的实体类而崩溃。
 
 
 
2，内嵌类
 
内嵌类经常会被混淆，结果在调用的时候为空就崩溃了，最好的解决方法就是把这个内嵌类拿出来，单独成为一个类。如果一定要内置，那么这个类就必须在混淆的时候保留，比如如下：
 
# 保留内嵌类不被混淆
-keep class com.example.xxx.MainActivity$* { *; }
这个$符号就是用来分割内嵌类与其母体的标志。
 
 
 
3，对WebView的处理
 
复制代码
# 对WebView的处理
-keepclassmembers class * extends android.webkit.webViewClient {
    public void *(android.webkit.WebView, java.lang.String, android.graphics.Bitmap);
    public boolean *(android.webkit.WebView, java.lang.String)
}
-keepclassmembers class * extends android.webkit.webViewClient {
    public void *(android.webkit.webView, java.lang.String)
}
复制代码
 
 
4，对JavaScript的处理
 
# 保留JS方法不被混淆
-keepclassmembers class com.example.xxx.MainActivity$JSInterface1 {
    <methods>;
}
 
——————————————————————————————————————————————————————————————————
针对第三方jar包的解决方案
 
我们在Android项目中不可避免要使用很多第三方提供的SDK，一般而言，这些SDK是经过ProGuard混淆的，而我们所需要做的就是避免这些SDK的类和方法在我们APP被混淆。
1，针对android-support-v4.jar的解决方案
复制代码
# 针对android-support-v4.jar的解决方案
-libraryjars libs/android-support-v4.jar
-dontwarn android.support.v4.**
-keep class android.support.v4.**  { *; }
-keep interface android.support.v4.app.** { *; }
-keep public class * extends android.support.v4.**
-keep public class * extends android.app.Fragment
复制代码
 2，其他的第三方jar包的解决方案
 
这个就取决于第三方包的混淆策略了，一般都有在各自的SDK中有关于混淆的说明文字，比如支付宝如下：
 
# 对alipay的混淆处理
-libraryjars libs/alipaysdk.jar
-dontwarn com.alipay.android.app.**
-keep public class com.alipay.**  { *; }
值得注意的是，不是每个第三方SDK都需要-dontwarn 指令，这取决于混淆时第三方SDK是否出现警告，需要的时候再加上。
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
 
