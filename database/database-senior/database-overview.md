# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 数据库进阶知识

### 规则
传统数据库遵循ACID规则：Atomic（原子性)Consistency（一致性）Isolation（隔离性）Durability（持久性）
NoSql一般为分布式，遵循CAP定理：一致性（Consistency）、可用性（Availability）、分区容错性（Partition tolerance）

### 常用操作数据库语句
查看mysql数据库所有信息：show variables
查看mysql字符串相关信息：show variables  like "%char%";

### 修改字段及属性
数据库中修改字段名：
  mysql中：alter table adminuser change AU_name（列名） namee（新列名） varchar(255)（原始列名属性）
	 注：数据类型的修改可以  使列名不变，只修改后面的列名属性
  oracle中：使用rename关键字来实现字段名的修改:alter table 表名 rename column 旧的字段名 to 新的字段名名;
	    使用modify关键字来实现对数据类型的修改:alter table 表名 modify 字段名 数据类型;
  sqlserver：改变字段名：ALTER TABLE table_name RENAME COLUMN old_name to new_name;
	  改变数据类型：ALTER TABLE table_name ALTER COLUMN column_name datatype
	  增加或删除列：ALTER TABLE table_name ADD（删除使用drop） column_name datatype（删除没有datatype）

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
* 读未提交（Read Uncommitted）：只处理更新丢失。如果一个事务已经开始写数据，则不允许其他事务同时进行写操作，但允许其他事务读此行数据。可通过“排他写锁”实现。
* 读提交（数据库引擎的默认级别）（Read Committed）：处理更新丢失、脏读。读取数据的事务允许其他事务继续访问改行数据，但是未提交的写事务将会禁止其他事务访问改行。可通过“瞬间共享读锁”和“排他写锁”实现。
* 可重复读取（Repeatable Read）：处理更新丢失、脏读和不可重复读取。读取数据的事务将会禁止写事务，但允许读事务，写事务则禁止任何其他事务。可通过“共享读锁”和“排他写锁”实现。
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

还有一种：给字段加上前缀索引，然后用like q%来查询就能走索引了，把捞到的数据再用程序做一次筛选就搞定（得建立合适的前缀索引，q要覆盖到前缀条件）










