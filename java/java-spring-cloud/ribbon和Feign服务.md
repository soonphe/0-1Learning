# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## ribbon和Feign服务

一 .ribbon+restTemplate
1.新建module
Cloud Discover——Eureka Discoverry 
Cloud Routing——Ribbon 
Web web

2.启动类注解
@EnableDiscoveryClient


二 .Feign （Feign默认集成了Ribbon）
1.新建module
Cloud Discover——Eureka Discoverry 
Cloud Routing——Feign 
Web web

2.启动类注解
@EnableDiscoveryClient



禁用多个feign客户端：
@FeignClient(value = "service-hi",configuration = Configuration2.class, fallback = SchedualServiceHiHystric.class)
@Configuration
public class Configuration2{

@Bean
public BasicAuthRequestInterceptor basicAuthRequestInterceptor (){
	return new BasicAuthRequestInterceptor ("user","123456");
}

//如果使用 feign.hystrix.enable=false 为全局，为所有的hystrix禁用
//加上这个
@Bean
@Scope("prototype")
public Feign.Builder feignBuilder(){
	return Feign.builder();	//Feign.Builder禁用了HysrixFeign。builder()的方法
}
}
