# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java泛型

### 泛型解决的主要问题
JDK1.5加入了泛型。加入泛型是为了解决类型转换的安全隐患，具体体现如下：

解决泛型对象实例化之后，可以随意添加任何类型的对象的问题。
解决获取泛型元素前，需要提前确定元素的类型的问题。
解决获取元素时，需要进行显式类型转换的问题。
解决容易出现类型转换出错的问题

泛型的设计初衷：是为了减少类型转换错误产生的安全隐患，而不是为了实现任意化，一定要记住这个初衷。

### 1.泛型相关概念
泛型可以应用在类、接口和方法的创建中，分别称为泛型类、泛型接口和泛型方法
* 泛型接口：interface IDemoDataGenerator<T>{，泛型接口最典型的应用就是各种类的生成器。
* 泛型类：class MyTimeMap<K,V,T>{，泛型类最典型的应用就是容器类：List、Set、Map等等。
* 泛型方法（重点）：public static <E> void printList(List<E> list){，泛型方法：注意参数列表中的泛型一定要在返回类型之前定义，如<E>

泛型方法语法：[作用域修饰符] <泛型类型标识> [返回类型] 方法名称(参数列表){}
void print(T t)不是一个泛型方法，
泛型类和泛型方法最大的区别在于：泛型类是通过实例化确定具体类型，泛型方式通过方法调用确定具体类型。
特别注意：
* 参数列表中用到的泛型类型，需要在返回类型之前通过<>定义。
* 泛型方法中的泛型类型与泛型类中的泛型类型没有关系。


### 1.1泛型类和泛型方法
首先通过泛型类与泛型方法的语法，对几个概念进行说明：
泛型类与泛型方法示例
````
/**
 * 泛型类语法示例
 * Created by 韩超 on 2018/2/22.
 */
public class MyGenericsType<T> {
    private T t;

    /**
     * <p>Title: 这是一个普通方法</p>
     * @author 韩超 2018/2/22 10:52
     */
    public T getT() {
        return t;
    }

    /**
     * <p>Title: 这是一个泛型方法</p>
     * @author 韩超 2018/2/22 10:53
     */
    public <T> T getTT() {
        T t = null;
        return t;
    }
}
````
其中，

MyGenericsType：泛型类名，即泛型原始类型
<T>：泛型标识，标识这是一个泛型类或者泛型方法
T：泛型类型
t：泛型类型的实例对象


泛型原始类型可以独立使用，如下：
泛型原始类型
````
/**
 * <p>Title: 泛型原始类型使用</p>
 * @author 韩超 2018/2/22 11:21
 */
public static void main(String[] args){
    //泛型原始类型
    MyGenericsType myGenericsType = new MyGenericsType();
    LOGGER.info(myGenericsType.getClass().toString());

    //泛型类型
    MyGenericsType<Integer> integerMyGenericsType = new MyGenericsType<Integer>();
    LOGGER.info(integerMyGenericsType.getClass().toString());
}
````
运行结果：
````
2018-02-22 11:23:11 INFO  MyGenericsType:38 - class pers.hanchao.generics.type.MyGenericsType
2018-02-22 11:23:11 INFO  MyGenericsType:42 - class pers.hanchao.generics.type.MyGenericsType
````

发现泛型原始类型和泛型类型的实例化对象是一样的，这是由于类型擦除造成的，后续会进行讲解。

### 2.泛型类型命名规范
泛型类型的命名并不是必须为T，也可以使用其他字母，如X、K等，只要是命名为单个大写字即可，例如：
````
/**
 * 泛型类语法示例
 * Created by 韩超 on 2018/2/22.
 */
class MyGenericsType2<X>{
    private X x;
}
````

虽然没有强制的命名规范，但是为了便于代码阅读，也形成了一些约定俗成的命名规范，如下：

T：通用泛型类型，通常作为第一个泛型类型
S：通用泛型类型，如果需要使用多个泛型类型，可以将S作为第二个泛型类型
U：通用泛型类型，如果需要使用多个泛型类型，可以将U作为第三个泛型类型
V：通用泛型类型，如果需要使用多个泛型类型，可以将V作为第四个泛型类型
E：集合元素 泛型类型，主要用于定义集合泛型类型
K：映射-键 泛型类型，主要用于定义映射泛型类型
V：映射-值 泛型类型，主要用于定义映射泛型类型
N：数值 泛型类型，主要用于定义数值类型的泛型类型
下面通过一些实例加深对这些类型的理解。

#### 2.1.通用泛型类型：T,S,U,V…
通用泛型类型：适用于所有的泛型类型。
````
/**
 * <p>Title: 通用泛型类型示例</p>
 * @author 韩超 2018/2/22 11:09
 */
public class MyMultiType<T,S,U,V,A,B> {
    private T t;
    private S s;
    private U u;
    private V v;
    private A a;
    private B b;

    private final static Logger LOGGER = Logger.getLogger(MyMultiType.class);

    public void set(T first,S seconde,U third,V fourth,A fifth,B sixth){
        LOGGER.info("第1个参数的类型是：" + first.getClass().getName().toString());
        LOGGER.info("第2个参数的类型是：" + seconde.getClass().getName().toString());
        LOGGER.info("第3个参数的类型是：" + third.getClass().getName().toString());
        LOGGER.info("第4个参数的类型是：" + fourth.getClass().getName().toString());
        LOGGER.info("第5个参数的类型是：" + fifth.getClass().getName().toString());
        LOGGER.info("第6个参数的类型是：" + sixth.getClass().getName().toString());
    }

    /**
     * <p>Title: 测试通用泛型类型</p>
     * @author 韩超 2018/2/22 11:08
     */
    public static void main(String[] args){
        MyMultiType<Integer,Double,Float,String,Long,Short> myMultiType = new MyMultiType<Integer, Double, Float, String, Long, Short>();
        myMultiType.set(1,1D,1F,"1",1L, (short) 1);
    }
}
````

运行结果：
````
2018-02-22 11:09:27 INFO  MyMultiType:20 - 第1个参数的类型是：java.lang.Integer
2018-02-22 11:09:27 INFO  MyMultiType:21 - 第2个参数的类型是：java.lang.Double
2018-02-22 11:09:27 INFO  MyMultiType:22 - 第3个参数的类型是：java.lang.Float
2018-02-22 11:09:27 INFO  MyMultiType:23 - 第4个参数的类型是：java.lang.String
2018-02-22 11:09:27 INFO  MyMultiType:24 - 第5个参数的类型是：java.lang.Long
2018-02-22 11:09:27 INFO  MyMultiType:25 - 第6个参数的类型是：java.lang.Short
````

#### 2.1.集合泛型类型：E
集合泛型类型：适用于泛型类型作为集合元素的泛型定义。
````
/**
 * <p>Title: 集合泛型类型示例</p>
 * @author 韩超 2018/2/22 11:11
 */
public class MyList<E> {
    private List<E> list = new ArrayList<E>();

    private final static Logger LOGGER = Logger.getLogger(MyList.class);

    public void myAdd(E e){
        list.add(e);
        LOGGER.info(e.toString());
    }

    public int mySize(){
        return list.size();
    }

    /**
     * <p>Title: 集合泛型类型示例</p>
     * @author 韩超 2018/2/22 11:11
     */
    public static void main(String[] args){
        MyList<String> stringMyList = new MyList<String>();
        stringMyList.myAdd(new String("hello!"));
    }
}
````

运行结果：
````
2018-02-22 11:10:59 INFO  MyList:23 - hello!
````

#### 2.3.映射泛型类型：K,V
映射泛型类型：适用于泛型类型作为键值对的泛型定义。
````
/**
 * <p>Title: 映射泛型类型示例</p>
 * @author 韩超 2018/2/22 11:15
 */
public class MySet<K,V> {
    private Map<K,V> map = new HashMap<K, V>();

    private final static Logger LOGGER = Logger.getLogger(MySet.class);


    public void myPut(K key,V value){
        map.put(key,value);
        LOGGER.info("key:" + key.toString() + ",value=" + value.toString());
    }

    public int mySize(){
        return map.size();
    }
    /**
     * <p>Title: 映射泛型类型示例</p>
     * @author 韩超 2018/2/22 11:14
     */
    public static void main(String[] args){
        MySet<String,Integer> mySet = new MySet<String, Integer>();
        mySet.myPut("001",100);
    }
}
````

运行结果：
````
2018-02-22 11:15:23 INFO  MySet:20 - key:001,value=100
````

#### 2.4.数值泛型类型
映射泛型类型：适用于泛型类型作为键值对的泛型定义。
````
/**
 * <p>Title: 数值泛型类型示例</p>
 * @author 韩超 2018/2/22 11:16
 */
public class MySquare<N> {
    private final static Logger LOGGER = Logger.getLogger(MySquare.class);

    public void square(N number){
        LOGGER.info(number.getClass().toString() + ":" + number);
    }

    /**
     * <p>Title: 数值泛型类型示例</p>
     * @author 韩超 2018/2/22 11:16
     */
    public static void main(String[] args){
        MySquare<Integer> mySquare = new MySquare<Integer>();
        mySquare.square(1);
    }
}
````
运行结果：
````
2018-02-22 11:19:07 INFO  MySquare:13 - class java.lang.Integer:1
````

### 3.有界泛型类型
如果不对泛型类型做限制，则泛型类型可以实例化成任意类型，这种情况可能产生某些安全性隐患。
为了限制允许实例化的泛型类型，我们需要一种能够限制泛型类型的手段，即：有界泛型类型
要实现有界泛型类型，只需要在泛型类型后面追加extends 父类型即可，语法如下：

//有界泛型类型语法 - 继承自某父类
<T extends ClassA>
//有界泛型类型语法 - 实现某接口
<T extends InterfaceB>
//有界泛型类型语法 - 多重边界
<T extends ClassA & InterfaceB & InterfaceC ... >

````
//示例
<N extends Number> //N标识一个泛型类型，其类型只能是Number抽象类的子类
<T extends Number & Comparable & Map> //T标识一个泛型类型，其类型只能是Person类型的子类，并且实现了Comparable 和Map接口
````

其中，

T：泛型类型
extends：边界关键字，可以标识extends ClassA，也可以标识implements InterfaceB。
&：多重边界，与类的继承机制类似：可以实现一个父类和实现多个接口。
ClassA ：父类：必须在第一位。
下面通过程序示例加深理解。
````
/**
 * 有界类型参数
 * Created by 韩超 on 2018/1/30.
 */
public class MyMath {

    private final static Logger LOGGER = Logger.getLogger(MyMath.class);

    /**
     * <p>Title: 有界参数类型（单重）</p>
     * @author 韩超 2018/1/31 10:37
     */
    public static <T extends Comparable> T MyMax(T x, T y) {
        T max = x;
        if (y.compareTo(max) > 0) {
            max = y;
        }
        return max;
    }

    /**
     * <p>Title: 多重有界参数类型</p>
     * @author 韩超 2018/1/31 13:20
     */
    public static <T extends Number & Comparable> T MyMax2(T x, T y){
        T dmax = x.doubleValue() >= y.doubleValue() ? x : y;
        return dmax;
    }

    /**
     * <p>Title: 有界泛型类型示例</p>
     * @author 韩超 2018/2/22 11:44
     */
    public static void main(String[] args){
        Integer result = MyMath.MyMax(1,2);
        LOGGER.info(result);

        Double result1 = MyMath.MyMax2(1D,2D);
        LOGGER.info(result1);
    }
}
````
运行结果：
````
2018-02-22 11:45:06 INFO  MyMath:42 - 2
2018-02-22 11:45:06 INFO  MyMath:45 - 2.0
````

使用有界泛型类型有两个好处：

在一定程度上限制泛型类型，提高程序安全性
因为定义了边界，所以可以调用父类或父接口的方法，如y.doubleValue()。这种情况比单纯的调用Object类提供的方法更加灵活


### 4. 泛型擦除
泛型类型擦除：泛型在编译阶段，生成的字节码中不包含泛型类型，只保留原始类型。
````
/**
 * <p>Title: 泛型原始类型使用</p>
 * @author 韩超 2018/2/22 11:21
 */
public static void main(String[] args){
    //泛型原始类型
    MyGenericsType myGenericsType = new MyGenericsType();
    LOGGER.info(myGenericsType.getClass().toString());

    //泛型类型
    MyGenericsType<Integer> integerMyGenericsType = new MyGenericsType<Integer>();
    LOGGER.info(integerMyGenericsType.getClass().toString());
}
````
我们对这个例子进行修改，如下：
````
//类型擦除
LOGGER.info("类型擦除:");
MyGenericsType myGenericsType = new MyGenericsType();
MyGenericsType<Integer> integerMyGenericsType = new MyGenericsType<Integer>();
MyGenericsType<Double> doubleMyGenericsType = new MyGenericsType<Double>();
LOGGER.info(myGenericsType.getClass().toString());
LOGGER.info(integerMyGenericsType.getClass().toString());
LOGGER.info(doubleMyGenericsType.getClass().toString());
````

运行结果：
````
2018-02-23 09:41:53 INFO  TypeErasureDemo:22 - 类型擦除:
2018-02-23 09:41:53 INFO  TypeErasureDemo:26 - class pers.hanchao.generics.type.MyGenericsType
2018-02-23 09:41:53 INFO  TypeErasureDemo:27 - class pers.hanchao.generics.type.MyGenericsType
2018-02-23 09:41:53 INFO  TypeErasureDemo:28 - class pers.hanchao.generics.type.MyGenericsType
````
通过观察运行结果，可以发现，泛型在编译之后，只保留了原始类型，即MyGenericsType。

#### 4.1 类型擦除的原始类型
结论：
：无界的泛型类型擦除之后的原始类型是Object类型。
：有界泛型类型擦除之后的原始类型:父类型

3.先检查后编译
既然泛型会在编译阶段进行类型擦除，那如何保证编译之前的类型转换安全呢？答案是：通过IDE提供的编译前检查功能

4.通过反射跳过类型检查
通过上面的章节已知，无法将Double类型的数据写入到Integer类型的泛型中，那么久真的无法实现吗？
其实是可以的：通过Java反射可以跳过泛型的类型检查。


### 泛型使用的8个限制
1.Java泛型不能使用基本类型
因为泛型在编译时，会进行类型擦除，最后只保留原始类型。而原始类型只能是Object类及其子类，当然不能使用基本数据类型。
2.Java泛型不允许进行实例化
而Object肯定无法转换成Integer的。Java泛型为了防止此类类型转换错误的发生，禁止进行泛型实例化。
3.Java泛型不允许进行静态化
原因：静态变量在类中共享，而泛型类型是不确定的，因此编译器无法确定要使用的类型，所以不允许进行静态化。
备注：这里的静态化针对的对象是泛型变量，并不是泛型方法。
4.Java泛型不允许直接进行类型转换（通配符可以不，但不建议这么做）
因为这违背了Java泛型设计的初衷：降低类型转换的安全隐患。
5.Java泛型不允许直接使用instanceof运算符进行运行时类型检查（通配符可以）
//LOGGER.info(stringList instanceof ArrayList<Double>);//不能直接使用instanceof，类型检查报错
6.Java泛型不允许创建确切类型的泛型数组（通配符可以）
//Demo6<Integer>[] iDemo6s = new Demo6<Integer>[2];//类型检查报错
7.Java泛型不允许定义泛型异常类或者catch异常（throws可以）
因为类型擦除之后，同时catch两个一样的异常，肯定不能通过编译。
8.Java泛型不允许作为参数进行重载
因为类型擦除之后，两个方法是一样的参数列表，这种情况无法重载。

* 总结
Java泛型其实还有其他的使用限制，这里不再赘述。
我们在理解Java泛型的使用限制时，应该首先用心理解下面两点：

Java泛型的设计初衷：降低类型转换的安全隐患，而非为了实现任意化。
Java泛型的实现原理：类型擦除。


### 通配符：上边界、下边界与无界
1.概念简介
在Java泛型定义时:

用<T>等大写字母标识泛型类型，用于表示未知类型。
用<T extends ClassA & InterfaceB …>等标识有界泛型类型，用于表示有边界的未知类型。
在Java泛型实例化时:

用<?>标识通配符，用于表示实例化时的未知类型。
用<? extends 父类型>标识上边界通配符，用于表示实例化时可以确定父类型的未知类型。
用<? super 子类型>标识下边界通配符，用于表示实例化时可以确定子类型的未知类型。

2.<T>与<?>的区别
<T>：泛型标识符，用于泛型定义（类、接口、方法等）时，可以想象成形参。
<?>：通配符，用于泛型实例化时，可以想象成实参。

<T>示例：
````
/**
 * <p>Title: <T>与<?>示例</></></p>
 * @author 韩超 2018/2/23 16:33
 */
static class Demo1<T>{
    private T t;
}
````

<?>示例：
````
//<？>示例
//我们可以确定具体类型时，直接使用具体类型
Demo1<Integer> integerList = new Demo1<Integer>();
//我们不能确定具体类型时，可以使用通配符
Demo1<? extends Number> numberList = null;
//我们能够确定具体类型时，再实例化成具体类型
numberList = new Demo1<Integer>();
numberList = new Demo1<Double>();
````

3.通配符详解
向上转型与向下转型
为了更好的理解通配符的，我们首先需要回顾Java向上转型与向下转型的概念。
向上转型：
向上转型：子类型通过类型转换成为父类型（隐式的）。
示例如下：
````
//子类Integer转换成父类型Object（向上转型）
Object integer = new Integer(1);
````
向上转型有很多好处，如减少重复代码、使代码变得简洁，提高系统扩展性，这里就不多讲述了。
向下转型：
向下转型：父类型通过类型转换成为子类型（显式的，有风险需谨慎）。
````
//向下转型（有风险需谨慎）
Integer integer1 = (Integer) new Object();
integer1.intValue();
````
运行结果：
````
Exception in thread "main" java.lang.ClassCastException: java.lang.Object cannot be cast to java.lang.Integer
    at pers.hanchao.generics.wildcard.BoundedWildcardsDemo.main(BoundedWildcardsDemo.java:126)
````
因为子类型存在一些父类型没有的方法，所以这种向下转型存在安全隐患


3.1上边界通配符（<? extends 父类型>） 3.2无边界通配符（<?>） 3.3.下边界通配符（<? super 子类型>）
上边界类型通配符（<? extends 父类型>）：
因为可以确定父类型，所以可以以父类型去获取数据（向上转型）。但是不能写入数据。

无边界类型通配符（<?>）：
无边界类型通配符（<?>） 等同于 上边界通配符<? extends Object>
因为可以确定父类型是Object，所以可以以Object去获取数据（向上转型）。但是不能写入数据。

下边界类型通配符（<? super 子类型>）：
因为可以确定最小类型，所以可以以最小类型去写入数据（向上转型）。。
因为可以确定最大类型是Object，所以可以以Object去获取数据（向上转型）。但是这么做意义不大。

3.4.总结
上边界类型通配符（<? extends 父类型>）：因为可以确定父类型，所以可以以父类型去获取数据（向上转型）。但是不能写入数据。
下边界类型通配符（<? super 子类型>）：因为可以确定最小类型，所以可以以最小类型去写入数据（向上转型）。而不能获取数据。
无边界类型通配符（<?>） 等同于 上边界通配符<? extends Object>，所以可以以Object类去获取数据，但意义不大。
下边界类型通配符（<? super 子类型>）下边界通配符<? super 子类型> + 上边界通配符<? extends Object>，所以可以以Object类去获取数据，但意义不大。



