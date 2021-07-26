# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Nginx
Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。

### brew方式安装
- brew search nginx   //搜索软件包
- brew install nginx  //安装软件包
- brew services start nginx   //启动nginx 
- brew services stop nginx   //关闭nginx
- nginx -v   // 查看nginx版本
- nginx -s reload     //重新加载nginx
- nginx -s stop       //停止nginx

### docker方式安装
- docker search nginx //搜索
- docker pull nginx:1.17.0    //拉取镜像
- docker run -p 80:80 --name nginx -d nginx:1.17.0    //启动容器

### 压缩包方式安装
- 官网下载地址：nginx.org/download（官网下载地址：nginx.org/download/nginx-x.xx.xx.tar.gz）
- wget下载：wget nginx.org/download/nginx-1.17.2.tar.gz

- yum安装nginx所需插件
```
yum update #更新系统软件
yum -y xxx：加上参数-y，就会自动选择y，不需要你再手动选择 Is this OK[y/d/N]

1、安装gcc（gcc是linux下的编译器）
查看gcc版本：gcc -v 
yum -y install gcc
或
yum -y install gcc-c++ 

2、pcre、pcre-devel安装（pcre是一个perl库，包括perl兼容的正则表达式库，让nginx支持rewrite）
yum install -y pcre pcre-devel

3、zlib安装（zlib库提供了很多种压缩和解压缩方式nginx使用zlib对http包的内容进行gzip）
yum install -y zlib zlib-devel

4、安装openssl（openssl是web安全通信的基石）
yum install -y openssl openssl-devel
```

- Linux中Nginx安装与配置详解
```
    1. 下载
    2. 解压
    # cd /usr/local/nginx
    # tar zxvf nginx-1.14.0.tar.gz
    3. 进入目录
    # cd nginx-1.14.0
    4. 配置
    # ./configure
    5. 编译
    # make
    6. 安装
    #  make install
    7. 检查是否安装成功
    # cd  /usr/local/nginx/sbin
    # ./nginx -t 
    结果显示：
    nginx: the configuration file /usr/local/nginx/conf/nginx.conf syntax is ok
    nginx: configuration file /usr/local/nginx/conf/nginx.conf test is successful。
```


### 常用命令
常用命令
```
cd /usr/local/nginx/sbin/
./nginx 
./nginx -s reload  # 向主进程发送信号，重新加载配置文件，热重启
./nginx -s reopen  # 重启 Nginx
./nginx -s stop    # 快速关闭
./nginx -s quit    # 等待工作进程处理完成后关闭
./nginx -T         # 查看当前 Nginx 最终的配置
./nginx -t -c <配置路径>  # 检查配置是否有问题，如果已经在配置目录，则不需要 -c
```
查询nginx进程：
```
ps aux|grep nginx
```

### Nginx 配置
基本结构：
```
main        # 全局配置，对全局生效
├── events  # 配置影响 nginx 服务器或与用户的网络连接
├── http    # 配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置
│   ├── upstream # 配置后端服务器具体地址，负载均衡配置不可或缺的部分
│   ├── server   # 配置虚拟主机的相关参数，一个 http 块中可以有多个 server 块
│   ├── server
│   │   ├── location  # server 块可以包含多个 location 块，location 指令用于匹配 uri
│   │   ├── location
│   │   └── ...
│   └── ...
└── ...
```

主要配置含义
```
main:nginx 的全局配置，对全局生效。
events:配置影响 nginx 服务器或与用户的网络连接。
http：可以嵌套多个 server，配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置。
server：配置虚拟主机的相关参数，一个 http 中可以有多个 server。
location：配置请求的路由，以及各种页面的处理情况。
upstream：配置后端服务器具体地址，负载均衡配置不可或缺的部分。
```

nginx.conf 配置文件的语法规则
```
配置文件由指令与指令块构成
每条指令以 “;” 分号结尾，指令与参数间以空格符号分隔
指令块以 {} 大括号将多条指令组织在一起
include 语句允许组合多个配置文件以提升可维护性
通过 # 符号添加注释，提高可读性
通过 $ 符号使用变量
部分指令的参数支持正则表达式，例如常用的 location 指令
```

内置变量：
```
TCP                 UDP
$host               请求信息中的Host，如果请求中没有Host行，则等于设置的服务器名
$request_method     客户端请求类型，如GET、POST
$remote_addr        客户端的IP地址 
$args               请求中的参数 
$content_length     请求头中的Content-length 字段 
$http_user_agent    客户端 agent信息
$http_cookie        客户端cookie 信息 
$remote_port        客户端的端口 
$server_protocol    请求使用的协议，如HTTP／1.1
$server_addr        服务器地址 
$server_name        服务器名称 
$server_port        服务器的端口号 
```

### 配置文件参考 
```
user  root;
worker_processes  4;    #工作进程：数目。根据硬件调整，通常等于cpu数量或者2倍cpu数量。

error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        logs/nginx.pid;  # nginx进程pid存放路径


events {
    worker_connections  1024;   # 工作进程的最大连接数量
}


http {

    include       mime.types;   #指定mime类型，由mime.type来定义
    default_type  application/octet-stream;

    # 日志格式设置
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    # 文件大小配置
    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 10000m;
    client_body_buffer_size 2560k;
    client_header_timeout 3m;
    client_body_timeout 3m;
    send_timeout 3m;

    access_log  logs/access.log  main;

    sendfile        on; #指定nginx是否调用sendfile函数来输出文件，对于普通应用，必须设置on。如果用来进行下载等应用磁盘io重负载应用，可设着off，
    #tcp_nopush     on; #此选项允许或禁止使用socket的TCP_CORK的选项，此选项仅在sendfile的时候使用

    #keepalive_timeout  0;  #keepalive超时时间
    keepalive_timeout  65;

    #gzip  on;   #开启gzip压缩服务

    #引入配置1
    include conf/conf.d/*.conf;
	#引入配置2
	include /etc/nginx/default.d/*.conf;

    #配置负载均衡（映射规则）
    upstream www.932edu.com {
        ip_hash;
        server 47.93.193.146:8080 weight=2;
        server 47.94.108.123:8080 weight=1;
    }
    
    server {
        listen       80;    #配置监听端口号
        #配置访问域名，域名可以有多个，用空格隔开
        #server_name  localhost; 
        #配置基于名称的虚拟主机
        server_name  www.932edu.net 932edu.net;
        if ($host != 'www.932edu.net'){
            rewrite ^/(.*)$ http://www.932edu.net/$1 permanent;
        }

        #charset koi8-r;    #字符集设置

        #access_log  logs/host.access.log  main;
        # 跳转规则，配置为 = ，可精确匹配加快访问速度
        location / {
            root         /usr/share/nginx/html;
            index  index.html index.htm;
        }
        # 静态文件配置
        location ~ /uploadtuyue.*\.(gif|jpg|jpeg|png|js|css|woff|woff2|ttf|mp4|mp3|apk|txt)$ {
            expires 7d;
            root /usr/local/;    
        }
        # 匹配路径
        location /tuyue {
            proxy_pass http://localhost:8080/tuyue/;
        }
        # 匹配路径
        location /jenkins {
            proxy_pass http://10.10.18.36:8080/;
            proxy_set_header Host $proxy_host; # 修改转发请求头，让8080端口的应用可以受到真实的请求
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        }

        # 配置转发代理跳转规则
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

        #error_page  404              /404.html;

        # redirect server error pages to the static page /50x.html
        #
        error_page   500 502 503 504  /50x.html;
        location = /50x.html {
            root   html;
        }

        # proxy the PHP scripts to Apache listening on 127.0.0.1:80
        #
        #location ~ \.php$ {
        #    proxy_pass   http://127.0.0.1;
        #}

        # pass the PHP scripts to FastCGI server listening on 127.0.0.1:9000
        #
        #location ~ \.php$ {
        #    root           html;
        #    fastcgi_pass   127.0.0.1:9000;
        #    fastcgi_index  index.php;
        #    fastcgi_param  SCRIPT_FILENAME  /scripts$fastcgi_script_name;
        #    include        fastcgi_params;
        #}

        # deny access to .htaccess files, if Apache's document root
        # concurs with nginx's one
        #
        #location ~ /\.ht {
        #    deny  all;
        #}
    }


    # another virtual host using mix of IP-, name-, and port-based configuration
    #
    #server {
    #    listen       8000;
    #    listen       somename:8080;
    #    server_name  somename  alias  another.alias;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}


    # HTTPS server
    #
    #server {
    #    listen       443;
    #    server_name  localhost;

    #    ssl                  on;
    #    ssl_certificate      cert.pem;
    #    ssl_certificate_key  cert.key;

    #    ssl_session_timeout  5m;

    #    ssl_protocols  SSLv2 SSLv3 TLSv1;
    #    ssl_ciphers  HIGH:!aNULL:!MD5;
    #    ssl_prefer_server_ciphers   on;

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

### nginx配置二级模块
```
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
```
 
这样访问
www.domain.com时root目录为/data/wwwsite/domain.com/www/，
nginx.domain.com时为/data/wwwsite/domain.com/nginx/，以此类推

### nginx配置二级域名
```
    server {
        listen          80;
        server_name     www.codeliu.com;
    
        location / {
            root    /usr/lib/apache-tomcat-8.5.33/webapps/CodeliuDemo;
            index   index.html index.htm;
        }
    }
    
    
    server {
        listen          80;
        server_name     test1.codeliu.com;
    
        location / {
            root   /usr/lib/apache-tomcat-8.5.33/webapps/Test1Demo;
            index  index.html index.htm;
        }
    }
    
    server {
        listen          80;
        server_name     test2.codeliu.com;
    
        location / {
            root    /usr/lib/apache-tomcat-8.5.33/webapps/Test2Demo;
            index   index.html index.htm;
        }
    }
```
 
 
### 代理文件配置
```
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
```


