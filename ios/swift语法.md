# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")



## swift语法

### 数据类型

### 变量和常量
变量：var
常量（无法变更）：let

### swift枚举
枚举是一种定义了一个通用类型的一组相关值的方式，可以通过枚举名和点语法来访问枚举的成员。

```
enum CompassPoint{
    case North
    case South
    case East
    case West
}
使用枚举：
var direction = CompassPoint.North  //定义枚举变量
direction = .East                   //可以省略枚举名
```

### 类型
```
var str = "hello" (类型推断)（一般采用这种）
var s:String = "word"(指明类型)
var i:Int = 100
```

### 字符串连接
字符串对象连接引用——\(变量)
```
var str= "hello"
str = "\(str),hello"    //hello,hello
```

### 数组
```
var arr = ["hello","world",100]
声明空数组：var arr1 = []                     //空数组
声明空数组且指明类型：var arr2 = String[]()    //String类型的空数组
```

数组遍历
```
for index in 0..arr.count{
    println(arr[index])                 //hello world 100
}
```

### 字典
```
var dict = ["name":"hello","age",16]
动态赋值：dict["sex"]="Female"           //增加一个键值对
根据key取值：dict["name"]                //hello
```

### 循环
```
for index in 0..100{                    //0-99
    println(index)
}
```
引用数组arr：
```
for value in arr{
    println(value)                     //hello world 100
}
```
for对字段进行遍历：
```
for (key,value) in dict{                //name hello age 16
    println("\(key) \(value)")
}
```
while循环:
```
while i<arr.count{                      //0-2
    println(arr[i])                     //hello world 100
    i++
}
```

### 流程控制
if判断：
```
if index%2==0{                        //偶数
    println("偶数")
}
```

可选变量：
```
var myName:String?="hello"          //声明一个可选变量，?表示标识可选变量
if let name=myName{             //如果myName有值，则赋值给name
    println(name)                    //hello

}
```

设置空：
```
myName = nil            //设置为空
```

### 语法糖
? 和 ! 其实分别是Swift语言中对一种可选类型（ Optional) 操作的语法糖。

#### 可选类型
那可选类型是干什么的呢？ 
Swift中是可以声明一个没有初始值的属性， Swift中引入了可选类型(Optional)来解决这一问题。它的定义是通过在类型声明后加一个 ? 操作符完成的。

Optional其实是个enum，里面有None和Some两种类型。其实所谓的nil就是Optional.None ， 非nil就是Optional.Some， 然后会通过Some(T)包装（wrap）原始值，这也是为什么在使用Optional的时候要拆包（从enum里取出来原始值）的原因。这里是enum Optional的定义

````
var name: String?
// 上面这个Optional的声明，是”我声明了一个Optional类型值，
````
它可能包含一个String值，也可能什么都不包含”，也就是说实际上我们声明的是Optional类型，而不是声明了一个String类型 (这其实理解起来挺蛋疼的...)

怎么使用Optional值呢？文档中也有提到说，在使用Optional值的时候需要在具体的操作，比如调用方法、属性、下标索引等前面需要加上一个?，如果是nil值，也就是Optional.None，会跳过后面的操作不执行，如果有值，就是Optional.Some，可能就会拆包(unwrap)，然后对拆包后的值执行后面的操作，来保证执行这个操作的安全性。
````
// 例如： let length = name?.characters.count
````

? 的使用场景:
1. 声明Optional值变量
2. 用在对Optional值操作中，用来判断是否能响应后面的操作
3. 使用 as? 向下转型(Downcast)

上面提到Optional值需要拆包(unwrap)后才能得到原来值，然后才能对其操作，那怎么来拆包呢？

拆包有两种方法：
- 可选绑定(Optional Binding) 是一种更简单更推荐的方法来解包一个可选类型。 使用可选绑定来检查可选类型的变量有值还是没值。如果有值, 解包它并且将值传递给一个常量或者变量。

- 硬解包
硬解包即直接在可选类型后面加一个感叹号（!）来表示它肯定有值。
````
var str1: String? = "Hello"
let greeting = "World!"
if (str1 != nil) { 
       let message = greeting + str1! 
       print(message)
}
````
上面例子，我们只是自己知道str1肯定有值, 所以才直接硬解包了str1变量。 但是万一有时候我们的感觉是错的, 那程序在运行时可能会出现严重的错误. 所以Swift中是推荐先检查可选类型是否有值, 然后再进行解包的!

! 的使用场景:
1.强制对Optional值进行拆包(unwrap)
2.声明隐式拆包变量，一般用于类中的属性



### 语言函数
**定义方法：**
```
func sayHello(name){        //定义无返回值的方法
	printlb("hello\(name)")
}
//调用方法：
sayHello("world")


func sayHello(name:String)->String{   //定义有返回值的方法
    return "hello \(name)"
}
```

**多个返回值：**
```
func getNums(name)->(Int,Int){  //返回两个值
	return(2,3)
}

//调用并接受返回值：
let (a,b) = getNums()
println(a)	//打印a
```

**函数即对象**——也就是可以当作一个变量：
```
var fun= sayHello   //将sayHello方法赋值给fun
//运行函数：
fun("zhangsan")
```

### swift函数闭包
闭包就是能够读取其他函数内部变量的函数，可以理解成定义在一个函数内部的函数。

简单的说它就是一个代码块，用{}包起来，他可以用在其他函数的内部，将其他函数的变量作为代码块的参数传入代码块中，在Swift中多用于回调。这个跟Object-C中的block是一样的。

例子
```
//有参有反
let testOne: (String, String) -> String = {(str1, str2) in return str1 + str2}

print(testOne("one", "two"))        //onetwo
```

```
//无参有反  可以直接省略 "in"
let testTwo: () -> String = {return "test闭包"}
//无参无反
let testThree: () -> Void = {print("test闭包")}
```
上面的例子中:后面是闭包的类型，而=后面的就是一个代码块，也就是闭包的具体实现，这些个OC中的block基本一样。

### 面向对象
#### 类
```
class Hi{                   //定义类
	func sayHi{             //定义方法
	  print("hello world")
	}
}

var hi = Hi()           //实例化
hi.sayHi()            //调用方法
```

#### 类的继承
```
class Hello:Hi{             //继承Hi类
  var _name:String
  //构造方法(初始化时候调用，这个是有参构造函数)
  init(name:String){
      self._name = name
  }
  //方法重写
  override func sayHi(){
      super.sayHi()                 //调用父类方法
      println("Hello \(self._name)")
  }
}

var h = Hello(name: "zhangsan")     //实例化
h.sayHi()                           //调用方法
```
#### 类的拓展
```
  extension Hello{          //拓展Hello类,添加新方法,不影响原有类,不需要继承,直接拓展,
      func sayHello(){
          println("Hello \(self._name)")
      }
  }
  
    extension Hi{           //拓展Hi类,添加新方法,不影响原有类,不需要继承,直接拓展,子类会自动继承此方法
      func sayHello(){
          println("Hello \(self._name)")
      }
  }
```

#### 接口
```
protocol HelloProtocol{         //定义接口
    func sayHello()             //无参数
    func sayHello(name:String)  //带参数
    func sayHello()->String     //返回值
}
```

#### 实现接口
```
class Hello:Hi,HelloProtocol{   //实现接口
    func sayHello(){
        println("Hello")
    }
    func sayHello(name:String){
        println("Hello \(name)")
    }
    func sayHello()->String{
        return "Hello"
    }
}
```

#### 命名空间
swift中没有命名空间的概念，但是可以通过类的嵌套来实现类似的功能
```
class A{
    class B{
        class C{
            func sayHello(){
                println("Hello")
            }
        }
    }
}

var c = A.B.C()     //实例化
c.sayHello()        //调用方法
```
拓展
```
class com{
    class soonphe{
        func sayHello(){
            println("Hello")
        }
    }
}

extension com.soonphe{
    class Hello{
        func sayHello(){
            println("Hello")
        }   
    }
}

var h = com.soonphe.Hello()     //实例化
h.sayHello()                    //调用方法
```

### swift 混合编程
swift和OC混合编程，可以通过桥接文件实现，桥接文件是一个.h文件，可以在里面引入OC的头文件，然后在swift文件中就可以直接使用OC的类了。

#### 创建桥接文件
1. 创建一个OC文件，命名为xxx.h
2. 在xxx.h文件中引入OC的头文件
3. 在swift文件中使用OC的类


### 日志定位
```
print("hello")    //输出hello
NSLog("hello")    //日志携带时间、文件名、行号
```

### swift异常处理
```
enum MyError:Error{    //定义异常
    case Zero
}
