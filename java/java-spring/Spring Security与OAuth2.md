# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


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

### springSecurity接入

1.表结构关系
用户表
角色表
用户角色关联表
权限表
角色权限关联表

2.security认证（authentication）和授权（authorization）
认证方式：
UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
SecurityContextHolder.getContext().setAuthentication(authentication);

过滤器：
jwtAuthenticationTokenFilter()：JWT认证（JWT认证通过则直接授权）
UsernamePasswordAuthenticationFilter：拦截表单请求，用户认证（用户）
FilterSecurityInterceptor：授权判断	//注册：.authorizeRequests()

自定义UserDetails类——AdminUserDetails implements UserDetails
包括用户基本信息和GrantedAuthority列表及其他接口实现方法
config中注册自定义userDetailsService并实现loadUserByUsername方法获取UserDetails

认证授权流程：
1.登录 验证用户名密码 获取权限collection 用户认证 生成token
2.访问 判断token  有则验证token正确性和实效性 再授权
3.授权时不能出现""，权限不能为""值

config注解说明：
@EnableGlobalMethodSecurity	//启动类角色权限
1.prePostEnabled：支持Spring EL表达式，开启后可以使用
@PreAuthorize：方法执行前的权限验证
@PostAuthorize：方法执行后再进行权限验证
@PreFilter：方法执行前对集合类型的参数或返回值进行过滤，移除使对应表达式的结果为false的元素
@PostFilter：方法执行后对集合类型的参数或返回值进行过滤，移除使对应表达式的结果为false的元素

2.secureEnabled : 开启后可以使用
@Secured：用来定义业务方法的安全性配置属性列表

3.jsr250Enabled ：支持JSR标准，开启后可以使用
@RolesAllowed：对方法进行角色验证
@DenyAll：允许所有角色调用
@PermitAll：不允许允许角色调用

方法权限验证注解：
hasAuthority([auth])：等同于hasRole
hasAnyAuthority([auth1,auth2])：等同于hasAnyRole
hasRole([role])：当前用户是否拥有指定角色。
hasAnyRole([role1,role2])：多个角色是一个以逗号进行分隔的字符串。如果当前用户拥有指定角色中的任意一个则返回true


login请求成功：
{
"returnCode": 200,
"returnMsg": "操作成功",
"data": {
"tokenHead": "Bearer",
"token": "eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJhZG1pbiIsImNyZWF0ZWQiOjE1Njk3NDI2OTA2MzMsImV4cCI6MTU3MDM0NzQ5MH0.IpyYnjhchic9Xv_wfJato3KrSngbwmTL9QywMnYacfqOOZgeFpsJvclX7G7GP_xPR75E0i63uUv01n1iw2caUA"
},
"ext": ""
}

#### 如何实现多设备同一时间只允许一个账号登录？
而spring security只需要一行代码搞定：
```
@Override
protected void configure(HttpSecurity http) throws Exception {
http.authorizeRequests()
.anyRequest().authenticated()
.and()
.formLogin()
.loginPage("/login.html")
.permitAll()
.and()
.csrf().disable()
.sessionManagement()
.maximumSessions(1); //只需要将最大会话数设置为 1 即可
}
```


如果相同的用户已经登录了，你不想踢掉他，而是想禁止新的登录操作怎么办？

那也好办，配置方式如下：
```
@Override
protected void configure(HttpSecurity http) throws Exception {
http.authorizeRequests()
.anyRequest().authenticated()
.and()
.formLogin()
.loginPage("/login.html")
.permitAll()
.and()
.csrf().disable()
.sessionManagement()
.maximumSessions(1)
.maxSessionsPreventsLogin(true);
}
```
我们还需要再提供一个 Bean：
```
@Bean
HttpSessionEventPublisher httpSessionEventPublisher() {
return new HttpSessionEventPublisher();
}
```
添加 maxSessionsPreventsLogin 配置即可。此时一个浏览器登录成功后，另外一个浏览器就登录不了了。是不是很简单？

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

