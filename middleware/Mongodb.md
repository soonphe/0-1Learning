# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Mongodb

1.下载Mongodb安装包，下载地址：https://fastdl.mongodb.org/win32/mongodb-win32-x86_64-2008plus-ssl-3.2.21-signed.msi
2.下载客户端程序：https://download.robomongo.org/1.2.1/windows/robo3t-1.2.1-windows-x86_64-3e50a65.zip
3.依赖
<dependency> 
<groupId>org.springframework.boot</groupId> 
<artifactId>spring-boot-starter-data-mongodb</artifactId> 
</dependency>
4.数据操作方式
①继承MongoRepository<MemberReadHistory,String>接口，获取数据操作方法
②衍生查询：定义接口方法 findByNameOrSubTitleOrKeywords
③@Query注解
@Query("{ 'memberId' : ?0 }") 
List<MemberReadHistory> findByMemberId(Long memberId);
5.常用注解：
@Document:标示映射到Mongodb文档上的领域对象
@Id:标示某个域为ID域
@Indexed:标示某个字段为Mongodb的索引字段

调试：
存储多级结构json存储
增删改查（已完成）

MongoRepository的缺点是不够灵活，MongoTemplate正好可以弥补不足

MongoTemplate
Repository 接口是 Spring Data 的一个核心接口，它不提供任何方法，开发者需要在自己定义的接口中声明需要的方法 
public interface Repository<T, ID extends Serializable> { } 
Spring Data可以让我们只定义接口，只要遵循 Spring Data的规范，就无需写实现类。  
与继承 Repository 等价的一种方式，就是在持久层接口上使用 @RepositoryDefinition 注解。

Repository 提供了最基本的数据访问功能，其几个子接口则扩展了一些功能。它们的继承关系如下： 
Repository： 仅仅是一个标识，表明任何继承它的均为仓库接口类
CrudRepository： 继承 Repository，实现了一组 CRUD 相关的方法 
PagingAndSortingRepository： 继承 CrudRepository，实现了一组分页排序相关的方法 
MongoRepository： 继承 PagingAndSortingRepository，实现一组 mongodb规范相关的方法



MongoTemplate是数据库和代码之间的接口，对数据库的操作都在它里面。
MongoTemplate核心操作类：Criteria和Query 
Criteria类：封装所有的语句，以方法的形式查询。
Query类：将语句进行封装或者添加排序之类的操作
MongoTemplate提供了非常多的操作MongoDB的方法。 它是线程安全的，可以在多线程的情况下使用。

