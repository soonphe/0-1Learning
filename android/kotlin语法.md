# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")



## kotlin语法

### 数据类型

### 变量和常量
val a: Int = 1  // 立即赋值
val b = 2   // 自动推断出 `Int` 类型
val c: Int  // 如果没有初始值类型不能省略
c = 3       // 明确赋值

### 函数
带有两个 Int 参数、返回 Int 的函数：

fun sum(a: Int, b: Int): Int {
    return a + b
}

### 表达式
条件表达式
fun maxOf(a: Int, b: Int): Int {
    if (a > b) {
        return a
    } else {
        return b
    }
}

for 循环
val items = listOf("apple", "banana", "kiwifruit")
for (item in items) {
    println(item)
}

### 使用区间（range）
val x = 10
val y = 9
if (x in 1..y+1) {
    println("fits in range")
}

### 创建对象
data class Customer(val name: String, val email: String)

创建单例
object Resource {
    val name = "Name"
}

### If not null and else 缩写
val files = File("Test").listFiles()
println(files?.size ?: "empty")

val values = ……
val email = values["email"] ?: throw IllegalStateException("Email is missing!")

val value = ……
value?.let {
    …… // 代码会执行到此处, 假如data不为null
}

val value = ……
val mapped = value?.let { transformValue(it) } ?: defaultValue 
// 如果该值或其转换结果为空，那么返回 defaultValue。



