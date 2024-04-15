# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## database-overview

### 目录
- [基础规则](#索引是什么)
- [存储过程和定时器](#存储过程和定时器)
- [查询](#查询)


### 基础规则
传统数据库遵循ACID规则：Atomic（原子性)Consistency（一致性）Isolation（隔离性）Durability（持久性）
NoSql一般为分布式，遵循CAP定理：一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）

### Mysql基础日志
- 重写日志（redo log）
- 回滚日志（undo log）
- 二进制日志（bin log）
- 错误日志（error log）
- 慢查询日志（slow query log）
- 一般查询日志（general log）

MySQL中的数据变化会体现在上面这些日志中，比如事务操作会体现在redo log、undo log以及bin log中，数据的增删改查会体现在 binlog 中。

**redo log**
redo log是一种基于磁盘的数据结构，用来在MySQL宕机情况下将不完整的事务执行数据纠正，redo日志记录事务执行后的状态。
当事务开始后，redo log就开始产生，并且随着事务的执行不断写入redo log file中。redo log file中记录了xxx页做了xx修改的信息，我们都知道数据库的更新操作会在内存中先执行，最后刷入磁盘。
redo log就是为了恢复更新了内存但是由于宕机等原因没有刷入磁盘中的那部分数据。

**undo log**
undo log主要用来回滚到某一个版本，是一种逻辑日志。undo log记录的是修改之前的数据，比如：当delete一条记录时，undolog中会记录一条对应的insert记录，从而保证能恢复到数据修改之前。在执行事务回滚的时候，就可以通过undo log中的记录内容并以此进行回滚。
undo log还可以提供多版本并发控制下的读取（MVCC）。

**bin log**
MySQL的bin log日志是用来记录MySQL中增删改时的记录日志。简单来讲，就是当你的一条sql操作对数据库中的内容进行了更新，就会增加一条bin log日志。查询操作不会记录到bin log中。bin log最大的用处就是进行主从复制，以及数据库的恢复。

### Redo Log和binLog的写入顺序
redo log要分两步写，中间再穿插写binlog：写完redo log 后，并不是直接将其标记为完成状态，而是将其标记为prepare状态， 然后等服务层的bin log 日志记录完成后，才会把引擎层的redo log 改为提交状态。

mysql是如何保证一致性的呢？最简单的做法是在每次事务提交的时候，将该事务涉及修改的数据页全部刷新到磁盘中。但是这么做会有严重的性能问题，主要体现在两个方面：

- 因为 Innodb 是以 页 为单位进行磁盘交互的，而一个事务很可能只修改一个数据页里面的几个字节，这个时候将完整的数据页刷到磁盘的话，太浪费资源了！
- 一个事务可能涉及修改多个数据页，并且这些数据页在物理上并不连续，使用随机IO写入性能太差！

因此 mysql 设计了 redo log ， 具体来说就是只记录事务对数据页做了哪些修改 ，这样就能完美地解决性能问题了(相对而言文件更小并且是顺序IO)。 
InnoDB 引擎会在适当的时候（如系统空闲时），将这个操作记录更新到磁盘里面（刷脏页）。

**为什么 redo log 具有 crash-safe 的能力，而 binlog 没有？**

redo log 是一个固定大小，“循环写”的日志文件，记录的是物理日志——“在某个数据页上做了某个修改”。

binlog 是一个无限大小，“追加写”的日志文件，记录的是逻辑日志——“给 ID=2 这一行的 c 字段加1”。

redo log 和 binlog 有一个很大的区别就是，一个是循环写，一个是追加写。也就是说 redo log 只会记录未刷盘的日志，已经刷入磁盘的数据都会从 redo log 这个有限大小的日志文件里删除。binlog 是追加日志，保存的是全量的日志。
当数据库 crash 后，想要恢复未刷盘但已经写入 redo log 和 binlog 的数据到内存时，binlog 是无法恢复的。虽然 binlog 拥有全量的日志，但没有一个标志让 innoDB 判断哪些数据已经刷盘，哪些数据还没有。

**redo log 和 binlog 是怎么关联起来的?#**
redo log 和 binlog 有一个共同的数据字段，叫 XID。崩溃恢复的时候，会按顺序扫描 redo log：

如果碰到既有 prepare、又有 commit 的 redo log，就直接提交；
如果碰到只有 parepare、而没有 commit 的 redo log，就拿着 XID 去 binlog 找对应的事务。

**MySQL 怎么知道 binlog 是完整的?#**
一个事务的 binlog 是有完整格式的：

statement 格式的 binlog，最后会有 COMMIT
row 格式的 binlog，最后会有一个 XID event

### 存储过程和定时器
- 存储过程（创建存储过程，这里的存储过程主要提供给mysql的定时器event来调用去执行）
```
create procedure mypro()
BEGIN
insert into mytable (name,introduce,createtime) values ('1111','inner mongolia',now());
end;
```

- 定时器
```
create event if not exists eventJob
on schedule every 1 second
on completion PRESERVE
do call mypro();
```

- mysql开启定时器和事件：
```
SET GLOBAL event_scheduler = 1;  -- 启动定时器
SET GLOBAL event_scheduler = 0;  -- 停止定时器

ALTER EVENT eventJob ON  COMPLETION PRESERVE ENABLE;   -- 开启事件
ALTER EVENT eventJob ON  COMPLETION PRESERVE DISABLE;  -- 关闭事件

SHOW VARIABLES LIKE '%sche%'; -- 查看定时器状态
```
系统会每隔一秒去表mytable插入一条数据

### 查询
显示系统变量的名称和值：show variables;
查看mysql字符串相关信息：show variables  like "%char%";

#### left(right) join和inner join的区别

inner join和left join区别为：返回不同、数量不同、记录属性不同。

一、返回不同
1、inner join：inner join只返回两个表中联结字段相等的行。
2、left join：left join返回包括左表中的所有记录和右表中联结字段相等的记录。

二、数量不同
1、inner join：inner join的数量小于等于左表和右表中的记录数量。
2、left join：left join的数量以左表中的记录数量相同。
**补充说明：LEFT JOIN 的是以左边表为根据查询，注意右边表与左边表on的字段一定要是唯一的，
不然查询出来的条数，就是一对多的关系，一定比左边表多**

三、记录属性不同
1、inner join：inner join不足的记录属性会被直接舍弃。
2、left join：left join不足的记录属性用NULL填充。

案例
````
SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for student
-- ----------------------------
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student`  (
  `id` int(10) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主键',
  `name` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NULL DEFAULT '' COMMENT '姓名',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = MyISAM AUTO_INCREMENT = 7 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_bin ROW_FORMAT = Dynamic;

DROP TABLE IF EXISTS `score`;
CREATE TABLE `score`  (
  `id` int(10) UNSIGNED NOT NULL AUTO_INCREMENT COMMENT '主键',
  `score` varchar(255) CHARACTER SET utf8mb4 COLLATE utf8mb4_bin NULL DEFAULT '' COMMENT '分数',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = MyISAM AUTO_INCREMENT = 7 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_bin ROW_FORMAT = Dynamic;


-- ----------------------------
-- Records of student
-- ----------------------------
INSERT INTO `student` VALUES (1, '张三');
INSERT INTO `student` VALUES (2, '李四');
INSERT INTO `student` VALUES (3, '王五');

-- ----------------------------
-- Records of score
-- ----------------------------
INSERT INTO `score` VALUES (2, '70');
INSERT INTO `score` VALUES (3, '80');
INSERT INTO `score` VALUES (4, '90');
````

1. left join
````
select 
    * 
from 
    student
left join score on student.id = score.id

结果集：
1 张三 null null
2 李四 2 70
3 王五 3 80

````

2. right join
````
select 
    * 
from 
    student
right join score on student.id = score.id

结果集：
2 李四 2 70
3 王五 3 80
null null 4 90
````

3. inner join（可简写为join）
````
select 
    * 
from 
    student
inner join score on student.id = score.id

结果集：
2 李四 2 70
3 王五 3 80
````

#### 分页为什么不要用offset和limit分页？
为了实现分页，每次收到分页请求时，数据库都需要进行低效的全表扫描。
解决：其实很简单，如果有主键，利用主键索引就够了！
Select * from table where id>10 limit 10

那如果我们的表没有主键，比如是具有多对多关系的表，那么就只能使用传统的 OFFSET/LIMIT 方式，但这样做存在潜在的慢查询问题。
完全可以在需要分页的表中使用自动递增的主键，即使只是为了分页。


---

### Mysql常见的优化手段
- 增加索引，索引是直观也是最快速优化检索效率的方式。
- 基于Sql语句的优化，比如最左匹配原则，用索引字段查询、降低sql语句的复杂度等。
- 表的合理设计，比如符合三范式、或者为了一定的效率破坏三范式设计等。
- 数据库参数优化，比如并发连接数、数据刷盘策略、调整缓存大小。
- 数据库服务器硬件升级。
- mysql主从复制方案，实现读写分离。

### 什么是SQL注入，该如何防止
一般使用单引号‘ 测试SQL注入

用？占位符可以避免SQL注入
```
PreparedStatement st = conn.prepareStatement(sql);
st.setString(1, "儿童"); // 参数赋值  ——这里做了字符转义
```

PreparedStatement也有漏洞：
比如Like查询中：
用户直接输入了"%儿童%"，整个查询的意思就变了，变成包含查询。实际上不用这么麻烦，用户什么都不输入，或者只输入一个%，都可以改变原意。
虽然此种SQL注入危害不大，但这种查询会耗尽系统资源，从而演化成拒绝服务攻击。
那如何防范呢？笔者能想到的方案如下：
·直接拼接SQL语句，然后自己实现所有的转义操作。这种方法比较麻烦，而且很可能没有PreparedStatement做的好，造成其他更大的漏洞，不推荐。
·直接简单暴力的过滤掉%。笔者觉得这方案不错，如果没有严格的限制，随便用户怎么输入，既然有限制了，就干脆严格一些，干脆不让用户搜索%，推荐。


### key和index的区别
* key 是数据库的物理结构，它包含两层意义和作用，
一是约束（偏重于约束和规范数据库的结构完整性），
二是索引（辅助查询用的）。
包括primary key, unique key, foreign key 等。
primary key 有两个作用，一是约束作用（constraint），用来规范一个存储主键和唯一性，但同时也在此key上建立了一个index；
unique key 也有两个作用，一是约束作用（constraint），规范数据的唯一性，但同时也在这个key上建立了一个index；
foreign key也有两个作用，一是约束作用（constraint），规范数据的引用完整性，但同时也在这个key上建立了一个index；

* INDEX
index是数据库的物理结构，它只是辅助查询的，它创建时会在另外的表空间（mysql中的innodb表空间）以一个类似目录的结构存储。索引要分类的话，分为前缀索引、全文本索引等；因此，索引只是索引，它不会去约束索引的字段的行为。
例如：create table t(id int, index inx_tx_id (id));


### 复合主键
所谓的复合主键 就是指你表的主键含有一个以上的字段组成,不使用无业务含义的自增id作为主键。
~~~~
create table test 
( 
   name varchar(19), 
   id number, 
   value varchar(10), 
   primary key (name,id) 
) 
~~~~
举个例子，我们在表中创建了一个ID字段，自动增长，并设为主键，这个是没有问题的，因为“主键是唯一的索引”，ID自动增长保证了唯一性，所以可以

此时，我们再创建一个字段name，类型为varchar，也设置为主键，你会发现，在表的多行中你是可以填写相同的name值的，这岂不是有违“主键是唯一的索引”这句话么？

所以我才说“主键是唯一的索引”是有歧义的。应该是“当表中只有一个主键时，它是唯一的索引；当表中有多个主键时，称为复合主键，复合主键联合保证唯一索引”。

为什么自增长ID已经可以作为唯一标识的主键，为啥还需要复合主键呢。因为，并不是所有的表都要有ID这个字段，比如，我们建一个学生表，没有唯一能标识学生的ID，怎么办呢，学生的名字、年龄、班级都可能重复，无法使用单个字段来唯一标识，这时，我们可以将多个字段设置为主键，形成复合主键，这多个字段联合标识唯一性，其中，某几个主键字段值出现重复是没有问题的，只要不是有多条记录的所有主键值完全一样，就不算重复。


### 锁级别
* 行级锁：容易造成死锁
* 表级锁：锁住整张表

### 悲观锁和悲观锁
* 悲观锁：总是假设最坏的情况，每次去拿数据的时候都认为别人会修改，所以每次在拿数据的时候都会上锁，这样别人想拿这个数据就会阻塞直到它拿到锁。传统的关系型数据库里边就用到了很多这种锁机制，比如行锁，表锁等，读锁，写锁等，都是在做操作之前先上锁。再比如Java里面的同步原语synchronized关键字的实现就是悲观锁，volatile关键字虽然是synchronized关键字的轻量级实现，但是其无法保证原子性，所以一般也要搭配锁使用。
* 乐观锁：顾名思义，就是很乐观，每次去拿数据的时候都认为别人不会修改，所以不会上锁，但是在更新的时候会判断一下在此期间别人有没有去更新这个数据，可以使用版本号等机制。

### Mysql中S 锁和 X 锁的区别
- S 锁，英文为 Shared Lock，中文译作共享锁，有时候我们也称之为读锁，即 Read Lock。S 锁之间是共享的，或者说是互不阻塞的。
当事务读取一条记录时，需要先获取该记录的 S 锁。

举个例子：
事务 T1 对记录 R1 加上了 S 锁，那么事务 T1 可以读取 R1 这一行记录，但是不能修改 R1，其他事务 T2 可以继续对 R1 添加 S 锁，但是不能添加 X 锁，只有当 R1 上面的 S 锁释放了，才能加上 X 锁。

- X 锁，英文为 Exclusive Lock，中文译作排他锁，有时候我们也称之为写锁，即 Write Lock。如同它的名字，X 锁是具有排他性的，即一个写锁会阻塞其他的 X 锁和 S 锁。
当事务需要修改一条记录时，需要先获取该记录的 X 锁。

举个例子：
事务 T1 对记录 R1 加上了 X 锁，那么事务 T1 即可以读取 R1 也可以修改 R1，而其他事务则不能对 R1 再添加任何锁，直到 T1 释放了 R1 上的锁。

案例：程序并发查询一条相同ID记录。
manual里面讲了啊，就是duplicate key error的插入，第一个插入成功的session加x锁，另外两个排队拿s锁检查duplicate。

如果插入成功的rollback了之后，另外两个session 同时获得s锁，此时需要覆盖记录两个都想升级 x锁，但是 s锁没人释放，x锁也得不到。只能abort其中一个。其中一个得到x锁之后插入成功。

### 数据库的隔离级别：
* 读未提交（Read Uncommitted）：只处理更新丢失。如果一个事务已经开始写数据，则不允许其他事务同时进行写操作，但允许其他事务读此行数据。可通过“排他写锁”实现。写数据时添加x锁，直到事务结束，读的时候不加锁。
* 读提交（数据库引擎的默认级别）（Read Committed）：处理更新丢失、脏读。读取数据的事务允许其他事务继续访问改行数据，但是未提交的写事务将会禁止其他事务访问改行。可通过“瞬间共享读锁”和“排他写锁”实现。写数据时添加x锁，直到事务结束，读的时候加上S锁，读完数据立刻释放。
* 可重复读取（Repeatable Read）：处理更新丢失、脏读和不可重复读取。读取数据的事务将会禁止写事务，但允许读事务，写事务则禁止任何其他事务。可通过“共享读锁”和“排他写锁”实现。写数据时添加x锁，直到事务结束，读的时候加上S锁，也是直到事务结束。
* 序列化（Serializable）：提供严格的事务隔离。要求失去序列化执行，事务只能一个接一个地执行，不能并发执行。仅仅通过“行级锁”是无法实现事务序列化的，必须通过其他机制保证新插入的数据不会被刚执行查询操作的事务访问到。

### 事务容易出现的问题及解决
脏读：（同时操作都没提交的读取）READ_COMMITTED解决
不可重复读：（同时操作，事务一分别读取事务二操作时和提交后的数据，读取的记录内容不一致）REPEATABLE_READ解决
幻读：（和可重复读类似，但是事务二的数据操作仅仅是插入和删除，不是修改数据，读取的记录数量前后不一致）SERIALIZABLE_READ解决


### innodb的半一致性读？
什么是半一致性读

就是发生在update语句中。在RC隔离级别或者innodb_locks_unsafe_for_binlog被设置为true，并发时，如果update的记录发生锁等待，那么返回该记录的prev 版本（在返回前会将锁等待的这个lock从trx中删除掉），到mysql层进行where判断，是否满足条件。如果满足where条件，那么再次进入innodb层，真正加锁或者发生锁等待。

这样做的好处是：减少同一行记录的锁冲突及锁等待；无并发冲突时，直接读取最新版本加锁，有冲突时，不加锁，读取prev版本不需要锁等待。

缺点：非冲突串行话策略，对于binlog来说是不安全的。只能发生在RC隔离级别和innodb_lock_unsafe_for_binlog下。


### 状态分类，分别降序、升序排列
例：按状态分类，分别降序、升序排列
~~~~
①
select * from test order by
case when state='已完成' then id end,
case when state='未完成' then state end,id desc
②
select * from test order by
case when state='已完成' then id end asc,
case when state='未完成' then id end desc,state desc
~~~~


### 获取当前日期和及时分秒信息
~~~~
SELECT curdate();
| 2013-07-29 |
~~~~

~~~~
mysql> select now();
| 2013-07-29 22:10:40 |
获取近三天：昨天，今天，明天
select * from t_clue_follow_record 
where creator_id = 999 
AND invite_time >= DATE_SUB(curdate(),INTERVAL +1 DAY) 
and  invite_time <= DATE_SUB(curdate(),INTERVAL -1 DAY)
~~~~

### sql insert 内容中，部分为查询的结果
普通插入时，我们只能：
```
insert into Zd(userId,url,others)values('id','aaaaa',‘others...’)
```
插入“动态”数据，即某些数据是查询结果，而某些数据是写死的，就可以写成这样：
```
insert into Zd(userId , url, others)
select top 1 id , 'aaaaa', 'others...'
from userInfo
```
即把写死的数据按顺序用逗号分隔加到 from 前。下面这条也是同样的效果：
```
insert into Zd(url, userId , others)
select top 1 'aaaaa', id , 'others...'
from userInfo
```

### MySQL中的field（）函数 
MySQL中的field（）函数，可以用来对SQL中查询结果集进行指定顺序排序。不在field中的字段放在最前面，其他则按filed中的顺序排序
根据输入的字段排序
```
...
order by field (trade_flow_no,'1','2','3')
```
```
假设test表中有a,b字段

a字段中有1,2,3,4,5,6,7,8,9,10...

要求：8,9,7先排序，剩下的按照b字段正序排列

select * from test order by field(a,7,9,8) desc , b asc

注意：这里field中的值顺序是相反的，field方法后面需要加上desc 否则会先按照 b字段 正序，再按照field中的顺序进行排列
```

### mysql 缓存问题，一致性问题
mysql的自带缓存是为了解决io效率的问题，本身就没保证强一致，就是为了让一些读热点页不用每次都去磁盘io，或者做非主键索引写的异步刷盘
一致性可是靠wal，redolog，binlog保证的
读不用说，写有wal，写成功了才算提交成功，崩溃恢复啥的也是从log里恢复啊。有脏页的话，只要触发了读和写一定先要purge的，还有双写

### mysql WAL机制
MySQL 里经常说到的 WAL技术，也就是先写日志，再写磁盘。
当内存数据页跟磁盘数据页内容不一致的时候，我们成这个内存页为“脏页”。内存数据写入磁盘后，内存和磁盘上的数据页内容就一致了，称为“干净页”。
MySQL 从 内存更新到磁盘的过程，称为刷脏页的过程（flush）。

- InnoDB 刷脏页的时机：
1. 内存中的redo log 写满了，这时系统就会停止所有更新操作，把checkoutpoint 往前推，redo log留出空间可以继续写。
往前推进之后，就要把两个点之间的日志对应的所有脏页都 flush 到磁盘上。
这种情况是 InnoDB 要尽量避免的。因为出现这种情况，整个系统都不能接受更新。更新数会跌为0。

2. 系统中内存不足时，当这个时候需要新的数据页到内存中，就要淘汰掉一些数据页，如果淘汰的是“脏页”，就要先将“脏页”写到磁盘。
那么为什么不能直接淘汰所有的内存，下次请求的时候，再从磁盘读入数据页，然后 拿 redo log 出来应用？这其实也是从性能的角度来考虑的，刷脏页一定写盘，就保证了每个数据页只有两种情况：

数据页直接在内存里，内存里的肯定是正确的，直接返回
内存里没有数据，就可以肯定数据文件上是正确的结果，读入内存后返回。 这样的效率最高。
这种情况在日常应用中其实是常态。在InnoDB 中，使用缓冲池 （buffer pool）管理内存，缓冲池中的内存页有三种状态：

- 还没有使用的；
- 使用了并且是干净页
- 使用了并且是脏页
3. 数据库空闲的时候刷脏页。
4. 数据库正常关闭的时候，也要把内存中所有的脏页全都flush 到磁盘上。

对性能的影响
刷脏页是常态，所以如果出现以下的情况，都会明明显影响性能：
- 一个查询要淘汰的脏页太多，会导致查询的响应时间明显变长；
- 日志写满，更新全部堵住，写性能跌为0，这种情况对于敏感业务来说是不能接受的。

binlog 的写入机制
binlog 的写入机制比较简单：事务执行的过程中，先把日志写到 binlog cache，事务提交的时候，再把 binlog cache 写到binlog 文件中。
系统给 binlog cache 分配了一片内存，每个线程一个，参数 binglog_cache_size 用于控制单个线程内 binlog cache 的内存大小，超过就要暂存在磁盘。
事务提交的时候，执行器把 binlog cache 里完整事务写入到 binlog 中，并清空 binlog cache。

- write 指的是把日志写入到文件系统的 page cache，并没有吧数据持久化到磁盘，所以速度比较快。
- fsync 是持久化到磁盘的操作，一般情况下， fsync 才会占磁盘的 IOPS。
write 和 fsync 的时机，是由参数 sync_binlog 控制的：

- sync_binlog=0 的时候，表示每次提交事务都只 write，不 fsync；
- sync_binlog=1 的时候，表示每次提交事务都会执行 fsync；
- sync_binlog=N(N>1) 的时候，表示每次提交事务都 write，但累积 N 个事务后才 fsync。
因此，在出现 IO 瓶颈的场景里，将 sync_binlog 设置成一个比较大的值，可以提升性能。在实际的业务场景中，考虑到丢失日志量的可控性，一般不建议将这个参数设成 0，比较常见的是将其设置为 100~1000 中的某个数值。但是，将 sync_binlog 设置为 N，对应的风险是：如果主机发生异常重启，会丢失最近 N 个事务的 binlog 日志。

- redo log 的写入机制
事务的执行过程中，生成的 redo log 是要先写到 redo log buffer 的。

redo log 三种状态：

- 存在 redo log buffer 中，物理上是在 MySQL 进程内存中
- 写到磁盘（write），但是没有持久化（fsync），物理上是在文件系统的 page cache 里
- 持久化磁盘，对应的是 hard disk
日志写到 redo log buffer 是很快的，write 到 page cache 也差不多，但是持久化到磁盘的速度就慢多了。

### mysql 双写机制
一：为啥会有两次写？必要了解partial page write 问题 :
InnoDB 的Page Size一般是16KB，其数据校验也是针对这16KB来计算的，将数据写入到磁盘是以Page为单位进行操作的。
而计算机硬件和操作系统，写文件是以4KB作为单位的,那么每写一个innodb的page到磁盘上，在os级别上需要写4个块.

在极端情况下（比如断电）往往并不能保证这一操作的原子性，16K的数据，写入4K 时，发生了系统断电/os crash ，只有一部分写是成功的，这种情况下就是 partial page write 问题。
有人会想到系统恢复后MySQL可以根据redolog 进行恢复，而mysql在恢复的过程中是检查page的checksum，checksum就是page的最后事务号，发生partial page write 问题时，page已经损坏，找不到该page中的事务号，就无法恢复。

为了解决 partial page write 问题 ，
- 当mysql将脏数据flush到data file的时候, 先使用memcopy 将脏数据复制到内存中的double write buffer ，
- 通过double write buffer再分2次，每次写入1MB到共享表空间，
- 然后马上调用fsync函数，同步到磁盘上，避免缓冲带来的问题。

在这个过程中，doublewrite是顺序写，开销并不大，在完成doublewrite写入后，在将double write buffer写入各表空间文件，这时是离散写入。如果发生了极端情况（断电），InnoDB再次启动后，发现了一个Page数据已经损坏，那么此时就可以从doublewrite buffer中进行数据恢复了。

两次写需要额外添加两个部分：
- 内存中的两次写缓冲（doublewrite buffer），大小为2MB
- 磁盘上共享表空间中连续的128页，大小也为2MB。其中120个用于批量写脏页，另外8个用于Single Page Flush。做区分的原因是批量刷脏是后台线程做的，不影响前台线程。而Single page flush是用户线程发起的，需要尽快的刷脏页并替换出一个空闲页出来。

---

### mysql中的innodb为什么用B+树不使用B树
1.B树把数据放在了每个节点上，而B+树把数据放在了叶子节点上，所以单个查询速度B+平均要快于B树，但是B-树的每个节点都有data域（指针），这无疑增大了节点大小，说白了增加了磁盘IO次数（磁盘IO一次读出的数据量大小是固定的，单个数据变大，每次读出的就少，IO次数增多），而B+树除了叶子节点其它节点并不存储数据，节点小，磁盘IO次数就少。
2.另一方面，由于B+树有链指针，所以更方便区间查询。

### mysql中int(1)指定的是数据长度么？

不是，其实这个int(1)和varchar(1)是不一样的，对于int型，不管你设计多少长度，它永远需要占用4个字节，默认就是11位，所以其实这个int(1)控制的不是数据的长度，而是数据的显示长度，它指明了mysql最大可能显示的数字个数。
所以如果不是特别必要，数据库的int型不加上长度设计也是可以的。

### MySQL中sql语句最大长度问题
MySQL默认sql语句最大为1M的长度，设置稍大点即可解决。

查看数据库设置的sql最大长度
show variables like '%max_allowed_packet%';

修改mysql的配置文件（my.ini）：
MySQL默认sql语句最大为1M的长度，改大即可。
max_allowed_packet=6M


### IN字段查询多少个值最合适？
为什么说是在IN查询的值数量不多时才是精确的，因为扫描性能的原因，MySQL在IN查询的值数量很多的情况下，扫描索引树成本提高，性能下降，导致查询成本分析代价也随之提高了。

IN查询的字段，该字段的值不要超过eq_range_index_dive_limit这个参数，让MySQL能够正确选择执行计划，保证SQL查询的性能。

**eq_range_index_dive_limit参数的默认值在5.7版本更新为200。**

SQL中IN查询字段id的值出现的数量小于eq_range_index_dive_limit，则走索引树扫描分析查询成本， 大于等于eq_range_index_dive_limit，则走索引统计的方式分析查询成本。

查询`eq_range_index_dive_limit`参数： SHOW VARIABLES LIKE '%dive%';

#### MRR
MySQL在处理上面这个问题时，想到了一个把随机IO转换为顺序IO的办法，去提升辅助索引主键到聚簇索引查询的性能，而这个办法就是MRR。

例：
优化器对rowids_buf中的6，8，2，5这四个主键做快速排序，然后，rowids_buf中的主键顺序变为2，5，6，8。
执行器从rowids_buf中按序依次取出主键2，5，6，8，然后，进行如下步骤：

相比原先按照主键6，8，2，5的顺序搜索聚簇索引，MRR的做法要聪明多了，因为按照6，8，2，5这个顺序搜索聚簇索引，这是一个随机查找，而转换为2，5，6，8后，搜索聚簇索引就变为顺序查找，这个性能相比前者一定会有很大提升的。

如果rowids_buf满了怎么办？这个不用担心，当rowids_buf满了，等rowids_buf中的主键都从聚簇索引中取出记录后，把rowids_buf清空，继续之前的步骤

既然rowids_buf会满，我想一次多加载一批辅助索引主键到rowids_buf，减少查找聚簇索引的次数，那该怎么办？这个MySQL也提供了一个参数去改变rowids_buf的大小，这个参数就是read_rnd_buffer_size。调整语句：

SET read_rnd_buffer_size = 1024*1024;


### 为什么不建议在数据库中存储大文本，图片视频类数据

像blob和text这样的字段，名义上是为存储很大的数据而设计的类型。

但这正因为如此，这跟关系数据库使用table的设计理念是冲突的。table中的每一列数据都是定长的，比如varchar(32)。但blob和text的上限太长了，MySQL不可能将它们存储在table中，因而会使用专门的外部存储区域进行存储，数据行内存储指针。

**这样做的其中一个结果是会导到多一次磁盘IO，性能开销比较严重。**

所以，在数据量较多是情况下，90%以上的时间都用在查text、bolb这些字段上。


### 两三百万的表， 需要对一个字段做like %q%的搜索， 非常慢。 怎么处理比较好， es, 或者放在mongo里查？
- 有条件的，当然是直接上搜索引擎 

- 业务方妥协，%q%改成q%，但这种对业务的限制就多了（原理是mysql在使用like查询中，但只有使用后面的%时，才会使用到索引）

- 然后使用mysql 全文索引，FULLTEXT INDEX 也是一种可行方案

- 利用函数，instr(字段,’q’)>0 。（MySQLl的内置函数INSTR(str,substr)，作用是获取子串第一次出现的位置，如果没有找到则返回0，也可以看作是返回substr字符串在str字段名中第一次出现的位置。）
  - 其他函数：IN函数效率，其次是 FIND_IN_SET，最慢的是INSTR。

- 还有一种解决方案：增加一个反转字段，查询的时候对原始字段q% or 对反转字段q%

但这种还是有问题的。

第一个。你这个考验分词的准确性。漂亮的美女。 冷艳的美女。我们用美女想做查询。你必须保证翻转的美女在前面

第二个。抱着你分词准确性的前提翻转了。人家用中间的关键词查询。

其实最关键的，还是要看字段多大的内容，富文本还是要上es。

如果是文件名或者一般量级的数据，可以看看去重后数量级还大吗，如果不大的话可以加个表单独记录文件名，先去这个表里匹配文件名，再拿匹配到的结果去大表里精确查询。参考文件系统里ls的做法

- 还有一种：给字段加上前缀索引，然后用like q%来查询就能走索引了，把捞到的数据再用程序做一次筛选就搞定（得建立合适的前缀索引，q要覆盖到前缀条件）

### 慢sql问题，常见解决思路，如果服务现在有很多用户正在使用又该如何解决
1. 联系DBA对现有等待线程kill掉
2. 服务必须限流，不然起来也要挂。牺牲部分用户体验
3. 如果是连接紧张导致的慢SQL可以加从库缓解顶一下
4. o如果是索引问题该加索引直接加就好了，没啥影响。当然如果是大表的话那特么就很操蛋需要考虑在这个期间优化SQL上线可能来得更快



### Redis与DB的双写一致性
```
一致性就是数据保持一致，在分布式系统中，可以理解为多个节点中数据的值是一致的。

- 强一致性：这种一致性级别是最符合用户直觉的，它要求系统写入什么，读出来的也会是什么，用户体验好，但实现起来往往对系统的性能影响大
- 弱一致性：这种一致性级别约束了系统在写入成功后，不承诺立即可以读到写入的值，也不承诺多久之后数据能够达到一致，但会尽可能地保证到某个时间级别（比如秒级别）后，数据能够达到一致状态
- 最终一致性：最终一致性是弱一致性的一个特例，系统会保证在一定时间内，能够达到一个数据一致的状态。这里之所以将最终一致性单独提出来，是因为它是弱一致性中非常推崇的一种一致性模型，也是业界在大型分布式系统的数据一致性上比较推崇的模型
```
首先多线程问题无法避免：
- 假设a.b两个线程先后操作了。不管是先删除缓存还是先更新数据库，并发问题需要解决。
- 使用乐观锁，或更新加锁，失败回滚，牺牲性能换取一致性

一般我们在更新数据库数据时，需要同步redis中缓存的数据，所以存在两种方法：
- 方案一：先执行缓存清除，再执行update操作。
- 方案二：先执行update操作，再执行缓存清除。

**方案一：先删除缓存，再更新数据库**
缓存删除失败，就不更新数据库，缓存和数据库都是旧数据，数据一致。
缓存删除成功，数据库更新失败，那么数据库是旧数据，缓存是空的，数据一致。

方案一问题：
- 先删除缓存效率太低，期间所有的请求都将穿过缓存，并发较高的情况下将会出现风险。
- 当请求1执行清除缓存后，还未进行update操作，此时请求2进行查询到了旧数据并写入了redis（redis脏数据将会一直存在）
- 如果以缓存优先的话，缓存崩溃的风险要远远大于数据库（内存溢出、宕机问题）

**方案二：先更新数据库，再删除缓存**

方案二问题：
- 当请求1执行update操作后，还未来得及进行缓存清除，此时请求2查询到并使用了redis中的旧数据（风险存在于未删除的这一段时间）
- 如果数据库更新成功，缓存删除失败，数据会不一致（redis脏数据将会一直存在），需要考虑数据库回滚方案

**方案三：延迟双删（保证最终一致）**
先进行缓存清除，再执行update，最后（延迟N秒）再执行缓存清除。

上述中（延迟N秒）的时间要大于一次写操作的时间，一般为3-5秒。
原因：如果延迟时间小于写入redis的时间，会导致请求1清除了缓存，但是请求2缓存还未写入的尴尬。。。（ps:一般写入的时间会远小于5秒）

问题：
- 缓存删除失败问题，数据库不会更新；缓存的风险要远大于数据库
- 并发下，大多数是读比写来得多，缓存删除成功之后数据库写入之前的请求会穿过缓存，读操作又把刚删的和缓存一样的值又加到缓存里了，更新数据库操作远比读操作来得慢，从删缓存到更新数据库结束是比较长的；
- 同时数据库主从同步延迟也会缓存脏数据

**方案四：延迟双删（保证最终一致）**
先执行update，再进行缓存清除，最后（延迟N秒）再执行缓存清除。

先更新数据库，再删除缓存的好处是：提升并发性能，缓解因主从延迟导致的缓存脏数据，同时如果缓存更新失败，DB也能保证是最新数据，等下一次缓存延迟双删即可。

当然这种方案也有不一致问题：后删缓存，不一致存在于DB更新成功-删除缓存成功这段时间内，而先删缓存的话，不一致可能存在于删除缓存-第二次删除这段时间内，明显先删缓存的不一致时间段更长。

> 延迟双删策略是分布式系统中数据库存储和缓存数据保持一致性的常用策略，但它不是强一致。其实不管哪种方案，都避免不了Redis存在脏数据的问题，只能减轻这个问题，要想彻底解决，得要用到同步锁和对应的业务逻辑层面解决。

> 两种延迟双删都无法避免脏数据：当且仅当1主库更新完，2删完缓存，3从库同步完成，在这 之后 的请求，才不会查到脏数据。其它的各个阶段都有可能读到脏数据。换个角度想想，既然都用缓存了，做到最终一致性就行了。

> 使用队列消费、补偿事务，mq+job+binlog等，保证最终一致


### MySQL中的各种自增ID
MySQL中有各种各样的自增ID。例如我们最常见的表的主键自增ID，Xid，事务的ID，线程的ID，表的编号ID，binlog日志文件的ID等等。

这些ID都是有它自己的增长规律的，并不是随机生成的。MySQL的整体功能设计，有很多地方都依赖于这些ID的增长规律。

总结：
- 表的自增id达到上限后，再申请时它的值就不会改变，进而导致继续插入数据时报主键冲突的错误。
- row_id达到上限后，则会归0再重新递增，如果出现相同的row_id，后写的数据会覆盖之前的数据。
- Xid只需要不在同一个binlog文件中出现重复值即可。虽然理论上会出现重复值，但是概率极小，可以忽略不计。InnoDB的max_trx_id递增值每次MySQL重启都会被保存起来，所以我们文章中提到的脏读的例子就是一个必现的bug，好在留给我们的时间还很充裕。
- thread_id是我们使用中最常见的，而且也是处理得最好的一个自增id逻辑了。

#### 主键自增ID
定义自增ID字段的类型为int，而int类型是一个大类，它有可以细分为`tinyint`、`smallint`、`mediumit`、`int`、`bigint`5中类型

|类型	|大小	|范围（有符号）	|范围（无符号）	|用途|
|---|---|---|---|---|
|TINYINT|	1 byte|	(-128，127)|	(0，255)	|小整数值|
|SMALLINT|	2 bytes|	(-32 768，32 767)|	(0，65 535)	|大整数值|
|MEDIUMINT|	3 bytes	(-8 388 608，8 388 607)|	(0，16 777 215)	|大整数值|
|INT或INTEGER|	4 bytes|	(-2 147 483 648，2 147 483 647)|	(0，4 294 967 295)	|大整数值|
|BIGINT|	8 bytes|	(-9,223,372,036,854,775,808，9 223 372 036 854 775 807)|	(0，18 446 744 073 709 551 615)	|极大整数值|
|FLOAT|	4 bytes|	(-3.402 823 466 E+38，-1.175 494 351 E-38)，0，(1.175 494 351 E-38，3.402 823 466 351 E+38)|	0，(1.175 494 351 E-38，3.402 823 466 E+38)	|单精度、浮点数值|
|DOUBLE|	8 bytes|	(-1.797 693 134 862 315 7 E+308，-2.225 073 858 507 201 4 E-308)，0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308)|	0，(2.225 073 858 507 201 4 E-308，1.797 693 134 862 315 7 E+308)	|双精度、浮点数值|
|DECIMAL|	对DECIMAL(M,D) ，如果M>D，为M+2否则为D+2|	|依赖于M和D的值|	依赖于M和D的值

- 单位换算规则
  不同的整型数据类型所占用的磁盘存储空间是不同的。具体的换算用到的单位如下：
```
1PB(拍字节)=1024TB(太字节)，简写为T
1TB=1024GB(吉字节)，简写为G
1GB=1024MB(兆字节)，简写为M
1MB=1024KB(千字节)，简写为K
1KB=1024Byte(字节)，简写为B
1Byte=8Bit(位)，简写为b
1Bit=1个二进制数字，值为0或者1
```

**tinyint**（tinyint占用1个byte，也就是8个bit，1byte=8bit，即为：一个字节等于8位）

- 无符号位的计算方式

一个8位的无符号二进制能存放的二进制数值范围是[00000000~11111111]，将其转换为十进制就是[0,255]，一共可以表示256个数。00000000为最小的二进制数，11111111为最大的二进制数。

- 有符号位的计算方式

在二进制中，正号用0表示，负号用1表示，并且需要把正负号放在二进制的最高位，也就是最左边的位置，剩余右边的7个位置用来表示二进制的具体数值。那么一个有正负号的8位二进制取值范围就是[11111111,01111111]。
> 去掉左侧第一位用来标记正负号的位置，还剩余7个位置，这7个位置都是1的时候是最大的二进制数。
> 如果前面使用一个负号(此时用1表示)就是最小的二进制数，如果前面增加一个正号(此时用0表示)就是最大的二进制数。
> 所以一个有正负号的8位的二进制数的取值范围为：[11111111,01111111]。即取值[-127,127]

那么问题就来了，怎么有符号的最小值是-127，而不是-128呢（这与我们印象中[-128，127]范围值不符啊），而且负的127个数+0+正的127个数=共255个数，少了一个去哪了？

那是因为，在计算机中，表示负值是用补码（正码取反再+1），正值的补码还是自身。

正0和负0在我们看来都是同一个数，但在有符号的二进制中却是不同的数，正0`00000000`，负0正码`10000000`，负0补码`10000000`

虽然“-0”也是“0”，但根据正、反、补码体系，“-0”的补码和“+0”是不同的，这样就出现两个补码代表一个数值的情况。为了将补码与数字一一对应，所以人为规定“0”一律用“+0”代表。同时为了充分利用资源，就将原来本应该表示“-0”的补码规定为代表-128。

**int**

- int和int(11)有什么区别（很多人在创建表的时候，习惯性的对int类型的字段指定一个长度单位，例如：int(11)是他们经常使用的方式，那你知道是为什么吗，那写成int和int(11)有什么区别？）

> int(11)中的11表示int类型所能存储的最小值的显示宽度。显示宽度只用于显示，并不能限制取值范围和占用空间。
> 如：int(3)、int(4)、int(8) 在磁盘上都是占用 4 bytes 的存储空间，int(3)它它允许的最大值也不会是999，他们的取值范围也都是[-2147483648,2147483647]有符号或者[0,4294967295]无符号，和int不指定长度一样。

- UNSIGNED ZEROFILL关键字

UNSIGNED：指定无符号取值范围

ZEROFILL：0填充（在使用ZEROFILL时，会自动添加UNSIGNED属性），表示不够M位时，用0在左边填充，如果超过M位，只要不超过数据存储范围即可

定义了一个int(11)类型字段后，如果后面不指定UNSIGNED ZEROFILL关键字，这个字段和int是一样的。只有指定的UNSIGNED ZEROFILL之后，这个int(11)中的11才起到作用。他起到的作用就是和UNSIGNED ZEROFILL配合使用，将我们插入的数据，在不满足长度的情况下，在前面补0。

比如我们定义了int(5)UNSIGNED ZEROFILL，那么当我们插入的数据值1234的时候，它会在1234前面补上0，显示为01234，仅此而已。如果整数值超过M位，就按照实际位数存储。只是无须再用字符 0 进行填充。

所以我们使用int类型的变量的时候，直接使用tinyint、smallint、mediumit、int、bigint中的某一种就可以，具体使用哪一种根据自己的业务量来定，而不需要指定长度。 除非你的业务需求中需要在不足数据位数的时候，在前面补0，但是这个功能需要在定义字段的时候结合UNSIGNED ZEROFILL关键字一起使用才有效果。

> 当自增主键的值，达到最大值之后，我们再次向表中插入数据的时候，会出现主键冲突，自增键的自增值将不会再次增加，一直保持最大值不在变化，我们获取到的自增值也一直是最大值

例如在mysql中int类型占四个字节，有符号书的话，最⼤值就是(2^31)-1，也就是2147483647，⼆⼗多亿。然后如果这个⾃增主键达到最⼤值，是会报错的
```
Duplicate entry '2147483647' for key 'PRIMARY'
```
解决⽅法：

- 修改id字段类型，int改为bigint，8个byte的bigint，这个类型的值，在理论上是不会用完的，但是与此同时，你要付出的存储空间也会别int大一倍。
- 分库分表
- 将int类型设置为⽆符号的可以扩⼤⼀倍，关键字`UNSIGNED`
- 也可以使用mysql重置自增id
  - 方法一：使用truncate命令（截断表）：truncate table tableName; 注：使用truncate命令会把表中数据全部删除，并且无法恢复，表和索引所占的空间会恢复到初始大小
  - 方法二：delete from tableName; alter tableName auto_increment=1;

#### row_id
我们在创建表的时候，如果不为表指定任何主键，那么MySQL会给这个表创建一个隐藏的自增ID主键，并且这个隐藏的自增ID的取值是从一个全局变量dict_sys.row_id中获取。这个变量是所有没有主键的表共享的。

这个变量占用6个byte，它的取值范围是2481，因为这个值对所有没有主键的表共享，如果你的MySQL数据库中，有很多没有主键的表，并且有很多的数据在这些表中，那么这个值是有可能达到最大值的。

如果这个全局变量的值达到了最大值，它就会从0开始从新开始计算。这就导致了没有主键的表中的数据可能会被覆盖的可能性。试想一下，如果一个表没有主键只有一列varchar类型的字段col_a，我们想里面插入数据的时候。当插入到最大行的时候，它会从0开始计算，此时我们插入an+1的时候，就会回到第一行a1的这个行上，会把a1这个行的数据内容被覆盖为an+1，以此类推，a2会被an+2覆盖掉。

所以建议所有的表都要设置一个主键，避免这个隐藏的全局自增值到达最大的2481之后会覆盖掉之前插入的数据。有了自增主键，即便是超过了自增值，在插入数据的时候，会有主键冲突的错误，这比不通知我们直接把数据给覆盖掉要好很多。

#### Xid
在MySQL的innodb数据表进行更新操作的时候，会涉及到redolog的两阶段提交和binlog日志的配合。以此来达到数据在逻辑上的一致性，从而保证了在MySQL数据库崩溃异常重启后，innodb表可以恢复已经正常提交的事务，这也就是我们经常所说的innodb的crash-safe的能力。

Xid是有MySQL的Server层维护的。

Xid是binlog文件中常见的一个ID，因为binlog是server层维护的日志，所以Xid也是由MySQL的Server层维护的。它在binlog文件中标识一个唯一的事务。

但是在不同的binlog文件中，这个Xid是有可能相同的。因为这个ID是来自于MySQL执行各种SQL语句的时候的查询编号，MySQL在为所有的SQL语句会分配一个唯一的编号，这个编号来自于全局变量：global_query_id。而global_query_id，它是维护在内存当中。它是占8个字节的bigint类型，最大值为：2的64次方减1。这就意味着，如果MySQL重启了，那么这个变量的值将会丢失，重启后这个值将会重新从0开始累加。

所以SQL语句的编号将会重新从0开始累加，这个查询语句的编号会赋值给对应的事务编号，但是binlog文件再MySQL重启后，会重新使用新的binlog日志文件。所以在同一个日志文件中，Xid是不可能相同的。

说Xid在同一个binlog日志文件中不可能相同的说法也不算太严谨，因为如果这个global_query_id达到最大值2的64次方减1之后，从新从0累计也有可能导致同一个binlog文件中的Xid的值重复。但是这个可能性几乎为0，因为我们的binlog日志文件在达到一定的大小后也会重新开启一个新的binlog日志文件。这个是有参数max_binlog_size控制的。

#### Innodb的事务ID
InnoDB的事务ID是指：trx_id。

和Xid不同，trx_id是由InnoDB引擎自己维护的。它的最大值为2的48次方减1。如果到达它的最大值之后，会从0开始累加。这个值再MySQL重启之后不会清零，它做了持久化的操作，所以重启后的MySQL事务ID是可以累积上一次的值的。

这可能潜在的隐藏一个bug，如果trx_id到达最大之后，重新从0累加，这就导致了事务的id重复了，这样在MySQL的MVCC多版本数据控制和一致性事务读取的时候，就可能会发生脏读。但是可以忽略这个bug，因为这个值已经很大了，不会那么快就出现这个bug。

trx_id的值来自于innodb内部自己维护的max_trx_id全局变量。每次需要申请新的trx_id的时候，就获得当前max_trx_id的值，然后再把max_trx_id的值加1为下次准备。注意：只读事务不会占用max_trx_id的值。

对于正在执行的事务，可以在information_schema.innodb_trx表中看到对应的事务信息，已经当前事务trx_id的值。

在MySQL的MVCC多版本控制的一致性事务视图在实现的过程中，就依赖于这个trx_id的值，因为它代表了每一行被修改数据的版本号，在每一行数据被修改后，都会拿当前修改这一行数据的事务的trx_id作为当前数据的版本号。当一个事务读到一行数据的时候，判断这个数据是否可见的方法，就是通过事务的一致性视图与这行数据的trx_id做对比。

#### 线程ID
线程ID是指：thread_id，我们平时执行showprocesslist;命令的时候就可以显示出这个线程ID。

thread_id的取值来自于系统保存的一个全局变量thread_id_counter，每新建一个连接，就将thread_id_counter赋值给这个新连接的线程变量。

它的大小是4个字节，最大值为：2的32次方减1，到达最大值之后，他会重新从0累加。但是它也不会重复，因为他们使用了唯一数组的设计理念，如下：

do{new_id=thread_id_counter++;}while(!thread_ids.insert_unique(new_id).second);


#### mysql sequences
前提：分布式场景使用，满足一定的并发要求

能想到的最简单的实现方式，一条数据库记录，不断update它的值。然后大部分的实现方案，都用到了函数。
```
CREATE TABLE `t_sequence` (

`sequence_name` varchar(64) CHARACTER SET utf8 COLLATE utf8_general_ci NOT NULL COMMENT '序列名称' ,

`value` int(11) NULL DEFAULT NULL COMMENT '当前值' ,

PRIMARY KEY (`sequence_name`)

)

ENGINE=InnoDB

DEFAULT CHARACTER SET=utf8 COLLATE=utf8_general_ci

ROW_FORMAT=COMPACT

;
```
获取当前值的函数：
```
CREATE DEFINER = `root`@`localhost` FUNCTION `currval`(sequence_name varchar(64))
 RETURNS int(11)
BEGIN
    declare current integer;
    set current = 0;
    select t.value into current from t_sequence t where t.sequence_name = sequence_name;
    return current;
end;
```
获取下一个值：
```
CREATE DEFINER = `root`@`localhost` FUNCTION `nextval`(sequence_name varchar(64))
 RETURNS int(11)
BEGIN
    declare current integer;
    set current = 0;
    
    update t_sequence t set t.value = t.value + 1 where t.sequence_name = sequence_name;
    select t.value into current from t_sequence t where t.sequence_name = sequence_name;

    return current;
end;
```


---


### mysql锁表处理
处理数据库锁表问题：直接定位线程kill
-- 查询所有正在使用中的表
show OPEN TABLES where In_use > 0;

-- 查看所有进程
show processlist;

-- kill指定线程
kill 893952;


### 数据库备份
1.编写sh脚本(文件目录需要提前创建)
mysqldump -uusername -ppassword DatabaseName > /home/dbback/DatabaseName_$(date +%Y%m%d_%H%M%S).sql
2.对脚本添加可执行权限
chmod u+x bkDatabase.sh

3.安装crontab定时任务模块（已完成）
crontab -u //设定某个用户的cron服务，一般root用户在执行这个命令的时候需要此参数
crontab -l //列出某个用户cron服务的详细内容
crontab -r //删除没个用户的cron服务
crontab -e //编辑某个用户的cron服务

使用crontab -e编写命令：*/1 * * * * /home/backup/bkDatabaseName.sh	//每分钟执行一次

cron:表达式
* * * * *——分 时 天 月 星期
* */2 * * *——每两小时
  0 23-7/2，8 * * *——晚上11点到早上8点每两个小时，和早上8点
  0 11 4 * 1-3 command line——每个月的4号和每个礼拜一到礼拜三的早上11点

* 表示该字段中的每个可能值。 ?意味着你不在乎价值。当您有两个可能相互矛盾的字段时使用它。
  常见的例子是月中的某一天和星期几字段。例如，考虑在每个月的第一天上午 10 点运行的 cron 规范：
  0 0 10 1 * ? *


### 数据库安全考虑

- 如何设计一个合理的DB：
用户表与密码表分开，表名加密，数据加密
同密码的hash码不同，防止字典攻击，
密码：使用动态salt

- 常见的破解方式
暴力攻击：给定长度，尝试每一种可能性
字典攻击：将常用的都放在一个文件中，不停的hash对比（取决字典大小以及字典是否合理）
查表破解：预先计算出密码字典中每一个密码的hash（设计查询器，提高查询效率）
反向查表破解：根据获取的数据库数据 做一个与用户名对应的hash表，然后将常用的字典密码hash之后跟这个表对比
彩虹表：使用空间换时间的技术，与查表破解相似，牺牲一些时间来达到更小的存储空间（已经能够破解8位任意字符的md5hash）

————————————————————————————————————————————————————————
哈希定律：只需要知道首尾，中间推算
————————————————————————————————————————————————————————

加盐：add salt
查表破解和彩虹表之所以能够有效，因为每一个密码都使用相同的方式hash
解决：每一个hash随机化，加 salt，同一个密码hash两次

错误用法： 一个salt在多个hash中使用或使用的salt很短

短的盐：例，3位ASCII码，一共 95×95×95=857,375可能，再为每一个salt制作一个包含1MB的常见密码表，一共才837G，所以。。。

注：不要用用户名做salt
盐的大小要跟hash函数一致，如：
SHA26的输出是256bits（32bytes），所以的salt的长度也应该为32位随机字符串

salt要使用密码上可靠些的伪随机数生成器
如：java：java.security。

——————————————————————————————————————
存储一个密码：
1.生成一个随机Salt
2.salt和密码拼接，使用标准hash函数加密
3.将salt和hash记录在数据库

验证一个密码：
1.从数据库取出用户的salt和hash
2.用密码和salt用相同的方式拼接在一起，使用相同的hash
3.比对

客户端hash
1.客户端密码并不是https（SSL/TLS）的替代品
2.部分浏览器可能不支持javascript，要判断模拟客户端的hash
3.客户端hash也要加盐，但不能是请求服务器得到用户的盐（防止验证）








