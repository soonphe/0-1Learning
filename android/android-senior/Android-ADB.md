# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android ADB
ADB是Android SDK的一种功能多样的命令行工具,可让您与设备进行通信，用它可以管理android 模拟器或者真实安卓设备，你是想获取你的设备序列号和连接状态，
adb 命令可用于执行各种设备操作（例如安装和调试应用），并提供对 Unix shell（可用来在设备上运行各种命令）的访问权限。它是一种客户端-服务器程序，包括以下三个组件：

* 客户端：用于发送命令。客户端在开发计算机上运行。您可以通过发出 adb 命令来从命令行终端调用客户端。
* 守护进程 (adbd)：在设备上运行命令。守护进程在每个设备上作为后台进程运行。
* 服务器：管理客户端和守护进程之间的通信。服务器在开发机器上作为后台进程运行。

adb 包含在 Android SDK 平台工具软件包中。您可以使用 SDK 管理器下载此软件包，该管理器会将其安装在 android_sdk/platform-tools/ 下。

### adb命令部分：
~~~~
以root权限重新启动adb的守护进程：adb root
重新挂载：adb remount (可用于清除system目录文件)
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

远程调试：
adb devices
adb tcpip 5555（端口号，可以指定其他值），该命令将会重启手机上的adbd，开启网络调试功能：
adb connect 192.168.1.137:5555，如果需要手机确认就点击确认，提示connected即为连接成功
adb disconnect：断开与Android设备的连接时
如果你想断开特定设备的连接，可以指定设备的IP地址或者端口：adb disconnect <IP地址>:<端口>，示例：adb disconnect 192.168.1.10:5555

推送资源：
adb -s BaytrailDB47EC8B push D:\xxx\Download /storage/sdcard0/
adb -s 0123456789ABCDEF push D:\xxx\Download /storage/sdcard0/

安装APK：
adb install <apkfile> //比如：adb install baidu.apk
保留数据和缓存文件，重新安装apk：
adb install -r <apkfile> //比如：adb install -r baidu.apk
安装测试APK
adb install -t xxxx.apk
安装apk到sd卡：
adb install -s <apkfile> // 比如：adb install -s baidu.apk
卸载APK：
adb uninstall <package> //比如：adb uninstall com.baidu.search
卸载app但保留数据和缓存文件：
adb uninstall -k <package> //比如：adb uninstall -k com.baidu.search
adb卸载预置app：
输入adb shell pm uninstall --user 0 包名 
-k 卸载应用且保留数据与缓存，如果不加 -k 则全部删除。
--user 指定用户 id，Android 系统支持多个用户，默认用户只有一个，id=0。

adb查找app：
pm path 包名  （获取特定应用程序包的路径。这个命令非常有用，尤其是在需要定位应用程序的具体存储位置时）

方法一：将获取手机内所有apk对应的包名和路径
adb shell pm list package -f
adb shell pm list packages >alllist.txt  //生成本地文件
方法二：打开APP再输命令(查找当前打开的APP全限定名)
adb shell dumpsys window w | findstr \/ | findstr name=
adb shell dumpsys activity | grep "activity"


adb获取屏幕分辨率有两种方法
adb shell dumpsys window displays
adb shell wm size


adb获取系统版本
获取系统版本:adb shell getprop ro.build.version.release
获取系统api版本:adb shell getprop ro.build.version.sdk

进入shell：adb shell
强制停止APP:adb shell am force-stop <package-name>
进入shell并输入ps指令：adb shell ps
进入shell并查询指定pid详细信息：adb shell cat /proc/<pid>/maps

adb查询运行内存和存储
adb shell cat /proc/meminfo   		查询运行内存（RAM）使用情况：
重点关注：
    - MemTotal：总可用RAM
    - MemFree: 空闲内存数量         
    - MemAvailable: 可用内存数量（应用程序可用内存数。系统中有些内存虽然已被使用但是可以回收的，比如cache/buffer、slab都有一部分可以回收，所以MemFree不能代表全部可用的内存，这部分可回收的内存加上MemFree才是系统可用的内存，即：MemAvailable≈MemFree+Buffers+Cached，它是内核使用特定的算法计算出来的，是一个估计值。它与MemFree的关键区别点在于，MemFree是说的系统层面，MemAvailable是说的应用程序层面。）
    - VmallocTotal：虚拟内存总量
adb shell df  		查询存储空间（内部和外部存储）使用情况：
adb shell cat /proc/cpuinfo  ADB查看CPU

ADB发送广播
adb shell am broadcast -a com.example.ACTION --es extra_key extra_string_value
* -a <action>：指定广播的action名称
* --es <extra_key> <extra_value>：可选参数，用于传递额外的数据
~~~~

adb shell dumpsys meminfo -a 包名，查看当前应用所占用内存：
```
Uptime：表示启动到现在的时长，不包含休眠的时间，单位毫秒(ms)
Realtime：表示启动到现在的时长，包含休眠的时间，单位毫秒(ms)
Native Heap: 进程<程序>本身使用的内存
Dalvik Heap : 虚拟机VM使用的内存
Dalvik Other : 虚拟机VM之外的内存（比如Java的GC内存）
Stack：应用中的原生堆栈和 Java 堆栈使用的内存
Pss Total: 应用程序真实占用了物理内存的空间
Heap Alloc : 程序虚拟已使用的内存
Heap Size：程序堆的总内存
Heap Free : 空闲的内存
private dirty : 私用共享内存
Private（Clean和Dirty的）：应用进程单独使用的内存，代表着系统杀死你的进程后可以实际回收的内存总量。通常需要特别关注其中更为昂贵的dirty部分，它不仅只被你的进程使用而且会持续占用内存而不能被从内存中置换出存储。申请的全部Dalvik和本地heap内存都是Dirty的，和Zygote共享的Dalvik和本地heap内存也都是Dirty的。
Dalvik Heap：Dalvik虚拟机使用的内存，包含dalvik-heap和dalvik-zygote，堆内存，所有的Java对象实例都放在这里。
Heap Alloc：累加了Dalvik和Native的heap。
PSS：这是加入与其他进程共享的分页内存后你的应用占用的内存量，你的进程单独使用的全部内存也会加入这个值里，多进程共享的内存按照共享比例添加到PSS值中。如一个内存分页被两个进程共享，每个进程的PSS值会包括此内存分页大小的一半在内。
Dalvik Pss内存 = 私有内存Private Dirty + （共享内存Shared Dirty / 共享进程数）
TOTAL：上面全部条目的累加值，全局的展示了你的进程占用的内存情况。
ViewRootImpl：应用进程里的活动窗口视图个数，可以用来监测对话框或者其他窗口的内存泄露。
AppContexts及Activities：应用进程里Context和Activity的对象个数，可以用来监测Activity的内存泄露。
```

adb shell df：查看当前设备存储
```
Filesystem            1K-blocks    Used Available Use% Mounted on
tmpfs                    712256  259312    452944  37% /dev
/dev/block/mmcblk0p29   1999300 1114992    867924  57% /system
/dev/block/mmcblk0p31    292784  188444     98296  66% /vendor
tmpfs                    712256       0    712256   0% /mnt
/dev/block/mmcblk0p30    132384     504    127272   1% /cache
/dev/block/mmcblk0p1        928     140       688  17% /productinfo
/dev/block/mmcblk0p2        928      52       776   7% /qcdata
/dev/block/dm-0        27456476 2382112  24869564   9% /data
/data/media            27456476 2382112  24869564   9% /storage/emulated
```

### ADB命令失败
问题原因：
用户如果在安装adb驱动时提示“系统找不到指定文件”，那么实际原因是系统在安装adb驱动的时候需要安装系统自带的winusb驱动。
而winusb所需的winusb.sys文件是依靠inf文件的“windows cd”字段来复制文件的。
有时间系统会不知道“windows cd”的位置就造成在安装过程中缺少winusb.sys文件而安装中止。


解决方法：
1、找到winusb的来源路径“c:\windows\system32\DriverStore\FileRepository\winusb.inf_x86_neutral_6cb50ae9f480775b\”目录下；
2、然后把把Winusb.sys文件复制到“c:\windows\inf”目录下即可；
3、进行重新安装
