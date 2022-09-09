# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## equals和==的区别

### java当中的数据类型和“==”的含义：
基本类型：byte,short,char,int,long,float,double,boolean。比较的就是值是否相同
引用类型：比较的就是内存地址是否相同(也就是说，除非引用指向的是同一个new出来的对象，此时他们使用`==`去比较得到true，否则，得到false)

## equals 的作用:
引用类型：默认情况下，比较的是内存地址值（为什么说默认，因为很多子类都对equals方法进行了重写）

equals追根溯源，是Object类中的一个方法，在该类中，equals的实现也仅仅只是比较两个对象的内存地址是否相等，但在一些子类中，如：String、Integer 等，该方法将被重写。

> equals与==的主要区别是：
1. 基本型和基本型封装型进行“==”运算符的比较，基本型封装型将会自动拆箱变为基本型后再进行比较，
2. 两个包裝类型的对象进行“==”比较时，如果有一方的对象是new获得的，返回false，因为引用地址不同。
3. 两个基本型的包装类型进行equals()比较，首先equals()会比较类型，如果类型相同，则继续比较值，如果值也相同，返回true，否则返回false。
4. 包装类型调用equals()方法,但是参数是基本类型，这时候，先会进行自动装箱，将基本型转换为其包装类型,若类型不同返回false,

注意：如果==和equals()用于比较对象，当两个引用地址相同，==返回true。而equals()可以返回true或者false主要取决于重写实现。

最常见的一个例子，字符串的比较，不同情况==和equals()返回不同的结果。
equals()方法最重要的一点是，能够根据业务要求去重写，按照自定义规则去判断两个对象是否相等。
重写equals()方法的时候，要注意一下hashCode是否会因为对象的属性改变而改变，否则在使用散列集合储存该对象的时候会碰到坑！！

Object中的equals方法：
```
    public boolean equals(Object obj) {
        return (this == obj);
    }
```

String中重写equals方法：
```
public boolean equals(Object anObject) {
        if (this == anObject) {
            return true;
        }
        if (anObject instanceof String) {
            String anotherString = (String)anObject;
            int n = value.length;
            if (n == anotherString.value.length) {
                char v1[] = value;
                char v2[] = anotherString.value;
                int i = 0;
                while (n-- != 0) {
                    if (v1[i] != v2[i])
                        return false;
                    i++;
                }
                return true;
            }
        }
        return false;
    }
```

Integer中重写equals方法：
```
    public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }
```

测试代码如下：
```
int intData =2 ;
String stringData ="2" ;
Integer integerData =2 ;
System.out.println(integerData.equals(stringData) );// false 类型不同
System.out.println(stringData.equals(integerData) );// false 类型不同
System.out.println(stringData.equals(integerData.toString()) );// true
System.out.println(integerData.equals(Integer.valueOf(stringData)) );// true
System.out.println(integerData.equals(intData) );// true
System.out.println(integerData.equals(1) );// false 数值不同
System.out.println(stringData.equals(1) );// false  类型不同,数值不同
```

## 包装类

### 包装类的缓存
以下五行代码输出的结果依次是什么？
```
System.out.println(Integer.valueOf("1000")==Integer.valueOf("1000"));
System.out.println(Integer.valueOf("128")==Integer.valueOf("128"));
System.out.println(Integer.valueOf("127")==Integer.valueOf("127"));
System.out.println(Integer.valueOf("-128")==Integer.valueOf("-128"));    
System.out.println(Integer.valueOf("-1000")==Integer.valueOf("-1000"));
```

### 我的答案
当时看到这段代码，我给出的答案依次是：false、false、false、false、false。
我当时的想法是，valueOf(String s)每次返回的都是新创建的对象。

### 正确答案
为了验证自己的答案是否正确，就使用IDE写了这几行代码，运行后得到了如下答案：
````
System.out.println(Integer.valueOf("1000")==Integer.valueOf("1000"));   --false
System.out.println(Integer.valueOf("128")==Integer.valueOf("128"));    --false
System.out.println(Integer.valueOf("127")==Integer.valueOf("127"));   --true
System.out.println(Integer.valueOf("-128")==Integer.valueOf("-128"));   --true   
System.out.println(Integer.valueOf("-1000")==Integer.valueOf("-1000"));    --false
````

### 问题分析
为什么结果既有false又有true呢？这里需要看下Integer类valueOf(String s)的源码，如下：
```
/**
* 此方法先调用 public static int parseInt(String s, int radix)将String类型转换为int，
* 然后又调用了public static Integer valueOf(int i)方法返回Integer类型对象
*/
 public static Integer valueOf(String s) throws NumberFormatException {
        return Integer.valueOf(parseInt(s, 10));
 }
/**
* IntegerCache是Integer的私有静态内部类，看这个名字可以猜测到这个类和缓存有关系：
* IntegerCache.low的值是-128
* IntegerCache.high的值是127
* 这个方法的作用就是当-128<=i<=127时，从IntegerCache类的数组中取出一个Integer对象，否则就新创建一个Integer对象。
*/
 public static Integer valueOf(int i) {
        if (i >= IntegerCache.low && i <= IntegerCache.high)
            return IntegerCache.cache[i + (-IntegerCache.low)];
        return new Integer(i);
 }

```
看了这段源码之后，就能理解为什么自己给的答案是错误的。
题目中-128和127在缓存的范围之内会使用缓存的对象，而-1000、128、1000在范围之外会创建的新的对象，所以才有了false、false、true、true、false这个答案。
虽然解决了内心的疑惑，但是有必要好好了解下IntegerCache这个类。了解一个类最快的方法当然是看源码，如下：
````
/**
  * Cache to support the object identity semantics of autoboxing for values between
  * -128 and 127 (inclusive) as required by JLS.
  * JLS要求通过缓存来支持在-128（包括）和127（包括）之间的数值自动装箱为同一对象。
  *
  * The cache is initialized on first usage.  The size of the cache
  * may be controlled by the {@code -XX:AutoBoxCacheMax=<size>} option.
  * During VM initialization, java.lang.Integer.IntegerCache.high property
  * may be set and saved in the private system properties in the
  * sun.misc.VM class.
  *缓存会在第一次使用Integer这个类时被初始化。缓存数组的大小可以被--XX:AutoBoxCacheMax选项控制。在VM初始化时， IntegerCache.high
  *属性可以被设置保存在sun.misc.VM类的私有系统配置中。
  */
private static class IntegerCache {
        static final int low = -128; //low的值为-128
        static final int high;
        static final Integer cache[];
        static {
            // high value may be configured by property
            int h = 127;
            String integerCacheHighPropValue =
                sun.misc.VM.getSavedProperty("java.lang.Integer.IntegerCache.high");
            if (integerCacheHighPropValue != null) {
                try {
                    int i = parseInt(integerCacheHighPropValue);
                    i = Math.max(i, 127); //取i和127中的最大值
                    // Maximum array size is Integer.MAX_VALUE
                    h = Math.min(i, Integer.MAX_VALUE - (-low) -1);
                } catch( NumberFormatException nfe) {
                    // If the property cannot be parsed into an int, ignore it.
                }
            }
            high = h; //将high的值设置为127
            cache = new Integer[(high - low) + 1]; //创建数组，length=127-(-128)+1=256
            int j = low;
            for(int k = 0; k < cache.length; k++)
                cache[k] = new Integer(j++); //数组的值从-128至127
            // range [-128, 127] must be interned (JLS7 5.1.7)
            assert IntegerCache.high >= 127;
        }
        private IntegerCache() {}
    }
````

通过IntegerCache类的注释来看，和其他系统属性一样，在JVM启动时，cache数组的大小是可以通过设置-Djava.lang.Integer.IntegerCache.high=xxx传递进来的。通过这个笔试题目，我们学习到Integer这个类是有缓存的。众所周知，Java中除了Integer还有其他类型的包装类。那么其他类型包装类存在缓存吗？答案是：除了Float和Double，其他包装类也存在缓存。在包装类中，缓存的基本数据类型值的范围如下：

| 包装类型    | 基本数据类型  | 缓存范围| 
| ---- | ---- | ---- |
| Boolean | boolean | true,false| 
| Byte    | byte    | -128-127| 
| Short   | short   | -128-127| 
| Character|    char|     0-127| 
| Integer | int | -128-127| 
| Long    | long  |   -128-127| 
| Float   | float |   无| 
| Double  | double |  无| 

Boolean、Byte、Short、Character 、Long类型的缓存原理和Integer类型的相似，源码中均有体现。
总结：部分包装类提供了对象的缓存，实现方式是在类初始化时提前创建好会频繁使用的包装类对象，当需要使用某个包装类的对象时，如果该对象包装的值在缓存的范围内，就返回缓存的对象，否则就创建新的对象并返回。