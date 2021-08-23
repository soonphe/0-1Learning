# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## IDEA快捷键(Mac)

### 说明
mac按键说明（对应win键盘）
control(⌃) = Ctrl
command(⌘) = win
option(⌥) = Alt
shift(⇧) = shift

### Top 10
#1 代码提示：Ctrl + Space，按类型提示：Ctrl + Shift + Space
    说明：Mac版IDEA本身就有代码提示，如果没有，去掉右下角Power Save  Mode勾选 
#2 自我修复：Option + Enter：显示意向动作和快速修复代码、导入包
    Option + /： 循环扩展
#3 自动完成：Command + Shift + Enter（补全末尾字符、括号、分号）
#4 重构(refactor)一切：Ctrl + T
    Shift+F6：改名（文件名和类名、方法名、变量名）
    Command+Option+V： 则是提取变量，
    Command+Option+F：则是提取为属性字段
#5 代码生成：Command + J查看所有模板（
    常用的有fori：生成循环
    sout：System.out
    psvm：main方法
#6 后缀自动补全(Postfix Completion)：
    定义一个变量时，使用$expr$.var + Enter——等于上面Command+Option+V
    判断一个对象不为null，使用$expr$.nn + Enter
    判断一个对象为null，使用$expr$.null + Enter
    新建一个对象，使用$expr$.new + Enter——可配合Command+Option+V使用
    .try：try catch包裹
    .return：返回对象
    .for：生成循环
#7 编辑
Command+N 新增任何类型的文件
Command+X 删除行
Command+D 复制行
Command + .：折叠行
Command + 左、右方向键：跳转当前行最左、右侧（可搭配shift变为选择功能）
Option + 左、右箭头：按单词、短句跳转（可搭配shift变为选择功能）
Option + 上、下箭头：连续选中/减少代码块
Ctrl+shift+num:定义书签 Ctrl+num跳转书签
Command+Option+L：格式化代码

#8 查找打开
shift+shift：搜任何（类、资源、配置项、方法等）
Command+F：当前窗口中查找
Command+shift+F：全工程查找
Command+7：打开文件结构

#9 Generate模块：Control + Enter（生成构造器、getter、setter方法等）

#10 前后导航编辑过的地方：Command + ALT + left/right    
    跳转到上次编辑的地方：Command + Shift + Backspace   

### IDEA快捷键
Command + 逗号(,)：打开当前应用的偏好设置
Command + 分号(;)：打开项目结构project structure
Command + N：新建文件或打开当前文件Generate选项
Command + E：最近更改的代码

command + /：注释//和释放注释
command + Option + /：注释/*-------*/
command + Option + L：格式化代码
Command + Option + O：优化包
Command + Option + T  生成try catch、if/else。。。
Command + SHIFT + T：在方法上点击生成测试此方法代码

Command + F：查找
Command + Shift + N：全局查找
Shift Shift(按两次)：在项目的所有目录全局搜索类、文件等
Command + R：在当前窗口替换文本  
Command + SHIFT + R：在全局/指定窗口替换文本  

Command + D 复制当前行 
Command + O 重写方法  
Command + I 实现方法
Command + shift + U 大小写转化  

Command + 左方向键：当前行最左侧
Command + 右方向键：当前行最右侧
Command + 下方向键：查看源码
Command + Shift + 上下键：上下移动当前代码
Command + Shift + 左右键：全选当前所在行 当前光标到最左侧/最右侧的代码
Command + option + 左右键：光标之前/之后停留的位置

Option + 7：靠左窗口显示当前文件的结构、即类中所有方法
Command + F12：浮动显示当前文件的结构
Option + F7：当前文件搜索 函数或者变量或者类的所有引用到的地方
Command + Option + F7  全局文件搜索 函数或者变量或者类的所有引用到的地方

Command + B 快速打开光标处的类或方法(Command + 鼠标左键也可以)
Command + ALT + B  找所有的子类  
Command + SHIFT + B  找变量的类  

Option + Shift + | 打开所有文件最后编辑日期及大小

### 调试相关
F2：高亮错误或警告快速定位
Control + Option + R：Run
Control + Option + D：Debug
Option + F10：选择可调试点
F8:进入下一步，如果当前断点是一个方法，则不进入当前方法体内
F7:进入下一步，如果当前行断点是一个方法，则进入当前方法体内，如果该方法体还有方法，则不会进入该内嵌的方法中

### 断点操作
1.循环的时候从某处断点
右击断点，在condition中输入i=40，表示在i=40的时候才停下。
2.回到上一断点：Drop Frame
3.断点赋值：F2
4.断点查看对象：添加执行代码Watches：Watches —— New Watches












