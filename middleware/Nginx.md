# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Nginx
Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。

### brew方式安装
brew search nginx   //搜索软件包
brew install nginx  //安装软件包
brew services start nginx   //启动nginx 
brew services stop nginx   //关闭nginx服务
nginx -v   // 查看nginx版本
nginx -s reload     //重新加载nginx
nginx -s stop       //停止nginx

### docker方式安装
docker search nginx //搜索
docker pull nginx:1.17.0    //拉取镜像
docker run -p 80:80 --name nginx -d nginx:1.17.0    //启动容器

### 压缩包方式安装
官网下载地址：nginx.org/download
下载 nginx 的压缩包文件到根目录，官网下载地址：nginx.org/download/nginx-x.xx.xx.tar.gz
```
yum update #更新系统软件
cd /
wget nginx.org/download/nginx-1.17.2.tar.gz
```

解压 tar.gz 压缩包文件，进去 nginx-1.17.2
```
tar -xzvf nginx-1.17.2.tar.gz
cd nginx-1.17.2
```

进入文件夹后进行配置检查
```
./configure
```

通过安装前的配置检查，发现有报错。检查中发现一些依赖库没有找到，这时候需要先安装 nginx 的一些依赖库
```
yum -y install pcre* #安装使nginx支持rewrite
yum -y install gcc-c++
yum -y install zlib*
yum -y install openssl openssl-devel
```

再次进行检查操作 ./configure 没发现报错显示，接下来进行编译并安装的操作
```
// 检查模块支持
  ./configure  --prefix=/usr/local/nginx  --with-http_ssl_module --with-http_v2_module --with-http_realip_module --with-http_addition_module --with-http_sub_module --with-http_dav_module --with-http_flv_module --with-http_mp4_module --with-http_gunzip_module --with-http_gzip_static_module --with-http_auth_request_module --with-http_random_index_module --with-http_secure_link_module --with-http_degradation_module --with-http_slice_module --with-http_stub_status_module --with-mail --with-mail_ssl_module --with-stream --with-stream_ssl_module --with-stream_realip_module --with-stream_ssl_preread_module --with-threads --user=www --group=www

```
 这里得特别注意下，你以后需要用到的功能模块是否存在，不然以后添加新的包会比较麻烦。

编译并安装
```
make && make install
```

查看 nginx 安装后在的目录，可以看到已经安装到 /usr/local/nginx 目录了
```
whereis nginx
$nginx: /usr/local/nginx
```

启动 nginx 服务
```
cd /usr/local/nginx/sbin/
./nginx
```

### 常用命令
常用命令
```
nginx -s reload  # 向主进程发送信号，重新加载配置文件，热重启
nginx -s reopen  # 重启 Nginx
nginx -s stop    # 快速关闭
nginx -s quit    # 等待工作进程处理完成后关闭
nginx -T         # 查看当前 Nginx 最终的配置
nginx -t -c <配置路径>  # 检查配置是否有问题，如果已经在配置目录，则不需要 -c
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
worker_processes  4;

error_log  logs/error.log;
#error_log  logs/error.log  notice;
#error_log  logs/error.log  info;

pid        logs/nginx.pid;


events {
    worker_connections  1024;
}

# Load dynamic modules, stream configure
#include /usr/share/nginx/modules/*.conf;

http {

    include       mime.types;
    default_type  application/octet-stream;

    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    server_names_hash_bucket_size 128;
    client_header_buffer_size 32k;
    large_client_header_buffers 4 32k;
    client_max_body_size 10000m;
    client_body_buffer_size 2560k;
    client_header_timeout 3m;
    client_body_timeout 3m;
    send_timeout 3m;

    access_log  logs/access.log  main;

    sendfile        on;
    #tcp_nopush     on;

    #keepalive_timeout  0;
    keepalive_timeout  65;

    gzip  on;

    #引入配置1
    include conf/conf.d/*.conf;
	#引入配置2
	include /etc/nginx/default.d/*.conf;
    
    server {
        listen       80;
        server_name  localhost;
        #charset koi8-r;

        #access_log  logs/host.access.log  main;

        location / {
            root         /usr/share/nginx/html;
            index  index.html index.htm;
        }

        location ~ /uploadtuyue.*\.(gif|jpg|jpeg|png|js|css|woff|woff2|ttf|mp4|mp3|apk|txt)$ {
            expires 7d;
            root /usr/local/;    
        }

        location /tuyue {
            proxy_pass http://localhost:8080/tuyue/;
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