# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java时间格式处理

### MySQL Timestamp 存储原理 mysql存时间戳还是日期 转载
1.切记不要用字符串存储日期
字符串占用的空间更大！
字符串存储的日期比较效率比较低（逐个字符进行比对），无法用日期相关的 API 进行计算和比较。

2.Datetime 和 Timestamp 之间抉择
Datetime 和 Timestamp 是 MySQL 提供的两种比较相似的保存时间的数据类型。
通常我们都会首选 Timestamp

2.1 DateTime 类型没有时区信息的
DateTime 类型是没有时区信息的（时区无关）
当你的时区更换之后，比如你的服务器更换地址或者更换客户端连接时区设置的话，就会导致你从数据库中读出的时间错误。

Timestamp 和时区有关。
Timestamp 类型字段的值会随着服务器时区的变化而变化，自动换算成相应的时间，说简单点就是在不同时区，查询到同一个条记录此字段的值会不一样。

2.2 DateTime 类型耗费空间更大
Timestamp 只需要使用 4 个字节的存储空间，但是 DateTime 需要耗费 8 个字节的存储空间。但是，这样同样造成了一个问题，Timestamp 表示的时间范围更小。

DateTime ：1000-01-01 00:00:00 ~ 9999-12-31 23:59:59
Timestamp： 1970-01-01 00:00:01 ~ 2037-12-31 23:59:59

Timestamp 在不同版本的 MySQL 中有细微差别。

3. Timestamp 只需要使用 4 个字节的存储空间，但是 DateTime 需要耗费 8 个字节的存储空间。

4.数值型时间戳是更好的选择吗？
很多时候，我们也会使用 int 或者 bigint 类型的数值也就是时间戳来表示时间。

这种存储方式的具有 Timestamp 类型的所具有一些优点，并且使用它的进行日期排序以及对比等操作的效率会更高，跨系统也很方便，毕竟只是存放的数值。缺点也很明显，就是数据的可读性太差了，你无法直观的看到具体时间。

### 几种时间戳时间格式，Instant类和 Date类和 Calendar类，LocalTime、LocalDate、LocalDateTime，Calendar
- Instant 类和 Date 类是基于 Unix 时间戳（自1970年1月1日起的毫秒数）实现的，精确到纳秒级别，并且不包含时区信息。
- LocalTime、LocalDate、LocalDateTime 和 ZonedDateTime 类都是 Java 8 引入的日期/时间类，用于处理本地日期和时间、日期时间组合以及带时区的日期时间。而 LocalDateTime 类则是本地日期时间的组合，即日期和时间信息都是相对于本地时区的。LocalTime 类和 LocalDate 类分别用于处理本地时间和本地日期。这些类提供了许多方法来操作日期和时间，例如获取时间段、计算日期差异、格式化日期时间等等。
- Calendar 类也是 Java API 中用于处理日期和时间的类之一，它提供了丰富的日历计算功能，如获取特定日期的年、月、日、时、分、秒等等，并支持时区信息。但是，Calendar 类的 API 设计比较复杂，而且存在线程安全问题。 

建议在 Java 8 及以上版本中使用 LocalDateTime、LocalTime、LocalDate 和 ZonedDateTime 等类来处理本地日期和时间，而使用 Instant 类则可以处理跨时区的 Unix 时间戳。
避免使用 Date 类和 Calendar 类，因为它们已经过时，而且存在局限性。

#### 当前时间，时间戳加减，时间格式化
当前时间：
- Instant now = Instant.now();：当前时间戳
- long l = Instant.now().toEpochMilli();：当前时间戳毫秒
- long l1 = System.nanoTime()：当前时间戳（纳秒）
- long l2 = System.currentTimeMillis();：当前时间戳毫秒
- LocalDateTime.now()：当前时间LocalDateTime
- OffsetDateTime接受postgresql时间戳timestamptz：private OffsetDateTime collectionTime;

时间戳加减
- Instant.now().minus(50,ChronoUnit.DAYS).toEpochMilli()
- Instant.now().plus(10,ChronoUnit.DAYS).toEpochMilli()
- localDateTime.plusMinutes(-5);

时间格式化：
DateTimeFormatter df=DateTimeFormatter.ofPattern("HH:mm:ss");
DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
- localTime.format(df);：时间格式化为字符串w
- LocalDateTime.parse(str,df);：字符串转化为LocalDateTime

#### 时间戳转localDatetime
- Instant转Long：Instant.tpEpochMilli(timestamp);
- Long转Instant：Instant.ofEpochMilli(date)
- long转LocalDateTime：LocalDateTime.ofInstant(instant, ZoneId.systemDefault());

- Date转LocalDatetime：new Date().toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime()
- localDatetime转Date:Date.from(ordOrderConsume.getCreateTime().atZone(ZoneId.systemDefault()).toInstant())

- localDatetime转Timestamp：Timestamp.valueOf(LocalDateTime.now()))


