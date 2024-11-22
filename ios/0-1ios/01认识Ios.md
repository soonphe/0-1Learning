# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## 认识ios

### 苹果公司历程
```
1976年创立
1976年推出AppleI
1977年推出Apple I
1980年推出Apple ll
1983年推出Apple Lisa
1984年推出Macintosh
1991年推出PowerBook，2006年被MacBook系列取代
1993年推Apple Newton掌上电脑
1998年推出iMac
2001年推出iPod
2007年推出iPhone
2010年推出iPad
2015年推出Apple Watch
2016年推出AirPods
2017年推出HomePod
2019年推出Apple Card
2019年推出Apple TV+
```

苹果电脑产品
```
1998年推出iMac
2005年推出Mac mini
2006年推出Mac Pro
2006年推出MacBook
2006年推出MacBook Pro
2008年推出MacBook Air
2015年推出MacBook
2016年推出MacBook Pro
2018年推出Mac mini
2019年推出Mac Pro
2021年推出Mac Studio
```

苹果音乐播放器
```
2001年推出iPod
2004年推出iPod Mini
2005年推出iPod nano、iPod Shuffle
2007年推出iPod Classic、iPod Touch
```

苹果平板电脑
```
2010年推出iPad
2011年推出iPad 2 
2012年推出iPad 3
2013年推出iPad 4
2013年推出iPad mini
2014年推出iPad Air
2014年推出iPad mini 2
2014年推出iPad Air 2
2015年推出iPad mini 3
2015年推出iPad mini 4
2015年推出iPad Pro
2016年推出iPad Pro 9.7
2017年推出iPad 5
2017年推出iPad Pro 10.5
2018年推出iPad 6
2018年推出iPad Pro 11
2019年推出iPad mini 5
2019年推出iPad Air 3
2019年推出iPad 7
2020年推出iPad Pro 12.9
2020年推出iPad Pro 11
2020年推出iPad Air 4
2021年推出iPad Pro 12.9
2021年推出iPad Pro 11
2021年推出iPad 8
2021年推出iPad mini 6
```

苹果电视
```
2006年发布Apple TV
2010年发布Apple TV 2
2012年发布Apple TV 3
2015年发布Apple TV 4
2017年发布Apple TV 4K
2021年发布Apple TV 4K 2
```

**ios是谁创造的：**
斯科特·福斯特尔和他的ios开发团队， 斯科特·福斯特尔是Mac OS X系统以及Aqua用户界面的最初设计者之一。在成功推出OS X Leopard系统后,福斯特尔被乔布斯安排开发iPhone操作系统,iOS是他最大的成就

### 苹果Mac OS X系统
```
2001年从Macintosh电脑上分离出来成为OSX10.0
2001年推出OS X10.1
2002年推出OS X10.2、10.3
2005年推出OS X10.4
2006年推出OS X10.5
2008年推出OSX10.6
2010年推出OS X10.7
2012年推出OSX10.8
2013年推出OS X10.9
2014年推出OS X10.10
```
OS X系统结构
```
C、C++、Objective-C、Swift

        OS X GUI
    
        Unix
```

### 苹果iOS系统
```
2007年发布iPhone Runs Os X
2008年改名为iPhone Os
2010年改名为i0s，发布i0S4(多任务)
2011年发布i0s5
2012年发布i0s6
2013年发布i0S7(扁平化)
2014年发布i0s8
```

iOS 系统结构
```
C、C++、Objective-C、Swift

        iOS  GUI
    
        Unix
```

### 苹果iOS开发软硬件环境要求
硬件环境要求
```
CPU 双核
内存8G
最好选择Macbook Pro，也可选择Macbook Air
测试手机iPhone 5+
```

软件环境要求
```
OS X 10.9.3+
Xcode 6.0+
```

### 开发工具——Xcode
Xcode是苹果公司为开发者提供的一款集成开发环境，用于开发iOS、macOS、watchOS、tvOS等应用程序。

Xcode是一款功能强大的开发工具，集成了代码编辑器、编译器、调试器、性能分析工具等多种功能，是开发者开发苹果平台应用程序的首选工具。

#### Xcode下载安装 
下载：App store 下载安装Xcode

#### Xcode菜单介绍
```
File：文件
Edit：编辑
View：视图
Find：查找
Navigate：导航
Editor：编辑器
Product：产品
Debug：调试
Itegrate：集成，包含Source Control：源代码控制(Git)
Window：窗口
Help：帮助
```

#### Xcode首选项
- 快捷键：command + ,：Xcode首选项
    - 选择General                    //常规设置
        - Appearance：选择编辑器主题，如Dark
        - Issues：选择错误提示,Show Inline显示内联，Show Minimized显示最小化
        - Mac Test Parallelization：选择并行化，分配内存
        - Simulator Test Parallelization：模拟器测试并行化，分配内存
        - LockFile：锁定文件
        - FileExtension：文件扩展名
        - Navigator Size：导航器
        - Bookmarks Navigator Detail：书签
        - Find Navigator Detail：查找
        - Issues Navigator Detail：问题
        - Dialog Warning：警告
    - Account                   //账户相关，可添加账户
        - Apple IDs:添加Apple ID
        - Source Control Accounts:添加git账户
    - Behaviors                 //行为相关，可设置行为
        - Run选中
        - Build选中
        - Test选中
    - Navigation                //导航相关，可设置快捷键
        - Activation：激活
        - Full Screen：全屏
        - Open quickly：快速打开
        - Command-click on Code：设置Command点击快捷键，默认跳转到代码定义
        - Option-click on Code：设置Option点击快捷键，默认查看代码解释
    - Themes                    //主题相关，可设置主题
        - Source Editor：源代码编辑器
        - Console：控制台
    - Text Editing              //文本编辑相关，可设置文本编辑
        - Display：显示，设置展示行号、代码折叠等
        - Editing：编辑，设置代码补全、格式化等
        - Indentation：缩进，默认4个空格
    - Key Bindings              //快捷键相关，可设置快捷键
        - Show Completions选中
        - Suggest completions while typing选中
    - Source Control            //源代码控制相关，可设置源代码控制
        - General：常规
            - Source Control：源代码控制
            - Text Editing：文本编辑
        - Git:设置git，账户、ignore文件等
    - Components                //组件相关，可设置组件
        - Platforms：平台
            - Mac OS
            - iOS
            - watchOS
            - tvOS
            - visionOS
    - Locations                 //位置相关，可设置位置
        - Locations
            - Derived Data：派生数据
            - Archives：存档
            - Command Line Tools：命令行工具

#### Xcode中创建iOS App项目
创建常见选项
- Create a new project  创建一个新项目
- Clone Git Repository  克隆一个仓库
- Open Existing project  打开已创建的项目

**新建一个ios项目**
右上角`File`选择`new project`，选择`ios`,然后选择`App`，点击`Next`，
- Product Name 填写项目名称，
- Team选择`None`，                                   //团队选择,默认None
- Organization Identifier填写`com.example`，         //组织标识符,也就是包名，默认com.example
- Interface选择`Swift`，                             //界面选择,默认Swift
- Language选择`Swift`，                              //语言选择,默认Swift
- Testing System选择`None`，                         //测试选项,默认None
- Storage勾选`Use Core Data`，                       //存储选项,使用Core Data，默认None

点击`Next`，选择`项目存储位置`(推荐创建固定文件夹存储ios相关项目)，点击`Create`，即可创建一个新的项目

#### Xcode创建其他项目
- Xcode中创建OSX命令行控制台项目 创建流程：File->New->Project->macOS->Command Line Tool->Next->填写项目名称->Next->Create
- Xcode中创建OSX窗体程序项目 创建流程：File->New->Project->macOS->App->Next->填写项目名称->Next->Create
- Xcode中创建OSX游戏项目 创建流程：File->New->Project->macOS->Game->Next->填写项目名称->Next->Create
- Xcode中创建iOS Game项目 创建流程：File->New->Project->iOS->Game->Next->填写项目名称->Next->Create
- Xcode中创建iOS Framework&Library项目 创建流程：File->New->Project->iOS->Framework&Library->Next->填写项目名称->Next->Create

#### Xcode ios App项目目录结构
```
MySwiftApp
    - AppDelegate.swift
    - ViewController.swift
    - Main.storyboard                   应用布局
    - Assets.xcassets                   静态文件库  .xcassets静态资源资产，可以配置图片不同尺寸，适配不同设备
    - LaunchScreen.storyboard           应用启动布局
    - Info.plist                        应用配置文件，包含应用的相关配置
  - MySwiftAppTests                     项目测试文件夹
  - MySwiftAppUITests                   项目UI测试文件夹
```

#### Xcode工作空间创建及使用
工作空间是Xcode的一个重要概念，可以将多个项目组合在一起，方便开发者同时管理多个项目。

创建工作空间：New->Workspace->选择存储位置->创建

#### Xcode常用快捷键
- esc：代码提示
- Command + 0：显示/隐藏导航器面板
- Command + Option + 0：显示/隐藏实用工具面板
- Command + 1：快速浏览代码、图片以及用户界面文件。
- Command + 2：查看版本控制导航
- Command + 3：查看书签导航
- Command + 4：查看搜索导航
- Command + 5：查看问题导航
- Command + 6：查看测试导航
- Control + 6：快速查看当前class的方法

- command + N：新建文件
- command + B：编译
- command + R：运行
- command + L：快速跳转到某一行
- command + control + ← , control + command + →  返回至上一次光标位置
- option + click：查看源码解释， `是swift开发的热键`没有之一

- command + control + option+ enter  打开Assitant

- command + option + ←  收起方法代码块
- command + option + →  展开方法代码块
- command + option + [：代码向上移动一行
- command + option + ]：代码向下移动一行
- command + click：查看git与当前差异化

- Command + Shift + F：搜索导航器(Find Navigator，也就是搜索)
- Command + Shift + O：快速打开
- Command + Shift + 0 (Zero)：文档和参考
- Command + Shift + K：清除工程
- 
- AVD快捷回到主界面：command+shift+H

#### Xcode远程调试
流程：数据线连接-iPhone安全与隐私(需要重启) - 添加apple id账户 - iphone VPN和设备 - 信任开发者app

1.用数据线连接IPhone到macOS
2.打开xcode,然后点击Window->Devices and Simulators
3.选中左边的Devices可看到已连接的IPhone,然后点击Connect via network使其选中.
选择后,左边的IPhone设备的右边出现一个地球图标,表示成功通过网络连接到IPhone
然后可断开数据线的连接,至此可以对IPhone设备进行远程调试了
注: iPhone与macOS需要在同一局域网.
4.打开或者新建一个iOS工程,点击调试设备列表,可以看到,iPhone设备带有地球图标符号,表示是通过网络连接的远程设备

#### Xcode项目上传git
1.先在github上新建一个空的项目，不需要ignore和readme文件
2.把项目github仓库地址拷贝后放到xcode > preferences > accounts里面
3.cd进入本地项目，执行以下步骤就可以把项目上传到github上啦
```
git init
git add .
git commit -m "first commit"
git branch -M main
git remote add origin https://github.com/soonphe/MySwiftApp.git
git push -u origin main
```
从github上更新代码：
```
git pull origin master
```
在Xcode中，Integrate菜单，可以操作git相关等操作

#### Xcode断点调试
鼠标点击行号，添加断点，然后运行程序，程序会在断点处停止，可以查看变量的值，查看程序执行的流程。
再次点击为取消断点。
拖动断点可以移动断点位置。
拖到代码内部，可以删除断点

- Step Over：单步执行，不进入函数
- Step Into：单步执行，进入函数
- Step Out：跳出函数

### Xcode定义注释模板
在Xcode中，可以定义注释模板，方便开发者在编写代码时快速添加注释。
1. 打开Xcode，点击Xcode->Preferences
2. 选择Text Editing->Code Snippets
3. 点击左下角的+号，添加一个新的代码片段
4. 在Title中填写注释的标题，如Author
5. 在Summary中填写注释的内容，如作者信息
6. 在Completion中填写注释的模板，如// Author: $name
7. 点击Done保存
8. 在代码中输入Author，按下Tab键，即可快速插入注释

### Xcode插件
Xcode插件是一种可以扩展Xcode功能的工具，可以帮助开发者提高开发效率。
appstore下载插件：Alcatraz、xvim、xcode-color-palette、xcode-snippets、xcode-themes、xcode-templates、xcode-extensions

### Xcode查看系统日志
**使用控制台（Console）**
当APP运行起来后，Xcode会自动打开Debug area区域，在这个区域的底部，你可以找到一系列的选项卡，其中包括了"Console"。这里会显示你的APP运行时输出的所有日志。
你可以在Console中看到由NSLog或者print函数输出的日志信息。
Console提供了搜索功能，你可以轻松地根据关键字查找日志。

**使用设备日志（Device Logs）**
如果你需要查看设备的系统级日志或者之前运行过程中的日志，你可以选择查看设备日志：
在Xcode的菜单中选择Window > Devices and Simulators，或者使用快捷键Shift + Command + 2。
在打开的窗口中选择你的设备，然后点击"View Device Logs"。
这里你可以看到设备上所有的日志，包括APP的崩溃日志等。你可以选择特定的日志文件查看详细内容，且支持根据应用、日期等维度来过滤日志。
点击Open Console按钮。这将打开一个新窗口，显示设备的实时日志。

**使用Xcode Instruments**
Xcode的Instruments工具提供了更为高级的日志分析功能：
你可以通过Product > Profile来启动Instruments。
Instruments提供了多种模板，比如Leaks用于检测内存泄露，Time Profiler用于性能分析等。
每个模板都可以为你提供详细的日志和性能数据，帮助你深入分析APP的行为和性能瓶颈。

### Xcode中Playground
Playground是Xcode中的一个功能，可以用来快速编写代码并查看结果，是一个非常适合学习和测试代码的工具。

创建Playground：File->New->Playground->填写名称->Create

实时预览：在Playground中编写代码时，可以实时查看结果，非常方便。

### iOS开发常用操作及技巧
查看帮助文档Search for Documentation：command + shift + 0
可以看到所有的文档，包括系统的API文档，第三方库的文档等
- API Reference：API参考
- SDK Guides：SDK指南
- Tools Guides：工具指南
- Sample Code：示例代码

Help->Develop Documentation->搜索关键字
Help->Search->搜索关键字
Help->Xcode Help->查看Xcode的帮助文档

### iOS设备与模拟器
Xcode界面中间选择编译旁的设备也可以选择实体机器或模拟器
其他设备：
- 面板：Window->Devices and Simulators 
- 创建模拟器：Xcode->Preferences->Components->点击+号->选择需要的模拟器->点击下载

模拟器快捷键：
- command + shift + H：模拟器回到主界面
- command + 左右方向键：模拟器旋转方向

连接实体机器：
- 用数据线连接实体机器到macOS
- 打开xcode,然后点击Window->Devices and Simulators
- 选中左边的Devices可看到已连接的实体机器
- 点击Connect via network使其选中
- 选择后,左边的实体机器的右边出现一个地球图标,表示成功通过网络连接到实体机器
- 然后可断开数据线的连接,至此可以对实体机器进行远程调试了
- iPhone与macOS需要在同一局域网

### iOS程序打包与发布
1. 归档：Product->Archive->选择对应的项目->点击Archive（等待编译完成 ）
2. 发行：选择对应的项目->点击Distribute App->选择对应的发布方式
   - App Store Connect：发布到App Store
   - Ad Hoc：发布到测试环境
   - Enterprise：发布到企业环境
3. ->Next->选择对应的证书->Next->选择对应的描述文件
4. 等待审核即可


