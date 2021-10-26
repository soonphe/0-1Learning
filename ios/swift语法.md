# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")



## swift语法

### 数据类型

### 变量和常量
变量：var
常量（无法变更）：let

var str = "hello" (类型推断)（一般采用这种）
var s:String = "word"(指明类型)
var i:Int = 100

字符串对象连接引用——\(变量)
var str= "hello"
str = "\(str),hello"

### 数组：
var arr = ["hello","world",100]
声明空数组：var arr1 = []
声明空数组且指明类型：var arr2 = String[]()

### 字典:
var dict = ["name":"hello","age",16]
动态赋值：dict["sex"]="Female"
根据key取值：dict["name"]


### 循环：
for index in 0..100{

}

引用数组arr：
for value in arr{
println(value)
}

for对字段进行遍历：
for (key,value) in dict{

}

while循环:
while i<arr.count{

}

### 流程控制
if判断：
if index%2==0{

}

可选变量：
var myName:String?="hello"——?表示标识可选变量
if let name=myName{

}

设置空：myName = nil

### 语法糖
? 和 ! 其实分别是Swift语言中对一种可选类型（ Optional) 操作的语法糖。
那可选类型是干什么的呢？ 
Swift中是可以声明一个没有初始值的属性， Swift中引入了可选类型(Optional)来解决这一问题。它的定义是通过在类型声明后加一个 ? 操作符完成的。

Optional其实是个enum，里面有None和Some两种类型。其实所谓的nil就是Optional.None ， 非nil就是Optional.Some， 然后会通过Some(T)包装（wrap）原始值，这也是为什么在使用Optional的时候要拆包（从enum里取出来原始值）的原因。这里是enum Optional的定义

````
var name: String?
// 上面这个Optional的声明，是”我声明了一个Optional类型值，
 它可能包含一个String值，也可能什么都不包含”，
也就是说实际上我们声明的是Optional类型，而不是声明了一个String类型 (这其实理解起来挺蛋疼的...)
````
怎么使用Optional值呢？文档中也有提到说，在使用Optional值的时候需要在具体的操作，比如调用方法、属性、下标索引等前面需要加上一个?，如果是nil值，也就是Optional.None，会跳过后面的操作不执行，如果有值，就是Optional.Some，可能就会拆包(unwrap)，然后对拆包后的值执行后面的操作，来保证执行这个操作的安全性。
````
// 例如： let length = name?.characters.count
````

? 的使用场景:
1.声明Optional值变量
2.用在对Optional值操作中，用来判断是否能响应后面的操作
3.使用 as? 向下转型(Downcast)

上面提到Optional值需要拆包(unwrap)后才能得到原来值，然后才能对其操作，那怎么来拆包呢？
拆包有两种方法：

可选绑定(Optional Binding)
可选绑定(Optional Binding)是一种更简单更推荐的方法来解包一个可选类型。 使用可选绑定来检查可选类型的变量有值还是没值。如果有值, 解包它并且将值传递给一个常量或者变量。

硬解包
硬解包即直接在可选类型后面加一个感叹号（!）来表示它肯定有值。
````
var str1: String? = "Hello"
let greeting = "World!"
if (str1 != nil) { 
       let message = greeting + str1! 
       print(message)
}

/**上面例子，我们只是自己知道str1肯定有值, 所以才直接硬解包了str1变量。 但是万一有时候我们的感觉是错的,
 那程序在运行时可能会出现严重的错误. 所以Swift中是推荐先检查可选类型是否有值, 然后再进行解包的!
*/ 
````

! 的使用场景:
1.强制对Optional值进行拆包(unwrap)
2.声明隐式拆包变量，一般用于类中的属性



### 语言函数：
func sayHello(name){
	printlb("hello\(name)")
}
调用方法：sayHello("world")

多个返回值：
func getNums(name)->(Int,Int){
	return(2,3)
}

调用并接受返回值：
let (a,b) = getNums()
println(a)	//打印a

函数即对象——也就是可以当作一个变量：
var fun= sayHello
运行函数：
fun("zhangsan")

### swift函数闭包
闭包就是能够读取其他函数内部变量的函数，可以理解成定义在一个函数内部的函数。
简单的说它就是一个代码块，用{}包起来，他可以用在其他函数的内部，将其他函数的变量作为代码块的参数传入代码块中，在Swift中多用于回调。这个跟Object-C中的block是一样的。

例子
```
//一般形式
//有参有反
let testOne: (String, String) -> String = {(str1, str2) in return str1 + str2}
print(testOne("one", "two"))
//无参有反  可以直接省略 "in"
let testTwo: () -> String = {return "test闭包"}
//无参无反
let testThree: () -> Void = {print("test闭包")}
```
归纳
上面的例子中:后面是闭包的类型，而=后面的就是一个代码块，也就是闭包的具体实现，这些个OC中的block基本一样。


### 面向对象：
class Hi{
	func sayHi{
	  print("hello world")
	}
}

继承
class Hello:Hi{
  var _name:String
  //构造方法(初始化时候调用，这个是有参构造函数)
  init(name:String){
      self._name = name
  }
  //方法重写
  override func sayHi(){
      println("Hello \(self._name)")
  }
}

函数调用
var hi = Hi()
hi.sayHi()
var h = Hello(name: "zhangsan")  //name为标签
h.sayHi()



