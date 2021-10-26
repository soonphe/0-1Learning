# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## springSecurity

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
