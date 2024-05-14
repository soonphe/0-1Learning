# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java时间格式处理

### 几种时间戳时间格式，Instant类和 Date类和 Calendar类，LocalTime、LocalDate、LocalDateTime，Timestamp
- Instant 类和 Date 类是基于 Unix 时间戳（自1970年1月1日起的毫秒数）实现的，精确到纳秒级别，并且不包含时区信息。
- Calendar 类也是 Java API 中用于处理日期和时间的类之一，它提供了丰富的日历计算功能，如获取特定日期的年、月、日、时、分、秒等等，并支持时区信息。但是，Calendar 类的 API 设计比较复杂，而且存在线程安全问题。
- LocalTime、LocalDate、LocalDateTime 和 ZonedDateTime 类都是 Java 8 引入的日期/时间类，用于处理本地日期和时间、日期时间组合以及带时区的日期时间。而 LocalDateTime 类则是本地日期时间的组合，即日期和时间信息都是相对于本地时区的。LocalTime 类和 LocalDate 类分别用于处理本地时间和本地日期。这些类提供了许多方法来操作日期和时间，例如获取时间段、计算日期差异、格式化日期时间等等。
- Timestamp 类是 java.sql 包下的一个类，它继承自 java.util.Date 类，用于处理数据库中的时间戳数据。Timestamp 类的精度是纳秒级别，而 Date 类的精度是毫秒级别。Timestamp 类还提供了一些方法来处理时间戳数据，例如获取时间戳、设置时间戳、将时间戳转换为日期时间等等。

避免使用 Date 类和 Calendar 类，因为它们已经过时，而且存在局限性。
建议在 Java 8 及以上版本中使用 LocalDateTime、LocalTime、LocalDate 和 ZonedDateTime 等类来处理本地日期和时间，而使用 Instant 类则可以处理跨时区的 Unix 时间戳。

```
代码：
System.out.println(new Date());
System.out.println(LocalDateTime.now());
System.out.println(new Timestamp(System.currentTimeMillis()));
System.out.println(Instant.now());

输出：
Wed Apr 24 10:39:32 CST 2024
2024-04-24T10:39:33.048
2024-04-24 10:39:33.048
2024-04-24T02:39:33.049Z
```

### LocalDateTime
- LocalDateTime.now();获取当前时间
- plusDays(1); 加上指定的天数，可以为负值，负值则为减去天数
- plusMinutes(-5);加上指定的分钟数，可以为负值，负值则为减去分钟数
- minusDays(long daysToSubtract)：减去指定的天数。

时间格式化：
DateTimeFormatter df = DateTimeFormatter.ofPattern("yyyy-MM-dd HH:mm:ss");
- localTime.format(df);：时间格式化为字符串w
- LocalDateTime.parse(str,df);：字符串转化为LocalDateTime

### TimeStamp(继承自 java.util.Date)
构造函数：
- Timestamp(long time)：通过指定的毫秒数创建一个Timestamp对象。可以传System.currentTimeMillis，如new Timestamp(System.currentTimeMillis() - 60000);
- Timestamp(int year, int month, int day, int hour, int minute, int second, int nano)：通过指定的年、月、日、时、分、秒和纳秒数创建一个Timestamp对象。
- OffsetDateTime接受postgresql时间戳timestamptz：private OffsetDateTime collectionTime;

方法：
- getTime()：获取Timestamp对象的毫秒数。
- setTime(long time)：设置Timestamp对象的毫秒数。
- toLocalDateTime()：将Timestamp对象转换为LocalDateTime对象。
- Timestamp比较大小：timestamp.after(endTime)

#### Instant当前时间，时间戳加减，时间格式化
当前时间：
- Instant now = Instant.now();：当前时间戳
- long l = Instant.now().toEpochMilli();：当前时间戳毫秒
- long l1 = System.nanoTime()：当前时间戳（纳秒）
- long l2 = System.currentTimeMillis();：当前时间戳毫秒

时间戳加减
- Instant.now().minus(50,ChronoUnit.DAYS).toEpochMilli()
- Instant.now().plus(10,ChronoUnit.DAYS).toEpochMilli()

#### 时间戳转localDatetime
- Instant转Long：Instant.tpEpochMilli(timestamp);
- Long转Instant：Instant.ofEpochMilli(date)

- long转LocalDateTime：LocalDateTime.ofInstant(instant, ZoneId.systemDefault());
- Date转LocalDatetime：new Date().toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime()
- localDatetime转Date:Date.from(ordOrderConsume.getCreateTime().atZone(ZoneId.systemDefault()).toInstant())

- localDatetime转Timestamp：Timestamp.valueOf(LocalDateTime.now()))




