
### 分页为什么不要用offset和limit分页？
为了实现分页，每次收到分页请求时，数据库都需要进行低效的全表扫描。
解决：其实很简单，如果有主键，利用主键索引就够了！
Select * from table where id>10 limit 10

那如果我们的表没有主键，比如是具有多对多关系的表，那么就只能使用传统的 OFFSET/LIMIT 方式，但这样做存在潜在的慢查询问题。
完全可以在需要分页的表中使用自动递增的主键，即使只是为了分页。


### pageHelper分页是如何实现的
PageHelper首先将前端传递的参数保存到page这个对象中，接着将page的副本存放入ThreadLoacl中，这样可以保证分页的时候，参数互不影响，
接着利用了mybatis提供的拦截器，取得ThreadLocal的值，重新拼装分页SQL，完成分页。

核心拼接转换代码：
```
public String getPageSql(String sql) {
    StringBuilder sqlBuilder = new StringBuilder(sql.length() + 120);
    sqlBuilder.append("select * from ( select tmp_page.*, rownum row_id from ( ");
    sqlBuilder.append(sql);
    sqlBuilder.append(" ) tmp_page where rownum <= ? ) where row_id > ?");
    return sqlBuilder.toString();
}
```
