接口参考：
参考：http://omdbapi.com/
搜索列表：http://omdbapi.com/?apikey=682d8365&s=Longest
详情：http://omdbapi.com/?apikey=682d8365&i=tt2726560



1.cocoapod解析（已完成）
2.SwiftyBeaver彩色日志
3.Carthage添加依赖，比CocoaPods更轻，swift package 包管理（已完成）
4.第三方解析（网络、图片、数据库等）
Alamofire（网络请求）+kingfisher（图片）+moya（网络抽象）+objectmapper（json）+realm（数据库）
5.Weibo——U17（无法跑）——Popcorn（LONGEST RIDE）
http://omdbapi.com/?apikey=682d8365&i=tt2726560

Xib 图形化创建界面。


先pod init  再pod install，最后打开项目.xcworkspace即可。


允许使用http请求：info.plist
App Transport Security Settings
	Allow Arbitrary Loads       YES

2.新建xib文件，new view（已完成）
2.界面跳转（segue跳转）（已完成）
添加storyboard reference，跳转选择present modally，
在storyboard中 添加Storyboard ID，并选择Use Storyboard ID

3.展示Alert对话框
    //提示信息
    func showAlert(titleInput: String, messageInput:String){
        let alert = UIAlertController(title: titleInput, message: messageInput, preferredStyle: .alert)
        alert.addAction(UIAlertAction(title: "Tamam", style: .default, handler: nil))
        self.present(alert, animated: true, completion: nil)
    }



1.UITableView，设置每个单元格样式和数据（已完成）
2.CellHelp，定义装载视图和可重用视图，以供UITableView和UICollectionView装载
tRoundAuctionBO.setStartTime(tenderStartTime);


1.不使用storyboard创建视图view（继承UIViewController即可）
添加子控件：
let myView = UIView(frame: CGRect(x: 100, y: 100, width: 100, height: 100))
添加点击事件：
btn.addTarget(self, action: #selector(btnDidClick), for: .touchUpInside)
2.底部导航4个模块+切换（已完成）
使用tabBarController
或
自定义tabBarController


AFNetworking请求，与OC相比只是语法不同，
使用Alamofire，采用链式编程的思想，与AFN是统一团队开发，
Moya是对Alamofire的高度封装）


Moya使用：
1.先定义provider，即请求发起对象
2.申明一个enum对请求进行明确分类，
3.最后让这个 enum 实现 TargetType 协议，在这里面定义我们各个请求的 url、参数、header 等信息。
https://www.jianshu.com/p/47fd2ed5a74d?utm_campaign=haruki
https://www.jianshu.com/p/2ee5258828ff

xcode插件：alcatraz