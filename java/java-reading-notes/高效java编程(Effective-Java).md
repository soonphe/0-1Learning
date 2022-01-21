# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 高效java编程(Effective-Java)

### 1.创建和销毁对象（Creating and Destroying Objects）

#### Item 1:   Consider static factory methods instead of constructors
项目1：考虑静态工厂方法，而不考虑构造函数

#### Item 2:   Consider a builder when faced with many constructor parameters
项目2：考虑构造函数时遇到许多构造函数参数项

#### Item 3:   Enforce the singleton property with a private constructor or an enum type
项3：用私有构造函数或单元格强制执行SuntLon属性枚举类型

#### Item 4:   Enforce noninstantiability with a private constructor
第4项：使用私有构造函数强制不可持久性

#### Item 5:   Prefer dependency injection to hardwiring resources
第5项：偏好依赖注入而非硬连线资源

#### Item 6:   Avoid creating unnecessary objects
项目6：避免创建不必要的对象

#### Item 7:   Eliminate obsolete object references
项目7：消除过时的对象引用

#### Item 8:   Avoid finalizers and cleaners
第8项：避免使用终结器和清洁器

#### Item 9:   Prefer try-with-resources to try-finally
第9项：宁愿使用资源进行尝试，也不愿最终尝试



### 2.所有对象共有方法（Methods Common to All Objects） 
#### Item 10: Obey the general contract when overriding equals
第10项：覆盖等于时遵守总合同

#### Item 11: Always override hashCode when you override equals
第11项：覆盖等于时始终覆盖哈希代码

#### Item 12: Always override toString
第12项：始终覆盖toString

#### Item 13: Override clone judiciously
第13项：明智地覆盖克隆

#### Item 14: Consider implementing Comparable
项目14：考虑实施可比性



### 3.类和接口（Classes and Interfaces）
#### Item 15: Minimize the accessibility of classes and members 
第15项：最小化类和成员的可访问性

#### Item 16: In public classes, use accessor methods, not public fields
第16项：在公共类中，使用访问器方法，而不是公共字段
#### Item 17: Minimize mutability
第17项：最小化可变性
#### Item 18: Favor composition over inheritance
第18项：偏爱组合而非继承

#### Item 19: Design and document for inheritance or else prohibit it
第19项：继承或禁止的设计和文档
#### Item 20: Prefer interfaces to abstract classes
第20项：更喜欢接口而不是抽象类

#### Item 21: Design interfaces for posterity
项目21：为后代设计接口
#### Item 22: Use interfaces only to define types
第22项：仅使用接口定义类型
#### Item 23: Prefer class hierarchies to tagged classes
第23项：与标记类相比，更喜欢类层次结构
#### Item 24: Favor static member classes over nonstatic
第24项：支持静态成员类而不是非静态成员类
#### Item 25: Limit source files to a single top-level class
第25项：将源文件限制为单个顶级类



### 4.泛型（Generics）
#### Item 26: Don’t use raw types
第26项：不要使用原始类型
#### Item 27: Eliminate unchecked warnings
项目27：消除未检查的警告
#### Item 28: Prefer lists to arrays
第28项：首选列表而非数组
#### Item 29: Favor generic types
项目29：通用类型
#### Item 30: Favor generic methods
项目30：支持通用方法
#### Item 31: Use bounded wildcards to increase API flexibility
第31项：使用有界通配符提高API灵活性
#### Item 32: Combine generics and varargs judiciously
第32项：明智地组合泛型和变量
#### Item 33: Consider typesafe heterogeneous containers
项目33：考虑类型化异构容器



### 5.枚举和注解（Enums and Annotations）
#### Item 34: Use enums instead of int constants
第34项：使用枚举而不是int常量
#### Item 35: Use instance fields instead of ordinals
第35项：使用实例字段而不是序数
#### Item 36: Use EnumSet instead of bit fields
第36项：使用枚举集而不是位字段

#### Item 37: Use EnumMap instead of ordinal indexing
第37项：使用EnumMap而不是顺序索引
#### Item 38: Emulate extensible enums with interfaces
第38项：使用接口模拟可扩展枚举
#### Item 39: Prefer annotations to naming patterns
第39项：首选注释而不是命名模式

#### Item 40: Consistently use the Override annotation
项目40：一致使用覆盖注释

#### Item 41: Use marker interfaces to define types
项目41：使用标记接口定义类型



### 6.函数式编程（Lambdas and Streams）
#### Item 42: Prefer lambdas to anonymous classes
第42项：更喜欢lambda而不是匿名类

#### Item 43: Prefer method references to lambdas
第43项：优先于lambdas的方法引用


#### Item 44: Favor the use of standard functional interfaces
第44项：支持使用标准功能接口


#### Item 45: Use streams judiciously
第45项：明智地使用流
#### Item 46: Prefer side-effect-free functions in streams
第46项：优先选择流中无副作用的函数

#### Item 47: Prefer Collection to Stream as a return type
第47项：优先选择集合而不是作为返回类型的流

#### Item 48: Use caution when making streams parallel
第48项：使流并行时要小心



### 7.方法（Methods）
#### Item 49: Check parameters for validity
第49项：检查参数的有效性

#### Item 50: Make defensive copies when needed
第50项：需要时制作防御性副本
#### Item 51: Design method signatures carefully
项目51：设计方法
#### Item 52: Use overloading judiciously
第52项：明智地使用重载
#### Item 53: Use varargs judiciously
第53项：明智地使用varargs
#### Item 54: Return empty collections or arrays, not nulls
第54项：返回空集合或数组，而不是null
#### Item 55: Return optionals judiciously
第55项：明智地返回选项
#### Item 56: Write doc comments for all exposed API elements
第56项：为所有公开的API元素编写文档注释



### 8.通用程序设计（General Programming）
#### Item 57: Minimize the scope of local variables
项目57：最小化局部变量的范围
#### Item 58: Prefer for-each loops to traditional for loops
第58项：与传统for循环相比，更喜欢for each循环


#### Item 59: Know and use the libraries
项目59：了解和使用图书馆


#### Item 60: Avoid float and double if exact answers are required
第60项：如果需要精确答案，请避免使用浮点和双精度


#### Item 61: Prefer primitive types to boxed primitives
项61：与装箱原语相比，更喜欢原语类型

#### Item 62: Avoid strings where other types are more appropriate
第62项：避免使用其他类型更合适的字符串

#### Item 63: Beware the performance of string concatenation
第63项：注意字符串连接的性能

#### Item 64: Refer to objects by their interfaces
第64项：通过对象的接口引用对象


#### Item 65: Prefer interfaces to reflection
第65项：选择接口而不是反射


#### Item 66: Use native methods judiciously
项目66：明智地使用本机方法


#### Item 67: Optimize judiciously
项目67：明智地优化


#### Item 68: Adhere to generally accepted naming conventions
项目68：遵守公认的命名惯例



###  9.异常（Exceptions）
#### Item 69: Use exceptions only for exceptional conditions
第69项：仅在特殊情况下使用例外

#### Item 70: Use checked exceptions for recoverable conditions and runtime exceptions for programming errors
第70项：对可恢复条件和运行时使用已检查的异常编程错误的例外情况

#### Item 71: Avoid unnecessary use of checked exceptions
项目71：避免不必要地使用已检查异常

#### Item 72: Favor the use of standard exceptions
第72项：赞成使用标准例外

#### Item 73: Throw exceptions appropriate to the abstraction
第73项：抛出适用于抽象的异常

#### Item 74: Document all exceptions thrown by each method
第74项：记录每个方法引发的所有异常

#### Item 75: Include failure-capture information in detail messages
第75项：在详细信息中包括故障捕获信息

#### Item 76: Strive for failure atomicity
项目76：争取失败原子性

#### Item 77: Don’t ignore exceptions
第77项：不要忽略例外情况



###  10.并发（Concurrency）
#### Item 78: Synchronize access to shared mutable data
第78项：同步访问共享可变数据

#### Item 79: Avoid excessive synchronization
项目79：避免过度同步

#### Item 80: Prefer executors, tasks, and streams to threads
第80项：与线程相比，更喜欢执行器、任务和流

#### Item 81: Prefer concurrency utilities to wait and notify
第81项：希望并发实用程序等待并通知

#### Item 82: Document thread safety
第82项：文档线程安全

#### Item 83: Use lazy initialization judiciously
第83项：明智地使用惰性初始化

#### Item 84: Don’t depend on the thread scheduler
第84项：不依赖于线程调度程序



###  11.序列化（Serialization）
#### Item 85: Prefer alternatives to Java serialization
第85项：首选Java序列化的替代方案

#### Item 86: Implement Serializable with great caution
第86项：非常小心地实现可序列化

#### Item 87: Consider using a custom serialized form
项目87：考虑使用自定义序列化窗体

#### Item 88: Write readObject methods defensively
项目88：防御性地编写readObject方法

#### Item 89: For instance control, prefer enum types to readResolve
第89项：对于实例控件，首选枚举类型而不是readResolve

#### Item 90: Consider serialization proxies instead of serialized instances
项目90：考虑序列化代理而不是序列化实例




