# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## where条件查询和Order By数据排序

### 前言 
    有没有觉得前面的查询过于简单，根本不满足我们的日常需求，
    其实，在查询中我们还可以添加很多查询条件，还可以添加运算符，加上排序，让我们赶紧试试吧。

### 要点
    1.where子句
    2.运算符的使用 
    3.Order by子句

### WHERE子句的使用 

WHERE子句一般用来限制数据，使用WHERE子句限定返回符合条件的记录

**• WHERE子句在 FROM 子句后，一般后面还会使用各类运算符去筛选数据**

• 语法：

    SELECT *|{[DISTINCT] 列名|表达式 [别名][,...]} 
    FROM 表名 
    WHERE 条件;

• 查询公司年龄大于22的员工信息。

    SELECT id, name, age 
    FROM student 
    WHERE age >= 22;

### 比较运算符的使用
|运算符|含义|
|---|---|
|=|等于|
|>|大于|
|>=|大于等于|
|<|小于|
|<=|小于等于|
|<>|不等于|

### 字符型与日期型大小写敏感的实例

• 使用比较运算符需要遵循以下原则：

– 字符及日期类型需要在两端用单引号；

– 字符类型大小写敏感；

– 日期类型格式敏感，默认格式'yyyy-MM-dd HH:mm:ss';

例:
• 查询创建时间大于1999年1月1日的用户信息。

    SELECT id, name, age 
    FROM student 
    WHERE c_create_date >= '1999-01-01';

### 特殊比较运算符
|运算符 |含义|
|---|---|
|BETWEEN...AND...|    确定范围，在两个值之间 (包含比较值)|
|IN（ 列表）| 确定集合|
|LIKE   | 字符串匹配查询|
|IS NULL |判断空值|


#### BETWEEN…AND…运算符的使用
• 查询年龄在20到22的雇员。
    SELECT id, name, age
    FROM student
    WHERE age BETWEEN 20 AND 22;


#### IN运算符的使用
• IN运算符主要对指定的值进行比较查看的时候使用。

• 查询ID为1、3或5的用户信息。

    SELECT id, name, age
    FROM student
    WHERE id IN (10, 90, 110);

#### LIKE运算符的使用

• 使用LIKE运算符完成模糊查询功能

• 使用通配符来代替未知的信息。常用通配符有 %和_ 。

– %可以代替任意长度字符（包括长度为0）。

– _可以代替一个字符。

例
• 查询name首字母是张的用户信息。

    SELECT id, name, age
    FROM student
    WHERE name LIKE '张%';


• %与_组合使用

例
• 查询name第二个字符为四的用户信息。

    SELECT id, name, age
    FROM student
    WHERE name LIKE '_四%';


#### IS NULL运算符的使用

• 查询包含空值的记录

• 查询班级信息为空的用户信息。

    SELECT id, name, age
    FROM student
    WHERE c_class IS NULL;

### 逻辑运算符的使用
|运算符 |含义|
|---|---|
|AND |如果组合的条件都是TRUE,返回TRUE。NULL和 FALSE组合，返回FALSE。|
|OR  |如果组合的条件之一是TRUE,返回TRUE。NULL和 TRUE组合，返回TRUE。|
|NOT |如果下面的条件是FALSE,返回TRUE。|

例
• 查询年龄在20到22之间的用户信息。

    SELECT id, name, age
    FROM student
    WHERE age>=20 AND age<=22;

• 年龄在20到22，并且班级在1和2的用户信息。

    SELECT id, name, age
    FROM student
    WHERE age>20 AND c_class in (1,2);

• 年龄在20到22，或者班级在1和2的用户信息。

    SELECT id, name, age
    FROM student
    WHERE age>20 OR c_class in (1,2);

• 查找班级不在1和2的用户信息。

    SELECT id, name, age
    FROM student
    WHERE c_class not in (1,2);

NOT运算符还可以和BETWEEN…AND、LIKE、IS NULL一起使用。

    – ...WHERE id NOT IN (1, 2);
    
    – ... WHERE age NOT BETWEEN 20 AND 22;
    
    – ... WHERE name NOT LIKE 'D%'
    
    – ... WHERE c_class IS NOT NULL

### 运算符的优先级
• 括号’()’优先于其他操作符。

|优先级 |运算分类   | 运算符举例|
|---|---|---|
|1   |数学运算符   *, \, +, -|
|2   |连接运算符   |||
|3   |通用比较运算符 =, <>, <, >, <=, >=|
|4   |其他比较运算符 IS [NOT] NULL, LIKE, [NOT] BETWEEN, [NOT] IN|
|5   |逻辑非 NOT|
|6   |逻辑与 AND|
|7   |逻辑或 OR|

注：
1、乘除的优先级高于加减；
2、同一优先级运算符从左向右执行；
3、括号内的运算先执行。

例:
查找班级编号为1 或 班级为2且年龄大于22的用户信息。（这里or的优先级会低于and的优先级）

    SELECT id, name, age
    FROM student
    WHERE c_class = 1 OR c_class = 2 AND age > 22;

查找年龄大于22 且 班级编号为1或者2的用户信息。（括号内的运算先执行）

    SELECT id, name, age
    FROM student
    WHERE (c_class = 1 OR c_class = 2) AND age > 22;

### ORDER BY子句

• ORDER BY子句后的语法结构如下：

    SELECT *|{[DISTINCT] 列名|表达式 [别名][,...]} 
    FROM 表名 [WHERE 条件] 
    ORDER BY {列名|表达式|别名} [ASC|DESC],…;

例：查看用户信息降序排列。

    SELECT id, name, age
    FROM student
    ORDER BY id DESC;


• 不同数据类型排序规则(以升序为例)

– 数字升序排列小值在前，大值在后。即按照数字大小顺序由 小到大排列。

– 日期升序排列相对较早的日期在前，较晚的日期在后。例 如：’01-SEP-06’在’01-SEP-07’前。

– 字符升序排列按照字母由小到大的顺序排列。即由A-Z排列； 中文升序按照字典顺序排列。

– 空值在升序排列中排在最后，在降序排列中排在最开始。

注：MySQL 默认升序
*特别说明*：mysql在不给定order by条件的时候，得到的数据结果的顺序是跟查询列有关的。
在查询不同列的时候，可能会使用到不同的索引条件。
Mysql在使用不同索引的时候，得到的数据顺序是不一样的

例•：查看用户信息，结果按照班级升序排列，年龄按照降序排列。

    SELECT id, name, age, c_class
    FROM student
    ORDER BY c_class, age desc;

• ORDER BY特殊使用 – ORDER BY子句可以出现在SELECT子句中没有出现过的列。

– ORDER BY子句后的列名，可以用数字来代替。这个数字是 SELECT语句后列的顺序号。

例：查看用户信息，按照班级从低到高显示，而具体的工资数不 显示。

    SELECT id, name, age
    FROM student
    ORDER BY c_class;

• 查看用户信息，结果按照年龄升序排列，班级降序排列。

    SELECT id, age, c_class
    FROM student
    ORDER BY 2, 3 desc;



