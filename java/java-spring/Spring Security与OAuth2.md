# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## SpringSecurity与OAuth

### SpringSecurity与OAuth的区别
spring security 主要使用目的为认证与授权 （验证账号密码与是否有权限访问资源） 而spring security以session作为交互
oauth2 主要目的为接入第三方登录  同时提供了以token作为访问权限

### CAS的单点登录和OAuth2的最大区别
>CAS的单点登录时保障客户端的用户资源的安全 。
OAuth2则是保障服务端的用户资源的安全 。
cas登录和OAuth2在流程上的最大区别就是，通过ST或者code去认证的时候，需不需要预先商量好的密码。

### OAuth2 和JWT区别与联系
>• OAuth2用在使用第三方账号登录的情况(比如使用weibo, qq, github登录某个app)
• JWT是用在前后端分离, 需要简单的对后台API进行保护时使用.(前后端分离无session, 频繁传用户密码不安全)
• 应用场景不一样，
OAuth2是一个相对复杂的协议, 有4种授权模式, 其中的access code模式在实现时可以使用jwt才生成code, 也可以不用. 它们之间没有必然的联系；
oauth2有client和scope的概念，jwt没有。如果只是拿来用于颁布token的话，二者没区别。常用的bearer算法oauth、jwt都可以用，只是应用场景不同而已。

参考文章：https://www.jianshu.com/p/63115c71a590

### OAuth接入

oauth2可以分为简易的分为三个步骤
	• 配置资源服务器
	• 配置认证服务器
	• 配置spring security
	
参考文章：https://blog.csdn.net/cowbin2012/article/details/94010892

1. oauth2依赖
```
<!--oauth2start-->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-security</artifactId>
</dependency>
<dependency>
<groupId>org.springframework.cloud</groupId>
<artifactId>spring-cloud-starter-oauth2</artifactId>
</dependency>
<!--不是starter,手动配置-->
<dependency>
<groupId>org.springframework.security.oauth</groupId>
<artifactId>spring-security-oauth2</artifactId>
<version>2.3.2.RELEASE</version>
</dependency>
<!--oauth2end-->
<!--将token存储在redis中-->
<dependency>
<groupId>org.springframework.boot</groupId>
<artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```
2. 配置资源服务器和授权服务器

3. spring security oauth2的认证思路
client模式：没有用户的概念，直接与认证服务器交互，用配置中的客户端信息去申请accessToken，客户端有自己的client_id,client_secret对应于用户的username,password
password模式：自己本身有一套用户体系，在认证时需要带上自己的用户名和密码，以及客户端的client_id,client_secret
4. 配置spring security

### OAuth授权模式
授权模式：
授权码（authorization-code）
隐藏式（implicit）
密码式（password）
客户端凭证（client credentials）

授权码（authorization-code）：先申请一个授权码，然后再用该码获取令牌，适用于那些有后端的 Web 应用
```
申请授权码：
https://b.com/oauth/authorize?
  response_type=code&
  client_id=CLIENT_ID&
  redirect_uri=CALLBACK_URL&
  scope=read
请求令牌：
https://b.com/oauth/token?
 client_id=CLIENT_ID&
 client_secret=CLIENT_SECRET&
 grant_type=authorization_code&
 code=AUTHORIZATION_CODE&
 redirect_uri=CALLBACK_URL
```

隐藏式（implicit）：纯前端应用，没有后端
```
https://b.com/oauth/authorize?
  response_type=token&
  client_id=CLIENT_ID&
  redirect_uri=CALLBACK_URL&
  scope=read
```

密码式（password）：
```
https://oauth.b.com/token?
  grant_type=password&
  username=USERNAME&
  password=PASSWORD&
  client_id=CLIENT_ID
```

客户端凭证（client credentials）：适用于没有前端的命令行应用，即在命令行下请求令牌
```
https://oauth.b.com/token?
  grant_type=client_credentials&
  client_id=CLIENT_ID&
  client_secret=CLIENT_SECRET
```

参考网址：http://www.ruanyifeng.com/blog/2019/04/oauth-grant-types.html

