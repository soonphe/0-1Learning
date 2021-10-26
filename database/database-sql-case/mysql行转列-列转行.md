# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## mysql行转列-列转行

### 需求
学生成绩表是单科存储，如何在一条记录中显示学生全部学科成绩

### 测试案例
```
CREATE TABLE student_score(
id BIGINT PRIMARY key auto_increment,
s_name VARCHAR(20) ,
s_sub VARCHAR(20),
s_score INT 
);

insert into student_score values(null,'张三','数学',90);
insert into student_score values(null,'张三','语文',85);
insert into student_score values(null,'张三','英语',92);
insert into student_score values(null,'李四','数学',88);
insert into student_score values(null,'李四','语文',91);
insert into student_score values(null,'李四','英语',99);
insert into student_score values(null,'王五','数学',100);
insert into student_score values(null,'王五','语文',82);
insert into student_score values(null,'王五','英语',88);

```
最终希望得到
```
s_name	数学	语文	英语
张三	90	85	92
李四	88	91	99
李四	100	82	88
```

### 解决方案一：case when
```
select s_name,
sum(case s_sub when '数学' then s_score else 0 end) 数学,
sum(case s_sub when '语文' then s_score else 0 end) 语文,
max(case s_sub when '英语' then s_score else 0 end) 英语
from student_score group by s_name
```
原理：就是把每一行都变成数学、语文、英语三列存在的表，有值就放入，无值就置为0，最后用聚合函数聚合
备注：这里用sum和max函数都行，实际看具体业务

---

### 拓展：多行转一行
希望得到
```
'张三'    '数学:90,语文:85,英语92'
```
完整sql如下：
```
select s_name,group_concat(s_sub,':',s_score separator '@') all_score 
from  student_score group by s_name
```
原理：group_concat参数可为多个列，separator为列之间的分隔符，默认为','
说明：最后一定要用group by 维度聚合,不写group by 会拼接所有的行

### 拓展：列转行
假设数据为：
```
s_name	数学	语文	英语
张三	90	85	92
李四	88	91	99
李四	100	82	88

DROP TABLE IF EXISTS `s_score`;
CREATE TABLE `s_score` (
  `id` bigint(20) NOT NULL AUTO_INCREMENT,
  `s_name` varchar(20) COLLATE utf8mb4_bin DEFAULT NULL,
  `c_math` int(11) DEFAULT NULL,
  `c_language` int(11) DEFAULT NULL,
  `c_english` int(11) DEFAULT NULL,
  PRIMARY KEY (`id`)
) ENGINE=InnoDB AUTO_INCREMENT=4 DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_bin;

-- ----------------------------
-- Records of s_score
-- ----------------------------
BEGIN;
INSERT INTO `s_score` VALUES (1, '张三', 90, 85, 92);
INSERT INTO `s_score` VALUES (2, '李四', 88, 91, 99);
INSERT INTO `s_score` VALUES (3, '王五', 100, 82, 88);
COMMIT;
```

想要实现
```
s_name s_sub s_score
张三 数学 90
张三 语文 85
张三 英语 92
...
```

完整sql如下：
```
SELECT
	s_name,
	'数学' AS s_sub,
	c_math AS s_score
  FROM s_score
UNION
SELECT
	s_name,
	'语文' AS s_sub,
	c_language AS s_score
  FROM s_score
UNION
SELECT
	s_name,
	'英语' AS s_sub,
	c_english AS s_score
  FROM s_score
```
原理：group_concat参数可为多个列，separator为列之间的分隔符，默认为','
说明：最后一定要用group by 维度聚合,不写group by 会拼接所有的行



