# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 数据类型

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
swift函数闭包：在函数内部写函数


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

appDelegate:侦听引用程序的状态


