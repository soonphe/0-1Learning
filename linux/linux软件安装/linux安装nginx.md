# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## linux安装nginx

Nginx安装：
参考：http://www.linuxidc.com/Linux/2016-08/134110.htm
 
安装前的准备
    1）准备 pcre-8.12.tar.gz。该文件为正则表达式库。让nginx支持rewrite需要安装这个库。
    2) 准备 nginx-1.5.0.tar.gz。该文件为nginx的linux版本安装文件。
    3）确保进行了安装了linux常用必备支持库。
 
需要安装g++、gcc。
rpm -qa | grep gcc 检查是否存在3个包
不存在：使用 # yum install gcc-c++ 安装
 
 2) 上传pcre-8.12.tar.gz, nginx-1.5.0.tar.gz 到 /usr/local/src/nginx目录下。
    
    3）解压pcre-8.12.tar.gz
    # cd /usr/local/src/nginx
    # tar zxvf pcre-8.12.tar.gz
    
    4）进入解压后的目录
    # cd pcre-8.12 
    
    5）配置
    #  ./configure
    6) 编译
    #  make
    7) 安装
    #  make install
 
————————————————————————————————————
3.3 Nginx安装
    0) 创建用户nginx使用的www用户。
    # groupadd  www  #添加www组    
    # useradd -g  www www -s /bin/false  #创建nginx运行账户www并加入到www组，不允许www用户直接登录系统
    
    创建安装目录与日志目录
    a) 安装目录
    # mkdir /usr/local/nginx
    b) 日志目录
    # mkdir /data0/logs/nginx
    # chown www:www /data0/logs/nginx -R
    
    1) 判断系统是否安装了zlib-devel。如果没有安装。使用
    # yum install -y zlib-devel
 
Linux中Nginx安装与配置详解
    
    2) 解压
    # cd /usr/local/src/nginx
    # tar zxvf nginx-1.5.0.tar.gz
    
    3) 进入目录
    # cd nginx-1.5.0
    
    4) 配置。通常将软件安装在/usr/local/目录下。
    # ./configure --user=www --group=www --prefix=/usr/local/nginx --with-http_stub_status_module --with-http_ssl_module --with-http_realip_module
    5）编译
    # make
    
    6)  安装
    #  make install
    
    7)  检查是否安装成功
    # cd  /usr/local/nginx/sbin
    # ./nginx -t 
    结果显示：
    nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
    nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful
 
 
——————————————————————————
 
关闭防火墙：
 
[root@local ~]# iptables -I INPUT -p tcp --dport 80 -j ACCEPT
 
[root@local ~]# service iptables save
 
关闭selinux：
 
[root@local ~]# setenforce 0
 
[root@local ~]# vim /etc/selinux/config
 
将SELINUX=enforcing改为SELINUX=disabled
 
 
——————————————————————————————————
启动、停止nginx
cd /usr/local/nginx/sbin/
./nginx 
./nginx -s stop
./nginx -s quit
./nginx -s reload
./nginx -s quit:此方式停止步骤是待nginx进程处理任务完毕进行停止。
./nginx -s stop:此方式相当于先查出nginx进程id再使用kill命令强制杀掉进程。
 
查询nginx进程：
ps aux|grep nginx
 
1.先停止再启动（推荐）：
对 nginx 进行重启相当于先停止再启动，即先执行停止命令再执行启动命令。如下：
 
./nginx -s quit
./nginx
2.重新加载配置文件：
当 ngin x的配置文件 nginx.conf 修改后，要想让配置生效需要重启 nginx，使用-s reload不用先停止 ngin x再启动 nginx 即可将配置信息在 nginx 中生效，如下：
./nginx -s reload
启动成功后，在浏览器可以看到这样的页面：welcome to nginx
 
    /*Server Side Include，通常称为服务器端嵌入*/
    ssi on;
    ssi_silent_errors on;
    #ssi_types
    默认是ssi_types text/html，所以如果需要htm和html支持，则不需要设置这句，如果需要shtml支持，则需要设置：ssi_types text/shtml
 
    gzip  on;
    gzip_min_length 1k;
    gzip_buffers    4 16k;
    #gzip_http_version 1.0;
    gzip_comp_level 6;
    gzip_types    text/plain application/x-javascript text/css application/xml text/javascript application/x-httpd-php image/jpeg image/gif image/png;
    gzip_vary off;
    gzip_disable "MSIE [1-6]\.";
 
 
    /*nginx配置负载均衡（映射规则）*/
    upstream www.932edu.com {
        ip_hash;
 
        server 47.93.193.146:8080 weight=2;
        server 47.94.108.123:8080 weight=1;
 
    }
 
    server {
        listen       80 default;
        #server_name  localhost;
#配置基于名称的虚拟主机
        server_name  www.932edu.net 932edu.net;
        if ($host != 'www.932edu.net'){
        rewrite ^/(.*)$ http://www.932edu.net/$1 permanent;
        }
 
/*跳转规则，配置为 = ，可精确匹配加快访问速度*/
        location = / {
            #root   html;
            #proxy_pass http://127.0.0.1:8080;
            #index  index.html index.htm;
            proxy_pass   http://www.932edu.net/lwgk/web/modules/index/index.html;
            proxy_redirect off;
            proxy_set_header Host $host:$server_port;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Headers X-Requested-With;
            add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
                        client_max_body_size    100M;
 
        }
 
/*跳转规则*/
        location /lwgk/ {
            #root   html;
            #index  index.html index.htm;
            proxy_pass   http://www.932edu.net/lwgk/;        #充当代理服务器，转发请求
            proxy_redirect off;                #对发送给客户端的URL进行修改，默认default
            proxy_set_header Host $host:$server_port;
            proxy_set_header X-Real-IP $remote_addr;        #获取真实ip
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;        #获取代理者的真实ip
            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Headers X-Requested-With;
            add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
                        client_max_body_size    100M;
        }
        location ~ .*\.(gif|jpg|jpeg|png|bmp|swf|apk|xlsx|xls)$ {
            expires      30d;
            proxy_pass http://47.93.193.146:8080;
            proxy_redirect off;
            proxy_set_header Host $host:$server_port;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
            add_header Access-Control-Allow-Origin *;
            add_header Access-Control-Allow-Headers X-Requested-With;
            add_header Access-Control-Allow-Methods GET,POST,OPTIONS;
                        client_max_body_size    100M;
 
        }
 
        location / {
            root  /usr/local/tomcat8/lwgk/web/common/html/error.html;
            #proxy_pass   http://www.932edu.com/lwgk/web/common/error.html;
        }
 
 
 
=================================================================
其他举例：
/*例：配置多个二级模块*/
server
   {
     listen       80;
     server_name  ~^(.+)?\.domain\.com$;
     index index.html;
     if ($host = domain.com){
         rewrite ^ http://www.domain.com permanent;
     }
     root  /data/wwwsite/domain.com/$1/;
   }
/*站点目录结构*/
/data/wwwsite/domain.com/www/
/data/wwwsite/domain.com/nginx/
 
这样访问www.domain.com时root目录为/data/wwwsite/domain.com/www/，nginx.domain.com时为/data/wwwsite/domain.com/nginx/，以此类推
 
 
 
 
-------------------------------------------------------------------
 
代理文件配置
#!nginx (-)
# proxy.conf
proxy_redirect          off;
proxy_set_header        Host $host;
proxy_set_header        X-Real-IP $remote_addr;  #获取真实ip
#proxy_set_header       X-Forwarded-For   $proxy_add_x_forwarded_for; #获取代理者的真实ip
client_max_body_size    10m;
client_body_buffer_size 128k;
proxy_connect_timeout   90;
proxy_send_timeout      90;
proxy_read_timeout      90;
proxy_buffer_size       4k;
proxy_buffers           4 32k;
proxy_busy_buffers_size 64k;
proxy_temp_file_write_size 64k;
