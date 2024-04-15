# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## IDEA快捷键(Mac)

> Mac按键说明（对应Windows键盘）
- control(⌃) = Ctrl
- command(⌘) = win
- option(⌥) = Alt
- shift(⇧) = shift

### Top 10
#### 1 代码提示：Ctrl + Space，按类型提示：Ctrl + Shift + Space
    说明：Mac版IDEA本身就有代码提示，如果没有，去掉右下角Power Save Mode勾选 
#### 2 自我修复：Option + Enter：显示意向动作和快速修复代码、导入包
    Option + /： 循环扩展
#### 3 自动完成：Command + Shift + Enter（补全末尾字符、括号、分号）
#### 4 重构(refactor)一切：Ctrl + T
    Shift+F6：改名（文件名和类名、方法名、变量名）
    Command+Option+V： 则是提取变量，
    Command+Option+F：则是提取为属性字段
#### 5 代码生成：Command + J查看所有模板（
    常用的有fori：生成循环
    sout：System.out
    psvm：main方法
    自定义模板等
#### 6 后缀自动补全(Postfix Completion)：
    定义一个变量时，使用$expr$.var + Enter——等于上面Command+Option+V
    判断一个对象不为null，使用$expr$.nn + Enter
    判断一个对象为null，使用$expr$.null + Enter
    新建一个对象，使用$expr$.new + Enter——可配合Command+Option+V使用
    .try：try catch包裹
    .return：返回对象
    .for：生成循环
#### 7 编辑
    Command+N 新增任何类型的文件
    Command+X 删除行
    Command+D 复制行
    Command + .：折叠行
    Command + 左、右方向键：跳转当前行最左、右侧（可搭配shift变为选择功能）
    Option + 左、右箭头：按单词、短句跳转（可搭配shift变为选择功能）
    Option + 上、下箭头：连续选中/减少代码块
    Ctrl+shift+数字num:定义书签 Ctrl+num跳转书签
    Command+Option+L：格式化代码

#### 8 查找打开
    Shift + Shift(即按两次)：在项目文件内或所有地方搜任何（类、文件、符号、方法、Git等）
    Command + F：当前窗口中查找
    Command + shift + F：全工程查找
    Command + 7：打开文件结构

#### 9 Generate模块：Control + Enter（生成构造器、getter、setter方法等，等于Command + N）

#### 10 前后导航光标停留过的地方：Command + ALT + left/right    
    跳转到上次编辑的地方：Command + Shift + Backspace

### IDEA快捷键总结
Command + 逗号(,)：打开当前应用的偏好设置
Command + 分号(;)：打开项目结构project structure
Command + N：新建文件或打开当前文件Generate选项
Command + E：最近更改的代码
Command + /：注释//和释放注释
Command + F：查找
Command + R：在当前窗口替换文本
Command + D：复制当前行
Command + B：快速打开光标处的引用及被引用类或方法(Command + 鼠标左键也可以)
Command + O：本项目内搜任何（类）——拓展：Command + Shift + O：本项目内搜任何（文件）
Command + 左方向键：当前行最左侧
Command + 右方向键：当前行最右侧
Command + 下方向键：查看源码（等同于Command + B）

Command + Shift + 上下键：上下移动当前代码
Command + Shift + 左右键：全选当前所在行 当前光标到最左侧/最右侧的代码

Command + shift + U 大小写转化
Command + SHIFT + R：在全局/指定窗口替换文本
Command + Shift + F：全局查找
Command + SHIFT + T：在方法上点击生成测试此方法代码

Command + Option + /：注释/*-------*/
Command + Option + L：格式化代码
Command + Option + T  生成try catch、if/else、while、do while等

Command + Option + 左右键：光标之前/之后停留的位置
Command + Option + U：打开当前包或者类图表（等同于在包或类上右键点击Diagrams）
Command + Option + F7  全局文件搜索 函数或者变量或者类的所有引用到的地方

Control + Option + O：优化包
Control + Option + R：Run
Control + Option + D：Debug

Command + ~：切换项目窗口
Command + 1：左窗口显示当前包路径，文件路径
Command + 2：左窗口显示favorites，我的最爱
Command + 3：底部显示Find
Command + 4：底部显示Run
Command + 5：底部显示Debug
Command + 6：底部显示Problems
Command + 7：左窗口显示当前文件的结构、即类中所有方法
Command + 8：底部显示Services
Command + 9：底部显示Git
Command + -：列表全部缩小
Command + =：列表全部展开

Command + F9：项目编译
Command + Shift + F9：项目重新编译
Command + F12：浮动显示当前文件的结构、即类中所有方法

Option + F1：IDEA快速定位文件
Option + F7：查看一个Java类、方法或变量的直接使用情况。当前文件搜索 函数或者变量或者类的所有引用到的地方
Option + F12：底部显示terminal

Control + I：实现方法
Control + O：重写方法

F2：高亮错误或警告快速定位
F5：复制文件
F6：移动文件
Shift + F6：重命名文件/文件夹

Navigate | Call Hierarchy：查看Java方法结构树（caller和callee两个方向）

### 调试相关
Option + F10：选择可调试点
F8:进入下一步，如果当前断点是一个方法，则不进入当前方法体内
F7:进入下一步，如果当前行断点是一个方法，则进入当前方法体内，如果该方法体还有方法，则不会进入该内嵌的方法中

**断点操作**
1. 循环的时候从某处断点
右击断点，在condition中输入i=40，表示在i=40的时候才停下。
2. 回到上一断点：Drop Frame
3. 断点赋值：F2
4. 断点查看对象：添加执行代码Watches：Watches —— New Watches












