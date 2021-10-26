# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java进阶-小数处理

### /和%的用法，精度提升
> "/"是取整  
> "%"是取余

> 两个整数相除时,明明无法除尽，为什么没有小数？   
> java中，当两个整数相除时，由于小数点以后的数字会被截断，运算结果将为整数，  
> 此时若希望得到运算结果为浮点数，必须将两整数其一或是两者都强制转换为浮点数。  

> 精度提升  
> float和int进行计算比较时，会自动提升为高精度的float计算比较

例：
```
        System.out.println(1/3);    //0
        System.out.println(1%3);    //1
        System.out.println((float)1/3);     //0.33333334    ——高精度和低精度计算比较会自动提升精度
        System.out.println((double)1/3);    //0.3333333333333333
        System.out.println(1/(float)8);     //0.125


```

### Math函数与4舍5入
> Math4舍5入  
> Math.sqrt() : 计算平方根  
> Math.cbrt() : 计算立方根  
> Math.pow(a, b) : 计算a的b次方  
> Math.max( , ) : 计算最大值  
> Math.min( , ) : 计算最小值  
> Math.abs() : 取绝对值  
>  
> Math.ceil(): 天花板的意思，就是逢余进一  
> Math.floor() : 地板的意思，就是逢余舍一  
> Math.rint(): 四舍五入，返回double值。注意.5的时候如果非小数位为偶数则舍弃，奇数则会进一  
> Math.round(): 四舍五入，float时返回int值，double时返回long值  
>  
> Math.random(): 取得一个[0, 1)范围内的随机数

例
```
        System.out.println(Math.ceil(1/(float)8));     //1.0
        System.out.println(Math.floor(1/(float)8));     //0.0
        System.out.println(Math.rint(10.5));     //10.0
        System.out.println(Math.rint(11.5));     //12.0
        System.out.println(Math.round(1/(float)8));     //0
        System.out.println(Math.round(2.1546));     //2
        System.out.println(Math.random()); // [0, 1)的double类型的数
        System.out.println(Math.random() * 2);//[0, 2)的double类型的数
        System.out.println(Math.random() * 2 + 1);// [1, 3)的double类型的数
```


### float和double的区别

float有效位数为7位
double有效位数为16位
```
(float)1/3=0.33333334
(double)1/3=0.3333333333333333
```

### BigDecimal运算
例：初始化和加、减、乘、除、舍入示例
```
        初始化
        BigDecimal num1 = new BigDecimal(0.005);
        //尽量用字符串的形式初始化
        BigDecimal num2 = new BigDecimal("1000000");

        //加法
        BigDecimal result1 = num1.add(num2);
 
        //减法
        BigDecimal result2 = num1.subtract(num2);
 
        //乘法
        BigDecimal result3 = num1.multiply(num2);
 
        //绝对值
        BigDecimal result4 = num1.abs();
 
        //除法
        BigDecimal result5 = num2.divide(num1,20,BigDecimal.ROUND_HALF_UP); //要精确的小数位数和舍入模式(这里为4舍5入)，不然会出现报错         
        
        //设置舍入
        num1.setScale(2, BigDecimal.ROUND_DOWN);
```
※ 注意：
1）System.out.println()中的数字默认是double类型的，double类型小数计算不精准。
2）使用BigDecimal类构造方法传入double类型时，计算的结果也是不精确的！

因为不是所有的浮点数都能够被精确的表示成一个double 类型值，有些浮点数值不能够被精确的表示成 double 类型值，因此它会被表示成与它最接近的 double 类型的值。
必须改用传入String的构造方法。这一点在BigDecimal类的构造方法注释中有说明。


#### BigDecimal舍入模式说明
1、ROUND_UP
舍入远离零的舍入模式。
在丢弃非零部分之前始终增加数字(始终对非零舍弃部分前面的数字加1)。
注意，此舍入模式始终不会减少计算值的大小。

2、ROUND_DOWN
接近零的舍入模式。
在丢弃某部分之前始终不增加数字(从不对舍弃部分前面的数字加1，即截短)。
注意，此舍入模式始终不会增加计算值的大小。

3、ROUND_CEILING
接近正无穷大的舍入模式。
如果 BigDecimal 为正，则舍入行为与 ROUND_UP 相同;
如果为负，则舍入行为与 ROUND_DOWN 相同。
注意，此舍入模式始终不会减少计算值。

4、ROUND_FLOOR
接近负无穷大的舍入模式。
如果 BigDecimal 为正，则舍入行为与 ROUND_DOWN 相同;
如果为负，则舍入行为与 ROUND_UP 相同。

注意，此舍入模式始终不会增加计算值。

5、ROUND_HALF_UP
向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则为向上舍入的舍入模式。
如果舍弃部分 >= 0.5，则舍入行为与 ROUND_UP 相同;否则舍入行为与 ROUND_DOWN 相同。
注意，这是我们大多数人在小学时就学过的舍入模式(四舍五入)。

6、ROUND_HALF_DOWN
向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则为上舍入的舍入模式。
如果舍弃部分 > 0.5，则舍入行为与 ROUND_UP 相同;否则舍入行为与 ROUND_DOWN 相同(五舍六入)。

7、ROUND_HALF_EVEN
向“最接近的”数字舍入，如果与两个相邻数字的距离相等，则向相邻的偶数舍入。
如果舍弃部分左边的数字为奇数，则舍入行为与 ROUND_HALF_UP 相同;
如果为偶数，则舍入行为与 ROUND_HALF_DOWN 相同。
注意，在重复进行一系列计算时，此舍入模式可以将累加错误减到最小。
此舍入模式也称为“银行家舍入法”，主要在美国使用。四舍六入，五分两种情况。
如果前一位为奇数，则入位，否则舍去。

以下例子为保留小数点1位，那么这种舍入方式下的结果。
1.15>1.2 1.25>1.2

8、ROUND_UNNECESSARY
断言请求的操作具有精确的结果，因此不需要舍入。


AuctionBaseInfoTempBizImpl


        

    
    
    
    
    
    