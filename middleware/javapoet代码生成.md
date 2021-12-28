# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## JavaPoet

### JavaPoet的基本介绍
（1）JavaPoet是一款可以自动生成Java文件的第三方依赖
（2）简洁易懂的API，上手快
（3）让繁杂、重复的Java文件，自动化生成，提高工作效率，简化流程

### JavaPoet的常用类
1. TypeSpec————用于生成类、接口、枚举对象的类 
2. MethodSpec————用于生成方法对象的类 
3. ParameterSpec————用于生成参数对象的类 
4. AnnotationSpec————用于生成注解对象的类 
5. FieldSpec————用于配置生成成员变量的类 
6. ClassName————通过包名和类名生成的对象，在JavaPoet中相当于为其指定Class 
7. ParameterizedTypeName————通过MainClass和IncludeClass生成包含泛型的Class 
8. JavaFile————控制生成的Java文件的输出的类


### JavaPoet的常用方法
设置修饰关键字
```
addModifiers(Modifier... modifiers)
```
Modifier是一个枚举对象，枚举值为修饰关键字Public、Protected、Private、Static、Final等等。
所有在JavaPoet创建的对象都必须设置修饰符(包括方法、类、接口、枚举、参数、变量)。


### 设置注解对象
```
addAnnotation（AnnotationSpec annotationSpec）
addAnnotation（ClassName annotation）
addAnnotation(Class<?> annotation)
```
该方法即为类或方法或参数设置注解，参数即可以是AnnotationSpec，也可以是ClassName，还可以直接传递Class对象。
一般情况下，包含复杂属性的注解一般用AnnotationSpec，如果单纯添加基本注解，无其他附加属性可以直接使用ClassName或者Class即可。


### 设置注释
```
addJavadoc（CodeBlock block）
addJavadoc(String format, Object... args)
```
在编写类、方法、成员变量时，可以通过addJavadoc来设置注释，可以直接传入String对象，或者传入CodeBlock（代码块）。


### JavaPoet生成类、接口、枚举对象
在JavaPoet中生成类、接口、枚举，必须得通过TypeSpec生成，而classBuilder、interfaceBuilder、enumBuilder便是创建其关键的方法：
```
创建类：
TypeSpec.classBuilder("类名“) 
TypeSpec.classBuilder(ClassName className)

创建接口：
TypeSpec.interfaceBuilder("接口名称")
TypeSpec.interfaceBuilder(ClassName className)

创建枚举：
TypeSpec.enumBuilder("枚举名称")
TypeSpec.enumBuilder(ClassName className)

继承类：
.superclass(ClassName className)

实现接口
.addSuperinterface(ClassName className)

继承存在泛型的父类
ParameterizedTypeName get(ClassName rawType, TypeName... typeArguments)

添加方法
addMethod(MethodSpec methodSpec)

添加枚举
addEnumConstan(String enumValue)

```

### JavaPoet生成成员变量
JavaPoet生成成员变量是通过FieldSpec的build方法生成.
```
只要传入TypeName(Class)、name（名称）、Modifier（修饰符），就可以生成一个基本的成员变量。
builder(TypeName type, String name, Modifier... modifiers)

注解
addAnnotation(TypeName name) 

修饰符
addModifiers(Modifier ...modifier)

注释
addJavadoc(String format, Object... args)

实例化
initializer(String format, Object... args)

initializer方法中的内容就是“=”后面的内容，下面看下具体的代码实现，上面的成员变量：
  ClassName activity = ClassName.get("android.app", "Activity");
  FieldSpec spec = FieldSpec.builder(activity, "mActivity")
                .addModifiers(Modifier.PUBLIC)
                .initializer("new $T", activity)
                .build();
```

### JavaPoet生成方法
JavaPoet生成方法分为两种，第一种是构造方法，另一种为常规的方法。
```
构造方法
MethodSpec.constructorBuilder()

常规方法
MethodSpec.methodBuilder(String name)

方法参数
addParameter(ParameterSpec parameterSpec)

返回值
returns(TypeName returnType)

方法体
在JavaPoet中，设置方法体内容有两个方法，分别是addCode和addStatement：
addCode()
addStatement()

方法体模板
在JavaPoet中，设置方法体使用模板是比较常见的，因为addCode和addStatement方法都存在这样的一个重载:
addCode(String format, Object... args)
addStatement(String format, Object... args)

在JavaPoet中，format中存在三种特定的占位符：$T、$N、$S
$T
$T 在JavaPoet代指的是TypeName，该模板主要将Class抽象出来，用传入的TypeName指向的Class来代替。
ClassName bundle = ClassName.get("android.os", "Bundle");
addStatement("$T bundle = new $T()",bundle)
上述添加的代码内容为：
Bundle bundle = new Bundle();

$N
$N在JavaPoet中代指的是一个名称，例如调用的方法名称，变量名称，这一类存在意思的名称
addStatement("data.$N()",toString)
上述代码添加的内容：
data.toString();

$S
$S在JavaPoet中就和String.format中%s一样,字符串的模板,将指定的字符串替换到$S的地方，需要注意的是替换后的内容，默认自带了双引号，如果不需要双引号包裹，需要使用$L.
.addStatement("return $S", “name”)
即将"name"字符串代替到$S的位置上.

抛出异常
.addException(TypeName name)
```

### JavaPoet生成方法参数
JavaPoet生成有参方法时,需要填充参数,而生成参数则需要通过ParameterSpec这个类。
```
addParameter(ParameterSpec parameterSpec)

初始化ParameterSpec
参数的构成包括:参数的类型(Class)、参数的名称（name）、修饰符（modifiers）、注解（Annotation）
ParameterSpec.builder(TypeName type, String name, Modifier... modifiers)

添加修饰符
.addModifiers(Modifier modifier)

添加注解
addAnnotation(TypeName name) 
```

### JavaPoet生成注解
在JavaPoet创建类、成员变量、方法参数、方法，都会用到注解。

```
如果使用不包含属性的注解可以直接通过
.addAnnotation(TypeName name)

如果使用的注解包含属性，并且不止一个时，这时候就需要生成AnnotationSpec来解决，下面简单了解下AnnotationSpec。

初始化AnnotationSpec
AnnotationSpec.builder(ClassName type)

设置属性
addMember(String name, String format, Object... args)
```

### JavaPoet如何生成代码
JavaPoet中负责生成的类是JavaFile
```
JavaFile.builder(String packageName, TypeSpec typeSpec)
JavaFile通过向build方法传入PackageName（Java文件的包名）、TypeSpec（生成的内容）生成。

打印结果
javaFile.writeTo(System.out)
生成的内容会输出到控制台中

生成文件
javaFile.writeTo（File file）
```




