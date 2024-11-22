# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## ios-overview

### cocoaPod包管理
mac自带gem（ruby包管理），可以替换国内源
> gem sources --add https://gems.ruby-china.com/ --remove https://rubygems.org/

```
相关命令：
安装cocoaPod：sudo gem install cocoapods  或  sudo gem install -n /usr/local/bin cocoapods
pod setup//配置pod
pod search MJExtension//查找第三方库
安装依赖包，命令为：pod install。
```

Podfile：要导入的第三方都要在这里面写上
Podfile的格式大概如下，其中。
```
platform :ios,'8.0'
target 'Test' do    //'Test'为你的target的名字（即项目名）
pod 'MJExtension', '~> 3.0.13'  //这里即为第三方包和版本
end
```

### cocoaPod依赖
Podfile文件：
```
# Uncomment the next line to define a global platform for your project
# platform :ios, '9.0'

target 'MNWeibo' do
  # Comment the next line if you don't want to use dynamic frameworks
  use_frameworks!

  # Pods for MNWeibo
pod "AFNetworking"  //网络请求
pod "SDWebImage"    //网络图片加载
pod "YYModel"       //
pod "FMDB"
pod 'SVProgressHUD', :git => 'https://github.com/SVProgressHUD/SVProgressHUD.git'
pod "pop"
pod 'SnapKit', '~> 5.0.0'
pod "Weibo_SDK", :git => "https://github.com/sinaweibosdk/weibo_ios_sdk.git"

  target 'MNWeiboTests' do
    inherit! :search_paths
    # Pods for testing
  end

  target 'MNWeiboUITests' do
    inherit! :search_paths
    # Pods for testing
  end

end

```

### Carthage包管理
```
安装brew install carthage

进入项目所在文件夹
cd ~/路径/项目文件夹
创建一个空的 Carthage 文件 Cartfile
touch Cartfile

使用 Xcode 打开 Cartfile 文件
open -a Xcode Cartfile

编辑 Cartfile【可手动打开进行编辑】
github "Alamofire/Alamofire" == 4.4.0

执行更新命令
$ carthage update --platform iOS

更新成功后，项目文件夹中会多出三个文件
        cartfile
        Cartfile.resolved
        Carthage/ 
        Build/
        Checkouts/
```

用Carthage来管理项目的第三方库时，在描述文件中添加完第三方库，在终端执行更新命令，显示获取完第三方库后，返回error: unable to find utility "xcodebuild", not a developer tool or in PATH，
并且Carthage的Build文件夹中什么也没有，根本没有动态库可以拖到项目中
即大概因为Carthage是先将第三方框架编译成动态库(.framework的二进制文件)，所以需要先指定一个编译工具。在Xcode > Preferences > Locations中的下拉菜单里选择命令行工具。如果只安装了Xcode的一个版本，那么应该只有一个选项。如果有几个版本的Xcode，那么选择需要的版本。


### Swift Package Manager包管理
点击项目——Swift Packages——添加对应的包即可

### AFNetworking
Swift Package Manager引入
```
dependencies: [
    .package(url: "https://github.com/AFNetworking/AFNetworking.git", .upToNextMajor(from: "4.0.0"))
]
```

### Alamofire（Swift网络框架）
引入
```
pod 'Alamofire', '~> 5.2'
```

使用一：
```
let parameters: Parameters = [
    "foo": [1,2,3],
    "bar": [
    "baz": "qux"
]
Alamofire.request("https://httpbin.org/post", method: .post, parameters: parameters, encoding: JSONEncoding.default)
```

使用二：
```
Alamofire.request("https://httpbin.org/get")
.validate(statusCode: 200..<300)
.validate(contentType: ["application/json"])
.responseData { response in
    switch response.result {
    case .success:
        print("Validation Successful")
        case .failure(let error):
        print(error)
    }
}
```

### Moya
Alamofire请求时输入各种请求的条件(url, parameters, header,validate etc)的时候略显累赘，如果我们要设置默认parameters，还有针对特定API做修改的时候，实现起来就会很费劲。
然后，就有了我们Moya。

简单使用：
```
public enum GitHub {
    case zen
    case userProfile(String)
    case userRepositories(String)
}

extension GitHub: TargetType {
    // 略过
}

let provider = MoyaProvider<GitHub>()
provider.request(.zen) { result in
    // `result` is either .success(response) or .failure(error)
}
```
1. 创建一个Provider
provider是网络请求的提供者，你所有的网络请求都通过provider来调用。我们先创建一个provider。

provider最简单的创建方法:

// GitHub就是一个遵循TargetType协议的枚举(看上面例子)
```
let provider = MoyaProvider<GitHub>()
```

2.网络请求
```
provider.request(.zen) { result in
            var message = "Couldn't access API"
            if case let .success(response) = result {
                let jsonString = try? response.mapString()
                message = jsonString ?? message
            }
            self.showAlert("Zen", message: message)
        }
```

### SDWebImage
图片处理：加载，圆角
导包
```
import SDWebImage
```
引入示例：
```
imageView.sd_setImage(with: URL(string: "http://www.domain.com/path/to/image.jpg"), placeholderImage: UIImage(named: "placeholder.png"))
```
引入示例：
```
avatarImageView.sd_setImage(with: url, placeholderImage: UIImage(named: "avatar_default_big"))
```

### FMDB
数据缓存：SQLite周围的Cocoa/ Objective-C包装器

### SVProgressHUD（进度条）
> pod 'SVProgressHUD'

### pop（动画库）
一个可扩展的iOS和OS X动画库，对于基于物理的交互非常有用。

### SnapKit（自动布局）
适用于iOS和OS X的Swift自动布局DSL

### Kingfisher（图片缓存）
用于从Web下载和缓存图像。它为您提供了使用纯Swift方法在​​下一个应用程序中处理远程图像的机会。

### Moya（网络抽象层）
> pod 'Moya', '~> 14.0'

### objectmapper（对象、JSON转换）
> pod 'ObjectMapper', '~> 3.5' (check releases to make sure this is the latest version)

### Realm（数据库管理）
> pod 'RealmSwift'
