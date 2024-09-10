# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Nginx
Nginx (engine x) 是一个高性能的HTTP和反向代理web服务器，同时也提供了IMAP/POP3/SMTP服务。

### brew方式安装
- brew search nginx   //搜索软件包
- brew install nginx  //安装软件包
- brew services start nginx   //启动nginx 
- brew services stop nginx   //关闭nginx
- nginx   //启动nginx
- nginx -v   // 查看nginx版本
- nginx -s reload     //重新加载nginx
- nginx -s stop       //停止nginx

配置地址：/usr/local/etc/nginx/nginx.conf
安装地址：/usr/local/Cellar/nginx/1.21.0
HTML默认地址：/usr/local/Cellar/nginx/1.21.0

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
       6.  安装
       #   make install
       7. 检查是否安装成功
       # cd   /usr/local/nginx/sbin
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
main                    # 全局配置，对全局生效
├── events              # 配置影响 nginx 服务器或与用户的网络连接
├── http                # 配置代理，缓存，日志定义等绝大多数功能和第三方模块的配置
    ├── upstream        # 配置后端服务器具体地址，负载均衡配置不可或缺的部分
        ├── server      # 要转发的server地址，可以配置多个，参数：weight权重，down暂时不参数负载，backup备份，max_fails最多失败次数，fail_timeout失败等待时长
    ├── server          # 配置虚拟主机的相关参数，一个 http 块中可以有多个 server 块
        ├── listen      # 监听端口
        ├── server_name # 配置基于名称的虚拟主机，不同server_name可以监听同一端口，匹配规则：准确的server_name匹配 > 以*通配符开始的字符串 > 以*通配符结束的字符串 > 匹配正则表达式
        ├── location    # 匹配请求的路由,server 块可以包含多个 location 块，location 指令用于匹配 uri
        ├── location... #其他location
        └── ...
    ├── server  # 其他server
    └── ...
└── ...
```
说明：当客户端向 Nginx 服务器发送请求时，Nginx首先会根据 IP地址和端口（listen 属性） 对server服务器进行配置；如果IP地址匹配不成功，会对 域名（server_name属性） 进行匹配；如果域名也匹配不成功，则会默认匹配第一个server服务器（因此，当只有一个Nginx服务器时，客户端的请任何情况下都会匹配到这个服务器上）。

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
user  root;                     #指定nginx运行的用户和用户组(只有当主进程以超级用户权限运行时，“user”指令才有意义)
worker_processes  4;            #指定nginx启动的进程数，根据硬件调整，通常等于cpu数量或者2倍cpu数量。

error_log  logs/error.log;          #指定错误日志文件
#error_log  logs/error.log  notice; #日志级别为notice
#error_log  logs/error.log  info;

pid        logs/nginx.pid;      #nginx进程号存放文件

events {
    worker_connections  1024;   #最大连接数
}

http {

    include       mime.types;                   #引入mime.types文件
    default_type  application/octet-stream;     #默认类型

    #日志格式
    log_format  main  '$remote_addr - $remote_user [$time_local] "$request" '
                      '$status $body_bytes_sent "$http_referer" '
                      '"$http_user_agent" "$http_x_forwarded_for"';

    #文件配置
    server_names_hash_bucket_size 128;      #服务器名称hash表桶的大小，默认是32或64
    client_header_buffer_size 32k;          #设置请求头的缓冲区大小
    large_client_header_buffers 4 32k;      #设置请求头的缓冲区大小，最大的请求头数目和请求头的缓冲区大小
    client_max_body_size 10000m;            #设置请求体的最大值
    client_body_buffer_size 2560k;          #设置请求体的缓冲区大小
    client_header_timeout 3m;               #设置请求头的超时时间
    client_body_timeout 3m;                 #设置请求体的超时时间
    send_timeout 3m;                        #设置发送响应的超时时间

    access_log  logs/access.log  main;      #访问日志文件

    sendfile        on;                     #开启sendfile，减少cpu占用，提高文件传输效率(对于普通应用，必须设置on。如果用来进行下载等应用磁盘io重负载应用，可设着off)
    #tcp_nopush     on;                     #开启tcp_nopush，减少网络报文段的数量，提高网络传输效率(此选项允许或禁止使用socket的TCP_CORK的选项，此选项仅在sendfile的时候使用)

    #keepalive_timeout  0;                  #保持连接的时间，如果为0，表示不启用长连接，如果大于0，表示在这个时间内，客户端可以持续发起请求
    keepalive_timeout  65;

    #gzip  on;                              #启用gzip压缩            
    #gzip_min_length 1k;                    #启用压缩的最小文件，小于设置值的文件将不会压缩

    #引入配置文件
    include conf/conf.d/*.conf;             #引入conf.d目录下的所有.conf文件
	include /etc/nginx/default.d/*.conf;    #引入default.d目录下的所有.conf文件

    #配置负载均衡（映射规则）
    upstream www.0-1Leaning.com {                                           #定义负载均衡的名称(upstream默认为轮询，weight不指定时，各服务器weight相同)
        #ip_hash;                                                           #ip_hash指令会根据客户端的ip地址进行hash，这样可以保证同一个客户端每次访问的服务器是一样的（可以解决session不能跨服务器的问题）
        server 47.93.193.146:8080 weight=2;                                 #定义后端服务器，weight表示权重，权重越高被分配到的几率越大
        server 47.94.108.123:8080 weight=1;                                 #weight\backup 不能和 ip_hash 关键字一起使用。
        #server 192.168.11.69:20201 weight=100 down;                        #down 表示当前的server暂时不参与负载
        #server 192.168.11.71:20201 weight=100 backup;                      #backup 表示备份的server，只有当所有的非backup的server挂掉后才会使用
        #server 192.168.11.72:20201 weight=100 max_fails=3 fail_timeout=30s;    #max_fails 表示允许请求失败的次数，默认为1。fail_timeout 表示在经历了max_fails次失败后，暂停服务的时间。（#当upstream中只有一个 server 时，max_fails 和 fail_timeout 参数可能不会起作用。）
    }
    
    server {
        listen       80;                                                    #监听端口号
        server_name www.0-1Leaning.com 0-1Leaning.com;                      #域名
        if ($host != 'www.0-1Leaning.com'){                                 #判断域名
            rewrite ^/(.*)$ http://www.0-1Leaning.com/$1 permanent;         #重定向，不为www开头，强制转换
            #rewrite ^/(.*)$ https://$host$1 permanent;                     #http重定向https
        }
        #root /usr/share/nginx/html/;                                       #根目录
        
        #charset koi8-r;    #字符集设置
        #access_log  logs/host.access.log  main;
        
        # 默认配置uri，配置为 = ，可精确匹配加快访问速度
        #/标识通用匹配，= 表示精确匹配，~ 表示区分大小写的正则匹配，~* 表示不区分大小写的正则匹配，!~ 表示区分大小写的正则不匹配，!~* 表示不区分大小写的正则不匹配，^~ 表示uri以某个字符串开头
        #在顺序上，前缀字符串顺序不重要，按照匹配长度来确定，正则表达式则按照定义顺序。
        #优先级，= 修饰符最高，^~ 次之，再者是正则，最后是前缀字符串匹配。
        location / {    
            root   /usr/share/nginx/html;                                   #根目录(就近原则，如果 location 中能匹配到，就用 location 中的 root 配置，当 location 中匹配不到的时候，则使用 server 中的 root 配置)
            index  index.html index.htm;                                    #默认访问文件
        }
        # 静态文件
        location ~ /uploadtuyue.*\.(gif|jpg|jpeg|png|js|css|woff|woff2|ttf|mp4|mp3|apk|txt)$ {
            expires 7d;                                                     #缓存时间
            root /usr/local/;                                               #根目录
        }
        # 匹配uri
        location /tuyue {
            proxy_pass http://localhost:8080/tuyue/;                        #转发请求
        }
        location /lwgk/ {
            proxy_pass http://www.0-1Leaning.com/lwgk/;                     #转发请求
            proxy_redirect off;                                             #不重定向，默认default
            proxy_set_header Host $host:$server_port;                       #设置请求头
            proxy_set_header X-Real-IP $remote_addr;                        #获取真实ip
            proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;    #获取代理者的真实ip
            add_header Access-Control-Allow-Origin *;                       #允许跨域
            add_header Access-Control-Allow-Headers X-Requested-With;       #允许请求头
            add_header Access-Control-Allow-Methods GET,POST,OPTIONS;       #允许请求方法
            client_max_body_size       100M;                                #设置请求体大小
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
    #    listen       443 ssl;                                              #监听端口号(此处如未添加ssl，可能会造成Nginx无法启动。)
    #    server_name  localhost;                                            #域名

    #    ssl                  on;                                           #开启ssl
    #    ssl_certificate      cert.pem;                                     #证书
    #    ssl_certificate_key  cert.key;                                     #证书key

    #    ssl_session_timeout  5m;                                           #ssl会话超时时间

    #    ssl_protocols  SSLv2 SSLv3 TLSv1;                                  #ssl协议
    #    ssl_ciphers  HIGH:!aNULL:!MD5;                                     #ssl加密算法
    #    ssl_prefer_server_ciphers   on;                                    #ssl优先使用服务器端的加密算法

    #    location / {
    #        root   html;
    #        index  index.html index.htm;
    #    }
    #}

}
```

### nginx同时配置了proxy_pass和root效果
- proxy_pass 指令用于将请求转发到另一台服务器
- root 指令用于设置请求资源的文件系统根路径。
如果配置了 proxy_pass 和 root 后请求同时生效，这通常不是预期的行为，可能是配置错误。
正常情况下，location 块中的 proxy_pass 和 root 指令不应该同时使用，因为它们分别代表了不同的用途：proxy_pass 用于定义代理服务器的地址，而 root 用于指定请求资源的文件系统路径。x

### 代理文件proxy_pass配置选项
```
# proxy.conf
proxy_redirect      off;
proxy_set_header    Host $host; #将原有请求的host放入转发的请求里
proxy_set_header    X-Real-IP $remote_addr;   #获取真实ip
proxy_set_header    X-Forwarded-For     $proxy_add_x_forwarded_for; #获取代理者的真实ip
proxy_set_header    X-Forwarded-Proto   $scheme; #识别协议HTTP或HTTPS的，及用户自定义header的
client_max_body_size       10m;
client_body_buffer_size 128k;
proxy_connect_timeout     90;
proxy_send_timeout           90;
proxy_read_timeout           90;
proxy_buffer_size             4k;
proxy_buffers                     4 32k;
proxy_busy_buffers_size 64k;
proxy_temp_file_write_size 64k;
```

说明：
nginx为了实现反向代理的需求而增加了一个ngx_http_proxy_module模块。其中proxy_set_header指令就是该模块需要读取的配置文件
X-Forwarded-For:简称XFF头，它代表客户端，也就是HTTP的请求端真实的IP，只有在通过了HTTP 代理或者负载均衡服务器时才会添加该项

当request header 中key存在特殊字符时（如“:”，“_”），修改配置：
解除ngnix的限制，当key含特殊字符时，忽略。
```
server{
    listen 80;
    ignore_invalid_headers off;
    ...
}
```

### nginx配置二级路径匹配
```
server {
    listen 80;
    server_name ~^(.+)?\.domain\.com$;
    index index.html;
    if ($host = domain.com){
        rewrite ^ http://www.domain.com permanent;
    }
    root /data/wwwsite/domain.com/$1/;
}
/*站点目录结构*/
/data/wwwsite/domain.com/www/
/data/wwwsite/domain.com/nginx/
```
访问**www.domain.com**时：root目录为/data/wwwsite/domain.com/www/，
访问**nginx.domain.com**时：root目录为/data/wwwsite/domain.com/nginx/，以此类推


### nginx配置二级域名匹配
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
    
    server {
        listen          80;
        server_name     www.ywzt.com ywzt.com;
    
        location / {
            root   html/ywzt;
            index  index.html index.htm;
        }
    }
    server {
        listen          80;
        server_name     nacos.ywzt.com;
    
        location / {
            proxy_pass   http://127.0.0.1:8848;
        }
    }
```

### nginx不配置server_name
Nginx不设置server_name是可以的。
在Nginx配置中,server_name用于指定域名或IP地址,以匹配请求的主机头。如果不设置server_name,Nginx将会使用默认的server块来处理请求

Nginx的请求路由转发，可以分成两个阶段。
- 第一个阶段是匹配定义的server。首先根据请求中的目的地址和端口进行匹配。如果相同的目的地址和端口同时还会对应多个servers，再根据server_name属性进行进一步匹配。需要强调的是，只有当listen指令无法找到最佳匹配时才会考虑评估server_name指令。
- 第二阶段，在匹配到server后，Nginx根据请求URL中的path信息再匹配server中定义的某一个location。

### nginx多次转发后匹配域名server_name
在Nginx中，如果你想要在两次转发之后匹配server_name，你可以在Nginx配置文件中使用proxy_set_header指令将原始的Host头部传递给后端服务器，并确保在第二个location块中使用proxy_pass进行转发。
```
server {
    listen 80;
    server_name example.com;
 
    location /first-stage/ {
        proxy_set_header Host $host;            # 保留原始的 Host 头部
        proxy_set_header X-Original-Host $host; # 额外的请求头，保存原始 Host
        proxy_pass http://backend1;
    }
 
    location /second-stage/ {
        proxy_set_header Host $host;
        proxy_pass http://backend2;
    }
}
```
请求首先到达/first-stage/时，它会被转发到backend1服务器，并保持原始的Host头部不变。当请求到达/second-stage/时，它会被转发到backend2服务器，同样保持Host头部不变。

在后端服务器上，可以根据Host头部来匹配server_name，以便进行适当的处理。
```
server {
    listen 80;
    server_name backend1.com;
    location / {
        # 处理来自第一个阶段的请求
    }
}
 
server {
    listen 80;
    server_name backend2.com;
    location / {
        # 处理来自第二个阶段的请求
    }
}
```
如果二次转发域名等同于本次域名，或直接为IP转发
```
http {
    upstream backend1 {
        server 192.168.1.100:8000; # 替换为实际的IP地址
    }

    upstream backend2 {
        server backend2.example.com; # 或者另一个IP地址
    }
    
    server {
        listen 80;

        server_name www.example1.com; # 最好明确指定要处理的域名

        location / {
            proxy_set_header Host $host;
            # 直接将请求转发到backend1对应的IP地址
            proxy_pass http://backend1;
        }
    }

    server {
        listen 80;

        server_name www.example2.com; # 最好明确指定要处理的域名

        location / {
            proxy_set_header Host $host;
            # 将请求转发到backend2
            proxy_pass http://backend2;
        }
    }
}
```


### 域名和IP和端口
域名是只能80端口吗？

“准确的说,域名什么端口也绑定不了,只能绑定ip地址。你说的80端口是web端口,浏览器访问的默认端口。所以,你用浏览器,浏览器当然只会访问80端口了。

这个不是因为域名解析的问题，是web服务器设置的问题。

如果80端口已被占用，那就没有办法了。

除非你使用隐藏的域名转发，但是实际还要加 `:` 端口

一台服务器可以被2个及以上域名访问，但一个域名不能同时访问2台服务器。

### mac下nginx文件服务器403问题
查看nginx日志
> 2024/06/07 17:27:46 [error] 58322#0: *27 directory index of "/Users/luoxiaosheng/upload/" is forbidden, client: 127.0.0.1, server: localhost, request: "GET / HTTP/1.1", host: "localhost"

指定目录权限
```
[~]$ chmod -R 777 /Users/luoxiaosheng/upload/
sudo chown -R _www:_www /Users/luoxiaosheng/upload/

[~]$ less /usr/local/var/log/nginx/error.log   
[~]$ vim /usr/local/etc/nginx/nginx.conf    
[~]$ sudo nginx -s reload
```

### nginx与Haproxy
Nginx：
* 多功能：Nginx不仅是一款优秀的负载均衡器，还能够作为高性能的Web服务器、反向代理服务器以及缓存服务器使用。
* 配置结构：采用类似编程语言的配置风格，具有清晰的文档结构，易于理解和维护。
* 扩展性：虽然其开源版本提供了丰富的基础功能，但若需要更高级的功能（如会话保持、健康检查等），可能需要借助第三方模块或购买商业版Nginx Plus。

Haproxy：
* 专注负载均衡：Haproxy专精于负载均衡任务，尤其擅长处理HTTP协议，且性能优异。
* 配置方式：采用命令式配置结构，定义和引用的方式可能需要一定的学习曲线，但对于特定负载均衡任务可能更为直接。
* 内置功能：官方版本即支持会话保持、健康检查、多种负载均衡策略等，基础功能覆盖全面，但在扩展性上相比Nginx可能稍显不足，第三方资源相对较少。
  
  



