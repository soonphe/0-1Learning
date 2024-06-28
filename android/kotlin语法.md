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

### Kotlin 中的常用注解 @JvmOverloads、@JvmStatic、@JvmField、@JvmName、@JvmMultifileClass
1、@JvmOverloads
为了解决 Java 不能重载 kotlin 有默认参数的方法

Kotlin中代码：
```
@JvmOverloads
fun show(name: String, age: Int = 20) {
println("name:$name, age:$age")
}

fun main() {
show("王五")
}
```
Java 中调用：会报错，Expected 2 arguments...
```
PersonFileKt.show("李四");
```
所以需要在 Kotlin 方法上添加 @JvmOverloads：
```
public class Test {
public static void main(String[] args) {
    PersonFileKt.show("李四");
}
}
```
自定义View的构造方法中：
```
class CustomView @JvmOverloads constructor(
private val context: Context,
attrs: AttributeSet? = null,
defStyleAttr: Int = 0
) : View(mContext, attrs, defStyleAttr)
```
就相当于写了三个构造方法：
```
public class CustomView extends View{

    public CustomView (Context context) {
        this(context, null);
    }

    public CustomView (Context context, AttributeSet attrs) {
        this(context, attrs, 0);
    }

    public CustomView (Context context, AttributeSet attrs, int defStyleAttr) {
        super(context, attrs, defStyleAttr);
    }
}
```
2、@JvmField
可以给 Kotlin 属性添加 @JvmField 注解，暴露它的支持字段给 Java 调用者，从而避免使用 getter 方法。
```
// Kotlin
class Spellbook {
@JvmField
val spells = listOf("Magic Ms. L", "Lay on Hans")
}

// Java
Spellbook spellbook = new Spellbook();
for (String spell : spellbook.spells) {
System.out.println(spell);
}
```
@JvmField 注解还能用来以静态方式提供伴生对象里定义的值。
```
// Kotlin
class Spellbook {
...
companion object {
val MAX_SPELL_COUNT = 10
}
}

// Java
Spellbook.Companion.getMAX_SPELL_COUNT();


// Kotlin
class Spellbook {
...
companion object {
@JvmField
val MAX_SPELL_COUNT = 10
}
}

// Java
System.out.println(Spellbook.MAX_SPELL_COUNT);
```
3、@JvmStatic
@JvmStatic 注解的作用类似于 @JvmField，允许直接调用伴生对象里的函数。
```
// Kotlin
class Spellbook {
@JvmField
val spells = listOf("Magic Ms. L", "Lay on Hans")

    companion object {
        @JvmField
        val MAX_SPELL_COUNT = 10

        @JvmStatic
        fun getSpellbookGreeting() = println("I am the Great Grimoire!")
    }
}

// Java
Spellbook.getSpellbookGreeting();
```
4、@JvmName
为了解决 Java 调用 Kotlin 文件中的字段或者方法，需要在原来 Kotlin 文件名基础后面加 Kt，加上 @JvmName 就可以去掉后面的 Kt，直接文件名调用了。
```
@file:JvmName("Person")
```
```
// 文件名 PersonFileKt
package annotations


fun getNameValueInfo(str: String) = println(str)
fun main() {

}
```
java中调用：
```
public class Test {
    public static void main(String[] args) {
        PersonFileKt.getNameValueInfo("张三");
    }
}
```
@file:JvmName("Person") 必须写在包名的前面，否则无法起作用
```
@file:JvmName("Person")
package annotations


fun getNameValueInfo(str: String) = println(str)
fun main() {

}
```
Java 中调用，就可以直接使用文件名 . 调用了：
```
public class Test {
    public static void main(String[] args) {
        Person.getNameValueInfo("张三");
    }
}
```
5、@JvmMultifileClass
如果我们在同一包名下面的两个 kt 文件中，用 @file:JvmName("Person") 注解了一样的 class 名字，就会报错，需要添加 @JvmMultifileClass 解决
```
// PersonFile.kt
@file:JvmName("Person")
@file:JvmMultifileClass
package annotations

fun getNameValueInfo(str: String) = println(str)
```
```
// PersonFile2.kt
@file:JvmName("Person")
@file:JvmMultifileClass

package annotations

fun getNameValueInfo2(str: String) = println(str)
```
Java 中调用：
```
public class Test {
    public static void main(String[] args) {
        Person.getNameValueInfo("张三");
        Person.getNameValueInfo2("李四");
    }
}
```





