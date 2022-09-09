# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## swagger

### 什么是swagger
github地址：https://github.com/swagger-api/swagger-ui
官网地址：https://swagger.io/tools/swagger-ui/

该存储库发布了三个不同的 NPM 模块：
swagger-ui 是一个传统的 npm 模块，旨在用于能够解析依赖项（通过 Webpack、Browserify 等）的单页应用程序。
swagger-ui-dist 是一个无依赖的模块，它包含了在服务器端项目或无法解析 npm 模块依赖的单页应用程序中提供 Swagger UI 所需的一切。
swagger-ui-react 是将 Swagger UI 打包为 React 组件，用于 React 应用程序。

### java使用swagger——springfox
依赖
```
<dependency>
    <groupId>io.springfox</groupId>
    <artifactId>springfox-boot-starter</artifactId>
    <version>3.0.0</version>
</dependency>
```
从2.0版本迁移
删除显式依赖springfox-swagger2
删除@EnableSwagger2注释
添加springfox-boot-starter依赖
Springfox 3.x 移除了对 guava 和其他 3rd 方库的依赖（还不是零依赖！依赖于 spring 插件和开放的注解和模型的 api 库），所以如果你使用了 guava 谓词/函数，那些将需要转换到 java 8 函数接口
如果您正在使用 WebMvc 但尚未使用@EnableWebMvc注解，请添加此注解。

### 自动配置
引入依赖，添加配置即可
```
logging.level.springfox.documentation=DEBUG
springfox.documentation.swagger-ui.base-url=/documentation
server.servlet.context-path=/mvc
springfox.documentation.swagger.v2.use-model-v3=false
```
启动类注解bean：
```
  @Bean
  public SecurityConfiguration securityConfiguration() {
    return SecurityConfigurationBuilder.builder()
        .enableCsrfSupport(true)
        .build();
  }
```


### 手动配置
启动类或配置类注解添加：
```
@EnableSwagger //Enable swagger 1.2 spec
@EnableSwagger2 //Enable swagger 2.0 spec
@EnableOpenApi //Enable open api 3.0.3 spec
```

手动配置
```
    //2.0版本
    @Bean
    public Docket createRestApi(){
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.soonphe.ywzt.hsf.gateway"))
                .paths(PathSelectors.any())
                .build();
    }
  
  //3.0版本  
  @Bean
  public Docket openApiPetStore() {
    return new Docket(DocumentationType.OAS_30)
        .groupName("open-api-pet-store")
        .select()
        .paths(petstorePaths())
        .build();
  }

    private ApiInfo apiInfo() {
        return new ApiInfoBuilder()
                .title("springfox")
                .description("springfox")
                .termsOfServiceUrl("http://springfox.io")
                .contact(new Contact("springfox", "", ""))
                .license("Apache License Version 2.0")
                .version("1.0")
                .build();
    }
```
Docket参数说明：
- .groupName("full-petstore-api")：分组名
- .apis(RequestHandlerSelectors.basePackage("com.xxx.xxx)):匹配路径断言
- .paths(PathSelectors.any())：路径匹配，PathSelectors.any()为全路径，也可以匹配部分路径：regex(".*/category.*").or(regex(".*/category").or(regex(".*/categories")));
- .ignoredParameterTypes(ApiIgnore.class)：添加忽略的控制器方法参数类型，以便框架不会为这些特定类型生成 swagger 模型或参数信息
- .enableUrlTemplating(true);：决定是否对路径使用 url 模板
- .securitySchemes(Collections.singletonList(oauth()))：security鉴权模块
- .securityContexts(Collections.singletonList(securityContext()));：security鉴权模块

常见如何定义securitySchemes和securityContexts
```
    AuthorizationScope[] authScopes = new AuthorizationScope[1];
    authScopes[0] = new AuthorizationScopeBuilder()
        .scope("read")
        .description("read access")
        .build();
    SecurityReference securityReference = SecurityReference.builder()
        .reference("test")
        .scopes(authScopes)
        .build();
    List<SecurityContext> securityContexts =
        Collections.singletonList(
            SecurityContext.builder()
                .securityReferences(Collections.singletonList(securityReference))
                .build());
    ...
    
    .securitySchemes(Collections.singletonList(new BasicAuth("test")))
    .securityContexts(securityContexts)
```

显示swagger-ui.html文档展示页，还必须注入swagger资源：
```
@Component
public class SwaggerUiWebMvcConfigurer implements WebMvcConfigurer {
  private final String baseUrl;

  public SwaggerUiWebMvcConfigurer(
      @Value("${springfox.documentation.swagger-ui.base-url:}") String baseUrl) {
    this.baseUrl = baseUrl;
  }

  @Override
  public void addResourceHandlers(ResourceHandlerRegistry registry) {
    String baseUrl = StringUtils.trimTrailingCharacter(this.baseUrl, '/');
    registry.
        addResourceHandler(baseUrl + "/swagger-ui/**")
        .addResourceLocations("classpath:/META-INF/resources/webjars/springfox-swagger-ui/")
        .resourceChain(false);
  }

  @Override
  public void addViewControllers(ViewControllerRegistry registry) {
    registry.addViewController(baseUrl + "/swagger-ui/")
        .setViewName("forward:" + baseUrl + "/swagger-ui/index.html");
  }

  @Override
  public void addCorsMappings(CorsRegistry registry) {
    registry
        .addMapping("/api/pet")
        .allowedOrigins("http://editor.swagger.io");
    registry
        .addMapping("/v2/api-docs.*")
        .allowedOrigins("http://editor.swagger.io");
  }
}
```

关闭Swagger有两种方式
- 方式一：
在Swagger2Config上使用@Profile注解标识，@Profile({“dev”,“test”})表示在dev和test环境才能访问swagger-ui.html，prod环境下访问不了。

- 方式二：
在Swagger2Config上使用@ConditionalOnProperty注解，

@ConditionalOnProperty(name = “swagger.enable”, havingValue = “true”)

表示配置文件中如果swagger.enable =true表示开启。所以只需要在开发环境的配置文件配置为true，生产环境配置为false即可。

推荐第一种方式，因为第二种方式还要在每个环境文件中去配置，并维护；Swagger一般用于开发和测试环境，所以直接限制Swagger启用的环境为dev和test即可，这样也不需要再维护配置文件了。

------

## knife4j

### 什么是knife4j？
knife4j 就是 swagger的升级版， 除了美化了swagger的界面。而且还有其他的增强功能

### 增强功能有哪些？
tags分组标签排序、api接口排序、markdown文档下载、权限控制

### 注意
聚合服务的文档需要用到gateway，所以想搭建聚合服务文档应先搭建网关
一个版本的 knife4j 有一种配置方法， 不可将不同版本knife4j的配置方式混在一起
使用排序时，需要先在文档页面进行设置： 访问文档访问地址->文档管理->个性化设置->将“启用Knife4j提供的增强功能”勾选即可
使用权限控制时， 网关不需要单独配置yml文件。但是需要权限控制的服务需要用到 yml 的文件配置
gateway是根据配置的路由去映射文档的。 请不要忘记添加映射的路由

##无论是网关还是其他服务，都引用如下maven
```
        <dependency>
            <groupId>com.github.xiaoymin</groupId>
            <artifactId>knife4j-spring-boot-starter</artifactId>
        </dependency>
```

### 网关配置
```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.beans.factory.annotation.Value;
import org.springframework.cloud.gateway.route.RouteLocator;
import org.springframework.stereotype.Component;
import springfox.documentation.swagger.web.SwaggerResource;
import springfox.documentation.swagger.web.SwaggerResourcesProvider;

import java.util.ArrayList;
import java.util.HashSet;
import java.util.List;
import java.util.Set;

/**
 * 在集成Spring Cloud Gateway网关的时候,会出现没有basePath的情况(即定义的例如/user、/order等微服务的前缀),
 *
 * 这个情况在使用zuul网关的时候不会出现此问题,因此,在Gateway网关需要添加一个Filter实体Bean
 *
 * @author qiusn
 * @date 2021-03-04
 */
@Component
public class SwaggerProvider implements SwaggerResourcesProvider {
    /**
     * 接口地址
     */
    public static final String API_URI = "/v2/api-docs";

    /**
     * 路由加载器
     */
    @Autowired
    private RouteLocator routeLocator;

    /**
     * 网关应用名称
     */
    @Value("${spring.application.name}")
    private String applicationName;

    @Override
    public List<SwaggerResource> get() {
        //接口资源列表
        List<SwaggerResource> resources = new ArrayList<>();
        //服务名称列表
        List<String> routeHosts = new ArrayList<>();
        // 获取所有可用的应用名称
        routeLocator.getRoutes().filter(route -> route.getUri().getHost() != null)
                .filter(route -> !applicationName.equals(route.getUri().getHost()))
                .subscribe(route -> routeHosts.add(route.getUri().getHost()));
        // 去重，多负载服务只添加一次
        Set<String> existsServer = new HashSet<>();
        routeHosts.forEach(host -> {
            // 拼接url
            String url = "/" + host + API_URI;
            //不存在则添加
            if (!existsServer.contains(url)) {
                existsServer.add(url);
                SwaggerResource swaggerResource = new SwaggerResource();
                swaggerResource.setUrl(url);
                swaggerResource.setName(host);
                resources.add(swaggerResource);
            }
        });
        return resources;
    }

}
```
```
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;
import reactor.core.publisher.Mono;
import springfox.documentation.swagger.web.*;

import java.util.Optional;

/**
 * swagger访问接口
 *
 * @author qiusn
 * @date 2021-03-04
 **/
@RestController
@RequestMapping("/swagger-resources")
public class SwaggerHandler {

    /**
     * 权限配置
     */
    @Autowired(required = false)
    private SecurityConfiguration securityConfiguration;

    @Autowired(required = false)
    private UiConfiguration uiConfiguration;

    private final SwaggerResourcesProvider swaggerResources;

    @Autowired
    public SwaggerHandler(SwaggerResourcesProvider swaggerResources) {
        this.swaggerResources = swaggerResources;
    }


    @GetMapping("/configuration/security")
    public Mono<ResponseEntity<SecurityConfiguration>> securityConfiguration() {
        return Mono.just(new ResponseEntity<>(
                Optional.ofNullable(securityConfiguration).orElse(SecurityConfigurationBuilder.builder().build()), HttpStatus.OK));
    }

    @GetMapping("/configuration/ui")
    public Mono<ResponseEntity<UiConfiguration>> uiConfiguration() {
        return Mono.just(new ResponseEntity<>(
                Optional.ofNullable(uiConfiguration).orElse(UiConfigurationBuilder.builder().build()), HttpStatus.OK));
    }

    /**
     * 获取接口信息
     */
    @GetMapping("")
    public Mono<ResponseEntity> swaggerResources() {
        return Mono.just((new ResponseEntity<>(swaggerResources.get(), HttpStatus.OK)));
    }
}
```

### 网关.yml文件
```
server:
  port: 8333

spring:
  application:
    name: gateway-service
  cloud:
    gateway:
      discovery:
        locator:
          enabled: true # 启用自动根据服务ID生成路由
          lower-case-service-id: true # 设置路由的路径为小写的服务ID
      routes:
        - id: order-service
          uri: lb://order-service
          predicates:
            - Path=/api/order/**
        - id: user-service
          uri: lb://user-service
          predicates:
            - Path=/api/user/**
```

### 其他服务配置
```
import com.github.xiaoymin.knife4j.spring.annotations.EnableKnife4j;
import com.google.common.base.Predicates;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import springfox.documentation.builders.ApiInfoBuilder;
import springfox.documentation.builders.PathSelectors;
import springfox.documentation.builders.RequestHandlerSelectors;
import springfox.documentation.service.ApiInfo;
import springfox.documentation.spi.DocumentationType;
import springfox.documentation.spring.web.plugins.Docket;
import springfox.documentation.swagger2.annotations.EnableSwagger2;


/**
 * 配置Swagger主页信息
 *
 * @author qiusn
 * @date 2021-03-05
 */
@Configuration
@EnableSwagger2
@EnableKnife4j
public class SwaggerConfig {


    /**
     * 创建RestApi 并包扫描controller
     */
    @Bean
    public Docket createRestApi() {
        return new Docket(DocumentationType.SWAGGER_2)
                .apiInfo(apiInfo())
                .select()
                .apis(RequestHandlerSelectors.basePackage("com.qsn.order"))
                .paths(PathSelectors.any())
                .paths(Predicates.not(PathSelectors.regex("/error.*")))
                .build();
    }

    /**
     * 创建Swagger页面 信息
     *
     * @return
     */
    private ApiInfo apiInfo() {
        return new ApiInfoBuilder().
                title("订单模块").
                contact("小凡").
                version("1.0 version").
                termsOfServiceUrl("api/order/**").
                description("用户模块-这是一个 cloud+nacos+gateway+knife4j 的项目")
                .build();
    }

}
```
### 其他服务.yml文件
```
knife4j:
  # 开启Swagger的Basic认证功能,默认是false
  # 注：（1）默认账号/密码  admin/123321; （2）但是如果不配置密码。 即使输入对了，也始终在输入密码的地方重新循环;（3）如果用浏览器记住密码了则不用输入， swagger会直接读取进去不会再手动输入一次；
  basic:
    enable: true
    # Basic认证用户名
    username: test
    # Basic认证密码
    password: 1234567
## 开启生产环境屏蔽(true看不到文档；false可以看到文档，但是密码失效)
#  production: false
```