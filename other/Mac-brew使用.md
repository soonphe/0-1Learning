# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")


## homebrew（Mac下包管理工具）


### homebrew安装
安装homebrew（/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"）

国内地址安装
苹果电脑 常规安装脚本（推荐 完全体 几分钟安装完成）：
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"
苹果电脑 极速安装脚本（精简版 几秒钟安装完成）：
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)" speed
苹果电脑 卸载脚本：
/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"

### homebrew命令
brew -v ：版本号：
brew list：列出所有已安装的公式。
brew list --versions:列出所有已安装的公式和版本。
brew search xxx ：搜索。例如 brew search mysql
brew install xxx ：安装。例如：brew install mysql
brew info xxx：查询。 例如：brew info mysql 主要查看具体的信息及依赖关系当前版本注意事项等
brew update：更新。 如果想要更新到当前最新的版本要先把当前 brew 更新到最新。这个时候他会先更新自己到最新 接下来的操作才更有意义
brew outdated：检测新版本。 会列出所有有新版本的程序
brew upgrade：升级。 升级所有 当然也可以指定升级（brew upgrade xxx指定的升级的程序名）
brew cleanup：清理。 清理不需要的版本及其安装缓存
brew uninstall：删除 xxx删除不需要的程序
brew remove mysql：卸载不需要的程序
man brew：更多命令详见

### 使用示例：
* 搜索
brew search mysql:
brew search elasticsearch:

* 安装：
brew install jdk：安装mysql
brew install mysql：安装mysql
brew install mysql@5.7：指定5.7版本安装
说明：安装完后要执行链接`brew link --force mysql@5.7`      
     执行 `mysql_secure_installation` 初始化密码，启动mysql：`mysql -uroot`
     可以配置环境变量： `echo 'export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"' >> ~/.zshrc`
brew install redis：安装redis
brew install elasticsearch：安装elasticsearch
brew install kibana：安装kibana
brew install docker：安装docker

* 启动：
启动 mysql, 并设置为开机启动
brew services start mysql 
brew services start redis 
brew services start kibana (http://localhost:5601/)

* 关闭 mysql
brew services stop mysql 

* 重启 mysql
brew services restart mysql

* 查看已安装的服务
brew list --versions


### homebrew cask
安装homebrew cask（brew tap caskroom/cask）


### oh-my-zsh（更强大的命令行工具）
安装oh-my-zsh
```
$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```
Mac系统默认使用dash作为终端，可以使用命令修改默认使用zsh:  
chsh -s /bin/zsh
如果想修改回默认dash，同样使用chsh命令即可:   
chsh -s /bin/bash
### 
安装zsh插件：
brew install zsh-syntax-highlighting    //语法高亮
brew install zsh-autosuggestions  //历史记录

autojump    //跳转目录
```
brew install autojump

最后把以下代码加入 .zshrc：
vim ~/.zshrc
[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh

使配置生效
source ~/.zshrc
```
其他：https://hufangyun.com/2017/zsh-plugin/



### homebrew cask
安装homebrew cask（brew tap caskroom/cask）








