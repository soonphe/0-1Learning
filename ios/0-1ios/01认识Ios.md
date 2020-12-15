# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")


## 认识ios

1976创建
1984 麦金塔其

### 开发工具
xcode

快捷键：
shift+command+N 新建一个工程
Shift +Command +L(M)	唤出控件拖动栏
command+R 运行工程
esc 代码提示
control+option+command+ enter  找到Assitant

帮助文档：
shift + command +0


模拟器:
command+shift+H 回到主界面

Tab ：接受代码提示
Esc ：显示代码提示菜单
Command + Shift + K ： 清除工程 
Command + Control  + ↑‖↓‖←‖→ ：程序中.h 和 .m文件间的快速切换
Command + option + enter  ： 同时显示2个页面
Command + i ：  代码对齐
Command + control + e  ： 修改变量名称的快捷键
Command + 鼠标左键单击（跳转到指定的方法中）/右键—>jump to definition

一次性修改一个scope里的变量名：
点击该变量，出现下划虚线，然后command+control+E激活所有相同变量，然后进行修改。
删除一个词：option+delete
删除一句话：command+delete

模拟器的快捷键
(1)command+shift+H 相当于iPhone的home键
(2)command+S 截取模拟器屏幕


1. 文件

Command + N: 新文件
Command + SHIFT + N: 新项目
Command + O: 打开
Command + S: 保存
Command + SHIFT + S: 另存为
Command + W: 关闭窗口
Command + SHIFT + W: 关闭文件

2. 编辑

Command + [: 左缩进
Command + ]: 右缩进
Command + CTRL + LEFT: 折叠
Command + CTRL + RIGHT: 取消折叠
Command + CTRL + TOP: 折叠全部函数
Command + CTRL + BOTTOM: 取消全部函数折叠
CTRL + U: 取消全部折叠
Command + D: 添加书签
Command + /: 注释或取消注释

3. 调试

Command + \: 设置或取消断点
Command + Option + \: 允许或禁用当前断点
Command + Option + B: 查看全部断点
Command + RETURN: 编译并运行（根据设置决定是否启用断点）
Command + R: 编译并运行（不触发断点）
Command + Y: 编译并调试（触发断点）
Command + SHIFT + RETURN: 终止运行或调试

4. 窗体

Command + Shift + B: 编译窗口
Command + Shift + Y: 调试代码窗口
Command + Shift + R: 调试控制台
Command + Shift + E: 主编辑窗口调整

5. 帮助

Command + Option + ?: 开发手册
Command + CTRL + ?: 快速帮助
Command + Shift + E ：扩展编辑器
Command + [ ：左移代码块
Command + ] ：右移代码块

Ctrl + . （句点）：循环浏览代码提示
Shift + Ctrl + . （句点）：反向循环浏览代码提示
Ctrl + / ：移动到代码提示中的下一个占位符
Command + Ctrl + S ：创建快照
Ctrl + F ：前移光标
Ctrl + B ：后移光标
Ctrl + P ：移动光标到上一行
Ctrl + N：移动光标到下一行
Ctrl + A : 移动光标到本行行首
Ctrl + E : 移动光标到本行行尾 
Ctrl + T ：交换光标左右两边的字符
Ctrl + D：删除光标右边的字符
Ctrl + K ：删除本行
Ctrl + L : 将插入点置于窗口正中
Command + Alt + D：显示open quickly 窗口
Command + Alt + 上方向键 ：打开配套文件
Command + D ：添加书签
Option + 双击：在文档中搜索
Command + Y ：以调试方式运行程序
Command + Alt + P ： 继续（在调试中）
Command + Alt + 0 ：跳过
Command + Alt + I ：跳入
Command + Alt + T ：跳出


————————————————————————————————————
IOS绘图API:
线条：重写drawRect(rect: CGRect)
var context = UIGraphicsGetCurrentContext()
CGContextMoveToPoint(context ,100,100)		//从哪里开始画
CGContextAddLineToPoint(context ,100,200)	//画直线，并指定终点
CGContextStrokePath(context)			//呈现

CGContextMovePoint(context ,100,100)		//移动开始画第二条线
。。。
矩形：
CGContextAddRect(。。。)
CGContextFillPath(context)
圆形：
第一种：CGContextAddArc(。。。)	
CGContextSetRGBFillColor(。。。)	//填充颜色
CGContextFillPath(context)
第二种：CGContextAddEllipseInRect()	//在矩形内绘制，依照外壳而定

绘制图片：图片y轴相反，需要添加缩放为-1
CGContextSaveGState(context)		//保存当前Context状态
CGContextTranslateCTM(context,10,400)	//移动
CGContextScalaCTM(context,1,-1)	//缩放
CGContextDrawImage(context,CGRect(x: 0 ,y: 0, width: 200, height: 200), uiImage初始化好的图片)
CGContextRestoreState(context)	//恢复状态，避免影响后续其他操作context的对象	

简易画板：
var path= CGPathCreateMutable()	//存储所有的图形
override func touchesBegan	//触摸起始点
var p = touches.anyObject().locationInView(self)
CGPathMoveToPoint(path, nil, p.x, p.y)	//移动到绘画起始点

override func touchesMoves	//移动点
var p = touches.anyObject().locationInView(self)
CGPathAddLineToPoint(path, nil, p.x, p.y)	//添加绘画终点
setNeedsDisplay()		//执行重绘方法，即重新执行drawRect方法

override fun drawRect{
var context = UIGraphicsGetCurrentContext()
CGContextAddPath(context, path)
CGContextStrokePath(context)


项目模板：
Master-Detail Application：日志添加，编辑
Page-Based Application：电子书模板
Tabbed Application：照片模板

屏幕适配：
匹配父级容器：
Aspect Fit 
图片匹配：Editor-》Pin-》Leading Space to Superview+其他三项

分割父级容器：
①调整左右间距：
Editor-》Pin-》Horizontal Spacing中间水平间距，左右边距
②可以调整比例：
Editor-》Pin-》Horizontal Spacing-》 左右边距-》Widths Equally可以调整比例

复杂布局：上下间距，两控件间距，左右间距

自定义控件属性：根据按钮state配置相应的图片

自定义控件效果：@INDesignable 可设计类

动画效果：
添加视图：self.view.addSubview(img1)
视图切换：UIView.transitionFromView(view1,view2)	//从view1切换到view2

视图动画效果：
①开始配置动画：UIView.beginAnimation(nil, context: ni)
②设置动画：
UIView.setAnimationTransition(...)
UIView.setAnimationDuration(1.0)
③提交动画：UIView.commitAnimations()

自定义动画：UIView.transitionWithView(img1, duration: 1.0, 
options: UIViewAnimationOptions.TransitionNone, animations: 自定义动画方法, completion: 完成方法)

传感器：
加速度传感器：
引入库: CoreMotion
cmm = CMMotionManager()	//创建实例
cmm.accelerometerUpdateInterval = 1	//配置获取数据的频率 /s

陀螺仪：
cmm.gyroUpdateInterval = 1

距离传感器：
UIDevice.currentDevice().proximityMonitoringEnabled = true //是否侦听距离传感器
NSNotificationCenter.defaultCenter().addObserver(self
, selector: Selector("proximityChanged"), name: UIDeviceProximityStateDidChangeNotification, object: nil)	//添加侦听器

定义方法：
func proximityChanged(){
	打印
}


