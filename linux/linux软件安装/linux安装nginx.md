# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## linux安装nginx

### 安装所需插件
1. gcc（gcc是linux下的编译器）
查看gcc版本：gcc -v 
yum -y install gcc
或
yum install gcc-c++ 

2. pcre、pcre-devel安装（pcre是一个perl库，包括perl兼容的正则表达式库，让nginx支持rewrite）
yum install -y pcre pcre-devel

3. zlib安装（zlib库提供了很多种压缩和解压缩方式nginx使用zlib对http包的内容进行gzip）
yum install -y zlib zlib-devel

4. 安装openssl（openssl是web安全通信的基石）
yum install -y openssl openssl-devel

5. 手动安装教程示例：
```
    1）下载
    2）上传
    3）解压pcre-8.12.tar.gz
    # cd /usr/local/src/nginx
    # tar zxvf pcre-8.12.tar.gz
    4）进入解压后的目录
    # cd pcre-8.12 
    5）配置
    #  ./configure
    6) 编译
    #  make
    7) 安装
    #  make install
```
 
### Nginx安装与配置

Linux中Nginx安装与配置详解
```
    1. 下载
    2. 解压
    # cd /usr/local/nginx
    # tar zxvf nginx-1.14.2.tar.gz
    3. 进入目录
    # cd nginx-1.14.0
    4. 配置
    # ./configure
    5. 编译
    # make
    6. 安装
    #  make install
    7. 检查是否安装成功
    # cd  /usr/local/nginx/sbin
    # ./nginx -t 
    结果显示：
    nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
    nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful。
```

### nginx命令(启动、停止nginx)(默认端口80)
cd /usr/local/nginx/sbin/
./nginx 
./nginx -s stop
./nginx -s quit
./nginx -s reload
./nginx -s quit:此方式停止步骤是待nginx进程处理任务完毕进行停止。
./nginx -s stop:此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程。
 
查询nginx进程：
ps aux|grep nginx


 

