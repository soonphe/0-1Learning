# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java反射

### 什么是反射或反射用途
通过java语言中的反射机制可以操作字节码文件（可以读和修改字节码文件。）

反射机制的相关类在哪个包下？
java.lang.reflect.*;

### 反射机制相关的重要的类有哪些？
|类|	含义|
|---|---|
|java.lang.Class	|代表整个字节码。代表一个类型，代表整个类。|
|java.lang.reflect.Method	|代表字节码中的方法字节码。代表类中的方法。|
|java.lang.reflect.Constructor	|代表字节码中的构造方法字节码。代表类中的构造方法。|
|java.lang.reflect.Field	|代表字节码中的属性字节码。代表类中的成员变量（静态变量+实例变量）。|

注：必须先获得Class才能获取Method、Constructor、Field。

### 反射获取class
|方式	|备注|
|---|---|
|Class.forName(“完整类名带包名”)	|静态方法|
|对象.getClass()||
|任何类型.class||

注：以上三种方式返回值都是Class类型。

要操作一个类的字节码，需要首先获取到这个类的字节码：
```
/*
三种方式
  第一种：Class c = Class.forName("完整类名带包名");
  第二种：Class c = 对象.getClass();
  第三种：Class c = 任何类型.class;
*/

/*
第一种方式：Class.forName()
   1、静态方法
   2、方法的参数是一个字符串。
   3、字符串需要的是一个完整类名。
   4、完整类名必须带有包名。java.lang包也不能省略。
*/
Class clazz = Class.forName("com.xxx.xxx");
//备注：这个方法的执行会导致类加载，类加载时，静态代码块执行，其它代码一律不执行。

// 第二种方式：java中任何一个对象都有一个方法：getClass()
String a = "abc";
Class c4 = a.getClass(); // c4代表String.class字节码文件；c4代表String类型。
        

// 第三种方式：java语言中任何一种类型，包括基本数据类型，它都有.class属性。
Class i = Integer.class; //i代表Integer类型
Class d = Date.class; // d代表Date类型
Class f = float.class; //f代表float类型

```

通过反射实例化对象：
```
Object obj = clazz.newInstance();
// 重点是：newInstance()调用的是无参构造，必须保证无参构造是存在的！
```

获取方法(包括私有)：
```
Method[] methods = clazz.getDeclaredMethods();
```

调用方法：
```
Class clazz = Class.forName("javase.reflectBean.UserService");
Object obj = clazz.newInstance();
Method method = clazz.getDeclaredMethod("login", String.class);
/*
   四要素：
   method方法
   obj对象
   args 实参
   retValue 返回值
*/
Object resValues = method.invoke(obj, args);

target - 调用方法的目标对象
args – 调用参数（可能为 null）
```

获取属性：
```
Field field = obj.getDeclaredField("no");
```

获取属性的修饰符列表：
```
Modifier.toString(field.getModifiers()));
field.getType().getSimpleName();// 获取属性的类型
field.getName();// 获取属性的名字
```

属性赋值：
```
/*
   虽然使用了反射机制，但是三要素还是缺一不可：
       要素1：obj对象
       要素2：no属性
       要素3：22222值
   注意：反射机制让代码复杂了，但是为了一个“灵活”，这也是值得的。
*/
field.set(obj, 22222);
```

> set()可以访问私有属性嘛？

不可以，需要打破封装，才可以。

打破方式： public void setAccessible(boolean flag)	默认false，设置为true为打破封装

获取构造器：
```
Constructor[] constructors = clazz.getDeclaredConstructors();
```

构造器初始化：
```
Constructor c1 = clazz.getDeclaredConstructor(int.class, String.class, String.class, boolean.class);

// 获取无参数构造方法
Object obj3 = c2.newInstance();
// 调用有参数的构造方法：Constructor类的newInstance方法
Object obj2 = c1.newInstance(321, "lsi", "1999-10-11", true);
```

获取类、方法、属性注解：
```
//第一种
clazz.getDeclaredAnnotation(XxxAnnotation.class);

//第二种，使用AnnotationUtils，实际也是clazz.getDeclaredAnnotation
AnnotationUtils.findAnnotation(clazz, XxxAnnotation.class);
```


Class类方法
```
方法名	备注
public T newInstance()	创建对象
public String getName()	返回完整类名带包名
public String getSimpleName()	返回类名
public Field[] getFields()	返回类中public修饰的属性
public Field[] getDeclaredFields()	返回类中所有的属性
public Field getDeclaredField(String name)	根据属性名name获取指定的属性
public native int getModifiers()	获取属性的修饰符列表,返回的修饰符是一个数字，每个数字是修饰符的代号【一般配合Modifier类的toString(int x)方法使用】
public Method[] getDeclaredMethods()	返回类中所有的实例方法
public Method getDeclaredMethod(String name, Class<?>… parameterTypes)	根据方法名name和方法形参获取指get定方法
public Constructor<?>[] getDeclaredConstructors()	返回类中所有的构造方法
public Constructor getDeclaredConstructor(Class<?>… parameterTypes)	根据方法形参获取指定的构造方法
----	----
public native Class<? super T> getSuperclass()	返回调用类的父类
public Class<?>[] getInterfaces()	返回调用类实现的接口集合
----
public <A extends Annotation> A getDeclaredAnnotation(Class<A> annotationClass);  返回指定类型的注解，如果存在则返回，否则为空。
```

Field类方法
```
方法名	备注
public String getName()	返回属性名
public int getModifiers()	获取属性的修饰符列表,返回的修饰符是一个数字，每个数字是修饰符的代号【一般配合Modifier类的toString(int x)方法使用】
public Class<?> getType()	以Class类型，返回属性类型【一般配合Class类的getSimpleName()方法使用】
public void set(Object obj, Object value)	设置属性值
public Object get(Object obj)	读取属性值
```

Method类方法
```
方法名	备注
public String getName()	返回方法名
public int getModifiers()	获取方法的修饰符列表,返回的修饰符是一个数字，每个数字是修饰符的代号【一般配合Modifier类的toString(int x)方法使用】
public Class<?> getReturnType()	以Class类型，返回方法类型【一般配合Class类的getSimpleName()方法使用】
public Class<?>[] getParameterTypes()	返回方法的修饰符列表（一个方法的参数可能会有多个。）【结果集一般配合Class类的getSimpleName()方法使用】
public Object invoke(Object obj, Object… args)	调用方法
```

Constructor类方法
```
方法名	备注
public String getName()	返回构造方法名
public int getModifiers()	获取构造方法的修饰符列表,返回的修饰符是一个数字，每个数字是修饰符的代号【一般配合Modifier类的toString(int x)方法使用】
public Class<?>[] getParameterTypes()	返回构造方法的修饰符列表（一个方法的参数可能会有多个。）【结果集一般配合Class类的getSimpleName()方法使用】
public T newInstance(Object … initargs)	创建对象【参数为创建对象的数据】
```

### ReflectionUtils 反射工具类
ReflectionUtils.invokeMethod(this.consumeMethod, this.consumeBean, args);：反射调用方法， 实际最终调的也是method.invoke(target, args);

1. doWithMethods类上所有方法回调
   org.springframework.util.ReflectionUtils#doWithMethods(java.lang.Class<?>, org.springframework.util.ReflectionUtils.MethodCallback)
2. findMethod寻找Method对象
3. findField寻找Field对象

示例：
```
//找到Field对象（在缓存中获取对象）
Field field = ReflectionUtils.findField(UserInfo.class, "name");
ReflectionUtils.makeAccessible(field);
//通过反射获取对象
Object value = ReflectionUtils.getField(field, userInfo);
```

### 如何通过反射将字符串转换为类
```java
package org.entity;
 
import java.lang.reflect.InvocationTargetException;
import java.lang.reflect.Method;
 
 
/**
 * 如何通过反射将字符串转换为类
 * */
public class Test2 {
  
	public static void main(String[] args) {
		String user = "org.entity.User";//字符串是该类的全限定名
			try {
				Class clzz = Class.forName(user);
				Object classObj=clzz.newInstance();//将class类转换为对象
				//--------------------反射类调用User中的sayHello()方法-----------------------------
				//注意导入正确的Method包名：
				// import java.lang.reflect.Method;
				//获取该类的所有方法
				Method[] methods = clzz.getMethods();
				//遍历方法
				for(Method m:methods){
					if(m.getName().equals("sayHello")){//找到sayHello这个方法
						try {
							//user类中的invoke方法第一个参数是要调用的类
							//第二个是要传入的参数
							m.invoke(classObj, "hello world");
						} catch (IllegalArgumentException e) {
							e.printStackTrace();
						} catch (InvocationTargetException e) {
							e.printStackTrace();
						}
					}
				}
			} catch (ClassNotFoundException e) {
				e.printStackTrace();
			} catch (InstantiationException e) {
				e.printStackTrace();
			} catch (IllegalAccessException e) {
				e.printStackTrace();
			}
		
	}
 
}
```


### 反射为什么性能慢，怎么优化它呢
反射一个类，调用其方法，调用十万次，能增加多少耗时，别动不动就说反射效率低，要看调用的频率数量级

1. 反射不能被aot优化
2. 反射和直接调用不一样，需要扫描函数列表，绕了好多弯最后才调到原函数
3. 反射的调用函数需要拆包解包

尽量规避反射，如果用也只在启动时使用



