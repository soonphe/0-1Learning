# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")


### ios辅助开发


### homebrew

安装homebrew（/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"）

### homebrew cask
安装homebrew cask（brew tap caskroom/cask）

### cocoaPod 
安装cocoaPod（sudo gem install cocoapods
pod setup//配置pod
pod search MJExtension//查找第三方库

在项目下生成并编辑一个Podfile文件，命令为vim Podfile，要导入的第三方都要在这里面写上。
进去后需要先按I键进入编辑状态，写完后按esc，然后按shift+zz(或者先按shift+:,再按wq)就可以保存退出了。
Podfile的格式大概如下，其中'Test'为你的target的名字。
platform :ios,'8.0'
target 'Test' do
pod 'MJExtension', '~> 3.0.13'
end

安装依赖包，命令为：pod install。
