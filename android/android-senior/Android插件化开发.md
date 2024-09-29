# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android插件化开发
为什么引入动态加载技术？ 各大厂商都碰到了AndroidNative平台的瓶颈：
* 一个应用程序dex文件的方法数最大不能超过65536个，从技术上讲，业务逻辑的复杂代码急剧膨胀，各大厂商陆续触到65535方法数的天花板；同时，对模块热更新提出了更高的要求。
* 可以让应用程序实现插件化、插拔式结构，对后期维护有益，在业务层面上，功能模块的解耦以及维护团队的分离也是大势所趋。

插件化技术主要解决两个问题：
1. 代码加载：类的加载可以使用Java的ClassLoader机制，还需要组件生命周期管理。
2. 资源加载：用AssetManager的隐藏方法addAssetPath。

Android简单来说就是如下操作：
* 开发者将插件代码封装成Jar或者APK
* 宿主下载或者从本地加载Jar或者APK到宿主中
* 将宿主调用插件中的算法或者Android特定的Class（如Activity）

什么是动态加载技术
- 动态加载技术就是使用类加载器加载相应的apk、dex、jar(必须含有dex文件)，再通过反射获得该apk、dex、jar内部的资源（class、图片、color等等）进而供宿主app使用。
- Android使用Dalvik虚拟机加载可执行程序，所以不能直接加载基于class的jar，而是需要将class转化为dex字节码。

关于动态加载使用的类加载器
* PathClassLoader - 只能加载已经安装的apk，即/data/app目录下的apk。
* DexClassLoader  - 能加载手机中未安装的apk、jar、dex，只要能在找到对应的路径。且支持从SD卡加载

#### Android插件化原理解析——Hook机制之动态代理 
使用代理机制进行API Hook进而达到方法增强。

代理Hook ：如果我们自己创建代理对象，然后把原始对象替换为我们的代理对象，就可以在这个代理对象中为所欲为了；修改参数，替换返回值，称之为Hook。

整个Hook过程简要总结如下：
1. 寻找Hook点，原则是静态变量或者单例对象，尽量Hook public的对象和方法，非public不保证每个版本都一样，需要适配。
2. 选择合适的代理方式，如果是接口可以用动态代理；如果是类可以手动写代理也可以使用cglib。
3. 偷梁换柱－用代理对象替换原始对象