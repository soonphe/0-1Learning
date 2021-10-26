# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## left(right) join和inner join的区别

### 区别总结

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


### 案例
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

2. left join
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







