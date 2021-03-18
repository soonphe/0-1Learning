# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## dependencyManagement和dependencies

### dependencyManagement：
为了项目的正确运行，必须让所有的子模块使用依赖项的统一版本，必须确保应用的各个项目的依赖项和版本一致，才能保证测试的和发布的是相同的结果。
在我们项目顶层的pom文件中，我们会看到dependencyManagement元素。
通过它元素来管理jar包的版本，让子项目中引用一个依赖而不用显示的列出版本号。
Maven会沿着父子层次向上走，直到找到一个拥有dependencyManagement元素的项目，然后它就会使用在这个dependencyManagement元素中指定的版本号。


### dependencies：
即使在子模块中不写该依赖项，那么子模块仍然会从父项目中继承该依赖项（全部继承）。


### scope参数
<scope>provided</scope> ：为这个provided是目标容器已经provide这个artifact，它只影响到编译，测试阶段
<scope>compile</scope> （默认）：也就是说这个项目在编译，测试，运行阶段都需要这个artifact对应的jar包在classpath中


### 聚合和继承父POM
聚合 VS 继承父POM
虽然聚合通常伴随着父POM的继承关系，但是这两者不是必须同时存在的。

继承父POM是为了抽取统一的配置信息和依赖版本控制，方便子POM直接引用，简化子POM的配置。

聚合（多模块）则是为了方便一组项目进行统一的操作而作为一个大的整体

所以要真正根据这两者不同的作用来使用，不必为了聚合而继承同一个父POM，也不比为了继承父POM而设计成多模块。



