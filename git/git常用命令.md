# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## git常用命令

### 0-1创建仓库和推送
通过“Git Bash”命令行窗口进入到想要建立版本仓库的目录，通过“git init”就可以轻松的建立一个仓库。
git add . 或者git add 文件名称  ：将文件添加到待提交区（暂存区），即此时更新为WorkSpace保存到Stage中。
git commit 提交 
git commit -m 提交 m后接对更新说明  此时更新已经从Stage保存到了Local Repo中。
git commit -am "提交说明" //提交时加上参数：-a ，表示新增。
git diff"是个很有用，而且会经常用到的命令，用于显示WorkSpace中的文件和暂存区文件的差异
git checkout --<file>..."来撤销WorkSpace中的更新。执行test.txt中的文本会变成两行"abc"。
git reset HEAD <file>..将更新移出到WorkSpace中。

git remote add origin（远程别名） https://github.com/soonphe/XXX.git	//本地项目与git项目建立关系
git push -u origin master	//本地的master分支推送到origin主机的master分支，如果master不存在，则被新建
git push -u origin master：-u选项指定一个默认主机


### 1 远程仓库相关命令
检出仓库：$ git clone git://github.com/jquery/jquery.git
查看远程仓库：$ git remote -v
添加远程仓库：$ git remote add [name远程仓库名] [url远程地址]
删除远程仓库：$ git remote rm [name]
修改远程仓库：$ git remote set-url --push [name] [newUrl]
拉取远程仓库：$ git pull [remoteName] [localBranchName]
拉取远程仓库但不自动merge：git fetch ——推荐fetch
推送远程仓库：$ git push [remoteName] [localBranchName]
例：
$ git push <远程主机名> <本地分支名>:<远程分支名>
$ git push -u origin master：-u选项指定一个默认主机
$ git push origin test:master         // 提交本地test分支作为远程的master分支
$ git push origin test:test              // 提交本地test分支作为远程的test分支
 
### 2 分支(branch)操作相关命令
查看本地分支：$ git branch
查看远程分支：$ git branch -r
创建本地分支：$ git branch [name] ----注意新分支创建后不会自动切换为当前分支
切换分支：$ git checkout [name]
创建新分支并立即切换到新分支：$ git checkout -b [name]
删除分支：$ git branch -d [name] ---- -d选项只能删除已经参与了合并的分支，对于未有合并的分支是无法删除的。如果想强制删除一个分支，可以使用-D选项
合并分支：$ git merge [name] ----将名称为[name]的分支与当前分支合并
创建远程分支(本地分支push到远程)：$ git push origin [name]
删除远程分支：$ git push origin :heads/[name] 或 $ gitpush origin :[name] 
*创建空的分支：(执行命令之前记得先提交你当前分支的修改，否则会被强制删干净没得后悔)
$git symbolic-ref HEAD refs/heads/[name]
$rm .git/index
$git clean -fdx

更新远程分支：git remote update origin --prune
git remote update origin -p
 
### 3 版本(tag)操作相关命令
查看版本：$ git tag
创建版本：$ git tag [name]
删除版本：$ git tag -d [name]
查看远程版本：$ git tag -r
创建远程版本(本地版本push到远程)：$ git push origin [name]
删除远程版本：$ git push origin :refs/tags/[name]
合并远程仓库的tag到本地：$ git pull origin --tags
上传本地tag到远程仓库：$ git push origin --tags
创建带注释的tag：$ git tag -a [name] -m 'yourMessage'

### 4 子模块(submodule)相关操作命令
添加子模块：$ git submodule add [url] [path]
   如：$git submodule add git://github.com/soberh/ui-libs.git src/main/webapp/ui-libs
初始化子模块：$ git submodule init  ----只在首次检出仓库时运行一次就行
更新子模块：$ git submodule update ----每次更新或切换分支后都需要运行一下
删除子模块：（分4步走哦）
 1) $ git rm --cached [path]
 2) 编辑“.gitmodules”文件，将子模块的相关配置节点删除掉
 3) 编辑“ .git/config”文件，将子模块的相关配置节点删除掉
 4) 手动删除子模块残留的目录


### 5 添加和删除文件
git add .               // 添加所有文件
git add file.txt        // 添加指定文件

git rm file.txt         // 从版本库中移除，删除文件
git rm file.txt -cached // 从版本库中移除，不删除原始文件
git rm -r xxx           // 从版本库中删除指定文件夹


### 6 撤销更新/版本回退
// 撤销最近的一个提交.
git revert HEAD

// 取消 commit + add
git reset --mixed

// 取消 commit
git reset --soft

// 取消 commit + add + local working
git reset --hard

git log，通过这个命令我们可以查看commit的历史记录。
在Git中，有一个HEAD指针指向当前分支中最新的提交。
当前版本，我们使用"HEAD^"，那么再前一个版本可以使用"HEAD^^"，如果想回退到更早的提交，可以使用"HEAD~n"。
（也就是，HEAD^=HEAD~1，HEAD^^=HEAD~2）
git reset --hard HEAD^
git reset --hard HEAD~1
git reset --c2760c5512bc67a8b990c1da508d40cca623f23
git log"只是包括了当前分支中的commit记录，而"git reflog"中会记录这个仓库中所有的分支的所有更新记录，包括已经撤销的更新

使用reset来撤销更新的时候，我们都是使用的"--head"选项，其实与之对应的还有一个"--soft"选项，
区别如下：
--head：撤销并删除相应的更新
--soft：撤销相应的更新，把这些更新的内容放到Stage中

---

### git设置用户名密码等
git config --list   查看git所有配置项
git config --global user.name "你的名字或昵称"
git config --global user.email "你的邮箱"


### Git fetch和git pull的区别：
Git中从远程的分支获取最新的版本到本地有这样2个命令：

1. git fetch：相当于是从远程获取最新版本到本地，不会自动merge
git fetch origin master:tmp
git diff tmp 
git merge tmp

2. git pull：相当于是从远程获取最新版本并merge到本地
git pull origin master
在实际使用中，git fetch更安全一些
因为在merge前，我们可以查看更新情况，然后再决定是否合并



### clone和branch、fork:
clone：克隆只是仓库的副本。

branch：分支是存储库中的东西。 从概念上讲，它代表了一个开发线程。 你通常有一个主分支，但是你也有一个分支，在那里你正在处理某些功能 xyz，另一个用于修复 Bug abc 。 当你已经签出一个分支，进行任何提交都会停留在这个分支，并不会与其他分支共享，直到你将它们用或者变基他们拖到 branch. 

fork：不是一个Git概念，它更像是一个政治/社会概念。 也就是说，如果有些人对项目的运行方式不满意，那么他们可以使用源代码，并自己独立于原始开发者。 这将被视为一个分叉。 因为大家都已经有自己的Git使得分叉容易"母版"的源代码副本，因此它很简单，只需要切割的领带与最初的项目开发人员，并且不需要导出历史记录用SVN从共享存储库( 与你所要做的。


### git正确的删除远程仓库的文件并用.gitignore忽略提交此文件
git rm -r --cached target
git commit -m "delete target/"
git push origin master
打开github看一下，target目录是不是没有提交了！ 
如果想把target目录以后都不用提交，可以作如下


### merge和rebase的区别
先说一下：merge和rebase都是合并分支

Merge：合并，分支就不存在了，但是在Git的所有分支历史中还能看到身影
git checkout bugfix; 切换到bugfix分支，
git merge master：因为master继承自bugfix，git就会直接将bugfix指向master所指向的提交记录

Rebase：所有的分支依然存在
git rebase 实际上就是取出一系列的提交记录，“复制”它们，然后在另外一个地方逐个的放下去（更加线性化）
git rebase master：假如当前分支在bugfix上，命令后，bugfix在master的顶端，


### github上的.gitignore和.gitattributes这两个文件区别：

.gitigonre 你想要忽略的文件或者目录
.gitattribute 用于设置文件的对比方式（常用非文本文件）


### github建立ssh通信，让Git操作免去输入密码的繁琐。

*   首先呢，我们先建立ssh密匙。
> ssh key must begin with 'ssh-ed25519', 'ssh-rsa', 'ssh-dss', 'ecdsa-sha2-nistp256', 'ecdsa-sha2-nistp384', or 'ecdsa-sha2-nistp521'.  -- from github

    根据以上文段我们可以知道github所支持的ssh密匙类型，这里我们创建ssh-rsa密匙。
    在command line 中输入以下指令:``ssh-keygen -t rsa``去创建一个ssh-rsa密匙。如果你并不需要为你的密匙创建密码和修改名字，那么就一路回车就OK，如果你需要，请您自行Google翻译，因为只是英文问题。
>$ ssh-keygen -t rsa
Generating public/private rsa key pair.
//您可以根据括号中的路径来判断你的.ssh文件放在了什么地方
Enter file in which to save the key (/c/Users/Liang Guan Quan/.ssh/id_rsa):

* 到 https://github.com/settings/keys 这个地址中去添加一个新的SSH key，然后把你的xx.pub文件下的内容文本都复制到Key文本域中，然后就可以提交了。
* 添加完成之后 我们用``ssh git@github.com`` 命令来连通一下github，如果你在response里面看到了你github账号名，那么就说明配置成功了。  *let's enjoy github ;)*


### git统计代码
统计每个人的增删行数
git log --format='%aN' | sort -u | while read name; do echo -en "$name\t"; git log --author="$name" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -; done

统计个人代码行数（按git用户名）
git log --author="soonphe" --pretty=tformat: --numstat | awk '{ add += $1; subs += $2; loc += $1 - $2 } END { printf "added lines: %s, removed lines: %s, total lines: %s\n", add, subs, loc }' -

统计仓库提交者排名前10名
git log --pretty='%aN' | sort | uniq -c | sort -k1 -n -r | head -n 10

贡献者统计：
git log --pretty='%aN' | sort -u | wc -l

提交数统计：
git log --oneline | wc -l