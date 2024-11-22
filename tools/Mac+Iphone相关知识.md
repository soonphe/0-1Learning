# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## Mac相关知识

### Mac pro分辨率：
例：16寸 3072 x 1920视网膜显示屏
3072*3072=9437184 1920*1920=3686400 9437184+3686400=13123584
13123584开平方：3622.6488
3622.6488/16=226.41 ppi

### Mac指令集
控制台命令：uname -a查看操作系统所有信息   uname -m主机硬件架构   uname -p处理器类型

确定了你的架构就可以基于你的架构选对应的软件了，一般情况下，不同的架构常常对应不同用途的设备：
- arm64和aarch64对应64位ARM架构(ARMv8),常见于移动设备和嵌入式系统。
- armv7l和arm对应32位ARM架构(ARMv7),常见于较旧的移动设备和嵌入式系统。
- x86_64、x64和amd64对应64位x86架构,常见于个人电脑和服务器。
- x86和i386对应32位x86架构,常见于较旧的个人电脑。（基本上可以淘汰了）
- dmg和zip通常表示macOS和Windows平台的安装包格式。

下面是一个常见架构名称的等价关系清单:
```
ARMv8 = ARM64 = AArch64
ARMv7 = armv7l = ARM
x86_64 = x64 = amd64
x86 = x86_32
```

### Mac上有3处可以设置环境变量
/etc/profile：系统全局变量，系统启动即加载该文件的配置（不建议添加）
/etc/bashrc：所有类型的bash shell 都会读取该文件的配置
~/.bash_profile：配置用户级环境变量（配置较多），在系统用户文件夹下创建，当用户登录后被加载的文件，该文件会被执行且仅执行一次，其内容主要为设置环境变量，随后该文件会显式调用 .bashrc
~/.bashrc：配置用户级bash环境变量（配置较少），每次启动新的shell时，或者被 .bash_profile调用时加载的文件，其内容主要为设置功能shopt和设置别名alias，也可用来设置环境变量。
~/.zshrc：配置用户级zshrc环境变量（大部分是zshrc相关配置和引入本机~/.bash_profile），默认终端是bash，如果配置zshrc则也可以引入~/.bash_profile 本机bash

### mac命令行启动mysql
启动mysql：sudo /usr/local/mysql/support-files/mysql.server start

如果报错可能是没有权限
sudo chmod -R a+rwx /usr/local/mysql/data/

### Mac文件权限
修改文件夹权限
sudo chown -R $(whoami) /usr/local/

如果失败提示Operation not permitted 或其他权限不足，则需要关闭Rootless

Rootless 苹果从 OS X El Capitan 10.11 系统开始使用了 Rootless 机制，系统默认将会锁定 /system、/sbin、/usr 这三个目录。用户要获取这三个目录的写权限，需要关闭Rootless

关闭SIP/Rootless
```
重启 Mac
开机时后按下 Command+R，进入恢复模式。
在上面的菜单实用工具中找到并打开 Terminal
输入如下命令：
csrutil disable
重启MAC，正常进入系统，此时已经可以给/system、/sbin、/usr 者几个目录进行权限更改
打开 Terminal
输入如下命令：
sudo chown -R $(whoami) /usr/local
```

### Mac在/home目录新建文件
```
sudo vim /etc/auto_master
屏蔽/home选项
保存退出后
cd /
sudo automount
```
不过这种重启会需要再来一遍

### mac关闭SIP
查看SIP状态：csrutil status
macOS 11.x Big Sur（Intel 处理器） 及以下系统关闭 SIP 步骤：
1、关机，然后重新启动你的Mac电脑，在开机时一直按住Command+R迸入Recovery模式。
2、进入Recovery模式后打开终端，进入恢复模式后，输入csrutil clear清除原有信息。
3、在终端上输入命令 csrutil disable然后回车。
4、点击左上角苹果图标，再点击重新启动

### mac打印
隔空打印：在“隔空打印”协议下，可通过 Wi-Fi、USB 和以太网络访问打印机的打印和扫描选项（若特定的打印机支持这些功能）。你无需下载或安装打印机软件就能使用支持“隔空打印”的打印机。支持“隔空打印”协议的打印机类型广泛，包括 Aurora、Brother、Canon、Dell、Epson、Fuji、Hewlett Packard、Samsung、Xerox 等等。
互联网打印协议 - IPP：现代打印机和打印服务器使用此协议；
行式打印机监控程序 - LPD：旧式打印机和打印服务器可能使用此协议；
HP Jetdirect – Socket：HP 和其他许多打印机制造商都使用此协议。

