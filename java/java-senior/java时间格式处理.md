# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java时间格式处理

### 时间戳时间格式

#### 时间、日期加减：
```
Instant.now().minus(50,ChronoUnit.DAYS).toEpochMilli()
Instant.now().plus(10,ChronoUnit.DAYS).toEpochMilli()
```

#### Instant.now().toEpochMilli()和System.currentTimeMillis()用法
Instant.now()：当前时间戳
System.nanoTime()：当前时间戳（纳秒）
Instant.now().toEpochMilli()：当前时间戳毫秒
System.currentTimeMillis()：当前时间戳毫秒

#### java date和LocalDateTime、LocalDate、LocalTime转换
LocalDateTime只能是日期和时间
LocalDate是日期
LocalTime是时间
```
DateTimeFormatter df=DateTimeFormatter.ofPattern("HH:mm:ss");
localTime.format(df)
```

#### datetime转localDatetime
```
getPaidTime().toInstant().atZone(ZoneId.systemDefault()).toLocalDateTime()
```
localDatetime转datetime:
```
Date.from(ordOrderConsume.getCreateTime().atZone(ZoneId.systemDefault()).toInstant())
```
LocalDateTime.now()：获取当前时间


### 字符串时间格式（yyyy-MM-dd HH:mm:ss）

