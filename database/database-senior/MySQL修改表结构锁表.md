# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## MySQL修改表结构锁表

### 关于DDL、DML和DCL
DDL（Data Definition Languages）语句：数据定义语言，这些语句定义了不同的数据段、数据库、表、列、索引等数据库对象的定义。
常用的语句关键字主要包括 create、drop、alter 等。

DML（Data Manipulation Language）语句：数据操纵语句，用于添加、删除、更新和查询数据库记录，并检查数据完整性。
常用的语句关键字主要包括 insert、delete、udpate 和 select 等。(增删改查）

DCL（Data Control Language）语句：数据控制语句，用于控制不同数据段直接的许可和访问级别的语句。这些语句定义了数据库、表、字段、用户的访问权限和安全级别。
主要的语句关键字包括 grant、revoke 等。


### 一、DDL 实现方式
在 MySQL5.6 版本以前，执行 DDL 主要有两种方式：Copy 方式 和 In-place 方式，5.6以上版本提供Online 方式

- Copy Table方式： 这是InnoDB最早支持的方式。顾名思义，通过临时表拷贝的方式实现的。新建一个带有新结构的临时表，将原表数据全部拷贝到临时表，然后Rename，完成创建操作。这个方式过程中，原表是可读的，不可写。但是会消耗一倍的存储空间。

- In-place方式：这是原生MySQL 5.5，以及innodb_plugin中提供的方式。所谓Inplace，也就是在原表上直接进行，不会拷贝临时表。相对于Copy Table方式，这比较高效率。原表同样可读的，但是不可写。

- Online方式：这是MySQL 5.6以上版本中提供的方式。无论是Copy Table方式，还是Inplace方式，原表只能允许读取，不可写。对应用有较大的限制，因此MySQL最新版本中，InnoDB支持了所谓的Online方式DDL。与以上两种方式相比，online方式支持DDL时不仅可以读，还可以写，对于dba来说，这是一个非常棒的改进。

### 常用DDL执行方式总结

|操作	            |支持方式	            |Allow R/W  |说明|
|---|---|---|---|
|add/create index	|online	                |允许读写	|当表上有FULLTEXT索引除外，需要锁表，阻塞写|
|add fulltext index	|in-place（5.6以上版本）	|仅支持读，阻塞写	|创建表上第一个fulltext index用copy table方式，除非表上有FTS_DOC_ID列。之后创建fulltext index用in-place方式，经过测试验证，第一次时5.6 innodb会隐含自动添加FTS_DOC_ID列，也就是5.6都是in-place方式|
|drop index	        |online	                |允许读写	|操作元数据，不涉及表数据。所以很快，可以放心操作|
|optimize table	    |online	                |允许读写	|当带有fulltext index的表用copy table方式并且阻塞写|
|alter table...engine=innodb	|online	    |允许读写	|当带有fulltext index的表用copy table方式并且阻塞写|
|add column	        |online	                |允许读写，(增加自增列除外)	|1、添加auto_increment列或者修改当前列为自增列都要锁表，阻塞写;2、虽采用online方式，但是表数据需要重新组织，所以增加列依然是昂贵的操作，小伙伴尤其注意啦|
|drop column	    |online 	            |允许读写(增加自增列除外)	|同add column，重新组织表数据，，昂贵的操作|
|Rename a column	|online	                |允许读写	|操作元数据;不能改列的类型，否则就锁表（已验证）|
|Reorder columns	|online	                |允许读写	|重新组织表数据，昂贵的操作|
|Make column NOT NULL	|online	            |允许读写	|重新组织表数据，昂贵的操作|
|Change data type of column	|copy table	    |仅支持读，阻塞写	|创建临时表，复制表数据，昂贵的操作（已验证）|
|Set default value for a column	  |online	|允许读写	|操作元数据，因为default value存储在frm文件中，不涉及表数据。所以很快，可以放心操作|
|alter table xxx auto_increment=xx	|online	|允许读写	    |操作元数据，不涉及表数据。所以很快，可以放心操作|
|Add primary key	|online	                |允许读写	|昂贵的操作（已验证）|
|Convert character set	|copy table	        |仅支持读，阻塞写	|如果新字符集不同，需要重建表，昂贵的操作|



#### Copy 方式执行DDL操作
1. 创建与原表结构定义完全相同的临时表
2. 为原表加MDL（meta data lock，元数据锁）锁，禁止对表中数据进行增删改，允许查询
3. 在临时表上执行DDL语句
4. 按照主键 ID 递增的顺序，把数据一行一行地从原表里读出来再插入到临时表中
5. 升级原表上的锁，禁止对原表中数据进行读写操作
6. 将原表删除，将临时表重命名为原表名，DDL操作完成

#### In-place 方式执行DDL操作
In-place 方式 又称为 Fast Index Creation 。与 Copy 方式相比，In-place方式不复制数据，因此大大加快了执行速度。但是这种方式仅支持**对二级索引进行添加、删除操作**，而且与Copy方式一样需要全程锁表。
下面以添加索引为例，简单介绍In-place方式的实现流程：

1. 创建新索引的数据字典
2. 为原表加MDL（meta data lock，元数据锁）锁，禁止对表中数据进行增删改，允许查询
3. 按照聚簇索引的顺序，查询数据，找到需要的索引列数据，排序后插入到新的索引页中
4. 等待打开当前表的所有只读事务提交
5. 创建索引结束

### 二、Online DDL
**MySQL5.6** 版本之后加入了 Online DDL 新特性，用于支持DDL执行期间DML语句的并行操作，提高数据库的吞吐量。
与 Copy 方式和 In-place 方式相比，Online 方式在执行DDL的时候可以对表中数据进行读写操作。
Online DDL可以有效改善DDL期间对数据库的影响：

- Online DDL期间，查询和DML操作在多数情况下可以正常执行，对表格的锁时间也会大大减少，尽可能的保证数据库的可扩展性；
- 允许 In-place 操作的 DDL，避免重建表格占用过多磁盘IO及CPU资源，减少对数据库的整体负荷，使得在DDL期间，能够维持数据库的高性能及高吞吐量；
- 允许 In-place 操作的 DDL，比需要COPY到临时文件的操作要更少占用buffer pool，避免以往DDL过程中性能的临时下降，因为以前需要拷贝数据到临时表，这个过程会占用到buffer pool ，导致内存中的部分频繁访问的数据会被清理出去。

Online DDL 实现实质上也可以分为2种方式：**Copy** 方式和 **In-place** 方式：

- 对于不支持Online DDL的 SQL，则采用 Copy 方式，比如删除主键，修改列数据类型，变更表字符集等。这些操作都会导致记录格式发生变化，无法通过简单的全量+增量的方式实现Online DDL。
- 对于支持Online DDL的 SQL，则采用 In-place 方式，MySQL 内部以“是否修改行记录格式”为标准，又将 In-place 方式分为两类：

    - 如果修改了行记录格式，则需要重建表，比如 添加主键，添加、删除字段，修行格式ROW_FORMAT，OPTIMIZE优化表 等操作，这种方式被称为 rebuild方式。
    - 如果没有修改行记录格式，仅修改表的元数据，则不需要重建表，比如 添加、删除、重命名二级索引，设置、删除字段的默认值，重命名字段，重命名表 等操作，这种方式被称为 no-rebuild方式。


更多详情可查阅官方文档：https://dev.mysql.com/doc/ref...

### 三、Online DDL 实现流程
Online DDL 主要包括3个阶段，Prepare阶段，Execute阶段，Commit阶段。
下面将主要介绍Online DDL执行过程中三个阶段的流程。

Prepare 阶段

- 持有 EXCLUSIVE-MDL 锁，禁止DML语句读写
- 根据DDL类型，确定执行方式(Copy,Online-rebuild,Online-no-rebuild)
- 创建新的 frm 和 ibd 临时文件（ibd临时文件仅rebuild类型需要）
- 分配 row_log 空间，用来记录 DDL Execute 阶段产生的DML操作（仅rebuild类型需要）

Execute 阶段

- 降级 EXCLUSIVE-MDL 锁，允许DML语句读写
- 扫描原表主键以及二级索引的所有数据页，生成B+树，存储到临时文件中
- 将 DDL Execute 阶段产生的DML操作记录到 row_log（仅rebuild类型需要）

Commit 阶段

- 升级到 EXCLUSIVE-MDL 锁，禁止DML语句读写
- 将 row_log 中记录的DML操作应用到临时文件，得到一个逻辑数据上与原表相同的数据文件（仅rebuild类型需要）
- 重命名 frm 和 idb 临时文件，替换原表，将原表文件删除
- 提交事务（刷事务的redo日志），变更完成

由上面的流程可知，Prepare阶段和Commit阶段都禁止读写，只有Execute允许读写，那为什么说Online DDL 方式在执行过程中可以对表中数据进行读写操作？
其实是因为Prepare和Commit阶段相对于Execute阶段时间特别短，因此基本可以认为是全程允许读写的。

Prepare阶段和Commit阶段都禁止读写，主要是为了保证数据一致性。

### 四、Online DDL 的语法与可选参数
ALTER TABLE …. , ALGORITHM[=]{DEFAULT|INPLACE|COPY}, LOCK[=]{DEFAULT|NONE|SHARED|EXCLUSIVE}

例句：

ALTER TABLE tablename DROP COLUMN age,ALGORITHM=INPLACE,LOCK=DEFAULT;

ALGORITHM 选项
- COPY：使用 Copy 方式 执行DDL操作，DDL 执行过程中，不允许DML操作。
- INPLACE：使用 In-place 方式 执行DDL操作，DDL 执行过程中，允许DML操作。
- DEFAULT：默认选项，根据DDL的操作类型，自动选择DDL执行方式，优先选择 In-place 方式，不满足条件时选择 Copy 方式。

LOCK 选项
- EXCLUSIVE：对整个表添加排他锁（X锁），不允许DML操作
- SHARED：对整个表添加共享锁（S锁），允许查询操作，但是不允许数据变更操作
- NONE：不对表加锁，既允许查询操作，也支持数据变更操作，即允许所有的 DML 操作，该模式下并发最好
- DEFAULT：默认选项，根据DDL的操作类型，最小程度的加锁，尽可能支持DML操作

### 5.6和5.7拓展varchar长度
在5.6 里面执行DDL 根本没有单独操作Varchar这个字段类型。所以说在5.6中执行varchar的更改还是会锁表，copy数据

5.7 支持Online DDL扩展VARCHAR列大小，但不锁表还是有条件的
```
ALTER TABLE tbl_name CHANGE COLUMN c1 c1 VARCHAR(255), ALGORITHM=INPLACE, LOCK=NONE;
```
- VARCHAR列 所需的长度字节数 必须保持相同。对于VARCHAR大小为0到255个字节的列，需要一个长度的字节来编码该值。对于VARCHAR 大小为256字节或更大的列，需要两个长度的字节。结果，就地ALTER TABLE仅支持将VARCHAR列大小从0 增大 到255字节，或从256字节增大到更大的大小。就地 ALTER TABLE不支持增加
- VARCHAR列，从小于256个字节到等于或大于256个字节的大小。在这种情况下，所需的长度字节数从1更改为2，仅表副本（ALGORITHM=COPY）支持。例如，尝试VARCHAR使用就地ALTER TABLE将单字节字符集的列大小从VARCHAR（255）更改为VARCHAR（256）会返回此错误：
```
ALTER TABLE tbl_name ALGORITHM=INPLACE, CHANGE COLUMN c1 c1 VARCHAR(256);
ERROR 0A000: ALGORITHM=INPLACE is not supported. Reason: Cannot change
column type INPLACE. Try ALGORITHM=COPY.
```
- 减少VARCHAR使用就地尺寸ALTER TABLE不被支持。减小VARCHAR 大小需要表副本（ALGORITHM=COPY）。
  
总结
1. 在数据量很大的时候，varchar通过Online DDL做到快速进行更改字段长度。但是前提条件就是不会进行锁表和copy数据的过程。
2. 这个前提条件就是数据库的支持5.7及5.7以上。
3. 还有就是更改的varchar大小小于256

> MySQL文档上的翻译

字符串的字段是以字节为单位存储的，utf8 一个字符需要三个字节，utf8mb4 一个字符需要4个字节。对于小于等于255字节以内的长度可以使用一个byte 存储。大于255个字节的长度则需要使用2个byte存储。

online ddl in-place 模式(不锁表)只支持字段的字节长度从0到255之间 或者256到更大值之间变化。

如果修改字段的长度，导致字段的字节长度无法使用 1 byte表示，得使用2个byte才能表示，比如从 240 修改为 256 ，如果在默认字符集为utf8mb4的情况下，varchar(60) 修改为 varchar(64)，则DDL需要以copy模式，也即会锁表，阻塞写操作。


### 如何监控是否使用copy或Online
任选一条sql语句：
```
alter TABLE biz_advert ADD Index `2_user_id_activity_Id` (`title`) USING BTREE
```
查看PROFILES
```
SHOW PROFILES;
```
根据PROFILES中的ID查询sql过程：
```
show profile for query 139;
```
最后，对比有没有copy语句就可以判定是否锁表。

说明：MySQL5.0.37版本以上支持PROFILING调试功能，让您可以了解SQL语句消耗资源的详细信息。因为它需要调用系统的getrusage()函数，所以只是在Linux/Unix类平台上才能使用，而不能在Windows平台上使用

如果没有开启的，可通过命令打开profile功能：
```
mysql> set profiling=1;
```
查看状态
```
SELECT @@profiling;
```
