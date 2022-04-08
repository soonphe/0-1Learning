# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java小数(金钱)格式应该如何处理


### 关于金额字段为什么不用decimal类型
主要两个原因：
1. 避免不同语言对浮点数麻烦的计算，一不小心非常容易算错（存整数进行计算的话，计算完再除以精度比较方便）
2. 对于多个国家，对小数的精度要求是不同的，用 decimal 没办法方便控制


