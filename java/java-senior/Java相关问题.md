# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Jvm相关问题
~~~~
问：八种基本数据类型的大小，以及他们的封装类
答：
八种基本数据类型，int ,double ,long ,float, short,byte,character,boolean
对应的封装类型是：Integer ,Double ,Long ,Float, Short,Byte,Character,Boolean

问：Switch能否用string做参数？
答：在Java 5以前，switch(expr)中，expr只能是byte、short、char、int。从Java 5开始，Java中引入了枚举类型，expr也可以是enum类型，从Java 7开始，expr还可以是字符串（String），但是长整型（long）在目前所有的版本中都是不可以的。

问：==与equals的主要区别是：
答：
==常用于比较原生类型，而equals()方法用于检查对象的相等性。
另一个不同的点是：如果==和equals()用于比较对象，当两个引用地址相同，==返回true。
而equals()可以返回true或者false主要取决于重写实现。最常见的一个例子，字符串的比较，不同情况==和equals()返回不同的结果。equals()方法最重要的一点是，能够根据业务要求去重写，按照自定义规则去判断两个对象是否相等。重写equals()方法的时候，要注意一下hashCode是否会因为对象的属性改变而改变，否则在使用散列集合储存该对象的时候会碰到坑！！理解equals()方法的存在是很重要的。
1. 使用==比较有两种情况：
        比较基础数据类型(Java中基础数据类型包括八中：short,int,long,float,double,char,byte,boolen)：这种情况下，==比较的是他们的值是否相等。
        引用间的比较：在这种情况下，==比较的是他们在内存中的地址，也就是说，除非引用指向的是同一个new出来的对象，此时他们使用`==`去比较得到true，否则，得到false。
2. 使用equals进行比较：
        equals追根溯源，是Object类中的一个方法，在该类中，equals的实现也仅仅只是比较两个对象的内存地址是否相等，但在一些子类中，如：String、Integer 等，该方法将被重写。


问：String、StringBuffer与StringBuilder的区别
答：其中String是只读字符串，也就意味着String引用的字符串内容是不能被改变的。
而StringBuffer和StringBulder类表示的字符串对象可以直接进行修改。
StringBuilder是JDK1.5引入的，它和StringBuffer的方法完全相同，
区别在于它是单线程环境下使用的，因为它的所有方面都没有被synchronized修饰，因此它的效率也比StringBuffer略高。

问：try catch finally，try里有return，finally还执行么？
答：会执行，在方法 返回调用者前执行。Java允许在finally中改变返回值的做法是不好的，因为如果存在finally代码块，try中的return语句不会立马返回调用者，而是纪录下返回值待finally代码块执行完毕之后再向调用者返回其值，然后如果在finally中修改了返回值，这会对程序造成很大的困扰，C#中就从语法规定不能做这样的事。

**Hashcode的作用。**

以Java.lang.Object来理解,JVM每new一个Object,它都会将这个Object丢到一个Hash哈希表中去,这样的话,下次做Object的比较或者取这个对象的时候,它会根据对象的hashcode再从Hash表中取这个对象。这样做的目的是提高取对象的效率。具体过程是这样: 
1. new Object(),JVM根据这个对象的Hashcode值,放入到对应的Hash表对应的Key上,如果不同的对象确产生了相同的hash值,也就是发生了Hash key相同导致冲突的情况,那么就在这个Hash key的地方产生一个链表,将所有产生相同hashcode的对象放到这个单链表上去,串在一起。 
2. 比较两个对象的时候,首先根据他们的hashcode去hash表中找他的对象,当两个对象的hashcode相同,那么就是说他们这两个对象放在Hash表中的同一个key上,那么他们一定在这个key上的链表上。那么此时就只能根据Object的equal方法来比较这个对象是否equal。当两个对象的hashcode不同的话，肯定他们不能equal. 


**Excption与Error区别**

Error表示系统级的错误和程序不必处理的异常，是恢复不是不可能但很困难的情况下的一种严重问题；比如内存溢出，不可能指望程序能处理这样的状况；Exception表示需要捕捉或者需要程序进行处理的异常，是一种设计或实现问题；也就是说，它表示如果程序运行正常，从不会发生的情况。











~~~~