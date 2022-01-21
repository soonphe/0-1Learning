# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java参数校验工具

### javax之@Valid（标准JSR-303规范）
- @Valid注解用于校验，所属包为：javax.validation.Valid。jdk提供的类包，都是在rt.jar中
- @Valid：可以用在方法、构造函数、方法参数和成员属性（field）上。

- 规则介绍：

|限制|	说明|
|---|---|
|@Null	|限制只能为null|
|@NotNull	|限制必须不为null|
|@AssertFalse	|限制必须为false|
|@AssertTrue	|限制必须为true|
|@DecimalMax(value)	|限制必须为一个不大于指定值的数字|
|@DecimalMin(value)	|限制必须为一个不小于指定值的数字|
|@Digits(integer,fraction)	|限制必须为一个小数，且整数部分的位数不能超过integer，小数部分的位数不能超过fraction|
|@Future	|限制必须是一个将来的日期|
|@Max(value)	|限制必须为一个不大于指定值的数字|
|@Min(value)	|限制必须为一个不小于指定值的数字|
|@Past	|限制必须是一个过去的日期|
|@Pattern(value)	|限制必须符合指定的正则表达式|
|@Size(max,min)	|限制字符长度必须在min到max之间|
|@Past	|验证注解的元素值（日期类型）比当前时间早|
|@NotEmpty	|验证注解的元素值不为null且不为空（字符串长度不为0、集合大小不为0）|
|@NotBlank	|验证注解的元素值不为空（不为null、去除首位空格后长度为0），不同于@NotEmpty，@NotBlank只应用于字符串且在比较时会去除字符串的空格|
|@Email	|验证注解的元素值是Email，也可以通过正则表达式和flag指定自定义的email格式|

- 使用示例：
```
/**
 * 实体bean声明需要被校验的字段
 */
public class User {
   
    @NotBlank(message = "密码不能为空")
    private String password;
    
    @Min(value = 18,message = "年龄不合法")
    private Integer age;
}
```
```
/**
 * 方法添加注解@Valid,并且需要传入BindingResult对象，用于获取校验失败情况下的反馈信息
 */
@PostMapping("/users")
public User addUser(@RequestBody @Valid User user, BindingResult bindingResult) {
  if(bindingResult.hasErrors()){
      System.out.println(bindingResult.getFieldError().getDefaultMessage());
      return null;
  }
  return userResposity.save(user);
}
```
- 拓展：
除此之外还可以自定义验证信息的要求，自定义注解并实现 ConstraintValidator<MyConstraint, Object>接口


### Spring Validation 之 @Validated（Spring’s JSR-303 规范，是标准 JSR-303 的一个变种）
- spring-boot中可以用@validated来校验数据，所属包为：org.springframework.validation.annotation;

- 规则介绍：
@AssertFalse 校验false
@AssertTrue 校验true
@DecimalMax(value=,inclusive=) 小于等于value，
inclusive=true,是小于等于
@DecimalMin(value=,inclusive=) 与上类似
@Max(value=) 小于等于value
@Min(value=) 大于等于value
@NotNull  检查Null
@Past  检查日期
@Pattern(regex=,flag=)  正则
@Size(min=, max=)  字符串，集合，map限制大小
@Validate 对po实体类进行校验

- 使用示例：
```
/**
 * 实体bean声明需要被校验的字段
 */
@data
public class XoPO{
    
    @validated
    private List<OrderPerson> personList;
    
    @NotNull
    @Size(max=32,message="code is null")
    private String code;

    @NotBlank
    @Size(max=32,message="product is null")
    private String product;
}

/**
 * 方法上添加注解：@Validated
 */
@RequestMapping(value="/url.json",method= {RequestMethod.POST})
@ResponseBody
@Transactional
public Result<?> xxmethod( @RequestBody @Validated  XoPO xoPo)     
    throws ParseException, UnsupportedEncodingException {}
```
当输入不能满足条件是，就会抛出异常，而后统一由异常中心处理.
也可以用BindingResult，但是用了这个后就必须手动处理异常，侵入了正常的逻辑过程，并不推荐


### @Validated和@Valid区别
在检验入参是否符合规范时，使用 @Validated 或者 @Valid 在基本验证功能上没有太多区别。但是在分组、注解地方、嵌套验证等功能上两个有所不同：
不同点：
1. 分组
   @Validated：提供分组功能，可以在入参验证时，根据不同的分组采用不同的验证机制。
   @Valid：没有分组功能。

2. 注解地方
   @Validated：用在类型、方法和方法参数上。但不能用于成员属性（field)。
   @Valid：可以用在方法、构造函数、方法参数和成员属性（field）上。
   两者是否能用于成员属性（字段）上直接影响能否提供嵌套验证的功能。

3. 嵌套验证：
   在比较两者嵌套验证时，先说明下什么叫做嵌套验证。

比如我们现在有个实体叫做Item：   
```
public class Item {

    @NotNull(message = "id不能为空")
    @Min(value = 1, message = "id必须为正整数")
    private Long id;

    @NotNull(message = "props不能为空")
    @Size(min = 1, message = "至少要有一个属性")
    private List<Prop> props;
}   
```
Item带有很多属性，属性里面有属性id，属性值id，属性名和属性值，如下所示：
```
public class Prop {

    @NotNull(message = "pid不能为空")
    @Min(value = 1, message = "pid必须为正整数")
    private Long pid;

    @NotNull(message = "vid不能为空")
    @Min(value = 1, message = "vid必须为正整数")
    private Long vid;

    @NotBlank(message = "pidName不能为空")
    private String pidName;

    @NotBlank(message = "vidName不能为空")
    private String vidName;
}
```
属性这个实体也有自己的验证机制，比如属性和属性值id不能为空，属性名和属性值不能为空等。

现在我们有个 ItemController 接受一个Item的入参，想要对Item进行验证，如下所示：
```
@RestController
public class ItemController {

    @RequestMapping("/item/add")
    public void addItem(@Validated Item item, BindingResult bindingResult) {
        doSomething();
    }
}
```
在上图中，如果Item实体的props属性不额外加注释，只有@NotNull和@Size，无论入参采用@Validated还是@Valid验证，Spring Validation框架只会对Item的id和props做非空和数量验证，不会对props字段里的Prop实体进行字段验证，也就是@Validated和@Valid加在方法参数前，都不会自动对参数进行嵌套验证。也就是说如果传的List中有Prop的pid为空或者是负数，入参验证不会检测出来。

为了能够进行嵌套验证，必须手动在Item实体的props字段上明确指出这个字段里面的实体也要进行验证。由于@Validated不能用在成员属性（字段）上，但是@Valid能加在成员属性（字段）上，而且@Valid类注解上也说明了它支持嵌套验证功能，那么我们能够推断出：@Valid加在方法参数时并不能够自动进行嵌套验证，而是用在需要嵌套验证类的相应字段上，来配合方法参数上@Validated或@Valid来进行嵌套验证。
我们修改Item类如下所示：
```
public class Item {

    @NotNull(message = "id不能为空")
    @Min(value = 1, message = "id必须为正整数")
    private Long id;

    @Valid // 嵌套验证必须用@Valid
    @NotNull(message = "props不能为空")
    @Size(min = 1, message = "props至少要有一个自定义属性")
    private List<Prop> props;
}
```
然后我们在ItemController的addItem函数上再使用@Validated或者@Valid，就能对Item的入参进行嵌套验证。此时Item里面的props如果含有Prop的相应字段为空的情况，Spring Validation框架就会检测出来，bindingResult就会记录相应的错误。

总结一下 @Validated 和 @Valid 在嵌套验证功能上的区别：

- @Validated： 用在方法入参上无法单独提供嵌套验证功能。不能用在成员属性（字段）上，也无法提示框架进行嵌套验证。能配合嵌套验证注解@Valid进行嵌套验证。

- @Valid： 用在方法入参上无法单独提供嵌套验证功能。能够用在成员属性（字段）上，提示验证框架进行嵌套验证。能配合嵌套验证注解@Valid进行嵌套验证。

### jodd参数校验
- 官方网站（网站和文档）：https://jodd.org/
- https://jodd.org/uphea/
- GitHub：http : //oblac.github.io/jodd
- Jodd微框架：http : //joddframework.org


Jodd分成许多模块，所以选择使用什么。一些工具和实用程序模块是：

- jodd-core包含许多实用程序，包括JDateTime。
- jodd-bean，我们臭名昭着的BeanUtil型式检查员和转换器。
- jodd-props是Java的超级替代品Properties。
- jodd-mail 更轻松地发送电子邮件
- jodd-upload，处理HTTP上传。
- jodd-servlet 与许多servlet实用程序，包括漂亮的标签库。
- jodd-http，小HTTP客户端。

和一些微框架：

- jodd-madvoc - 漂亮的MVC框架。
- jodd-petite - 务实的DI容器。
- jodd-lagarto- 带有Jerry和的HTML解析器CSSelly。
- jodd-decora - 页面装饰。
- jodd-htmlstapler - 静态页面资源处理程序。
- jodd-proxetta- 动态代理和Paramo。
- jodd-db - 薄的数据库层和对象映射器。
- jodd-json - JSON解析器和序列化器。
- jodd-vtor - 验证框架。

1. pom依赖：
```
    <dependency>
      <groupId>org.jodd</groupId>
      <artifactId>jodd-vtor</artifactId>
    </dependency>
  </dependencies>
```
2. 参数注解：
```
public class TestRequest extends Serializable {

   @NotBlank(message = "id不能为空")
   private String id;
   
   @NotBlank(message = "名称不能为空")
   private String name;
}
```
3. 规则校验
```
   public String queryOrdOrderConsumerList(OrderConsumeRequest request) {
      List<Violation> validates = Vtor.create().validate(request);
      return !CollectionUtils.isEmpty(validates) ? validates.get(0).getCheck().getMessage() : null;
   }
```


