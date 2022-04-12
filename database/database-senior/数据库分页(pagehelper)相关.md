
### 分页为什么不要用offset和limit分页？
为了实现分页，每次收到分页请求时，数据库都需要进行低效的全表扫描。
解决：其实很简单，如果有主键，利用主键索引就够了！
Select * from table where id>10 limit 10

那如果我们的表没有主键，比如是具有多对多关系的表，那么就只能使用传统的 OFFSET/LIMIT 方式，但这样做存在潜在的慢查询问题。
完全可以在需要分页的表中使用自动递增的主键，即使只是为了分页。


### pageHelper分页是如何实现的
使用pageHelper一般的分页查询sql，我们可以看到会执行两条sql，分部是`select count(0)...`，和`select ... limit ?,?`

原理：
- 调用PageHelper.startPage(pageNo,pageLimit)的时候,就已经静悄悄地把我们的分页参数存储到一个变量 ThreadLocal<Page> LOCAL_PAGE;这样可以保证分页的时候，多线程参数互不影响
- 执行Mapper进行查询,实际上是被一个叫做PageInterceptor.java拦截到了,执行了它重写的interceptor方法. 这里涉及到MyBatis里的拦截器原理,本文不重点说明了,相信聪明你肯定已经知道啦;
该方法里,主要是做了如下事情:
  - (1) 获取到MappedStatement, 拿到业务写好的sql, 将sql改造成select count(0) 并执行查询,并将执行结果存到了LOCAL_PAGE里的Page里的total属性.
  - (2) 获取到我们自己写在xml里的sql , 并append一些分页sql段,然后执行, 将执行结果存到了LOCAL_PAGE里的Page里的list属性.Page 其实是ArrayList的子类.
  - 可以看出,结果都是封装到了Page里, 最后交由PageInfo,可以获取到总条数,总页数,是否还有下一页等参数.


核心拼接转换代码：
- 改造sql
  - 统计总条数: this.dialect.getCountSql
  - 分页: this.dialect.getPageSql
  - 底层都是先借助PageHelper, 然后再落实到具体的实现类.
  - 重点关注this.dialect.getPageSql（数据库不同，改造sql也不同）

例：
mysql:
```
public String getPageSql(String sql, Page page, CacheKey pageKey) {
        StringBuilder sqlBuilder = new StringBuilder(sql.length() + 14);
        sqlBuilder.append(sql);
        if (page.getStartRow() == 0L) {
            sqlBuilder.append("\n LIMIT ? ");
        } else {
            sqlBuilder.append("\n LIMIT ?, ? ");
        }

        return sqlBuilder.toString();
    }
```

oracle
```
public String getPageSql(String sql, Page page, CacheKey pageKey) {
        StringBuilder sqlBuilder = new StringBuilder(sql.length() + 120);
        sqlBuilder.append("SELECT * FROM ( ");
        sqlBuilder.append(" SELECT TMP_PAGE.*, ROWNUM PAGEHELPER_ROW_ID FROM ( \n");
        sqlBuilder.append(sql);
        sqlBuilder.append("\n ) TMP_PAGE)");
        sqlBuilder.append(" WHERE PAGEHELPER_ROW_ID <= ? AND PAGEHELPER_ROW_ID > ?");
        return sqlBuilder.toString();
    }
```


