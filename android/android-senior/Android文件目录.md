# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Android文件目录

Android应用安装涉及到如下几个目录：
 
1. system/app系统自带的应用程序，无法删除。
2. data/app用户程序安装的目录，有删除权限。安装时把apk文件复制到此目录。
3. data/data存放应用程序的数据。
4. data/dalvik-cache将apk中的dex文件安装到dalvik-cache目录下(dex文件是dalvik虚拟机的可执行文件,其大小约为原始apk文件大小的四分之一)。
 
APP安装过程：复制APK安装包到data/app目录下，解压并扫描安装包，把dex文件(Dalvik字节码)保存到dalvik-cache目录，并data/data目录下创建对应的应用数据目录
APP卸载过程：删除安装过程中在上述三个目录下创建的文件及目录。

手机存储：
- 内部存储
- 外部存储
 
### 内部存储 
内部存储：没有root的手机不能打开此文件夹
```
主要方法        路径
Environment.getDataDirectory()        /data
Environment.getDownloadCacheDirectory()        /cache
Environment.getRootDirectory()        /system
```
常见的系统app和用户app安装路径：
```
系统app路径：/system/app/xxx/xxx.apk
用户安装路径：/data/app/com.xx.xx/xxx.apk
```
例：user/0 对应手机多用户，实际路径还是/data/data/com.xxx.xxx/files
```
getContext().getFilesDir().getPath()        /data/user/0/cn.connxun.train/files
getContext().getFilesDir().getAbsolutePath()        /data/user/0/cn.connxun.train/files
```
注：有包名的路径我们都是调用Context中的方法来获得，没有包名的路径，我们直接调用Environment中的方法获得。
 
### 外部存储
SD卡根目录：
> Environment.getExternalStorageDirectory().getPath();  /storage/emulated/0

常见的SD卡访问目录
```
方法        路径
Environment.getExternalStoragePublicDirectory(DIRECTORY_ALARMS)        /storage/sdcard0/Alarms
Environment.getExternalStoragePublicDirectory(DIRECTORY_DCIM)        /storage/sdcard0/DCIM
Environment.getExternalStoragePublicDirectory(DIRECTORY_DOWNLOADS)        /storage/sdcard0/Download
Environment.getExternalStoragePublicDirectory(DIRECTORY_MOVIES)        /storage/sdcard0/Movies
Environment.getExternalStoragePublicDirectory(DIRECTORY_MUSIC)        /storage/sdcard0/Music
Environment.getExternalStoragePublicDirectory(DIRECTORY_NOTIFICATIONS)        /storage/sdcard0/Notifications
Environment.getExternalStoragePublicDirectory(DIRECTORY_PICTURES)        /storage/sdcard0/Pictures
Environment.getExternalStoragePublicDirectory(DIRECTORY_PODCASTS)        /storage/sdcard0/Podcasts
Environment.getExternalStoragePublicDirectory(DIRECTORY_RINGTONES)        /storage/sdcard0/Ringtones
```
 
**私有目录：**
在外部存储的App的包名下，如：/storage/emulated/0/Android/data/cwj.test(包名)/files/test
```
getContext().getExternalFilesDir()        /storage/emulated/0/Android/data/cwj.test(包名)/files/test
getContext().getExternalCacheDir        /storage/emulated/0/Android/data/cwj.test(包名)/cache/test
```

#### 文件存入被其他APP不能读取实现：
1. 创建隐藏文件夹
.XX文件：以 . 开头的文件，不会被系统读取，但其他APP还是能读取到
 
2. 修改文件尾缀，读取时做出判断（BlankJ的FileUtils）
例：打开.mp41文件：
```
FileUtils.startActionFile(filePath.filePath);
 
if (end.equals("3gp") || end.equals("mp4")||end.equals("mp41")) {
    contentType = "video/*";
}
```