# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")

### jdk包结构（linux 1.8版本）
- **bin目录**: JDK开发工具的可执行文件
- **lib目录**: 开发工具使用的归档包文件
- **jre**:  Java 运行时环境的根目录，包含Java虚拟机，运行时的类包和Java应用启动器， 但不包含开发环境中的开发工具
- demo: 含有源代码的程序示例
- include: 包含C语言头文件,支持Java本地接口与Java虚拟机调试程序接口的本地编程技术

### jdk中jar包结构功能树（linux 1.8版本）
- rt.jar：Java基础类库，也就是Java doc里面看到的所有的类的class文件。
- tools.jar：是系统用来编译一个类的时候用到的，即执行javac的时候用到。
- dt.jar：dt.jar是关于运行环境的类库，主要是swing包。
- i18n.jar: 字符转换类及其它与国际化和本地化有关的类。