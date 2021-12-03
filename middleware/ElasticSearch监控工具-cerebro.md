# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## ElasticSearch监控工具-cerebro

### 安装
1. 下载解压
https://github.com/lmenezes/cerebro/releases

```
wget https://github.com/lmenezes/cerebro/releases/download/v0.8.1/cerebro-0.8.1.tgz
tar xzf cerebro-0.8.1.tgz
```
2. 启动
```
cerebro-0.8.1/bin/cerebro
[info] play.api.Play - Application started (Prod)
[info] p.c.s.AkkaHttpServer - Listening for HTTP on /0:0:0:0:0:0:0:0:9000
```

3. 指定端口
bin/cerebro -Dhttp.port=8080

4. 配置服务器
非必须：如果经常使用的话，可以先在conf/application.conf中配置好ElasticSearch服务器地址

```
hosts = [
  {
    host = "http://localhost:9200"
    name = "Some Cluster"
  },
  # Example of host with authentication
  #{
  #  host = "http://some-authenticated-host:9200"
  #  name = "Secured Cluster"
  #  auth = {
  #    username = "username"
  #    password = "secret-password"
  #  }
  #}
]
```

5. 使用
浏览器打开连接http://192.168.58.101:9000

