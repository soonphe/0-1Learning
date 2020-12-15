# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## scala

Scala：函数式编程
解决java：	线程安全
	线程通信
	有状态
	中间结果
Java  => 编译 => *.class => JVM
Scala => 编译 => *.class => JVM

方法定义：  //以性好为方法名时，必须指定参数类型，无需指定返回类型，scala会自动判断
如果方法内有多个表达式，最后一个表达式就是方法的返回值，不需要return关键字。
scala:
def 方法名（参数：类型[泛型]）：返回值={
	表达式1；表达式2
	表达式3
	表达式4
}
例：
def  main(args: Array[String]): Unit = {
    print("hello world!");
}

var变量，val常量
var a = 10; 可以修改 var a:Int = 10;不写类型，Scala会自动推演类型
val b = 20; 这个就无法修改 error: reassignment to val		//val类似java中的final，final成员变量表示常量，只能被赋值一次
一次赋值多个
val (a,b,c) = (1,2,"a")
val a,b,c = 10	

字符串：
 // s : StringContext
val c = s"the value is: ${a/5}"
s是StringContext类的一个方法，看着很别扭，用起来很简洁。
转义字符：
val json  = "{\"name\":\"张三\"}"
三个双引  val json2 = """{"name":"张三"}"""

循环：
for(i <- 1 to 10) {	//<-叫做提取符，每次把后面集合的一个值赋值给i进行处理
println("i = " + i);
}
遍历数组
val a = Array(1,2,3,4,5,6,7,8,9,10)
for(x <- a){
    println(x)
}
列出目录下的txt文件
scala> val files = new java.io.File("c:/").listFiles

for语句支持判断进行数据过滤：
for(f <- files if f.getName.endsWith(".txt")) println(f)

注：
for比java的更加强大，if过滤数据，yield数据加工

过滤加计算：
val array = Array(1,2,3,4,5)
    
val array3 = for(x <- array if x>2) yield x
for(y <- array3){
	println(y)
}

val array4 = for(x <- array if x>2) yield x*10
for(y <- array4){
	println(y)
}

*匿名函数：——本质上就是对象

Option选项类型：
val x1 = div(10, 5).getOrElse(0)
val x2 = div(10, 0).getOrElse(0)
println(x1)
println(x2)

集合：
注意：和java不同的是，它的内容不可变。 list(3)=40这是不行的，它是只读的。
当然scala也提供了可变版本，ArrayBuffer对应Array，ListBuffer对应List。
    //取1到11之间的数值，步长为2，_*代表把每个元素提出来
    val list = Array[Int](1 to 11 by 2: _*)


函数式编程：
Scala编程=FP+OP		函数式编程+面向对象编程
java语言中方法要依赖于类或接口存在，而scala的函数可以不依赖于类，不依赖于接口，不依赖于方法而独立存在，所以才能把它赋值给一个变量。
def fn(name:String){ println(name) }
val f = fn _	//fn _就代表fn函数的原型

f("Spark")
fn("Spark")
fn("Scala")

函数式编程核心价值在于计算是不仅传递数据还传递算法

高阶函数(HighOrdered)：方法接收参数是个函数，或者方法返回是另一个函数，这个方法就称为高阶函数。
高阶函数是scala和java使用中最大的不同，也是学习scala中最大的价值所在。
高级函数有个最大的特征就是类型推断


Scala中=>的用法:
参考网址：https://www.cnblogs.com/jizhao/p/5945908.html
1. 表示函数的类型(Function Type)
2. 匿名函数(Anonymous functions/Function Literals/Lambda)
3. By-Name Parameters
4. case语句

闭包 Closure：超出了函数的作用域，但函数中的变量依然能被访问，这就叫闭包。
一旦函数引用了外部的变量或常量，那么就称此函数为闭包函数。
  val x = 3
  val fn = (y: Int) => x + y//闭包
（x:Int） => println(x+10)  //不是闭包。


  def say(content:String) = (msg:String)=> println(content+":"+msg)
  // 两次调用say，传入不同的content，创建不同的函数返回
  // 然而content只是一个局部变量，
  val r = say("Spark")	//函数调用结束，内部的局部变量值就释放
  //传入content的值 ，后面r方法依然可以访问到
  r("Good")


Currying柯里化
假设原来的函数有两个参数的话，可以把它转换成两个函数，第一个函数会接收原来的第一个函数，第二个函数接收原来第二个参数。
def sum(x:Int, y:Int) = x+y
sum(1,2)

def sumCurrying(x:Int) = (y:Int)=>x+y
sumCurrying(1)(2)
//这里就应用了闭包，x在第一次计算使用完毕，但在第二个函数内还使用了它

def sumCurryingFinal(x:Int)(y:Int)=x+y		//终极写法
sumCurryingFinal(1)(2)
意义：原来一个大函数的调用，变成两个连续的函数调用。

高级函数：
2.2.1partition拆分
val b = Array(1,2,3,4,5).toList
b.partition( (x:Int) => (x%2==0))	//按奇偶数将集合划分为两个

2.2.2map映射
val list = List(1,2,3,4,5,6)
list.map { x => x+1 }    //每个元素+1

//加上prefix和suffix，并自动转换为字符串类型
scala> val list = List("index","item","show")
list: List[String] = List(index, item, show)
scala> list.map{ x => "/WEB-INF/views/"+x+".jsp" }

2.2.3filter、filterNot过滤
val list = List(1,2,3,4,5)
list.filter( x=> x>3)
list.filterNot( x=>x%2==1)

2.2.4reduce化简——将上次的运行结果和下一个值进行运算
val list = Array(1,2,3,4)
list.reduce{ (x,y) => x+y }

list.reduce{ (x,y) => println(s"x:$x y:$y");x+y }	//注意外层必须是大括号
执行结果：
x:1 y:2
x:3 y:3
x:6 y:4
res62: Int = 10

JAVA并发线程的4种风格：
4种风格：Thread裸线程（用过）、Executor服务（用过）、ForkJoin框架（得靠JVM经验）、Actor模型

前面三种方式运行时，出现问题：无法估计，无法监控，无法解决bug

JDK中没有actor的实现；因此你必须引用一些实现了actor的库。
Akka actor在内部使用ForkJoin框架来处理工作

Scala默认的actor实现就是akka

5.Akka框架——轻松并发百万actor，把所有的资源抽象成actor，actor最终代表的就是CPU的core
actor比线程轻，一个线程可以绑定很多的actor
actor通讯消息：两种形式：！不需要返回值    ？需要返回值
依赖：
<dependency>
    <groupId>com.typesafe.akka</groupId>
    <artifactId>akka-remote_2.11</artifactId>	//对应scala版本，2.11
    <version>2.3.11</version>
</dependency>
<dependency>
    <groupId>com.typesafe.akka</groupId>
    <artifactId>akka-actor_2.11</artifactId>
    <version>2.3.11</version>
</dependency>
