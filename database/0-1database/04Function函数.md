# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")



## Function函数

### 前言 
    为什么要有函数，不如问函数是什么，是干嘛的！
    函数是为了方便我们的各种操作而产生的，就是一个设计好的方程式，给出参数，它就给你对应的结果

### 要点
    1.字符函数
    2.数值函数
    3.日期函数
    4.转换函数
    5.流程函数
    6.系统函数
    7.函数嵌套


### 单行函数
• 语法:
函数名[(参数1，参数2,…)]

• 其中的参数可以是以下之一：
– 变量
– 列名
– 表达式

• 单行函数还有以下的一些特征：
– 单行函数对单行操作
– 每行返回一个结果
– 有可能返回值与原参数数据类型不一致（转换函数）
– 单行函数可以写在SELECT、WHERE、ORDER BY子句中
– 有些函数没有参数，有些函数包括一个或多个参数
– 函数可以嵌套

### 字符函数
字符函数：主要指参数类型是字符型，不同函数返回值可能是 字符型或数字类型。

* 字符函数
    * 大小写转换：   
        • LOWER(列名|表达式): 将字符转换成 小写
        • UPPER(列名|表达式): 将字符转换成 大写
    * 字符处理：    
        • LENGTH:取字符长度 格式:LENGTH(column | expression)
        • CONCAT:连接两个值,等同于|| 格式：CONCAT(column1|expression1,column2|expression2)
        • SUBSTR:截取，返回第一个参数中从n1字符开始长度为n2的子串，如果n1是负值，表示 从后向前数的abs(n1)位，如果n2省略,取n1之后的所有字符 格式：SUBSTR(column | expression,n1[,n2])
        • INSTR:定位，返回s1中，子串s2从n1开始，第n2次出现的位置。n1,n2默认值为1 格式:INSTR(s1,s2,[,n1],[n2])
        • TRIM:去除空格，也可去除字符串头部或尾部（头尾）的字符 格式:TRIM(leading | trailing | both trim_character From trim_source)
        • LPAD:左填充，返回s1被s2从左面填充到n1长度。 格式：LPAD(s1,n1,s2)
        • RPAD:右填充，返回s1被s2从右面填充到n1长度。 格式：RPAD(s1,n1,s2)
        • REPLACE:替换，把s1中的s2用s3替换。 格式:REPLACE(s1,s2,s3)

#### 字符大小写操作函数

• LOWER：示例：LOWER('SQL Course') 结果：sql course
• UPPER：示例：UPPER('SQL Course') 结果：SQL COURSE 

#### 字符处理函数  
|函数 |结果|
|---|---|
|LENGTH('String')   | 6|
|CONCAT('Good', 'String')  |  GoodString|
|SUBSTR('String',1,3)   | Str|
|INSTR('String', 'r')    |3|
|TRIM('S' FROM 'SSMITH')   |  MITH|
|LPAD(sal,10,'*')   | ******5000|
|RPAD(sal,10,'*')    |5000******|
|REPLACE（'abc','b','d')  |adc|

LEFT(str,len):返回字符串str的最左面len个字符。
select LEFT('foobarbar', 5);
 
RIGHT(str,len):返回字符串str的最右面len个字符。 
select RIGHT('foobarbar', 4); 

LOCATE(substr,str):返回子串substr在字符串str第一个出现的位置，如果substr不是在str里面，返回0. 
select LOCATE('bar', 'foobarbar'); 
select LOCATE('xbar', 'foobar'); 

LTRIM(str):返回删除了其前置空格字符的字符串str。
select LTRIM(' barbar'); 

RTRIM(str):返回删除了其拖后空格字符的字符串str。
select RTRIM(‘barbar ’); 

REPEAT(str,count):返回由重复countTimes次的字符串str组成的一个字符串。如果count <= 0，返回一个空字符串。如果str或count是NULL，返回NULL。 
select REPEAT('MySQL', 3); 

REVERSE(str):返回颠倒字符顺序的字符串str。 
select REVERSE('abc');

INSERT(str,pos,len,newstr):返回字符串str，在位置pos起始的子串且len个字符长的子串由字符串newstr代替。 
select INSERT(‘whatareyou', 5, 3, ‘is'); 

查询示例：
SELECT id, CONCAT(name, age) NAME, LENGTH (name) length 
FROM student 


### 数字函数
• ROUND(列名|表达式, n)：四舍五入，将列或表达式所表示的数值四舍五 入到小数点后的第n位。
• CEIL(列名|表达式, n)：向上取整
• Floor(列名|表达式, n)：向下取整
• truncate(列名|表达式,n)：截断，将列或表达式所表示的数值截取到小 数点后的第n位。
• MOD(m,n)：取余，取m除以n后得到的余数。

示例：
SELECT ROUND(65.654,2),CEIL(65.654),Floor(65.654),truncate(65.654,1),mod(65.654,2) ;  
结果-- 65.65	66	65	65.6	1.654

### 日期函数

常用的日期运算如下：
• now()：返回系统日期+时间
• sysdate()：返回系统日期+时间，sysdate() 日期时间函数跟 now() 类似，不同之处在于：now() 在执行开始时值就得到了， sysdate() 在函数执行时动态得到值；
• current_timestamp, current_timestamp()：获得当前时间戳函数
• curdate():返回系统当前日期，不返回时间
• curtime():返回当前系统中的时间，不返回日期

#### MySQL 日期转换函数、时间转换函数
• str_to_date 将日期格式的字符转换成指定格式的日期
• date_format(date,format) 日期转换字符串
• time_format(time,format) 日期转换字符串
示例：
date_format('2008-08-08 22:23:01', '%Y%m%d%H%i%s') -- 20080808222301 
select str_to_date('08/09/2008', '%m/%d/%Y'); -- 2008-08-09 
select str_to_date('08/09/08' , '%m/%d/%y'); -- 2008-08-09 
select str_to_date('08.09.2008', '%m.%d.%Y'); -- 2008-08-09 
select str_to_date('08:09:30', '%h:%i:%s'); -- 08:09:30 
select str_to_date('08.09.2008 08:09:30', '%m.%d.%Y %h:%i:%s'); -- 2008-08-09 08:09:30

（日期、天数）转换函数
to_days(date)：日期转换天数
time_to_sec(time)：时间转换秒数
sec_to_time(seconds)：秒数转换时间
示例
select to_days('2008-08-08'); -- 733627
select time_to_sec('01:00:05'); -- 3605 
select sec_to_time(3605); -- '01:00:05'

拼凑日期、时间函数
makdedate(year,dayofyear)：拼凑日期
maketime(hour,minute,second)：拼凑时间
示例
select makedate(2001,31); -- '2001-01-31' 
select makedate(2001,32); -- '2001-02-01' 
select maketime(12,15,30); -- '12:15:30'


#### MySQL 日期时间计算函数
date_add()：日期增加一个时间间隔
示例
```
set @dt = now(); 

select date_add(@dt, interval 1 day); -- add 1 day 
select date_add(@dt, interval 1 hour); -- add 1 hour 
select date_add(@dt, interval 1 minute); -- ... 
select date_add(@dt, interval 1 second); 
select date_add(@dt, interval 1 microsecond); 
select date_add(@dt, interval 1 week); 
select date_add(@dt, interval 1 month); 
select date_add(@dt, interval 1 quarter); 
select date_add(@dt, interval 1 year); 
 
select date_add(@dt, interval -1 day); -- sub 1 day

select date_add(@dt, interval '01:15:30' hour_second); 
结果：2008-08-09 13:28:03  
 
select date_add(@dt, interval '1 01:15:30' day_second); 
结果：2008-08-10 13:28:03
```

date_sub()：为日期减去一个时间间隔
```
select date_sub('1998-01-01 00:00:00', interval '1 1:1:1' day_second); 
结果：1997-12-30 22:58:59 
```

datediff(date1,date2)：两个日期相减 date1 - date2，返回天数。
timediff(time1,time2)：两个日期相减 time1 - time2，返回 time 差值：
```
select datediff('2008-08-08', '2008-08-01'); -- 7 
select datediff('2008-08-01', '2008-08-08'); -- -7
select timediff('2008-08-08 08:08:08', '2008-08-08 00:00:00'); -- 08:08:08 
select timediff('08:08:08', '00:00:00'); -- 08:08:08 
注意：timediff(time1,time2) 函数的两个参数类型必须相同。
```

时间戳（timestamp）转换、增、减函数
timestamp(date) -- date to timestamp
timestamp(dt,time) -- dt + time 
timestampadd(unit,interval,datetime_expr)  
timestampdiff(unit,datetime_expr1,datetime_expr2)  
```
select timestamp('2008-08-08'); -- 2008-08-08 00:00:00 
select timestamp('2008-08-08 08:00:00', '01:01:01'); -- 2008-08-08 09:01:01 
select timestamp('2008-08-08 08:00:00', '10 01:01:01'); -- 2008-08-18 09:01:01 
 
select timestampadd(day, 1, '2008-08-08 08:00:00'); -- 2008-08-09 08:00:00 
select date_add('2008-08-08 08:00:00', interval 1 day); -- 2008-08-09 08:00:00 
 
MySQL timestampadd() 函数类似于 date_add()。 
select timestampdiff(year,'2002-05-01','2001-01-01'); -- -1 
select timestampdiff(day ,'2002-05-01','2001-01-01'); -- -485 
select timestampdiff(hour,'2008-08-08 12:00:00','2008-08-08 00:00:00'); -- -12 
 
select datediff('2008-08-08 12:00:00', '2008-08-01 00:00:00'); -- 7
```


### 转换函数
Cast(字段名 as 转换的类型 )，其中类型可以为：

CHAR[(N)] 字符型 
DATE  日期型
DATETIME  日期和时间型
DECIMAL  float型
SIGNED  int
TIME  时间型

示例
```
cast(123   AS   CHAR(3))    //整数转字符串
cast( '123 '   as   SIGNED   INTEGER)   //字符串转整数
select cast(11 as unsigned int) /*整型*/
select cast(11 as decimal(10,2)) /*浮点型*/
```

### 控制流程函数

CASE value WHEN [compare-value] 
THEN result [WHEN [compare-value] THEN result ...] [ELSE result] 
END 
CASE WHEN [condition] 
THEN result [WHEN [condition] THEN result ...] [ELSE result] 
END 

示例：
```
SELECT CASE 11 WHEN 1 
THEN 'one'
WHEN 2 
THEN 'two' ELSE 'more' END;

SELECT CASE WHEN 1>0 THEN 'true' ELSE 'false' END;
```

IF(expr1,expr2,expr3) 
如果 expr1 是TRUE (expr1 <> 0 and expr1 <> NULL)，则 IF()的返回值为expr2; 否则返回值则为 expr3。IF() 的返回值为数字值或字符串值，具体情况视其所在语境而定。
示例：
```
SELECT IF(1>2,2,3);
SELECT IF(1<2,'yes ','no');
SELECT IF(STRCMP('test','test1'),'no','yes');
```

STRCMP(expr1,expr2) 
如果字符串相同，STRCMP()返回0，如果第一参数根据当前的排序次序小于第二个，返回-1，否则返回1。
Strcmp(str1,str2):如果str1>str2返回1,str1=str2反回0，str1<str2返回-1)
示例：
```
select STRCMP('text', 'text2'); 
select STRCMP('text2', 'text'); 
select STRCMP('text', 'text');
```

### 系统信息函数
获取MySQL版本号、连接数、数据库名的函数

VERSION()函数返回数据库的版本号；
CONNECTION_ID()函数返回服务器的连接数，也就是到现在为止MySQL服务的连接次数；
DATABASE()和SCHEMA()返回当前数据库名。
USER()、SYSTEM_USER()、SESSION_USER()、CURRENT_USER()和CURRENT_USER这几个函数可以返回当前用户的名称。
CHARSET(str)函数返回字符串str的字符集，一般情况这个字符集就是系统的默认字符集；
COLLATION(str)函数返回字符串str的字符排列方式。
LAST_INSERT_ID()函数返回最后生成的AUTO_INCREMENT值。

### 其他函数
加密函数PASSWORD(str)
PASSWORD(str)函数可以对字符串str进行加密。一般情况下，PASSWORD(str)函数主要是用来给用户的密码加密的。下面使用PASSWORD(str)函数为字符串“abcd”加密。

加密函数MD5(str)
MD5(str)函数可以对字符串str进行加密。MD5(str)函数主要对普通的数据进行加密。下面使用MD5(str)函数为字符串“abcd”加密。

加密函数ENCODE(str,pswd_str)
ENCODE(str,pswd_str)函数可以使用字符串pswd_str来加密字符串str。加密的结果是一个二进制数，必须使用BLOB类型的字段来保存它。

解密函数
DECODE(crypt_str,pswd_str)函数可以使用字符串pswd_str来为crypt_str解密。crypt_str是通过ENCODE(str,pswd_str)加密后的二进制数据。字符串pswd_str应该与加密时的字符串pswd_str是相同的。下面使用DECODE(crypt_str,pswd_str)为ENCODE(str,pswd_str)加密的数据解密。

格式化函数FORMAT(x,n)

FORMAT(x,n)函数可以将数字x进行格式化，将x保留到小数点后n位。这个过程需要进行四舍五入。

ASCII(s)返回字符串s的第一个字符的ASCII码；
BIN(x)返回x的二进制编码；
HEX(x)返回x的十六进制编码；
OCT(x)返回x的八进制编码；
CONV(x,f1,f2)将x从f1进制数变成f2进制数。

IP地址与数字相互转换的函数
INET_ATON(IP)函数可以将IP地址转换为数字表示；INET_ATON(IP)函数中IP值需要加上引号
INET_NTOA(n)函数可以将数字n转换成IP的形式。

加锁函数和解锁函数
GET_LOCT(name,time)函数定义一个名称为nam、持续时间长度为time秒的锁。如果锁定成功，返回1；如果尝试超时，返回0；如果遇到错误，返回NULL。RELEASE_LOCK(name)函数解除名称为name的锁。如果解锁成功，返回1；如果尝试超时，返回0；如果解锁失败，返回NULL；IS_FREE_LOCK(name)函数判断是否使用名为name的锁。如果使用，返回0；否则，返回1。
重复执行指定操作的函数
BENCHMARK(count,expr)函数将表达式expr重复执行count次，然后返回执行时间。该函数可以用来判断MySQL处理表达式的速度。

改变字符集的函数
CONVERT(s USING cs)函数将字符串s的字符集变成cs

注：CAST(x AS type)和CONVERT(x,type)这两个函数将x变成type类型。这两个函数只对BINARY、CHAR、DATE、DATETIME、TIME、SIGNED INTEGER、UNSIGNED INTEGER这些类型起作用。但两种方法只是改变了输出值的数据类型，并没有改变表中字段的类型。

### 函数嵌套
• 单行函数可以嵌套于任何层。
• 嵌套的函数是从最里层向最外层的顺序计算的。











