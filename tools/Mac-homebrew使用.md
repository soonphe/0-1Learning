# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## Mac-homebrew使用（Mac下包管理工具）

### homebrew是什么

简单来说，就是一款mac内置的程序，可以用这个程序下载很多常用的软件。可能有人会问：为什么不用网站去下载安装啊？​网站你得搜索吧，找个下载界面点击下载。 步骤太多，过于繁琐。使用homebrew就是简单了，光放库支持，打开终端，输入命令直接下载，是不是很cool。

### homebrew安装

- 安装homebrew
安装方式：终端 - 输入链接
官方推荐默认安装链接：/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"

上面的链接如果在国内大几率会失败。

**所以在国内使用以下地址安装**
- 苹果电脑 常规安装脚本（推荐 完全体 几分钟安装完成）：
`/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)"`
- 苹果电脑 极速安装脚本（精简版 几秒钟安装完成）：
`/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/Homebrew.sh)" speed`
- 苹果电脑 卸载脚本：
`/bin/zsh -c "$(curl -fsSL https://gitee.com/cunkai/HomebrewCN/raw/master/HomebrewUninstall.sh)"`

![alt text](../static/software/brew_install.png "brew安装")

### homebrew命令
brew有很多使用的工具，如搜索、安装、更新等，都是可以一键操作完成的。

列举如下：
- brew config：查看brew配置来源
- brew -v ：版本号
- brew list：列出所有已安装formula（软件包）和cask（应用包）
- brew list --versions:列出所有已安装的formula（软件包）和cask（应用包）及版本。
- brew services list：查看服务运行情况
- brew search xxx ：搜索formula（软件包）和cask（应用包）。例如 brew search mysql
- brew install xxx ：安装。例如：brew install mysql
- brew install xxx@x.x ：安装指定版本。例如：brew install mysql@5.7
- brew info xxx：查询。 例如：brew info mysql 主要查看具体的信息及依赖关系当前版本注意事项等
- brew update：更新。 如果想要更新到当前最新的版本要先把当前 brew 更新到最新。这个时候他会先更新自己到最新 接下来的操作才更有意义
- brew outdated：检测新版本。 会列出所有有新版本的程序
- brew upgrade：升级。 升级所有 当然也可以指定升级（brew upgrade xxx指定的升级的程序名）
- brew cleanup：清理。 清理不需要的版本及其安装缓存
- brew uninstall：删除 xxx删除不需要的程序
- brew remove mysql：卸载不需要的程序
- man brew：更多命令详见

![alt text](../static/software/brew_config.png "brew命令")

有时候我们不知道brew可以下载哪些软件，这里有一个参考网址，打开网址即可，文件较大，json格式，有下载量、打分
*https://formulae.brew.sh/api/analytics/install-on-request/90d.json*


### brew使用示例
* 帮助：brew –help

* 搜索(会搜索formula（软件包）和cask（应用包）)
  - brew search mysql:
  - brew search elasticsearch:

* 安装：(可以安装formula（软件包）和cask（应用包）)
  - brew install jdk：安装mysql
  - brew install mysql：安装mysql
  - brew install mysql@5.7：指定5.7版本安装
  说明：安装完后要执行链接`brew link --force mysql@5.7`      
     执行 `mysql_secure_installation` 初始化密码，启动mysql：`mysql -uroot`
     可以配置环境变量： `echo 'export PATH="/usr/local/opt/mysql@5.7/bin:$PATH"' >> ~/.zshrc`
  - brew install redis：安装redis
  - brew install elasticsearch：安装elasticsearch
  - brew install kibana：安装kibana
  - brew install docker：安装docker
  - brew install curl：URL语法在命令行下工作的文件传输工具
  - brew install wget：下载工具
  - brew install yarn：安装yarn，javascript包管理工具
  - brew install nginx：安装nginx
  - brew install maven：安装maven
  - brew install --cask chrome：安装应用chrome
  - brew install --cask wechat：安装应用wechat
  - brew install --cask typora： 安装typora
  - brew install --cask firefox     //以应用包形式安装
  - brew install --cask visual-studio-code
  - brew install --cask docker 

* brew list            #显示已经安装软件列表	
* brew list --versions #列出所有已安装的formula（软件包）和cask（应用包）及版本。
* brew uninstall git   #卸载
* brew outdated	    #查看那些已安装的程序需要更新
* brew update	    #更新软件，把所有的Formula目录更新，并且会对本机已经安装并有更新的软件用*标明。
* brew upgrade git  #更新某具体软件
* brew cleanup git  #单个软件删除，和upgrade一样
* brew cleanup      #删除所有
* brew home git     #用浏览器打开git主页
* brew info git	    #显示软件内容信息（可以查询安装路径、配置文件地址等）
* brew deps git	    #显示包依赖
* brew config	    #查看brew配置

* 启动：
  - 启动 mysql, 并设置为开机启动
  - brew services start mysql 
  - brew services start redis 
  - brew services start kibana (http://localhost:5601/)

* 关闭 mysql
  - brew services stop mysql 

* 重启 mysql
  - brew services restart mysql

* 查看已安装的服务
  - brew list --versions

* 查找软件安装位置
  - which mysql

### brew更换源
**查看当前镜像源的信息**
```
# 查看brew镜像源
git -C "$(brew --repo)" remote -v

# 查看homebrew-core镜像源
git -C "$(brew --repo homebrew/core)" remote -v

# 查看homebrew-cask镜像源（需要安装后才能查看）
git -C "$(brew --repo homebrew/cask)" remote -v 
```

执行 brew 命令安装应用的时候，跟以下 3 个仓库地址有关。
- brew.git （源代码仓库）
- homebrew-core.git （核心软件仓库）
- homebrew-bottles （预编译二进制软件包）

**国内镜像地址**
- 科大: https://mirrors.ustc.edu.cn
- 阿里: https://mirrors.aliyun.com/homebrew/
- 清华：https://mirrors.tuna.tsinghua.edu.cn/git/homebrew

```
# brew.git镜像源
git -C "$(brew --repo)" remote set-url origin https://mirrors.ustc.edu.cn/brew.git

# homebrew-core.git镜像源
git -C "$(brew --repo homebrew/core)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-core.git

# homebrew-cask.git镜像源
git -C "$(brew --repo homebrew/cask)" remote set-url origin https://mirrors.ustc.edu.cn/homebrew-cask.git

if [ $SHELL = "/bin/bash" ] # 如果你的是bash
then 
    echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/' >> ~/.bash_profile
    source ~/.bash_profile
elif [ $SHELL = "/bin/zsh" ] # 如果用的shell 是zsh 的话
then
    echo 'export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles/' >> ~/.zshrc
    source ~/.zshrc
fi

brew update
```

**如果需要恢复原有镜像源的话（国内镜像源突然不能用了或版本不够新）**
```
git -C "$(brew --repo)" remote set-url origin https://github.com/Homebrew/brew.git

git -C "$(brew --repo homebrew/core)" remote set-url origin https://github.com/Homebrew/homebrew-core.git

git -C "$(brew --repo homebrew/cask)" remote set-url origin https://github.com/Homebrew/homebrew-cask.git

# 找到 ~/.bash_profile 或者 ~/.zshrc 中的HOMEBREW_BOTTLE_DOMAIN 一行删除

brew update
```

**如果恢复不成功或有其他系统问题可以依次尝试以下命令**
```
brew doctor
brew update-reset
brew update
```

### Homebrew禁用自动更新

如果在安装插件时，想跳过自动更新，在使用前，设置一下环境变量即可

```
export HOMEBREW_NO_AUTO_UPDATE=true
```

如果不想要每次安装都去设置环境变量，也可以配置在（~/.bash_profile）中，将上面的命令追加在后面即可。
那下次想要更新brew了怎么办？可以通过命令主动更新：

```
brew update
```

### brew 和 brew cask
- brew 是从下载源码解压然后 ./configure && make install ，同时会包含相关依存库。并自动配置好各种环境变量，而且易于卸载。简单的指令，就能快速安装和升级本地的各种开发环境。
- brew cask 是软件/应用，已经编译好了的应用包 （.dmg/.pkg），下载、解压并放在统一的目录中（/opt/homebrew-cask/Caskroom），省掉了自己去下载、解压、拖拽（安装）等步骤，同样，卸载相当容易与干净

Homebrew Cask 是 Homebrew 的扩展，借助它可以方便地在 macOS 上安装图形界面程序，即我们常用的各类应用.*使用一个快速命令安装的应用程序：不单击、不拖放、不拖放。*

- 安装brew cask
  - Homebrew已默认集成了homebrew cask,如果没有则执行：brew tap homebrew/cask-cask
- 搜索应用：
  - brew casks   列表本地所有可安装的cask
- 安装应用：
  - brew install --cask firefox     //以应用包形式安装
  - brew install --formulae firefox //以软件包形式安装
- 制作cask：
  - brew create --cask foo

### brew 命令拓展
```
--cache                   -- Display Homebrew's download cache
--caskroom                -- Display Homebrew's Caskroom path
--cellar                  -- Display Homebrew's Cellar path
--env                     -- Summarise Homebrew's build environment as a plain list
--prefix                  -- Display Homebrew's install path
--repository              -- Display where Homebrew's git repository is located
--version                 -- Print the version numbers of Homebrew, Homebrew/homebrew-core and Homebrew/homebrew-cask (if tapped) to standard output
analytics                 -- Control Homebrew's anonymous aggregate user behaviour analytics
audit                     -- Check formula for Homebrew coding style violations
autoremove                -- Uninstall formulae that were only installed as a dependency of another formula and are now no longer needed
bottle                    -- Generate a bottle (binary package) from a formula that was installed with `--build-bottle`
bump                      -- Display out-of-date brew formulae and the latest version available
bump-cask-pr              -- Create a pull request to update cask with a new version
bump-formula-pr           -- Create a pull request to update formula with a new URL or a new tag
bump-revision             -- Create a commit to increment the revision of formula
bump-unversioned-casks    -- Check all casks with unversioned URLs in a given tap for updates
casks                     -- List all locally installable casks including short names
cat                       -- Display the source of a formula or cask
cleanup                   -- Remove stale lock files and outdated downloads for all formulae and casks, and remove old versions of installed formulae
command                   -- Display the path to the file being used when invoking `brew` cmd
commands                  -- Show lists of built-in and external commands
completions               -- Control whether Homebrew automatically links external tap shell completion files
config                    -- Show Homebrew and system configuration info useful for debugging
create                    -- Generate a formula or, with `--cask`, a cask for the downloadable file at URL and open it in the editor
deps                      -- Show dependencies for formula
desc                      -- Display formula's name and one-line description
dispatch-build-bottle     -- Build bottles for these formulae with GitHub Actions
doctor                    -- Check your system for potential problems
edit                      -- Open a formula or cask in the editor set by `EDITOR` or `HOMEBREW_EDITOR`, or open the Homebrew repository for editing if no formula is provided
extract                   -- Look through repository history to find the most recent version of formula and create a copy in tap
fetch                     -- Download a bottle (if available) or source packages for formulae and binaries for casks
formula                   -- Display the path where formula is located
formulae                  -- List all locally installable formulae including short names
generate-man-completions  -- Generate Homebrew's manpages and shell completions
gist-logs                 -- Upload logs for a failed build of formula to a new Gist
home                      -- Open a formula or cask's homepage in a browser, or open Homebrew's own homepage if no argument is provided
info                      -- Display brief statistics for your Homebrew installation
install                   -- Install a formula or cask
install-bundler-gems      -- Install Homebrew's Bundler gems
irb                       -- Enter the interactive Homebrew Ruby shell
leaves                    -- List installed formulae that are not dependencies of another installed formula
link                      -- Symlink all of formula's installed files into Homebrew's prefix
linkage                   -- Check the library links from the given formula kegs
list                      -- List all installed formulae and casks
livecheck                 -- Check for newer versions of formulae and/or casks from upstream
log                       -- Show the `git log` for formula, or show the log for the Homebrew repository if no formula is provided
man                       -- Generate Homebrew's manpages
migrate                   -- Migrate renamed packages to new names, where formula are old names of packages
mirror                    -- Reupload the stable URL of a formula for use as a mirror
missing                   -- Check the given formula kegs for missing dependencies
options                   -- Show install options specific to formula
outdated                  -- List installed casks and formulae that have an updated version available
pin                       -- Pin the specified formula, preventing them from being upgraded when issuing the `brew upgrade` formula command
postinstall               -- Rerun the post-install steps for formula
pr-automerge              -- Find pull requests that can be automatically merged using `brew pr-publish`
pr-publish                -- Publish bottles for a pull request with GitHub Actions
pr-pull                   -- Download and publish bottles, and apply the bottle commit from a pull request with artifacts generated by GitHub Actions
pr-upload                 -- Apply the bottle commit and publish bottles to a host
prof                      -- Run Homebrew with a Ruby profiler
readall                   -- Import all items from the specified tap, or from all installed taps if none is provided
reinstall                 -- Uninstall and then reinstall a formula or cask using the same options it was originally installed with, plus any appended options specific to a formula
release                   -- Create a new draft Homebrew/brew release with the appropriate version number and release notes
release-notes             -- Print the merged pull requests on Homebrew/brew between two Git refs
rubocop                   -- Installs, configures and runs Homebrew's `rubocop`
ruby                      -- Run a Ruby instance with Homebrew's libraries loaded
search                    -- Perform a substring search of cask tokens and formula names for text
sh                        -- Enter an interactive shell for Homebrew's build environment
shellenv                  -- Print export statements
sponsors                  -- Update the list of GitHub Sponsors in the `Homebrew/brew` README
style                     -- Check formulae or files for conformance to Homebrew style guidelines
tap                       -- Tap a formula repository
tap-info                  -- Show detailed information about one or more taps
tap-new                   -- Generate the template files for a new tap
test                      -- Run the test method provided by an installed formula
tests                     -- Run Homebrew's unit and integration tests
typecheck                 -- Check for typechecking errors using Sorbet
unbottled                 -- Show the unbottled dependents of formulae
uninstall                 -- Uninstall a formula or cask
unlink                    -- Remove symlinks for formula from Homebrew's prefix
unpack                    -- Unpack the source files for formula into subdirectories of the current working directory
unpin                     -- Unpin formula, allowing them to be upgraded by `brew upgrade` formula
untap                     -- Remove a tapped formula repository
update                    -- Fetch the newest version of Homebrew and all formulae from GitHub using `git`(1) and perform any necessary migrations
update-license-data       -- Update SPDX license data in the Homebrew repository
update-maintainers        -- Update the list of maintainers in the `Homebrew/brew` README
update-python-resources   -- Update versions for PyPI resource blocks in formula
update-report             -- The Ruby implementation of `brew update`
update-reset              -- Fetch and reset Homebrew and all tap repositories (or any specified repository) using `git`(1) to their latest `origin/HEAD`
update-test               -- Run a test of `brew update` with a new repository clone
upgrade                   -- Upgrade outdated casks and outdated, unpinned formulae using the same options they were originally installed with, plus any appended brew formula optio
uses                      -- Show formulae and casks that specify formula as a dependency; that is, show dependents of formula
vendor-gems               -- Install and commit Homebrew's vendored gems
vendor-install            -- Install Homebrew's portable Ruby
aspell-dictionaries          postgresql-upgrade-database  services
```

### brew 安装的软件相关信息
使用：`brew info xxx`查看安装软件版本、官网、安装路径、大小、依赖、配置文件地址、操作命令等

使用示例
- brew info nginx
- brew info redis
```
(base) [/usr/local/etc/nginx]$ brew info redis
redis: stable 6.2.4, HEAD
Persistent key-value database, with built-in net interface
https://redis.io/
/usr/local/Cellar/redis/6.2.4 (14 files, 2.0MB)
  Poured from bottle on 2021-07-01 at 23:19:46
From: https://mirrors.ustc.edu.cn/homebrew-core.git/Formula/redis.rb
License: BSD-3-Clause
==> Dependencies
Required: openssl@1.1 ✔
==> Options
--HEAD
	Install HEAD version
==> Caveats
To restart redis after an upgrade:
  brew services restart redis
Or, if you don't want/need a background service you can just run:
  /usr/local/opt/redis/bin/redis-server /usr/local/etc/redis.conf
==> Analytics
install: 27,174 (30 days), 124,245 (90 days), 124,245 (365 days)
install-on-request: 24,766 (30 days), 121,837 (90 days), 121,837 (365 days)
build-error: 0 (30 days)
```

brew安装redis软件的相关信息：
- 版本：redis: stable 6.2.4, HEAD
- 官网：https://redis.io/
- 安装路径：/usr/local/Cellar/redis/6.2.4 (14 files, 2.0MB)
- 配置文件：/usr/local/etc/redis.conf
- 命令：
  - 重启：brew services restart redis
  - 手动启动：/usr/local/opt/redis/bin/redis-server /usr/local/etc/redis.conf

brew安装nginx软件的相关信息：
- 版本：nginx: stable 1.21.0, HEAD
- 官网：https://nginx.org/
- 安装路径：/usr/local/Cellar/nginx/1.21.0 (25 files, 2.2MB) *
- 配置文件：/usr/local/etc/nginx/nginx.conf
- 命令：
  - 重启：brew services start nginx
  - 手动启动：nginx

brew安装elasticsearch软件的相关信息:
- 版本：elasticsearch: stable 7.10.2
- 官网：https://www.elastic.co/products/elasticsearch
- 安装路径：/usr/local/Cellar/elasticsearch/7.10.2 (156 files, 113.5MB) *
- 配置文件：/usr/local/etc/elasticsearch/
- 命令：
  - 重启：  brew services start elasticsearch
  - 手动启动：elasticsearch