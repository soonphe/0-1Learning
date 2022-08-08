# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java编码规范

### 一 命名规范

#### project命名规范
产线-业务名称-项目名称(根据需要可酌情调整)

由一个小写单词和`-`组成,严禁中英文混搭，也不要直接用中文拼音，除非是国际通用的名词.

- 例:business-replacement-base

#### package命名规范
com.公司名.产线.业务名称.项目名称(根据需要可酌情调整)

由一个小写单词和`.`组成,严禁中英文混搭，也不要直接用中文拼音，除非是国际通用的名词.

- 例:com.xxx.business.project.framework

#### class命名规范
- 类名严格按照驼峰命名发命名，首字母必须大写，做到望名知意，不要怕类名太长但是严禁用中文拼音加英文单词组合类名（显得太low）。
  - 例：反例：WeixinManager  正例：WeChatManager

- 抽象类命名使用Abstract或Base开头

- 对于接口和实现类的命名，实现类需要用impl后缀用来接口区别。
  - 正例 ：CacheServiceImpl 实现 CacheService 接口

#### function命名规范
方法名严格按照驼峰命名发命名，首字母必须小写，做到望名知意即可。

- 反例：Update
- 正例：updateWeChatInfo

#### config命名规范
例：mq.ons.业务名称.项目名称.模块名称.topic

#### database命名规范
> 见本项目mysql开发规范


### 二 注释规范

#### 类（接口）注释
类（接口）的注释必须准确，否者对后期维护害大于利
```
/**
 * @projectName:        ${PROJECT_NAME}  
 * @package:              ${PACKAGE_NAME}
 * @className:           ${NAME}
 * @author:                 ${USER}
 * @date:                    ${DATE}  ${TIME}
 * @description: 
 * @version:                1.0
 */
```

#### 方法注释
方法注释一般需要说明方法用途，入参说明，返参说明，及其他注意补充说明
```
/**
 *
 * @param  $param$
 * @return  $return$
 */
```

#### 行注释
- 行注释没有明确的规定但是按照惯例一般注释要说明的代码的上方,务必保证注释的准确性。
- 软性要求：(1):每五行代码一句注释。(2):调用其他方法时需要一句注释。
                

### 三 常用编码的规范

- 与业务无关的代码，禁止写到业务代码中
- 尽早返回内容，代码简洁易读
- 创建对象尽可能晚、销毁对象尽可能早
- 方法的长度不要超过80行
- 相同的代码块出现在两个以及两个以上的方法里就要进行代码的抽离
- 不要使用递归
- 业务数据标识字段，必须与定义方保持一致，禁止同名不同类型，禁止进行类型转换（如将int转string（或long转int），因后者存储时有长度限制，后续定义方可能扩容）
- 常量命名必须大写且一定要加注释说明
- 常量的定义要根据具体业务区分，不能所有常量定义在一个类中。
- 不允许任何魔法值直接出现在代码中。包括不限于String和int等。
- 枚举的构造方法不要public，禁止提供set方法。避免枚举被重新创建或修改属性
- 对外提供的接口状态类等严禁直接使用int或者integer类型，必须定义成枚举类型
  - 反例：private Integer status;//0:"ff",1:"zz";
  - 正例：private OrderStatusEnum orderStatus;

- 不允许多个短语写在一行中，即一行只写一条语句。 反例：user.getPhone().getLen(); 正例：String phone=user.getPhone();    int lengt = phone.getlen();
- if、for、do、while、case、switch、default等语句自占一行。  
    且if、for、do、while等语句的执行语句部分无论多少都要加括号{}。
- if else复杂度最多只允许套3层
- 数组的命名应该以类型 byte[] buffer 而不是 byte buffer[]。
- package imports的时候最好不要出现“*”

- boolean类型的字段定义时严禁在字段前加is。 反例：isSuccess 正例：success 。RPC框架在反向解析的时候，“以为”对应的属性名称是 success，导致属性获取不到，进而抛出异常。
- 线程资源必须通过线程池提供，不允许在应用中自行显式创建线程。
说明:使用线程池的好处是减少在创建和销毁线程上所花的时间以及系统资源的开销，解决资源不足的问题。如果不使用线程池，有可能造成系统创建大量同类线程而导致消耗完内存或者“过度切换”的问题。

- 封装类必须使用equals比较
  Integer a = 155;  Integer b = 155;
  反例 a == b
  正例 a.equals(b)
- 比较的时候要把有值的放在前面
  反例：s.equals("ss");
  正例："ss".equals(s);说明"ss"属于魔法值要单独定义出来

- 使用orm或者原生mybatis的时候必须使用#{},严禁使用${}。防止sql注入，说明：#{}是预编译处理，$ {}是字符串替换
- 分页查询以及list查询必须限制单次查询的条数。严禁超过1000，如果不传默认设置200
- 一个类的长度不要超过500行，如果超过500行了就要拆分一些方法出来，可以按照功能性聚合或者业务性聚合。

- 禁止对对象使用copy类的方法   如果源类或目标类的成员变量类型被修改，复制将不生效，并且很可能没人注意到此情况。或源&目标各自加了同名成员变量但非相同含义。
  - 解决：定义一个Builder类，使用一个静态方法编码复制对象的每个属性：有变更类型编辑器将报错
- 禁止吞掉异常信息：包括不打印或不重新抛出或不重新包装抛出，仅输出e.getMessage等

- Map仅适用于私有方法，用于公开方法或接口必须严格Review（容易出现，只放数据，不用了之后无人管理）
- 日志输出使用toString，不要使用json（使用lombok生成toString，避免使用json的负面问题	）
- 引用新的jar/升级版本，注意检查是否有关联jar被动升级（默认禁止升级）可能引起关联jar错误升级造成业务风险
- 禁用StringBuffer，用StringBuilder代替（性能+没有在多线程下拼接同一字符串的场景	）
- 禁止缓存其它业务和团队的数据（将多方数据重新组合例外）
- 如果项目中有需要调用其他项目的接口。必须封装一层严禁在逻辑代码中直接调用其他接口。
- 外部数据零信任，因此需要在入口处做检查（类型、空值等）包括不限于前端或第三方传入、RPC、DB/缓存/MQ等 包括不限于对数据类型、空值等的检查


### 四 日志规范
1、进出逻辑复杂的方法都必须加日志。
2、每个日志必须可以被追诉。
反例：logger.info("开始更新用户的信息");
正例：logger.info("开始更新用户的信息userId=[{}]",userId);
3、任何被catch到的异常都应该打日志，并且是应该是error级别的。
4、避免大量地输出无效日志，不利于系统性能提升，也不利于快速定位错误点。
5、谨慎地记录日志。生产环境禁止输出 debug日志；
6、日志文件推荐至少保存15天，因为有些异常具备以“周”为频次发生的特点。


### 五 异常处理
1. 不要捕获 Java 类库中定义的继承自 RuntimeException 的运行时异常类，如:IndexOutOfBoundsException / NullPointerException，这类异常由程序员预检查来规避，保证程序健壮性。
正例:if(obj != null) {...}
反例:try { obj.method() } catch(NullPointerException e){...}
2. 异常不要用来做流程控制，条件控制，因为异常的处理效率比条件分支低。
3. 对大段代码进行 try-catch，这是不负责任的表现。catch 时请分清稳定代码和非稳定代码，稳定代码指的是无论如何不会出错的代码。对于非稳定代码的 catch 尽可能进行区分异常类型，再做对应的异常处理。
4. 捕获异常是为了处理它，不要捕获了却什么都不处理而抛弃之，如果不想处理它，请将该异常抛给它的调用者。最外层的业务使用者，必须处理异常，将其转化为用户可以理解的内容。
5. 有 try 块放到了事务代码中，catch 异常后，如果需要回滚事务，一定要注意手动回滚事务。
6. finally 块必须对资源对象、流对象进行关闭，有异常也要做 try-catch。说明:如果 JDK7，可以使用 try-with-resources 方式。
7. 不能在 finally 块中使用 return，finally 块中的 return 返回后方法结束执行，不会再执行 try 块中的 return 语句。
说明：执行顺序：try finally，执行结果：当函数执行finally时，finally中的return会覆盖try中的return
8. 捕获异常与抛异常，必须是完全匹配，或者捕获异常是抛异常的父类。说明:如果预期对方抛的是绣球，实际接到的是铅球，就会产生意外情况。
9. 对外提供的http/https接口方法必须要用一个try catch兜底。
10. 对外接口的接口、RPC调用应进行try/catch，对catch的异常重新包装为本服务的特定兜底异常抛出（与查询结果为null区分开，以供上层业务处理）

### 六 代码（仓库）规范

#### git规范
默认5️个分支，主干master/预发pre/sit集成测试/开发dev/测试stable
个人开发分支只在本地，不需要提到git上
线上bug修复默认从master拉一个bugfix分支，验证通过后合并到dev提交给测试验证，测试验证通过后合到pre分支验证。最后上线。pre/master的合并需要每组的leader去负责。

#### maven依赖规范
所有 pom 文件中的依赖声明放在<dependencies>语句块中，所有版本仲裁放在<dependencyManagement>语句块中。
说明:<dependencyManagement>里只是声明版本，并不实现引入，因此子项目需要显式的声明依赖，version 和 scope 都读取自父 pom。而<dependencies>所有声明在主 pom 的<dependencies>里的依赖都会自动引入，并默认被所有的子项目继承。

