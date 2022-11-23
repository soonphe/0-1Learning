# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")



## oh-my-zsh（更强大的命令行工具）
github地址：https://github.com/ohmyzsh/ohmyzsh

### 安装oh-my-zsh
可以使用 curl、wget 或其他类似工具通过命令行安装它。
* 应该先安装 curl 或 wget
* 应该先安装 git（推荐 v2.4.11 或更高版本）

|Method	|Command|
|---|---|
|curl	|sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"|
|wget	|sh -c "$(wget -O- https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"|
|fetch	|sh -c "$(fetch -o - https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"|

例：
```
$ sh -c "$(curl -fsSL https://raw.github.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

安装完成之后查看版本：运行 zsh --version

Mac系统默认使用dash作为终端，可以使用命令修改默认使用zsh:  
```
chsh -s /bin/zsh
```
如果想修改回默认dash，同样使用chsh命令即可: 
```  
chsh -s /bin/bash
```

### 使用oh-my-zsh

#### 默认插件
Oh My Zsh 附带了大量插件供您使用。

您可以查看[插件目录](https://github.com/ohmyzsh/ohmyzsh/tree/master/plugins)或 [wiki](https://github.com/ohmyzsh/ohmyzsh/wiki/Plugins) 以查看当前可用的内容。

**配置文件**

在 .zshrc 文件中启用它们。您将在 $HOME 目录中找到 zshrc 文件。用你最喜欢的文本编辑器打开它，你会看到一个列出你想要加载的所有插件的地方。
```
vi ~/.zshrc
```
例如，这可能看起来像这样：
```
plugins=(
  git
  bundler
  dotenv
  osx
  rake
  rbenv
  ruby
)
```
>请注意，插件由空格（空格、制表符、新行...）分隔。不要在它们之间使用逗号，否则会中断。

- 获取更新：DISABLE_UPDATE_PROMPT=true
- 要禁用自动升级：DISABLE_AUTO_UPDATE=true
- 手动更新：控制台命令(omz update)

- 主题： 默认主题：ZSH_THEME="robbyrussell"。要使用不同的主题，只需更改值以匹配所需主题的名称。例如：
```
ZSH_THEME="agnoster" # (this is one of the fancy ones)
# see https://github.com/ohmyzsh/ohmyzsh/wiki/Themes#agnoster
```
- 其他配置
  - HIST_STAMPS="yyyy-mm-dd"：history 命令查看历史输入命令的时间展示格式
  - ZSH_THEME="random"：随机主题
  - alias go="git-open"：别名

**默认附带插件：**
- git：git 命令缩写。默认已开启。使用示例：git add --all ===> gaa
- autojump：目录间快速跳转,不用再一直cd。例：j local：跳转/usr/local/目录 ；jo local：跳转并打开/usr/local/目录

### 安装oh-my-zsh插件
- brew install zsh-syntax-highlighting    //语法高亮，关键位置用彩色，替换原有黑白色
- brew install zsh-autosuggestions  //键自动补全、历史记录、命令提示

- 手动安装autojump 跳转目录
```
brew install autojump

最后把以下代码加入 .zshrc：
vim ~/.zshrc
[[ -s ~/.autojump/etc/profile.d/autojump.sh ]] && . ~/.autojump/etc/profile.d/autojump.sh

使配置生效
source ~/.zshrc
```

- 手动安装zsh-syntax-highlighting 语法高亮
```
git clone https://github.com/zsh-users/zsh-syntax-highlighting.git ${ZSH_CUSTOM:-~/.oh-my-zsh/custom}/plugins/zsh-syntax-highlighting

在 ~/.zshrc 中配置
plugins=(其他的插件 zsh-syntax-highlighting)

使配置生效
source ~/.zshrc
```

- 手动安装zsh-autosuggestions 自动补全
```
git clone git://github.com/zsh-users/zsh-autosuggestions $ZSH_CUSTOM/plugins/zsh-autosuggestions

在 ~/.zshrc 中配置
plugins=(其他的插件 zsh-autosuggestions)

使配置生效
source ~/.zshrc
```

- 手动安装git-open
```
git clone https://github.com/paulirish/git-open.git $ZSH_CUSTOM/plugins/git-open

在 ~/.zshrc 中配置
plugins=(其他的插件 git-open)

使配置生效
source ~/.zshrc
```

### oh-my-zsh配置参考
```
# If you come from bash you might have to change your $PATH.
# export PATH=$HOME/bin:/usr/local/bin:$PATH

# Path to your oh-my-zsh installation.
export ZSH="/Users/luoxiaosheng/.oh-my-zsh"

# Set name of the theme to load --- if set to "random", it will
# load a random theme each time oh-my-zsh is loaded, in which case,
# to know which specific one was loaded, run: echo $RANDOM_THEME
# See https://github.com/ohmyzsh/ohmyzsh/wiki/Themes
ZSH_THEME="gallois"

# Set list of themes to pick from when loading at random
# Setting this variable when ZSH_THEME=random will cause zsh to load
# a theme from this variable instead of looking in $ZSH/themes/
# If set to an empty array, this variable will have no effect.
# ZSH_THEME_RANDOM_CANDIDATES=( "robbyrussell" "agnoster" )

# Uncomment the following line to use case-sensitive completion.
# CASE_SENSITIVE="true"

# Uncomment the following line to use hyphen-insensitive completion.
# Case-sensitive completion must be off. _ and - will be interchangeable.
# HYPHEN_INSENSITIVE="true"

# Uncomment the following line to disable bi-weekly auto-update checks.
# DISABLE_AUTO_UPDATE="true"

# Uncomment the following line to automatically update without prompting.
# DISABLE_UPDATE_PROMPT="true"

# Uncomment the following line to change how often to auto-update (in days).
# export UPDATE_ZSH_DAYS=13

# Uncomment the following line if pasting URLs and other text is messed up.
# DISABLE_MAGIC_FUNCTIONS="true"

# Uncomment the following line to disable colors in ls.
# DISABLE_LS_COLORS="true"

# Uncomment the following line to disable auto-setting terminal title.
# DISABLE_AUTO_TITLE="true"

# Uncomment the following line to enable command auto-correction.
# ENABLE_CORRECTION="true"

# Uncomment the following line to display red dots whilst waiting for completion.
# COMPLETION_WAITING_DOTS="true"

# Uncomment the following line if you want to disable marking untracked files
# under VCS as dirty. This makes repository status check for large repositories
# much, much faster.
# DISABLE_UNTRACKED_FILES_DIRTY="true"

# Uncomment the following line if you want to change the command execution time
# stamp shown in the history command output.
# You can set one of the optional three formats:
# "mm/dd/yyyy"|"dd.mm.yyyy"|"yyyy-mm-dd"
# or set a custom format using the strftime function format specifications,
# see 'man strftime' for details.
 HIST_STAMPS="yyyy-mm-dd"

# Would you like to use another custom folder than $ZSH/custom?
# ZSH_CUSTOM=/path/to/new-custom-folder

# Which plugins would you like to load?
# Standard plugins can be found in $ZSH/plugins/
# Custom plugins may be added to $ZSH_CUSTOM/plugins/
# Example format: plugins=(rails git textmate ruby lighthouse)
# Add wisely, as too many plugins slow down shell startup.
plugins=(
  git incr zsh-autosuggestions autojump
)

source $ZSH/oh-my-zsh.sh

# User configuration

# export MANPATH="/usr/local/man:$MANPATH"

# You may need to manually set your language environment
# export LANG=en_US.UTF-8

# Preferred editor for local and remote sessions
# if [[ -n $SSH_CONNECTION ]]; then
#   export EDITOR='vim'
# else
#   export EDITOR='mvim'
# fi

# Compilation flags
# export ARCHFLAGS="-arch x86_64"

# Set personal aliases, overriding those provided by oh-my-zsh libs,
# plugins, and themes. Aliases can be placed here, though oh-my-zsh
# users are encouraged to define aliases within the ZSH_CUSTOM folder.
# For a full list of active aliases, run `alias`.
#
# Example aliases
# alias zshconfig="mate ~/.zshrc"
# alias ohmyzsh="mate ~/.oh-my-zsh"
# autojump
pasteinit() {
  OLD_SELF_INSERT=${${(s.:.)widgets[self-insert]}[2,3]}
  zle -N self-insert url-quote-magic # I wonder if you'd need `.url-quote-magic`?
}

pastefinish() {
  zle -N self-insert $OLD_SELF_INSERT
}
zstyle :bracketed-paste-magic paste-init pasteinit
zstyle :bracketed-paste-magic paste-finish pastefinish

# brew镜像源
export HOMEBREW_BOTTLE_DOMAIN=https://mirrors.ustc.edu.cn/homebrew-bottles

# 禁止终端利用 homebrew 安装插件时候的自动更新
alias disableHomebrewUpdate="export HOMEBREW_NO_AUTO_UPDATE=true"

# Add RVM to PATH for scripting. Make sure this is the last PATH variable change.
export PATH="$PATH:$HOME/.rvm/bin"

# 本机bash
source ~/.bash_profile

# 语法高亮
source "$ZSH_CUSTOM/plugins/zsh-syntax-highlighting/zsh-syntax-highlighting.zsh"


```







