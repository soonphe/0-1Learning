# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## MySQL 优化之 index merge(索引合并)

### index merge是什么

我们的 where 中可能有多个条件(或者join)涉及到多个字段，它们之间进行 AND 或者 OR，那么此时就有可能会使用到 index merge 技术。

index merge 技术如果简单的说，其实就是：对多个索引分别进行条件扫描，然后将它们各自的结果进行合并(intersect/union)。

MySQL5.0之前，一个表一次只能使用一个索引，无法同时使用多个索引分别进行条件扫描。但是从5.1开始，引入了 index merge 优化技术，对同一个表可以使用多个索引分别进行条件扫描。

相关文档：http://dev.mysql.com/doc/refman/5.6/en/index-merge-optimization.html (注意该文档中说的有几处错误)
> The Index Merge method is used to retrieve rows with several range scans and to merge their results into one. The merge can produce unions, intersections, or unions-of-intersections of its underlying scans. This access method merges index scans from a single table; it does not merge scans across multiple tables.
> In EXPLAIN output, the Index Merge method appears as index_merge in the type column. In this case, the key column contains a list of indexes used, and key_len contains a list of the longest key parts for those indexes.

index merge: 同一个表的多个索引的范围扫描可以对结果进行合并，合并方式分为三种：union, intersection, 以及它们的组合(先内部intersect然后在外面union)。

查看数据库是否开启了index_merge：show variables like ‘%optimizer_switch%’;
关闭index_merge（重启才能生效）：set @@global.optimizer_switch = ‘index_merge=off’ ；
开启index_merge（重启才能生效）：set @@global.optimizer_switch = ‘index_merge=on’ ；
默认index_merge索引优化是开启的；

官方文档给出了四个例子：
```
SELECT * FROM tbl_name WHERE key1 = 10 OR key2 = 20;
SELECT * FROM tbl_name WHERE (key1 = 10 OR key2 = 20) AND non_key=30;
SELECT * FROM t1, t2 WHERE (t1.key1 IN (1,2) OR t1.key2 LIKE 'value%') AND t2.key1=t1.some_col;
SELECT * FROM t1, t2 WHERE t1.key1=1 AND (t2.key1=t1.some_col OR t2.key2=t1.some_col2);
```
但是第四个例子，感觉并不会使用 index merge. 因为 t2.key1=t1.some_col 和 t2.key2=t1.some_col2 之间进行的是 OR 运算，而且 t2.key2 是复合索引的第二个字段(非第一个字段)。所以：t2.key2 = t1.some_col2 并不能使用到复合索引。(文档这里应该是错误的)

index merge 算法根据合并算法的不同分成了三种：intersect, union, sort_union.

### index merge 之 intersect

简单而言，index intersect merge就是多个索引条件扫描得到的结果进行交集运算。显然在多个索引提交之间是 AND 运算时，才会出现 index intersect merge. 下面两种where条件或者它们的组合时会进行 index intersect merge:

1. 条件使用到复合索引中的所有字段或者左前缀字段(对单字段索引也适用)
```
key_part1=const1 AND key_part2=const2 ... AND key_partN=constN
```
2. 主键上的任何范围条件

例子：
```
SELECT * FROM innodb_table WHERE primary_key < 10 AND key_col1=20;
SELECT * FROM tbl_name WHERE (key1_part1=1 AND key1_part2=2) AND key2=2;
```
上面只说到复合索引，但是其实单字段索引显然也是一样的。比如 select * from tab where key1=xx and key2 =xxx; 也是有可能进行index intersect merge的。另外上面两种情况的 AND 组合也一样可能会进行 index intersect merge.

The Index Merge intersection algorithm performs simultaneous scans on all used indexes and produces the intersection of row sequences that it receives from the merged index scans. (intersect merge运行方式：多个索引同时扫描，然后结果取交集)

If all columns used in the query are covered by the used indexes, full table rows are not retrieved (EXPLAIN output contains Using index in Extra field in this case). Here is an example of such a query:(索引覆盖扫描，无需回表)
```
SELECT COUNT(*) FROM t1 WHERE key1=1 AND key2=1;
```
If the used indexes do not cover all columns used in the query, full rows are retrieved only when the range conditions for all used keys are satisfied.(索引不能覆盖，则对满足条件的再进行回表)

If one of the merged conditions is a condition over a primary key of an InnoDB table, it is not used for row retrieval, but is used to filter out rows retrieved using other conditions.

### index merge 之 union

简单而言，index uion merge就是多个索引条件扫描，对得到的结果进行并集运算，显然是多个条件之间进行的是 OR 运算。

下面几种类型的 where 条件，以及他们的组合可能会使用到 index union merge算法：

1) 条件使用到复合索引中的所有字段或者左前缀字段(对单字段索引也适用)

2) 主键上的任何范围条件

3) 任何符合 index intersect merge 的where条件；

上面三种 where 条件进行 OR 运算时，可能会使用 index union merge算法。

例子：
```
SELECT * FROM t1 WHERE key1=1 OR key2=2 OR key3=3;
SELECT * FROM innodb_table WHERE (key1=1 AND key2=2) OR (key3='foo' AND key4='bar') AND key5=5;
```
第一个例子，就是三个 单字段索引 进行 OR 运算，所以他们可能会使用 index union merge算法。

第二个例子，复杂一点。(key1=1 AND key2=2) 是符合 index intersect merge; (key3='foo' AND key4='bar') AND key5=5 也是符合index intersect merge，所以 二者之间进行 OR 运算，自然可能会使用 index union merge算法。

### index merge 之 sort_union

This access algorithm is employed when the WHERE clause was converted to several range conditions combined by OR, but for which the Index Merge method union algorithm is not applicable.(多个条件扫描进行 OR 运算，但是不符合 index union merge算法的，此时可能会使用 sort_union算法)

官方文档给出了两个例子：
```
SELECT * FROM tbl_name WHERE key_col1 < 10 OR key_col2 < 20;
SELECT * FROM tbl_name WHERE (key_col1 > 10 OR key_col2 = 20) AND nonkey_col=30;
```
但是显然：因为 key_col2 不是复合索引的第一个字段，对它进行 OR 运算，是不可能使用到索引的。所以这两个例子应该也是错误的，它们实际上并不会进行 index sort_union merge算法。

The difference between the sort-union algorithm and the union algorithm is that the sort-union algorithm must first fetch row IDs for all rows and sort them before returning any rows.(sort-union合并算法和union合并算法的不同点，在于返回结果之前是否排序，为什么需要排序呢？可能是因为两个结果集，进行并集运算，需要去重，所以才进行排序？？？)

### index merge的局限

1）If your query has a complex WHERE clause with deep AND/OR nesting and MySQL does not choose the optimal plan, try distributing terms using the following identity laws:
```
(x AND y) OR z = (x OR z) AND (y OR z)
(x OR y) AND z = (x AND z) OR (y AND z)
```
如果我们的条件比较复杂，用到多个 and / or 条件运算，而MySQL没有使用最优的执行计划，那么可以使用上面的两个等式将条件进行转换一下。

2）Index Merge is not applicable to full-text indexes. We plan to extend it to cover these in a future MySQL release.(全文索引没有index merge)

3）Before MySQL 5.6.6, if a range scan is possible on some key, the optimizer will not consider using Index Merge Union or Index Merge Sort-Union algorithms. For example, consider this query:
```
SELECT * FROM t1 WHERE (goodkey1 < 10 OR goodkey2 < 20) AND badkey < 30;
```
For this query, two plans are possible:

An Index Merge scan using the (goodkey1 < 10 OR goodkey2 < 20) condition.

A range scan using the badkey < 30 condition.

However, the optimizer considers only the second plan.

这一点对以低版本的MySQL是一个很大的缺陷。就是如果where条件中有 >, <, >=, <=等条件，那么优化器不会使用 index merge，而且还会忽略其他的索引，不会使用它们，哪怕他们的选择性更优。

### 对 index merge 的进一步优化

index merge使得我们可以使用到多个索引同时进行扫描，然后将结果进行合并。听起来好像是很好的功能，但是如果出现了 index intersect merge，那么一般同时也意味着我们的索引建立得不太合理，因为 index intersect merge 是可以通过建立 复合索引进行更一步优化的。

比如下面的select:
```
SELECT * FROM t1 WHERE key1=1 AND key2=2 AND key3=3;
```
显然我们是可以在这三个字段上建立一个复合索引来进行优化的，这样就只需要扫描一个索引一次，而不是对三个所以分别扫描一次。

percona官网有一篇 比较复合索引和index merge 的好文章：Multi Column indexes vs Index Merge：https://www.percona.com/blog/2009/09/19/multi-column-indexes-vs-index-merge/


### index merge触发死锁问题
场景：
某一个事务未结束，而下一个事务进入来修改数据，这时就会陷入等待，最后等待超时，事务进行了回滚，发生死锁的是两条update语句，当sql语句的where语句中使用两个索引时，mysql的优化器可能会对这两个索引进行合并，使用explain分析会显示Using intersect(index1,index2); 表示将index1和index2合并来查询。该表中只有index1,index2两个索引。

文档中似乎并没有很明确的触发实际，从这些死锁的SQL来看，某些SQL在事后explain的时候，并没有走index merge，而有些却走了。

只查到一个必要条件是：

> Intersect和Union都需要使用的索引是ROR的，也就是ROWID ORDERED，即针对不同的索引扫描出来的数据必须是同时按照ROWID排序的，这里的 ROWID其实也就是InnoDB的主键(如果不定义主键，InnoDB会隐式添加ROWID列作为主键)。只有每个索引是ROR的，才能进行归并排序，你懂的。当然你可能会有疑惑，查不记录后内部进行一次sort不一样么，何必必须要ROR呢，不错，所以有了SORT-UNION。SORT-UNION就是每个非ROR的索引 排序后再进行Merge


### 解决方案
实际上，由于index merge，客观上就会增加update语句的死锁可能性，相关bug连接如下：https://bugs.mysql.com/bug.php?id=77209

而其实如果出现了index merge，在很多情况下意味着我们索引的建立可能并不合理。

解决方案：
1. 建立联合索引，包含index1+index2的，优化器会直接使用这个索引，以避免index merge
2. 取消index merge的优化
3. 在SQL语句使用force index()来指定要使用的索引，但在实际开发中不太方便
4. 删除掉index2或者index1的索引，这样也可以解决问题，让优化器只能使用一个索引










