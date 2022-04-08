# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java中常见的时间格式操作

### 

### 时间、日期加减：
```
Instant.now().minus(50,ChronoUnit.DAYS).toEpochMilli()
Instant.now().plus(10,ChronoUnit.DAYS).toEpochMilli()
```

### Instant.now().toEpochMilli()和System.currentTimeMillis()用法
Instant.now()：当前时间戳
System.nanoTime()：当前时间戳（纳秒）
Instant.now().toEpochMilli()：当前时间戳毫秒
System.currentTimeMillis()：当前时间戳毫秒