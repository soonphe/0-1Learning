# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Hystrix断路器

Hystrix断路器：阀值（Hystric 是5秒20次）

一.在ribbon中使用Hystrix
1.添加依赖
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-hystrix</artifactId>
</dependency>

2.启动类注解
@EnableHystrix

3.service注解
    @HystrixCommand(fallbackMethod = "hiError")
    public String hiService(String name) {
        return restTemplate.getForObject("http://SERVICE-HI/hi?name="+name,String.class);
    }

    public String hiError(String name) {
        return "hi,"+name+",sorry,error!";
    }

二.在Feign中使用Hystrix

Feign是自带断路器的，在D版本的Spring Cloud中，它没有默认打开。需要在配置文件中配置打开它，在配置文件加以下代码：

feign.hystrix.enabled=true


三、Hystrix Dashboard (断路器：Hystrix 仪表盘)

基于service-ribbon 改造，Feign的改造和这一样。

首选在pom.xml引入spring-cloud-starter-hystrix-dashboard的起步依赖：

<dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-actuator</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.cloud</groupId>
            <artifactId>spring-cloud-starter-hystrix-dashboard</artifactId>
        </dependency>

在启动类上加上注解
@EnableHystrix
@EnableHystrixDashboard

访问http://localhost:8764/hystrix

填入http://localhost:8764/hi?name=forezp 和参数




