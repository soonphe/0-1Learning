# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

### Mac上有3处可以设置环境变量
/etc/profile ：系统全局变量，系统启动即加载该文件的配置（不建议添加）
/etc/bashrc：所有类型的bash shell 都会读取该文件的配置
~/.bash_profile：配置用户级环境变量，在系统用户文件夹下创建，当用户登录时，该文件会被执行且仅执行一次

### mac命令行启动mysql
启动mysql：sudo /usr/local/mysql/support-files/mysql.server start

如果报错可能是没有权限
sudo chmod -R a+rwx /usr/local/mysql/data/

### Mac文件权限
修改文件夹权限
sudo chown -R $(whoami) /usr/local/

如果失败提示Operation not permitted 或其他权限不足，则需要关闭Rootless

Rootless 苹果从 OS X El Capitan 10.11 系统开始使用了 Rootless 机制，系统默认将会锁定 /system、/sbin、/usr 这三个目录。用户要获取这三个目录的写权限，需要关闭Rootless

关闭Rootless
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
