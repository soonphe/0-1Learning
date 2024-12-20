## MVC,MVP,MVVM

### MVC
软件可以分为三部分

* 视图（View）:用户界面
* 控制器（Controller）:业务逻辑
* 模型（Model）:数据保存

各部分之间的通信方式如下：

1. View传送指令到Controller
2. Controller完成业务逻辑后，要求Model改变状态
3. Model将新的数据发送到View，用户得到反馈

Tips：所有的通信都是单向的。

互动模式: 接受用户指令时，MVC可以分为两种方式。
- 一种是通过View接受指令，传递给Controller。
- 另一种是直接通过Controller接受指令


### MVP
MVP模式将Controller改名为Presenter，同时改变了通信方向。

1. 各部分之间的通信，都是双向的
2. View和Model不发生联系，都通过Presenter传递
3. View非常薄，不部署任何业务逻辑，称为"被动视图"(Passive View)，即没有任何主动性，而Presenter非常厚，所有逻辑都部署在那里。

#### 为什么需要MVP
1. 尽量简单
	大部分的安卓应用只使用View-Model结构,程序员现在更多的是和复杂的View打交道而不是解决业务逻辑。当你在应用中只使用Model-View时，到最后，你会发现“所有的事物都被连接到一起”。复杂的任务被分成细小的任务，并且很容易解决。越小的东西，bug越少，越容易debug，更好测试。在MVP模式下的View层将会变得简单，所以即便是他请求数据的时候也不需要回调函数。View逻辑变成十分直接。
2. 后台任务
	当你编写一个Actviity、Fragment、自定义View的时候，你会把所有的和后台任务相关的方法写在一个静态类或者外部类中。这样，你的Task不再和Activity联系在一起，这既不会导致内存泄露，也不依赖于Activity的重建。
	
#### MVP优缺点
优点：
1. 降低耦合度，实现了Model和View真正的完全分离，可以修改View而不影响Modle
2. 模块职责划分明显，层次清晰
3. 隐藏数据
4. Presenter可以复用，一个Presenter可以用于多个View，而不需要更改Presenter的逻辑（当然是在View的改动不影响业务逻辑的前提下）
5. 利于测试驱动开发。以前的Android开发是难以进行单元测试的（虽然很多Android开发者都没有写过测试用例，但是随着项目变得越来越复杂，没有测试是很难保证软件质量的；而且近几年来Android上的测试框架已经有了长足的发展——开始写测试用例吧），在使用MVP的项目中Presenter对View是通过接口进行，在对Presenter进行不依赖UI环境的单元测试的时候。可以通过Mock一个View对象，这个对象只需要实现了View的接口即可。然后依赖注入到Presenter中，单元测试的时候就可以完整的测试Presenter应用逻辑的正确性。
6. View可以进行组件化。在MVP当中，View不依赖Model。这样就可以让View从特定的业务场景中脱离出来，可以说View可以做到对业务完全无知。它只需要提供一系列接口提供给上层操作。这样就可以做到高度可复用的View组件。
7. 代码灵活性

缺点：
1. Presenter中除了应用逻辑以外，还有大量的View->Model，Model->View的手动同步逻辑，造成Presenter比较笨重，维护起来会比较困难。
2. 由于对视图的渲染放在了Presenter中，所以视图和Presenter的交互会过于频繁。
3. 如果Presenter过多地渲染了视图，往往会使得它与特定的视图的联系过于紧密。一旦视图需要变更，那么Presenter也需要变更了。
4. 额外的代码复杂度及学习成本。

#### 在MVP模式里通常包含4个要素:

1. View :负责绘制UI元素、与用户进行交互(在Android中体现为Activity);
2. View interface :需要View实现的接口，View通过View interface与Presenter进行交互，降低耦合，方便进行单元测试;
3. Model :负责存储、检索、操纵数据(有时也实现一个Model interface用来降低耦合);
4. Presenter :作为View与Model交互的中间纽带，处理与用户交互的负责逻辑。


### MVVM

MVVM模式将Presenter改名为ViewModel，基本上与MVP模式完全一致。

唯一的区别是，它采用双向绑定(data-binding)：View的变动，自动反映在ViewModel，反之亦然。

### mvp和mvvm的区别
mvp：activity持有presenter对象，presenter被初始化attachView(this)相互持有，
presenter持有view接口，activity实现view接口，presenter调用api，调用接口中的方法更新view
mvvm：activity持有viewmodel对象，vm被初始化new MainModel(MainActivity.this, this);
viewmodel持有view接口，activity实现view接口，viewmodel调用api，调用接口中的方法更新view


