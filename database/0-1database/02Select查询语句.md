# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## Select查询语句

### 前言 
    有了数据库和数据，下面我们肯定是要查询数据库，那么我们就来编写第一条查询语句。

### 本章要点
    1.SQL语言简介
    2.基本SELECT查询语句
    3.SQL语句的书写规则
    4.算术表达式的使用
    5.空值（NULL）的应用
    6.列别名的使用
    7.连接运算符的使用
    8.DISTINCT关键字的用法


### SQL语言简介
• SQL称结构化查询语言 (Structured Query Language)

• SQL 是操作和检索关系型数据库的标准语言。

• 使用SQL语句，程序员和数据库管理员可以完成如下的任务:

    – 改变数据库的结构
    
    – 更改系统的安全设置
    
    – 增加用户对数据库或表的许可权限
    
    – 在数据库中检索需要的信息
    
    – 对数据库的信息进行更新

### SQL语句分类

    – DQL语句（数据查询语言） Select
    
    – DML语句（数据操作语言） Insert / Update / Delete / Merge
    
    – DDL语句（数据定义语言） Create / Alter / Drop / Truncate
    
    – DCL语句（数据控制语言） Grant / Revoke
    
    – TCL语句事务控制语句 Commit / Rollback / Savepoint

### 基本SELECT语句

• 基本查询语句语法：

    SELECT *|{[DISTINCT] 列名|表达式 [别名][,...]} 
    FROM    表名;

语法解析,主要可分为三部分：
1. select：标志为查询语句
2. “*”号的使用：查询所有列（查找部分列则给出列名即可）
3. from 表名：from后面接表名，标识从哪张表去操作数据，增删改查SQL都会有这部分结构

### 准备表和数据
* 例：新建student表，SQL如下
```
SET NAMES utf8mb4;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
-- Table structure for student
-- ----------------------------
DROP TABLE IF EXISTS `student`;
CREATE TABLE `student` (
  `id` int(10) unsigned NOT NULL AUTO_INCREMENT COMMENT '主键',
  `name` varchar(255) COLLATE utf8mb4_bin DEFAULT '' COMMENT '姓名',
  `age` int(3) DEFAULT '0' COMMENT '年龄',
  `c_class` int(4) DEFAULT '0' COMMENT '班级',
  `c_score` varchar(100) COLLATE utf8mb4_bin DEFAULT NULL COMMENT '分数',
  `c_create_date` datetime DEFAULT NULL COMMENT '创建时间',
  PRIMARY KEY (`id`) USING BTREE
) ENGINE=MyISAM AUTO_INCREMENT=22 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin;

-- ----------------------------
-- Records of student
-- ----------------------------
BEGIN;
INSERT INTO `student` VALUES (1, '张三', 22, 1, 61, '1999-01-01 00:00:00');
INSERT INTO `student` VALUES (2, '李四', 26, 1, 65, '2000-01-01 00:00:00');
INSERT INTO `student` VALUES (3, '王五', 20, 2, 80, '2001-01-01 00:00:00');
INSERT INTO `student` VALUES (4, '赵六', 20, 2, 80, '2002-01-01 00:00:00');
INSERT INTO `student` VALUES (5, '孙七', 22, 3, 90, '2003-01-01 00:00:00');
INSERT INTO `student` VALUES (6, '李八', 28, 3, 90, '2004-01-01 00:00:00');
INSERT INTO `student` VALUES (7, '阿九', 28, 3, 95, '2005-01-01 00:00:00');
INSERT INTO `student` VALUES (8, '钱石', 28, 98, NULL, NULL);
INSERT INTO `student` VALUES (9, '三班', 28, 99, NULL, NULL);
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;
```

### 第一条查询语句
• 查询所有学生的信息。

    SELECT * FROM student;
    
• 查询所有学生的信息（查询全部列和指定列）。

    SELECT id, name,age,c_class FROM student;
    
    SELECT id, name FROM student;

比较哪条语句执行效率更高？
    
    两者差别几乎可忽略，但是在数据传输的网络负担，还有在查询的字段是索引的情况时， * 查询会较慢


### SQL语句的书写规则

• SQL语句相关概念：

– 关键字（Keyword） ：SQL语言保留的字符串，在自己的 语法使用。例如，SELECT 和FROM 是关键字。

– 语句（statement）：一条完整的SQL命令。例如， SELECT * FROM student;是一条语句。

– 子句（clause）：部分的SQL语句，通常是由关键字加上 其他语法元素构成。例如，SELECT *是子句，FROM student也是子句。

• SQL语句规则：

    • 不区分大小写。也就是说SELECT，select，Select，执行时效 果是一样的。
    
    • 可以单行来书写，也可以书写多行,建议分多行书写，增强代码 可读性。通常以子句分行。
    
    • 关键字不可以缩写、分开以及跨行书写。如SELECT不可以写 成SEL或SELE CT等形式。
    
    • 每条语句需要以分号（；）结尾。
    
    • 关键字大写，其他语法元素（如列名、表名等）小写。
    
    • 代码适当缩进。

### 算术表达式的使用

• 算术运算符：+，-，*，/

• 算术表达式中优先级规则：

    – 先算乘除，后算加减。
    
    – 同级操作符由左到右依次计算。
    
    – 括号中的运算优先于其他运算符。
    
    • 对NUMBER型数据可以使用算数操作符创建表达式(+ - * /)
    
    • 对DATE型数据可以使用部分算数操作符创建表达式 (+ -)

优先级示例：

    SELECT id, name,age+10 FROM student;
    
    SELECT id, name,age*12 FROM student;

    SELECT id, name,(400+age)*12 FROM student;


### 空值（NULL）的应用

• NULL：表示未定义的，未知的。空值不等于零或空格。任意 类型都可以支持空值。

• 空值（NULL）在算术表达式中的使用

    – 包括空值的任何算术表达式都等于空
    
### 使用列别名的方法 
• 列别名基本书写方法有两种方式：

– 第一种方式：列名 列别名

– 第二种方式：列名 AS 列别名

• 以下三种情况，列别名两侧需要添加双引号（""）：

    – 列别名中包含有空格
    
    – 列别名中要求区分大小写
    
    – 列别名中包含有特殊字符

示例：

    SELECT id, name as student_name, (10+age)*12 "Big Age" FROM student;

### DISTINCT关键字的用法

• DISTINCT取消重复行

    SELECT DISTINCT c_class FROM student;
    
    SELECT DISTINCT age, c_class FROM student;

### DESC[RIBE]命令

• DESC + 表名 命令：显示表结构

    – DESC student




