# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android逆向
一、技能要求
• 编程语言掌握： 精通Java和C/C++编程语言，具备良好的代码阅读和编写能力。熟悉Smali/Dalvik汇编语言，能够理解和分析Android应用的中间代码。熟悉JNI的使用。
• 系统架构理解： 深入理解Android操作系统架构，包括应用层、框架层、运行时和库层、硬件抽象层和内核层。
• 加解密知识： 掌握常见的加解密算法，能够分析和破解应用中的加密通讯。
• 网络协议分析：理解HTTP、HTTPS等网络协议，能够分析应用的网络通讯。
• 动态调试技术：熟练使用GDB、LLDB、JDB等调试工具，跟踪应用的运行时行为。
• 静态代码分析：能够阅读和理解源代码或反编译代码，进行静态分析。
• 加壳与脱壳技术：识别应用是否被加壳，并掌握脱壳技术。
• 文件格式理解： 理解APK、DEX、OAT、ELF等文件格式，能够进行文件分析。

二、工具使用能力：
• APKTool：熟练使用APKTool进行APK文件的反编译和重组。
• JEB Decompiler：熟练使用JEB Decompiler进行静态分析和动态调试。
• IDA Pro：熟练使用IDA Pro进行逆向工程分析。
• Smali/Baksmali：熟练使用Smali/Baksmali编辑和生成Smali代码。
• JD-GUI：熟练使用JD-GUI查看和分析Java字节码。
• Dex2Jar：熟练使用Dex2Jar将DEX文件转换为JAR文件。
• Androguard：熟练使用Androguard进行Android应用的分析和修改。 一个是java层逆向工具:jadx，一个是so层工具ida，这两个比较通用
• Xposed Framework和Frida：（必备）熟练使用Xposed Framework和Frida进行动态代码插桩。
• Wireshark和Charles Proxy(抓包工具)：熟练使用Wireshark和Charles Proxy进行网络协议分析。
• Burp Suite：熟练使用Burp Suite分析应用的网络通讯。
• Radare2：熟练使用Radare2进行逆向工程框架分析。


APK反编译
- Apktool(已成功)：解析apk文件，生成.class文件
- dex2jar(已成功)：解析apk文件，生成jar文件(class文件)
- jd-gui(jar包已成功)：解析jar包和class文件，生成java源码，图形化界面操作
- jadx(启动失败)
- enjarify

### APKTool
#### 2进制安装
Download the Mac wrapper script. (Right click, Save Link As apktool)
Download the latest version of Apktool.
Rename the downloaded jar to apktool.jar.
Move both apktool.jar and apktool to /usr/local/bin. (root needed)
Make sure both files are executable. (chmod +x)
Try running apktool via CLI.

#### Brew安装
Install Homebrew as described in this page.
Execute the command brew install apktool in the terminal.
Try running apktool via CLI.

使用命令-
- apktool d test.apk    Adds debuggable="true" to AndroidManifest.xml.
- apktool b test.apk    Prevents baksmali from writing out debug info. (.local, .param, .line, etc.).

```
$ apktool d test.apk
I: Using Apktool 2.10.0 on test.apk
I: Loading resource table...
I: Decoding AndroidManifest.xml with resources...
I: Loading resource table from file: 1.apk
I: Regular manifest package...
I: Decoding file-resources...
I: Decoding values */* XMLs...
I: Baksmaling classes.dex...
I: Copying assets and libs...
I: Copying unknown files...
I: Copying original files...

$ apktool b test
I: Using Apktool 2.10.0 on test
I: Checking whether sources has changed...
I: Smaling smali folder into classes.dex...
I: Checking whether resources has changed...
I: Building resources...
I: Building apk file...
I: Copying unknown files/dir...
```

### dex2jar
github：https://github.com/pxb1988/dex2jar

使用步骤：
```
In the root directory run: ./gradlew distZip
cd dex-tools/build/distributions
Unzip the file dex-tools-2.1-SNAPSHOT.zip (file size should be ~5 MB)
Run d2j-dex2jar.sh from the unzipped directory
```
Example usage:sh d2j-dex2jar.sh -f ~/path/to/apk_to_decompile.apk

参考命令：./d2j-dex2jar.sh ~/Downloads/android逆向/app-release.apk -o  ~/Downloads/android逆向/app-release-test.jar

### JD-GUI
github：http://java-decompiler.github.io/
下载完毕：jd-gui-osx-1.6.6.tar，双击拖动app,要求jdk1.8

也可以下载：jd-gui-1.6.6.jar，启动命令：java -jar jd-gui-1.6.6.jar，然后选择对应文件分析即可

### jadx
github：https://github.com/skylot/jadx
jadx - command line version
jadx-gui - UI version

安装：brew install jadx

运行图形化界面：java -jar jadx-gui-0.7.1.jar
点击“openfile”，选择将要反编译的文件

使用命令：jadx[-gui] [command] [options] <input files> (.apk, .dex, .jar, .class, .smali, .zip, .aar, .arsc, .aab, .xapk, .jadx.kts)


### android dex反编译
以下是反编译DEX文件的步骤：
* 		下载dex2jar和jd-gui工具。
* 		使用dex2jar将DEX文件转换成JAR文件。
* 		使用jd-gui打开JAR文件以查看和分析Java代码。

示例命令行操作：
- 下载dex2jar (https://github.com/pxb1988/dex2jar/releases)
- 下载jd-gui (http://jd.benow.ca/)

将DEX文件转换成JAR
```
dex2jar.sh -d -f -o output.jar input.dex
```
使用jd-gui打开JAR文件
```
jd-gui output.jar
```

### ida-free下载
IDA全称是交互式反汇编器专业版（Interactive Disassembler Professional），人们其简称为IDA，IDA pro

下载地址：https://out7.hex-rays.com/files/idafree84_mac.app.zip

基础使用：
- New（新建）。选择 New 将启动一个标准的 File Open 对话框来选择将要分析的文件。根 据选择的文件，IDA 会显示另外一个或多个对话框，你可以选择特定的文件分析选项， 然后再加载、分析和显示该文件。
- Go（运行）。Go 按钮终止加载过程，使 IDA 打开一个空白的工作区。这时，如果要打开 一个文件，可以将一个二进制文件直接拖放到 IDA 工作区，或者使用 File 菜单中的某个 选项打开该文件。
- Previous（上一个）。使用 Previous 按钮可以打开其下“最近用过的文件”列表中的一个 文件。“最近用过的文件”列表中包含 IDA 的 Windows 注册表项的 History 子项中的值。

### androguard
安装： pip install androguard

Androguard是一个完整的python工具，用于处理Android文件。
```
DEX，ODEX
APK
Android的二进制xml
Android资源
分解DEX/ODEX字节码
DEX/ODEX文件的基本反编译器
Frida支持轻松进行动态分析
用于保存会话的SQLite数据库
```

使用：androguard analyze app-release.apk
```
>>> filename
app-release.apk
>>> a
<androguard.core.apk.APK object at 0x10dc9fe60>
>>> d
[<androguard.core.dex.DEX object at 0x10dd4f830>, <androguard.core.dex.DEX object at 0x124a4f3e0>]
>>> dx
<analysis.Analysis VMs: 2, Classes: 12874, Methods: 99493, Strings: 85557>

Androguard version 4.1.2 started
In [1]:
```
展示图形化界面： androguard gui -i C:\Users\86178\Downloads\cal.apk


几种功能分别是：
analyze（分析）： 打开一个 IPython Shell，并开始进行逆向工程分析。
apkid（APK 标识）： 提取每个 APK 的包名（packageName）、版本号（versionCode）和版本名称（versionName）信息。
arsc（资源解码）： 解码 resources.arsc 文件，可以直接从给定文件或 APK 中进行解码。
axml（AndroidManifest.xml 解析）： 解析 AndroidManifest.xml 文件，显示应用的清单信息。
cg（调用图创建）： 创建一个调用图（Call Graph）并将其导出为图形格式，用于显示 Android 应用中的函数调用关系。
decompile（反编译）： 反编译 APK 文件，并创建控制流图（Control Flow Graphs）以便于分析应用程序的控制流程。
disassemble（反汇编）： 从给定的地址和指定大小开始，对 Dalvik 代码进行反汇编。
gui（图形用户界面）： 启动 Androguard 的图形用户界面，提供更直观的操作界面。
sign（证书指纹提取）： 提取 APK 内所有证书的指纹信息。

