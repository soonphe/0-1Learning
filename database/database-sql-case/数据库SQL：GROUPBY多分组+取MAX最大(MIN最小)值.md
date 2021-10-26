# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 数据库SQL：GROUPBY多分组+取MAX最大(MIN最小)值

### 需求
如何在一条sql中，对数据表中的数据进行分组，同时求每组最大(小)值

### 测试案例
求每个班级中的年龄最大的学生
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
  `age` int(3) NULL DEFAULT 0 COMMENT '年龄',
  `c_class` int(4) NULL DEFAULT 0 COMMENT '班级',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE = MyISAM AUTO_INCREMENT = 7 CHARACTER SET = utf8mb4 COLLATE = utf8mb4_bin ROW_FORMAT = Dynamic;

-- ----------------------------
-- Records of student
-- ----------------------------
INSERT INTO `student` VALUES (1, '张三', 22, 1);
INSERT INTO `student` VALUES (2, '李四', 26, 1);
INSERT INTO `student` VALUES (3, '王五', 20, 2);
INSERT INTO `student` VALUES (4, '赵六', 20, 2);
INSERT INTO `student` VALUES (5, '孙七', 22, 3);
INSERT INTO `student` VALUES (6, '李八', 28, 3);
INSERT INTO `student` VALUES (7, '阿九', 28, 3);
````

### 案例分析
有两种方式都可以找到结果
* 第一种实现方式
    先排序，然后外层包一层查询+分组（这种有个问题，就是分组必须取第一条数据，**可是分组默认并不一定取第一条数据**）
* 第二种实现方式
    先查询所有的最大(小)值，然后外面包一层查询+分组（这种也存在问题，一是性能问题，还有同组存在多个最大值相同也有问题）

### 错误写法——使用MAX函数
````
SELECT *,MAX(age) FROM student
SELECT *,MAX(age) FROM student GROUP BY c_class
````
错误分析：
MAX并不会取到最大值所在行，*分组并不会取最大值所在行*


### 错误写法——使用排序+分组
````
SELECT
    * 
FROM
    ( SELECT * FROM student order by age desc,c_class asc) AS b
GROUP BY
    c_class;
````
错误分析：
分组并不会取排序的第一条结果


### 第一种方式正确写法——使用limit
````
SELECT
    * 
FROM
    ( SELECT * FROM student order by age desc,c_class asc limit 99999999) AS b
GROUP BY
    c_class;
````
分析：
limit 99999999是必须要加的，如果不加的话，数据不会先进行排序，通过 explain 查看执行计划，可以看到没有 limit 的时候，少了一个 DERIVED(得到) 操作。
  
### 第一种方式正确写法——使用having
````
SELECT
    * 
FROM
    ( SELECT * FROM student having 1 order by age desc,c_class asc) AS b
GROUP BY
    c_class;
````
分析：
通过 explain 查看执行计划，可以看到 使用having 1，也会使用 DERIVED(得到) 操作。


### 子查询展开/派生类合并——（这也是为什么不加limit只查询到一张表，加limit才走两张表）
不加limit的时候，执行日志显示只有一个表的处理，不对呀，应该是两张表，先从from查询出一张表然后再从这张表筛选出一张新表，总共两张表才对。 
原来，这是mysql的版本在捣鬼，
在mysql5.6中，如果是这样写确实会出现两张表的处理，执行日志显示出现了一个主表一个Derived表，
Derived为派生表，也就是说，from里面查询出的是派生表，也可以理解为临时表，先将查询到的记录放到这个临时表，然后再从这个临时表进行分组，分组后的结果放入一张新表，就产生了正确结果。

**那么为什么切换了版本后就好了呢？**
其实mysql5.7针对于5.6版本做了一个优化，针对mysql本身的优化器增加了一个控制优化器的参数叫 derived_merge ，什么意思呢，“派生类合并”。
ok，既然已经了解了很多，原来是派生类合并在作怪。
官方手册：

>优化器可以使用两种策略（也适用于视图引用）处理派生类引用：
1.将派生类合并到外部查询块中
2.将派生类实现为内部临时表
````
SELECT * FROM
    ( SELECT * FROM student order by age desc,c_class asc) AS b;

等价于
SELECT * FROM student order by age desc,c_class asc;
````

同时由于这个机制，子查询中的里面的 order by 应该会跟外部块一起执行，
也就是说 order by 会跑到外面来（说的形象一点哈），那么为什么结果的排序依旧是乱的：    
官方使用文档：

> 如果这些条件都为真，则优化器将派生类或视图引用中的ORDER BY子句传播到外部查询块。
1.外部查询未分组或聚合
2.外部查询未指定DISTINCT,HAVING或ORDER BY
3.外部查询将此派生表或视图引用作为FROM子句中的唯一源

>否则，优化器将忽略ORDER BY子句

我们之前的sql里的外部块由于使用到了分组，那么优化器会忽略掉 order by 子句

### 使合并派生类失效
其实也有多种办法不需要修改 derived_merge 参数而使合并派生类失效，具体做法可参考官方使用手册， 摘抄手册文：

> 可以通过在子查询中使用任何阻止合并的构造来禁用合并，尽管这些构造对实现的影响并不明确。 防止合并的构造对于派生表和视图引用是相同的：
   1.聚合函数（ SUM() ， MIN() ， MAX() ， COUNT()等）
   2.DISTINCT
   3.GROUP BY
   4.HAVING
   5.LIMIT
   6.UNION或UNION ALL
   7.选择列表中的子查询
   8.分配给用户变量
   9.仅引用文字值（在这种情况下，没有基础表）

---

### 第二种方式写法分析
错误写法示例
````
SELECT 
    * 
FROM student 
WHERE age in 
    (select max(age) Max_age FROM student GROUP BY c_class) 
````
分析：
数据量如果很大会存在效率问题，max会扫描全表，in也会扫描全表，
如果存在同组多个最大值相同也有问题，同组可能会得出多个结果


所以，只能在最外层再添加一层GROUP BY
````
SELECT 
    * 
FROM student 
WHERE age in 
    (select max(age) Max_age FROM student GROUP BY c_class) 
GROUP BY 
    c_class;
````

> 是不是到这里感觉的结果是对的，但是你以为这样就正确了吗？

这里的分组与第一种写法会面临同样的问题，分组并不会取第一条数据
例如：如果我要取同组最大值最新一条数据，就会报错
你可以试试重新排序再分组，看能不能得到对应的结果（我试了，结果并不能）
````
select * from (
	SELECT 
			* 
	FROM student 
	WHERE age in 
			(select max(age) Max_age FROM student GROUP BY c_class) 
	order by age desc,id desc,c_class asc
			) as b
GROUP BY 
    c_class;
````

但是使用having或者limit可以实现分组取最新一条数据
````
SELECT
    * 
FROM
    ( SELECT * FROM student having 1 order by age desc,id desc) AS b
GROUP BY
    c_class;
````

### 结论

推荐使用having或者limit实现多分组取最大(小)值

