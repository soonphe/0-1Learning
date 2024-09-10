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

### Kotlin 特殊符号的用法：双感叹号!!，问号?，双冒号::

- 双问号??用法
   在Kotlin中!!跟?都是用于判断空参数异常的。?.意思是这个参数可以为空,并且程序继续运行下去，!!.的意思是这个参数如果为空,就抛出异常。
```
在Kotlin中

val nullClass: NullClass? = null
nullClass?.nullFun()

翻译成Java

NullClass nullClass = null;
        
if (nullClass!=null) {
    ullClass.nullFun();
}
```
在一开始的时候我们声明了一个类,并且在类名后面加了一个? 意思就是这个类可以为空,然后在下面用到这个类里面的一个方法时又加了一个问号,意思就是,当程序运行到这一行时,如果这个参数为空,就跳过这一行,程序继续执行下去。
所以?.的用法就是相当于Java里的if()判断null。
一般?.的用法是：在新建一个参数的类名后面加一个? 表示这个参数可以为空。还有就是在用到这个参数的时候后面加? 表示空参数就跳过并且程序继续执行。

- 双感叹号!!只用于用到这个参数的时候在后面加!!,表示空参数就抛出异常。
```
在Kotlin中

val nullClass: NullClass?=null
nullClass!!.nullFun()
翻译成Java

NullClass nullClass = null;
        
if (nullClass!=null) {
    nullClass.nullFun();
}else {
    throw new NullPointerException();
}
```
在第二行参数后面加个!!,意思就是当程序执行到这行,判断这个参数如果是空参数,就抛出异常。

所以!!.的用法就是相当于Java里的if()else()判断null。

- 双冒号::
   Kotlin 中双冒号操作符表示把一个方法当做一个参数，传递到另一个方法中进行使用，通俗的来讲就是引用一个方法。先来看一下例子：
```
fun main(args: Array<String>) {
    println(lock("param1", "param2", ::getResult))
}

/**
 * @param str1 参数1
 * @param str2 参数2
 */
fun getResult(str1: String, str2: String): String = "result is {$str1 , $str2}"

/**
 * @param p1 参数1
 * @param p2 参数2
 * @param method 方法名称
 */
fun lock(p1: String, p2: String, method: (str1: String, str2: String) -> String): String {
    return method(p1, p2)
}
```


### kotlin中val和var
- var：kotlin我们可以在变量前使用var关键字来替换变量的具体类型。
- val：与var相似的一个关键字是val，其用来声明常量，赋值后不能再进行修改。







