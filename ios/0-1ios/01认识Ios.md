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

模拟器:
command+shift+H 回到主界面

### ios项目结构
Main.storyboard/Launch.storyboard:主界面UI
是否使用自动布局use Auto layout（一般关闭）

选择控件(右上角+号)：例如（Image view图片控件，View Controller界面,Navigation controller界面）
file inspector(文件属性)：命名，文件路径等
Identity inspector：关联的Controller，Module等
attribute inspector(控件属性)：，可分别设置Image View属性（图片链接，缩放等）和View属性（定位其实位置，背景等）
size inspector（位置大小属性）：其实位置，偏移量等

资源文件存放地址：xcassets，可以配置图片不同尺寸，适配不同设备

### 程序执行流程
1.info.plist：程序启动读取
Launch screen interface file base name：设置启动时的storyboard
2.View Controller：程序控制器
重载方法：
viewDidLoad：Controller的入口
didReceiveMemoryWarning：释放资源


关闭代码预览：miniMap
关闭手机预览:canvas 快捷键option+command+return
AVD快捷回到主界面：command+shift+H

storyboard跳转view：安装control拖动（可在左侧或视图中拖动）


### UI设计传统方法

### 程序添加控件
控件绑定代码
新建控件，双击选择reference outlet，选择viewController中新建的控件
````
    @IBOutlet var iv:UIImageView!
    override func viewDidLoad() {
        super.viewDidLoad()
        print("Hello")
        //程序设置图片内容
        iv.image = UIImage(named: "wawa.jpeg")
        // Do any additional setup after loading the view.
    }
````

### 事件绑定
新建控件button，双击选择touch up inside，选择viewController中新建方法

````
    @IBAction func btnClick(sender:AnyObject){
        print("click ")
    }
````

### 控件与swift类绑定
在storyboard中新建viewController，拖动跳转，选择present modally模式
新建MyviewController：选择Cocoa Touch Class，继承UIViewController
在main.story将新的viewController绑定给MyviewController

### 源代码添加控件

````
        //代码添加控件
        var label = UILabel(frame: <#T##CGRect#>(x: 50,y: 50,width: 100,height: 100))
        label.text = "Hello"
        view.addSubview(label)
````


storyborad界面跳转：按住按钮拖动，返回界面只需要杀死当前controller
self.dissmissModalViewControllerAnimated(true)

@IBXX : InterfaceBuilder 界面设计器程序

nib文件做IOS界面设计：
跳转：self.presenModalViewController(MyViewController(nibName: "MyViewController",bundle: nil), animated: true)

跳转页面中传递数据：
let vc = MyViewController(nibName: "MyViewController",bundle: nil)
vc.labelContent = input.text	//直接给跳转页面的变量赋值
self.presenModalViewController(vc, animated: true)
第二个界面定义变量：
var lableContent: String = ""

第二个界面向第一个界面传递数据：调用父级Controller并赋值
self.parentViewController

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


