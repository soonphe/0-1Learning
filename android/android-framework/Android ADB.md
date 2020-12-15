# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Andorid ADB

ADB是Android SDK的一种功能多样的命令行工具,可让您与设备进行通信，用它可以管理android 模拟器或者真实安卓设备，你是想获取你的设备序列号和连接状态，
adb 命令可用于执行各种设备操作（例如安装和调试应用），并提供对 Unix shell（可用来在设备上运行各种命令）的访问权限。它是一种客户端-服务器程序，包括以下三个组件：

* 客户端：用于发送命令。客户端在开发计算机上运行。您可以通过发出 adb 命令来从命令行终端调用客户端。
* 守护进程 (adbd)：在设备上运行命令。守护进程在每个设备上作为后台进程运行。
* 服务器：管理客户端和守护进程之间的通信。服务器在开发机器上作为后台进程运行。

adb 包含在 Android SDK 平台工具软件包中。您可以使用 SDK 管理器下载此软件包，该管理器会将其安装在 android_sdk/platform-tools/ 下。


### adb命令部分：
~~~~
以root权限重新启动adb的守护进程：adb root
查看目录下的文件和文件夹：adb ls system/app
查看连接计算机的设备：adb devices
重启机器：adb reboot
查看log：adb logcat
终止adb服务进程：adb kill-server
重启adb服务进程：adb start-server 
将system分区重新挂载为可读写分区：adb remount
查看SD卡文件目录 adb ls storage/emulated/0
从本地复制文件到设备：adb push <local> <remote> 
设备文件发送到电脑：adb pull storage/emulated/0/BD_.txt D:Logs
移动文件：adb shell mv path/file newpath/file
新建文件夹：adb shell mkdir path/foldelname
查看文件内容：adb shell cat <file> 
进入文件夹，等同于dos中的cd 命令：adb shell cd <folder> 
重命名文件：adb shell rename path/oldfilename path/newfilename 
删除system/avi.apk：adb shell rm /system/avi.apk
删除文件夹及其下面所有文件：db shell rm -r <folder> 
su root	切换到root用户
su shell切换到普通用户

推送资源：
adb -s BaytrailDB47EC8B push D:\JuzhongWork\Download /storage/sdcard0/
adb -s 0123456789ABCDEF push D:\JuzhongWork\Download /storage/sdcard0/

安装APK：
adb install <apkfile> //比如：adb install baidu.apk
保留数据和缓存文件，重新安装apk：
adb install -r <apkfile> //比如：adb install -r baidu.apk
安装apk到sd卡：
adb install -s <apkfile> // 比如：adb install -s baidu.apk
卸载APK：
adb uninstall <package> //比如：adb uninstall com.baidu.search
卸载app但保留数据和缓存文件：
adb uninstall -k <package> //比如：adb uninstall -k com.baidu.search


adb查找app：
方法一：将获取手机内所有apk对应的包名和路径
adb shell pm list package -f
adb shell pm list packages >alllist.txt  //生成本地文件
方法二：打开APP再输命令(查找当前打开的APP全限定名)
adb shell dumpsys window w | findstr \/ | findstr name=
adb shell dumpsys activity | grep "activity"

adb卸载app：
输入adb shell pm uninstall --user 0 加软件包的名字回车就行能卸载的应用自己搜百度 


adb获取屏幕分辨率有两种方法
adb shell dumpsys window displays
adb shell wm size


adb获取系统版本
获取系统版本:adb shell getprop ro.build.version.release
获取系统api版本:adb shell getprop ro.build.version.sdk

~~~~

### ADB命令失败
问题原因：
用户如果在安装adb驱动时提示“系统找不到指定文件”，那么实际原因是系统在安装adb驱动的时候需要安装系统自带的winusb驱动。
而winusb所需的winusb.sys文件是依靠inf文件的“windows cd”字段来复制文件的。
有时间系统会不知道“windows cd”的位置就造成在安装过程中缺少winusb.sys文件而安装中止。


解决方法：
1、找到winusb的来源路径“c:\windows\system32\DriverStore\FileRepository\winusb.inf_x86_neutral_6cb50ae9f480775b\”目录下；
2、然后把把Winusb.sys文件复制到“c:\windows\inf”目录下即可；
3、进行重新安装
