# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Andorid Gradle

Gradle wrapper：用来管理版本与相关控制
  其中gradle-wrapper.jar ，是使用gradle-wrapper的一个辅助库
  gradle配置必须与Gradle plugin版本匹配，否则编译就会失败

Gradle plugin：位于项目的根目录下的build.gradle中配置的插件
 gradle配置必须与Gradle wrapper版本不匹配，编译就会失败。

gradlew：W意思是wrapper，它是一个用bash命令包装过的gradle编译启动脚本，里面会进行环境变量检测和设置。最终进行编译的还是gradle

首次导入项目的时候AS默认是使用 gradle-wrapper-properties 中默认的设置，它会从网上下载所需要的对应版本的 gradle
  解决方法一(断网操作)：AS设置,快捷键 ctrl+alt+s,在搜索框输入gradle，选择gradle本地编译
  解决方法二：删除gradle-wrapper-properties 中的最后一行网址（distributionUrl=""）

gradlehome：C:\Users\anna\.gradle\wrapper\dists
  可以设置gradlehome的环境变量，变量值为：gradlehome路径 + gradle-2.14.1-all\8bnwg5hd3w55iofp58khbp6yv（项目对应的gradle版本）
	设置环境变量可以解决导入不了项目的问题,但是每次导入都得重新设置一遍
