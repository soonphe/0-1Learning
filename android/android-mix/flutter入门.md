# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")

### 1.安装

#### 选择安装 Flutter 的操作系统：

* Windows
* macOS
* Linux
* Chrome OS

这里选择macOS作为示例
https://flutterchina.club/setup-macos/

#### 系统要求

要安装和运行 Flutter，您的开发环境必须满足以下最低要求：

* 操作系统：macOS（64 位）
* 磁盘空间：2.8 GB（不包括用于 IDE/工具的磁盘空间）。
* 工具：Flutter 使用 git 进行安装和升级。我们建议安装 Xcode，其中包括 git，但您也可以单独安装 git。

#### Get the Flutter SDK

1. 下载以下安装包以获取 Flutter SDK 的最新稳定版本：
   https://storage.googleapis.com/flutter_infra_release/releases/stable/macos/flutter_macos_2.2.3-stable.zip

对于其他发布渠道和旧版本，请参阅 SDK 发布页面

2. 在所需位置提取文件，例如：
   
   ```
   cd ~/development
   unzip ~/Downloads/flutter_macos_2.2.3-stable.zip
   ```
   
   添加 flutter 到你的 path:
   
   ```
   export PATH="$PATH:`pwd`/flutter/bin"
   ```
   
   此命令仅为当前终端窗口设置 PATH 变量。要将 Flutter 永久添加到您的路径中，请参阅更新您的路径。
   添加以下行并将 [PATH_OF_FLUTTER_GIT_DIRECTORY] 更改为 Flutter git repo 克隆的路径：
   
   ```
   export PATH="$PATH:[PATH_OF_FLUTTER_GIT_DIRECTORY]/bin"
   ```
   
   运行 source $HOME/.<rc file> 刷新当前窗口，或者打开一个新的终端窗口来自动获取文件。

通过运行以下命令验证 flutter/bin 目录现在是否在您的 PATH 中：

```
 echo $PATH
```

通过运行以下命令验证 flutter 命令是否可用：

```
 which flutter
```

运行以下命令以查看是否需要安装任何依赖项以完成设置（对于详细输出，添加 -v 标志）：

```
 flutter doctor
```

此命令检查您的环境并向终端窗口显示报告。 Dart SDK 与 Flutter 捆绑在一起；不需要单独安装 Dart。仔细检查您可能需要安装的其他软件或要执行的其他任务的输出（以粗体显示）。
示例：

```
[-] Android toolchain - develop for Android devices
    • Android SDK at /Users/obiwan/Library/Android/sdk
    ✗ Android SDK is missing command line tools; download from https://goo.gl/XxQghQ
    • Try re-installing or updating your Android SDK,
      visit https://flutter.dev/setup/#android-setup for detailed instructions.
```

3. ios需要特别设置：brew安装flutter工具

4. flutter 更新版本
   
   ```
   $ flutter upgrade
   ```

### 2.设置编辑器

安装 Android Studio
Android Studio 为 Flutter 提供了完整的集成 IDE 体验。
Android Studio, version 3.0 or later

安装 Flutter 和 Dart 插件（安装说明因平台而异。）

要安装这些:

1. 启动Android Studio.
2. 打开插件首选项 (Preferences>Plugins on macOS, File>Settings>Plugins on Windows & Linux).
3. 选择 Browse repositories…, 选择 Flutter 插件并点击 install.
4. 重启Android Studio后插件生效.

### 3. 体验

创建应用程序

1. 选择 File>New Flutter Project
2. 选择 Flutter application 作为 project 类型, 然后点击 Next
3. 输入项目名称 (如 myapp), 然后点击 Next
4. 点击 Finish
5. 等待Android Studio安装SDK并创建项目.

运行应用程序

1. 找到主 Android Studio 工具栏：
2. 在目标选择器中，选择一个 Android 设备来运行应用程序。如果没有列出可用，请选择Tools> Android > AVD 管理器并在那里创建一个。
3. 单击工具栏中的运行图标，或调用菜单项 Run > Run.

尝试热重载

1. 打开 lib/main.dart。
2. 更改字符串
3. 保存更改：调用“全部保存”，或单击“热重载”闪电。
   您几乎会立即在正在运行的应用程序中看到更新后的字符串。

### 4. 编写你的第一个应用

### 5. 了解更多
