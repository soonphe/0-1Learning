# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## JWT

### JWT和session的区别
    session：服务端存储登录态（多台设备只有一次登录有效）
    JWT：客户端存储登录态（实现单点登录，一次登录共享多个系统）

### JWT格式
* JWT格式：header.payload.signature
* header存放签名算法：{"alg": "HS512"}
* payload存放用户名，token生成时间和过期时间：{"sub":"admin","created":1489079981393,"exp":1489684781}
* signature为以header和payload生成的签名：一旦header和payload被篡改，验证将失败
* 例：String signature = HMACSHA512(base64UrlEncode(header) + "." +base64UrlEncode(payload),secret)


### 真正的请求格式
Authorization : Bearer xxxxxxxxxxx

### token

配置参数：
~~~~
jwt:
  tokenHeader: Authorization #JWT存储的请求头
  secret: mySecret #JWT加解密使用的密钥
  expiration: 604800 #JWT的超期限时间(60*60*24)
  tokenHead: Bearer  #JWT负载中拿到开头
~~~~
  
token生成：
1. 参数使用map构造
        Map<String, Object> claims = new HashMap<>();
        claims.put(CLAIM_KEY_USERNAME, userDetails.getUsername());	//sub：用户名
        claims.put(CLAIM_KEY_CREATED, new Date());	//created：创建时间
        
2. 生成token
        return Jwts.builder()
                .setClaims(claims)	//参数对象
                .setExpiration(generateExpirationDate())	//过期时间(例：new Date(System.currentTimeMillis() + expiration * 1000))
                .signWith(SignatureAlgorithm.HS512, secret)	//签名算法+秘钥
                .compact();


3. token解析获取用户名：
~~~~
String authToken = authHeader.substring(this.tokenHead.length());	// 去除JWT请求头"Bearer "
String username = jwtTokenUtil.getUserNameFromToken(authToken);     //从token中获取登录用户名
    //getUserNameFromToken实际方法：
            claims = Jwts.parser()
                    .setSigningKey(secret)	//用秘钥解密
                    .parseClaimsJws(token)	//解析token
                    .getBody();
            username =  claims.getSubject();    //从token中获取登录用户名
                    claims.getExpiration();     //从token中获取过期时间
~~~~

4. 认证流程
先去除Bearer (携带空格)，再对剩下的字符串进行解析
先获取username，再根据username获取userDetails，再验证token和userDetails，再授权
//用户认证
UsernamePasswordAuthenticationToken authentication = new UsernamePasswordAuthenticationToken(userDetails, null, userDetails.getAuthorities());
authentication.setDetails(new WebAuthenticationDetailsSource().buildDetails(request));
LOGGER.info("authenticated user:{}", username);
SecurityContextHolder.getContext().setAuthentication(authentication);
例：
test：
Bearer eyJhbGciOiJIUzUxMiJ9.eyJzdWIiOiJ0ZXN0IiwiY3JlYXRlZCI6MTU2OTgwNzEyMjk0MywiZXhwIjoxNTcwNDExOTIyfQ.JBgoRw1v1mUKMPG3F1azb_x8PM7T8ebW42azmRDjKDau063D0WtNsZV3qT52uYURGZe1N49KGy_0KnNsORUa9A



