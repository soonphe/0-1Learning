# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## 数据库SQL：GROUPBY多分组+取MAX最大(MIN最小)值

### 测试目的
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
  `class` int(4) NULL DEFAULT 0 COMMENT '班级',
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

### 错误写法
````
SELECT *,MAX(age) FROM student
SELECT *,MAX(age) FROM student GROUP BY `class`
````
错误分析：
MAX并不会取到最大值所在行

````
SELECT
    * 
FROM
    ( SELECT * FROM student order by age desc,class asc) AS b
GROUP BY
    `class`;
````
错误分析：
分组并不会取排序的第一条结果


### 第一种方式正确写法——使用limit
````
SELECT
    * 
FROM
    ( SELECT * FROM student order by age desc,class asc limit 99999999) AS b
GROUP BY
    `class`;
````
分析：
limit 99999999是必须要加的，如果不加的话，数据不会先进行排序，通过 explain 查看执行计划，可以看到没有 limit 的时候，少了一个 DERIVED(得到) 操作。


### 第一种方式正确写法——使用having
````
SELECT
    * 
FROM
    ( SELECT * FROM student having 1 order by age desc,class asc) AS b
GROUP BY
    `class`;
````
分析：
通过 explain 查看执行计划，可以看到 使用having 1，也会使用 DERIVED(得到) 操作。

---

### 第二种方式写法分析
错误写法示例
````
SELECT 
    * 
FROM student 
WHERE age in 
    (select max(age) Max_age FROM student GROUP BY `class`) 
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
    (select max(age) Max_age FROM student GROUP BY `class`) 
GROUP BY 
    `class`;
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
			(select max(age) Max_age FROM student GROUP BY `class`) 
	order by age desc,id desc,class asc
			) as b
GROUP BY 
    `class`;
````

但是使用having或者limit可以实现分组取最新一条数据
````
SELECT
    * 
FROM
    ( SELECT * FROM student having 1 order by age desc,id desc) AS b
GROUP BY
    `class`;
````

### 结论

推荐使用having或者limit实现多分组取最大(小)值

