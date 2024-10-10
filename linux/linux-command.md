# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## linux常用命令

### java -jar（后台运行一个jar包）
java -jar执行并打印日志：nohup java -jar tron-wallet-1.1-SNAPSHOT.jar >> tron-wallet-1.1.log &

| ：管道符命令，作用将两个命令隔开，管道符左边命令的输出就会作为管道符右边命令的输入
& ：将下面这个命令放到后台运行
示例：
```
cp -R original/dir/ backup/dir/
```
这个命令的目的是将 original/dir/ 的内容递归地复制到 backup/dir/ 中。 虽然看起来很简单，但是如果原目录里面的文件太大，在执行过程中终端就会一直被卡住。 =所以，可以在命令的末尾加上一个 & 号，将这个任务放到后台去执行

**nohup和&**
nohup和&在Unix和Linux系统中都用于将命令或程序在后台运行，但它们的功能和使用场景有所不同。
以下是两者的区别：
忽略挂断信号。使用nohup运行的命令会忽略SIGHUP信号，这意味着即使终端会话结束，命令也会继续在后台运行。这对于需要在断开连接后继续运行的长时间任务非常有用。
输出重定向。当使用nohup运行命令时，它会将标准输出和错误输出重定向到当前目录下的nohup.out文件。如果当前目录不可写，输出会被重定向到用户的home目录下。
综上所述，nohup用于在后台运行命令，并忽略挂断信号，保持命令的持续运行，而&用于将命令放入后台执行，但当用户退出时，命令也会结束。

**不输出任何日志**
nohup java -jar your-application.jar > /dev/null 2>&1 &
上述命令中的> /dev/null表示将标准输出重定向到/dev/null，2>&1表示将标准错误输出重定向到与标准输出相同的位置。最后的&符号将程序放入后台运行。
通过将输出重定向到/dev/null，所有的日志信息将被丢弃，而不会写入任何文件或显示在终端上。请注意，这也意味着你将无法查看应用程序的任何输出，包括潜在的错误消息。如果需要调试或记录日志，请考虑使用其他日志记录机制或将输出重定向到指定的文件。

### 后台运行nohup & 和守护进程daemon
1)守护进程已经完全脱离终端控制台了，而后台程序并未完全脱离终端，在终端未关闭前还是会往终端输出结果
2)守护进程在关闭终端控制台时不会受影响，而后台程序会随用户退出而停止，需要在以nohup xxx & 格式运行才能避免影响（&后台运行）
3)守护进程的会话组和当前目录，文件描述符都是独立的。后台运行只是终端进行了一次fork，让程序在后台执行，这些都没改变。

### 程序忘记加no hup后台运行
ctrl+z，冻结程序暂停运行
输入bg，bg命令是 background（后台）的缩写，用途相当于给你暂停的程序按下了“播放键”，让它在后台畅快地跑起来
输入fg，fg是 foreground（前台）的缩写，就是把程序拉到前台的意思
如果你运行bg命令后直接关掉终端窗口，那程序还是会停止运行的。
其实，之所以程序会停掉，是因为你的终端有个逻辑：终端被关掉之后，就要给子进程发送 SIGHUP 信号把它们关掉。
那…有没有办法让终端不发这个信号呢？有！那就是 disown 命令。
只需要盲打出一条 disown 命令，你的程序就再也不会被关掉了：
总结：Ctrl + Z、bg、disown 三连即可！当你熟练之后，甚至可以直接在终端里面敲下 bg;disown，这样不仅一次能执行两条命令，还不会被程序的输出干扰。

### 目录操作
- mkdir：	创建一个目录	mkdir dirname
- rmdir：	删除一个目录	rmdir dirname
- mvdir：	移动或重命名一个目录	mvdir dir1 dir2
- cd：	改变当前目录	cd dirname
- pwd：	显示当前目录的路径名	pwd
- ls：	显示当前目录的内容	ls -la
- dircmp：	比较两个目录的内容	dircmp dir1 dir2


### 文件操作相关
查找文件相关
```
open 路径：打开文件
find path option ：查看文件路径，可以指定一个匹配条件（文件名-name，文件类型-type，用户-user，时间戳mtime，权限prem）
    -X                      -- warn if filename contains characters special to xargs
    -d                      -- depth first traversal
示例：
find / -name 文件名
find / -type f -name "*.log"

locate /etc/sh：其实是“find -name”的另一种写法，但是要比后者快得多，原因在于它不搜索具体目录，而是搜索一个数据库(/var/lib/locatedb)
whereis grep：只能用于程序名的搜索，而且只搜索二进制文件(参数-b)、man说明文件(参数-m)和源代码文件(参数-s)。
which grep：在PATH变量指定的路径中，搜索某个系统命令的位置，并且返回第一个搜索结果
type cd：不能算查找命令，它是用来区分某个命令到底是由shell自带的，还是由shell外部的独立二进制文件提供的
```

文件相关：
```
cd 绝对路径
cd ~    相对用户路径
cd -		//返回刚才访问的目录
pwd -P		//显示当前路径；参数：-P显示出当前路径，而非使用链接(link)路径
ls -al 目录	//-a：列出所有文件（含属性与隐藏文件） 
		//-l：列出文件的详细信息（创建者，创建时间，权限）
		//-s：打印文件大小
		//-t：按时间对文件排序
ls -al --full-time 目录	//呈现文件的修改时间

ln -s 源文件 目标文件。 //添加软连接，为某一个文件在另外一个位置建立一个同不的链接
ln -s /usr/local/mysql/bin/mysql /usr/bin        //创建mysql软链接
ln -s /root/.nvm/versions/node/v10.10.0/bin/npm /usr/local/bin/npm  //常见npm软链接

echo 字符串的输出 将你想要显示的的信息打印在屏幕上，只用在 echo 后面加上想要显示的内容就好了。
echo string
echo $JAVA_HOME #获取java环境变量
echo $PATH      #后去环境变量path的值
echo "hello world" > file.txt   功能2：使用 ">" 和 ">>" 运算符，将echo后面的内容写入目标文件。(>覆盖重定向，>>追加重定向)
```

批量操作文件：
```
ls |grep 2146967166348241   搜索指定文件名列表
ls *.md                     搜索指定尾缀文件列表

rm *.gif    批量删除指定尾缀的文件
mv *.txt 111/  批量移动文件    

grep -r test log  文件夹批量搜索日志内容
```

文件操作相关：
```
mkdir -m	//创建文件 -m：权限
		//-p：连同上层“空”目录一起删除
touch test.txt  ：新建文件：
    示例：touch 文件	//自动更新文件为当前时间

rm -r test	//将整个目录删除，不管test是否为空 -f不用提示确认删除-r删除目录
mv 文件 目录	//将文件移动到目录
mv 文件 新文件名	//文件重命名
mv 文件1 文件2 目录	//将文件1和文件2移动到目录

cp 路径文件 目录	//复制文件，同时可以重命名
cp 路径文件 .	//将路径文件复制到当前目录
cp 目录1 目录2	//将目录1下的所有文件复制到目录2

chmod [-cfvR] [--help] [--version] mode file...：修改用户文件权限
mode : 权限设定字串，格式如下 :
[ugoa...][[+-=][rwxX]...][,...]
    u 表示该文件的拥有者，g 表示与该文件的拥有者属于同一个群体(group)者，o 表示其他以外的人，a 表示这三者皆是。
    + 表示增加权限、- 表示取消权限、= 表示唯一设定权限。
    r 表示可读取，w 表示可写入，x 表示可执行，X 表示只有当该文件是个子目录或者该文件已经被设定过为可执行。
    -c : 若该文件权限确实已经更改，才显示其更改动作
    -f : 若该文件权限无法被更改也不要显示错误讯息
    -v : 显示权限变更的详细资料
    -R : 对目前目录下的所有文件与子目录进行相同的权限变更(即以递归的方式逐个变更)
    --help : 显示辅助说明
    --version : 显示版本
八进制语法：
7	读 + 写 + 执行	rwx	111
6	读 + 写	rw-	110
5	读 + 执行	r-x	101
4	只读	r--	100
3	写 + 执行	-wx	011
2	只写	-w-	010
1	只执行	--x	001
0	无	---	000

示例：
chmod -R a+rwx /usr/local/mysql/data/ ：为所有用户添加可读、可写、可执行权限
chmod u+x *：为文件所有者添加执行权限

修改文件拥有者
chown [-R] 账号名称      文件/目录
chown [-R] 账号名称:组群  文件/目录

例：
chown admin:groupa file     //修改fil的拥有者为admin，并且所属组为groupa
chown -R admin fileDir    //同时文件夹下改变其下所有文件拥有者

source	//在当前bash环境下读取并执行FileName中的命令。
*注：该命令通常用命令“.”来替代。
使用范例：
source filename
. filename #（中间有空格）

sh 执行.sh脚本
	“-x”选项实现shell脚本逐条语句的跟踪
```
文件解压与打包
```
jar命令解压zip：jar xf file.zip
解压jar：jar xvf xxx.jar
解压jar：jar xvf hello-0.0.1.jar     //-c 创建war包   -v 显示过程信息  -f 指定 JAR 文件名，通常这个参数是必须的
压缩jarjar：jar cvfm 新名字.jar META-INF/MANIFEST.MF com/ mapper/ static/ templates/ application.properties generatorConfig

war包解压：unzip -oq common.war -d common

解压.gz：gzip -dv *

解压tar：tar -zxvf redis-6.0.16.tar.gz ./
压缩tar：tar -zcvf test01.tar.gz ./test

解压zip：unzip test1.zip     unzip example.jar -d .    //其中-d选项指定了解压后的目标路径，.表示当前目录。
压缩zip：zip -r test1.zip ./susu 把当前目录下的susu文件夹下的内容压缩为test1.zip  
压缩zip：zip -r test2.zip susu liu   把当前目录下，susu文件夹和liu文件夹下的内容压缩为test2.zip
```

### 查看文件及内容（head、tail、cat、nl、grep、less、more）
head -n 行数值 文件名 # 查看文件前多少行

        -c -- 显示第一个指定字节
        -n -- 显示第一个指定的行
示例：
```
head -n 10 system.log  # 显示system.log的前10行数据
```

tail    //将某个文件的最后几行显示到终端上 

        -F -- 暗示 -f，但也检测文件重命名
        -b -- 从指定块开始（512 字节）
        -c -- 从指定字节开始
        -f -- 等待新数据追加到文件中
        -n -- 从指定行开始
        -q -- 从不输出给出文件名的标题
        -r -- 以相反的顺序显示文件    
示例：
```
tail notes.log          # 默认显示最后 10 行
tail -100  system.log #显示最后100行数据
tail -n 100  system.log #显示最后100行数据(同上)
tail -n -100  system.log #显示最后100行数据(同上)
tail +100  system.log #显示从100行开始到最后一行数据
tail -n +100  system.log #显示从100行开始到最后一行数据(同上)
tail -200f system.log   # 查看日志最后200行、循环读取
tail -100f /system.log  |grep POST # 根据条件聚合查看api请求记录，循环读取
```

cat -n 文件	//查看文件后多少行 （concatenate：连接）

        -n 或 --number：由 1 开始对所有输出的行数编号。
    	-b -- 编号非空白输出行
        -e -- 在每行末尾显示 $（暗示 -v）
        -s -- 将多个空行压缩为一个
        -t -- 将制表符显示为 ^I（暗示 -v）
        -u -- 不缓冲输出
        -v -- 将非打印字符显示为 ^X 或 M-a
        //-A显示时文件中的[tab]会以“^I”表示，而空格依然显示空格。断行符是以”$”表示
示例：
```
cat error.log | grep -C 5 'nick' 显示file文件里匹配foo字串那行以及上下5行
cat error.log | grep -B 5 'nick' 显示foo及前5行
cat error.log | grep -A 5 'nick' 显示foo及后5行
cat -n textfile1 > textfile2    # textfile1 的文档内容加上行号后输入 textfile2 这个文档里：
cat -b textfile1 textfile2 >> textfile3 # textfile1 和 textfile2 的文档内容加上行号（空白行不加）之后将内容附加到 textfile3 文档里：
```

nl 文件		//打印文件内容，且添加行号（默认忽略空白行）

        -b -- 指定身体线条的样式
        -d -- 用指定的分隔符分隔逻辑页
        -f -- 指定页脚线的样式
        -h -- 指定标题行的样式
        -i -- 每行行号递增
        -l -- 将连续的空行计为一
        -n -- 指定行号的格式
        -p -- 不要在逻辑页重置行号
        -s -- 在行号后添加指定的字符串
        -v -- 指定每个逻辑页的第一行号
        -w -- 指定行号的列数

grep		//文件搜索。若不指定任何文件名称，或是所给予的文件名为 -，则 grep 指令会从标准输入设备读取数据

		-a 或 --text : 不要忽略二进制的数据。
	    -A<显示行数> 或 --after-context=<显示行数> : 除了显示符合范本样式的那一列之外，并显示该行之后的内容。
	    -b 或 --byte-offset : 在显示符合样式的那一行之前，标示出该行第一个字符的编号。
	    -B<显示行数> 或 --before-context=<显示行数> : 除了显示符合样式的那一行之外，并显示该行之前的内容。
	    -c 或 --count : 计算符合样式的列数。
	    -C<显示行数> 或 --context=<显示行数>或-<显示行数> : 除了显示符合样式的那一行之外，并显示该行之前后的内容。
	    -d <动作> 或 --directories=<动作> : 当指定要查找的是目录而非文件时，必须使用这项参数，否则grep指令将回报信息并停止动作。
	    -e<范本样式> 或 --regexp=<范本样式> : 指定字符串做为查找文件内容的样式。
	    -E 或 --extended-regexp : 将样式为延伸的正则表达式来使用。
	    -f<规则文件> 或 --file=<规则文件> : 指定规则文件，其内容含有一个或多个规则样式，让grep查找符合规则条件的文件内容，格式为每行一个规则样式。
	    -F 或 --fixed-regexp : 将样式视为固定字符串的列表。
	    -G 或 --basic-regexp : 将样式视为普通的表示法来使用。
	    -h 或 --no-filename : 在显示符合样式的那一行之前，不标示该行所属的文件名称。
	    -H 或 --with-filename : 在显示符合样式的那一行之前，表示该行所属的文件名称。
	    -i 或 --ignore-case : 忽略字符大小写的差别。
	    -l 或 --file-with-matches : 列出文件内容符合指定的样式的文件名称。
	    -L 或 --files-without-match : 列出文件内容不符合指定的样式的文件名称。
	    -n 或 --line-number : 在显示符合样式的那一行之前，标示出该行的列数编号。
	    -o 或 --only-matching : 只显示匹配PATTERN 部分。
	    -q 或 --quiet或--silent : 不显示任何信息。
	    -r 或 --recursive : 此参数的效果和指定"-d recurse"参数相同。
	    -s 或 --no-messages : 不显示错误信息。
	    -v 或 --invert-match : 显示不包含匹配文本的所有行。
	    -V 或 --version : 显示版本信息。
	    -w 或 --word-regexp : 只显示全字符合的列。
	    -x --line-regexp : 只显示全列符合的列。
	    -y : 此参数的效果和指定"-i"参数相同。

示例：
```
grep test *log     # 查找后缀有 log 字样的文件中包含 test 字符串的文件，并打印出该字符串的行
grep -r test /logs # 查找目录及其子目录下所有文件中包含字符串"test"的文件，并打印出该字符串所在行的内容
grep -v test *test* # 查找文件名中包含 test 的文件中不包含test 的行
grep -a nginx /etc/nginx/nginx.conf	//在nginx.conf文件中查找nginx字符串
ll | grep test.txt	//管道搜索，在ls显示的内容中查看包含test的行
```


less + 日志		//文件查看    *less不必读整个文件，加载速度会比more更快*

    -N           -- show line numbers  显示行号 
    -o           -- copy input to file  复制文件

less的常用动作命令:

    g:跳到第一行
    shift + G 命令到文件尾部  然后输入 ？加上你要搜索的关键字例如 ？1213
    Space *空格*向下滚动一屏；
    /字符串：向下搜索“字符串”的功能
    ?字符串：向上搜索“字符串”的功能
    n：重复前一个搜索（与 / 或 ? 有关
    N：反向重复前一个搜索（与 / 或 ? 有关）
    b  向后翻一页 (backword)；    
    d  向后翻半页
    f (forword)向下滚动一屏； 
    w 可以指定显示哪行开始显示，是从指定数字的下一行显示；比如指定的是6，那就从第7行显示；
    p n% 跳到n%，比如 10%，也就是说比整个文件内容的10%处开始显示；

示例：
```
less -N /etc/profile 显示行号
```


more 显示输出的内容，然后根据窗口的大小进行分页显示，然后还能提示文件的百分比；
more [参数选项] [文件]

    +num 从第num行开始显示；
    -num 定义屏幕大小，为num行；

more 的常用动作指令：
    
    Enter 向下n行，需要定义，默认为1行；
    Ctrl+f 向下滚动一屏；
    Space 向下滚动一屏；
    Ctrl+b 返回上一屏；

more 命令中查找：
1.:使用more基本查看命令more 51.log
2.:按下V键,调用vi编辑器结果:--More--(5%)看不见了，进入了vim模式
3.:输入/,后面在跟你需要搜索的字符串。/2019
4.：然后按下回车，就搜到你要的字符串了，按n是匹配当前文本的下一个字符串
5.：退出vim模式，按Esc键，输入:q 就可以退出vim，返回more命令格式

日志清空：
三种方式
```
cat /dev/null > filename

：> filename

> filename
```


### 编辑文件
vi文本操作跳转：

    gg	# 进入行首
    dG	# 文件内容将被全部清空
    dd	# 清除一行
    ctrl-f # 整页翻页 f就是forword 
    ctrl-b # 整页翻页 b就是backward
    gg # 跳转文件第一行 -也可输入 :0 或者 :1   回车
    G # 移动到这个文件的最后一行  -也可输入 :$   回车
    nG #  n 为数字，移动到这个文件的第n行.

vim		//编辑模式，输入i,I.o,O,a,A,r,R任一字母进入编辑模式

		//i从光标所在处插入，I从光标所在处插入
		//a从光标下一个字符处插入，A从光标所在行最后一个字符处插入
		//o从光标所在处下一行插入新的一行，O从光标所在处上一行插入新的一行
		//r替换光标所在处字符一次，R一直替换直到按下Esc
		//一般模式，输入 ：/？三个字符中任一按钮
		//0光标此行最前面字符，$光标移动到此行行尾
		// /word向下查找，？word向上查找
	    //x向后删除一个字符，nx向后删除n个字符
		//X向前删除	
		//dd删除光标所在行
		//yy复制光标所在行
		// :wq!保存后离开
		// :q!不保存离开
启动参数：

		-A -- 以阿拉伯语模式启动
	    -C -- 以兼容模式启动
	    -D -- 调试模式
	    -E -- 改进的防爆模式
	    -F -- 以波斯语模式启动
	    -H -- 以希伯来语模式启动
	    -L -r -- 列出交换文件并退出交换文件或从交换文件中恢复
	    -M -- 不允许修改文本
	    -N -- 以不兼容模式启动
	    -O -​​- 垂直拆分打开的窗口数（默认为每个文件一个）
	    -R -- 只读模式
	    -S -- 加载第一个文件后获取会话文件
	    -T -- 设置终端类型
	    -V -- 详细级别
	    -W -- 将所有键入的命令写入给定文件，覆盖现有文件
	    -X -- 不连接到 X 服务器
	    -Z -- 限制模式
	    -b -- 二进制模式
	    -c -- 在加载第一个文件后执行给定的命令
	    -d -- 差异模式
	    -e -- 前模式
	    -g -- 从 GUI 开始
	    -i -- 使用指定的 viminfo 文件
	    -l -- lisp 模式
	    -m -- 不允许修改（写入文件）
	    -n -- 无交换文件（仅内存）
	    -nb -- 作为 NetBean 服务器启动
	    -o -- 要打开的窗口数（默认：每个文件一个）
	    -p -- 要打开的选项卡数量（默认值：每个文件一个）
	    -q -- 快速修复文件
	    -s -- 从脚本文件中读取正常模式命令
	    -t -- 编辑定义标签的文件
	    -u -- 使用给定的 vimrc 文件而不是默认的 .vimrc
	    -v -- vi 模式
	    -w -- 将所有键入的命令附加到给定文件
	    -x -- 编辑加密文件
	    -y -- 简单模式
vi下按u只能撤销一个，vim可以无限撤消

### 传输文件
scp -r /home/demo root@服务器IP:/root	//将本极demo目录拷贝到服务器IP的/root目录下
scp /Users/codez/Downloads/jdk-8u144-linux-x64.tar.gz root@139.224.235.xxx:/root/java/jdk-8u144-linux-x64.tar.gz

### 服务器内存、磁盘
```
top		//动态观察程序的变化
        -F -- 不计算共享库的统计信息
        -O -​​- 指定二级排序键
        -R -- 不遍历和报告每个进程的内存对象映射
        -S -- 显示交换和可清除内存的全局统计信息
        -a -- 累计计数事件
        -c -- 设置事件计数模式
        -d -- 计数相对于前一个样本的事件
        -e -- 使用绝对计数器计数事件
        -f -- 计算共享库的统计信息
        -h -- 打印使用信息并退出
        -i -- 为 -f 选项指定样本之间的间隔
        -l -- 日志模式。定期输出指定数量的样本
        -n -- 只显示指定数量的进程
        -ncols -- 在日志模式下输出指定的列数
        -o -- 指定主排序键
        -pid -- 只显示指定进程
        -r -- 遍历并报告每个进程的内存对象映射
        -s -- 设置更新之间的延迟
        -stats -- 只显示指定字段
        -u -- 与 -o cpu -O 时间相同
        -user -U -- 只显示指定用户拥有的进程 
```
具体字段说明：
```
PID       进程 id
COMMAND   命令
%CPU      cup 占比
TIME      运行时间
#TH       线程数量
#WQ
#PORT
MEM       内存
PURG
CMPRS     表示属于您的进程的压缩数据的字节数（不是位）。
PGRP
PPID
STATE
BOOSTS
%CPU_ME
%CPU_OTHRS
UID

VIRT：virtual memory usage 虚拟内存
1、进程“需要的”虚拟内存大小，包括进程使用的库、代码、数据等
2、假如进程申请100m的内存，但实际只使用了10m，那么它会增长100m，而不是实际的使用量

RES：resident memory usage 常驻内存
1、进程当前使用的内存大小，但不包括swap out
2、包含其他进程的共享
3、如果申请100m的内存，实际使用10m，它只增长10m，与VIRT相反
4、关于库占用内存的情况，它只统计加载的库文件所占内存大小

SHR：shared memory 共享内存
1、除了自身进程的共享内存，也包括其他进程的共享内存
2、虽然进程只使用了几个共享库的函数，但它包含了整个共享库的大小
3、计算某个进程所占的物理内存大小公式：RES – SHR
4、swap out后，它将会降下来
```
示例：
```
top -o -mem：以内存排序
top -o -cpu：CPU排序
top -o -pid：pid排序

或
先输入top
键入大写P – 以 CPU 占用率大小的顺序排列进程列表
键入大写M – 以内存占用率大小的顺序排列进程列表
```

free -m：查看服务器内存使用情况
free -h：GBytes，MBytes，KBytes自行显示列出所有文件系统
```
    Mem 行(第二行)是内存的使用情况。
    Swap 行(第三行)是交换空间的使用情况。
    total 列显示系统总的可用物理内存和交换空间大小。
    used 列显示已经被使用的物理内存和交换空间。
    free 列显示还有多少物理内存和交换空间可用使用。
    shared 列显示被共享使用的物理内存大小。
    buff/cache 列显示被 buffer 和 cache 使用的物理内存大小。
    available 列显示还可以被应用程序使用的物理内存大小。
```

df(disk free)	//显示磁盘的相关信息
		//-a：列出所有文件系统
		//-k：以KBytes的容量列出所有文件系统
		//-m：以MBytes的容量列出所有文件系统
        //-h：GBytes，MBytes，KBytes自行显示列出所有文件系统

du   //du命令用于估算文件或文件夹的磁盘使用情况。
du -sh * | sort -rh
```
-s选项用于显示文件夹的总大小，而不显示其子文件夹的详细信息。
-h选项用于以人类可读的格式显示文件夹大小（例如，使用KB，MB，GB等单位）。
*通配符表示当前目录下的所有文件夹。
sort命令用于排序输出结果。
-r选项用于反向排序，即从大到小排列。
-h选项用于人类可读的排序，以便正确地处理文件夹大小的单位。
```
df命令主要显示文件系统的磁盘使用情况，包括每个挂载点的磁盘空间占用情况；du命令可以显示指定目录或文件的大小，也可以显示整个文件系统的使用情况。
        
shutdown -h now/20：25/+10	//系统管理 立刻/时间点/10分钟后
shutdown -r	//系统立刻重启
reboot		//重启
halt		//关闭系统，等同于shutdown -h now和poweroff

### 网络相关
- ping：检查网络是否通畅或者网络连接速度的命令
  -t 表示将不间断向目标IP发送数据包，直到强迫其停止。
  -l 定义发送数据包的大小，默认为32字节，我们利用它可以最大定义到65500字节。
  -n 定义向目标IP发送数据包的次数，默认为3次。如果网络速度比较慢，3次对我们来说也浪费了不少时间，因为现在我们的目的仅仅是判断目标IP是否存在，那么就定义为一次吧。
- telnet：远程登陆命令
```
telnet 127.0.0.1 8080
Escape character is '^]'.
如果返回Escape character is '^]'. 则代表端口是通的

mac telnet 替代命令
检测8080端口：nc -vz -w 2 127.0.0.1 8080
检测443端口是否开启：nc -vz -w 2 -G 2 $x 443
-v 详细信息
-z 不发数据
-w 后面是数字/秒。
-G tcp连接超时/秒
-u udp 协议，默认tcp

centos命令：nc -w 1 $x 443 < /dev/null && echo ‘domain_ok:’
```
- ftp	在本地主机与远程主机之间传输文件	ftp ftp.sp.net.edu.cn

### 端口相关
```
netstat命令各个参数说明如下：
　　-t : 指明显示TCP端口
　　-u : 指明显示UDP端口
　　-l : 仅显示监听套接字(所谓套接字就是使应用程序能够读写与收发通讯协议(protocol)与资料的程序)
　　-p : 显示进程标识符和程序名称，每一个套接字/端口都属于一个程序。
　　-n : 不进行DNS轮询，显示IP(可以加速操作)
netstat -nltp;（简单的进程用kill -9 端口号杀死）
netstat -natp |grep 21
netstat -ntlp //查看当前所有tcp端口
netstat -ntulp |grep 80   //查看所有80端口使用情况
netstat -an 	//查看网络端口 
netstat -lanp	//查看一台服务器上面哪些服务及端口
```

lsof -i:$PORT"查看应用该端口的程序（$PORT指对应的端口号）

查询端口：sudo lsof -i tcp:port
查看所有端口使用情况：netstat -natp tcp （在mac中使用netstat -tlp tcp或netstat -tlp udp 来指定其后使用的协议）


- Nmap：主要用于检测TCP连接,UDP连接和Unix域套接字的信息。
- ss：检测TCP连接和UDP连接信息,但 ss的输出更加精简,主要包含源和目的连接等基本信息。（Socket Statistics）命令主要功能是显示套接字信息。与netstat命令的使用十分的相似，都是用于显示套接字信息，而ss命令的优势在于它能够显示更多TCP和连接状态的详情信息，并且速度更快更高效。
    * ss -s 列出当前socket详细信息
    * ss -l 显示本地打开的所有端口
    * ss -pl 显示每个进程具体打开的socket
    * ss |wc -l ：列出已建立的连接
    * ss | head -n 3 :列出已建立的连钱
    * ss -lt：监听TCP协议的套接字信息(***)
    * ss -tnl：查看主机监听的端口（-n表示不解析域名，显示的是IP+端口的格式）（***）
    * ss -tlp  |head -10：显示在运行进程的信息（***）
    * ss -tn state established | grep ':8081' | wc -l  查询指定端口的tcp连接数 
        * 		-t 表示只显示TCP连接。
        * 		-n 表示不解析服务名称，只显示数字。
        * 		state established 表示只显示处于建立状态的连接。
        * 		grep ':端口号' 是用来过滤出你感兴趣的端口号的连接。
        * 		wc -l 用来计算行数，即连接数。
- netstat -nat：使用netstat检测TCP连接

### 进程命令
查看进程命令：ps（显示与进程相关的PID号）	
```
ps aux | less  显示所有运行中的进程：
ps ax|grep java  有哪个jvm在跑 
ps a 显示现行终端机下的所有程序，包括其他用户的程序。
ps -A 显示所有程序。
ps c 列出程序时，显示每个程序真正的指令名称，而不包含路径，参数或常驻服务的标示。
ps -e 此参数的效果和指定"A"参数相同。
ps e 列出程序时，显示每个程序所使用的环境变量。
ps f 用ASCII字符显示树状结构，表达程序间的相互关系。
ps -H 显示树状结构，表示程序间的相互关系。
ps -N 显示所有的程序，除了执行ps指令终端机下的程序之外。
ps s 采用程序信号的格式显示程序状况。
ps S 列出程序时，包括已中断的子程序资料。
ps -t<终端机编号> 指定终端机编号，并列出属于该终端机的程序的状况。
ps u 以用户为主的格式来显示程序状况。
ps x 显示所有程序，不以终端机来区分。
```
示例：
```
ps -ef | grep mmp-front  //mmp-front是进程关键字
```

### cut命令
cut 命令从文件的每一行剪切字节、字符和字段并将这些字节、字符和字段写至标准输出。
如果不指定 File 参数，cut 命令将读取标准输入。必须指定 -b、-c 或 -f 标志之一。
```
语法：
cut  [-bn] [file]
cut [-c] [file]
cut [-df] [file]

参数:
-b ：以字节为单位进行分割。这些字节位置将忽略多字节字符边界，除非也指定了 -n 标志。
-c ：以字符为单位进行分割。
-d ：自定义分隔符，默认为制表符。
-f ：与-d一起使用，指定显示哪个区域。
-n ：取消分割多字节字符。仅和 -b 标志一起使用。如果字符的最后一个字节落在由 -b 标志的 List 参数指示的
范围之内，该字符将被写出；否则，该字符将被排除

示例：
who|cut -b 3    //提取who文件每一行的第3个字节
```

### awk命令
AWK 是一种处理文本文件的语言，是一个强大的文本分析工具。
```
语法
awk [选项参数] 'script' var=value file(s)
或
awk [选项参数] -f scriptfile var=value file(s)

选项参数说明：
-F fs or --field-separator fs
指定输入文件折分隔符，fs是一个字符串或者是一个正则表达式，如-F:。
-v var=value or --asign var=value
赋值一个用户定义变量。
-f scripfile or --file scriptfile
从脚本文件中读取awk命令。
-mf nnn and -mr nnn
对nnn值设置内在限制，-mf选项限制分配给nnn的最大块数目；-mr选项限制记录的最大数目。这两个功能是Bell实验室版awk的扩展功能，在标准awk中不适用。
-W compact or --compat, -W traditional or --traditional
在兼容模式下运行awk。所以gawk的行为和标准的awk完全一样，所有的awk扩展都被忽略。
-W copyleft or --copyleft, -W copyright or --copyright
打印简短的版权信息。
-W help or --help, -W usage or --usage
打印全部awk选项和每个选项的简短说明。
-W lint or --lint
打印不能向传统unix平台移植的结构的警告。
-W lint-old or --lint-old
打印关于不能向传统unix平台移植的结构的警告。
-W posix
打开兼容模式。但有以下限制，不识别：/x、函数关键字、func、换码序列以及当fs是一个空格时，将新行作为一个域分隔符；操作符**和**=不能代替^和^=；fflush无效。
-W re-interval or --re-inerval
允许间隔正则表达式的使用，参考(grep中的Posix字符类)，如括号表达式[[:alpha:]]。
-W source program-text or --source program-text
使用program-text作为源代码，可与-f命令混用。
-W version or --version
打印bug报告信息的版本。

示例：
awk '{[pattern] action}' {filenames}   # 行匹配语句 awk '' 只能用单引号
awk '{print $1,$4}' log.txt     //每行按空格或TAB分割，输出文本中的1、4项
awk '{printf "%-8s %-10s\n",$1,$4}' log.txt //格式化输出

awk -F  #-F相当于内置变量FS, 指定分割字符
awk -F, '{print $1,$2}'   log.txt   //使用","分割
awk -F '[ ,]'  '{print $1,$2,$5}'   log.txt //使用多个分隔符.先使用空格分割，然后对分割结果再使用","分割
```

### sort命令
Linux sort命令用于将文本文件内容加以排序。
sort 命令将以默认的方式将文本文件的第一列以ASCII 码的次序排列，并将结果输出到标准输出。
```
参数说明：
-b 忽略每行前面开始出的空格字符。
-c 检查文件是否已经按照顺序排序。
-d 排序时，处理英文字母、数字及空格字符外，忽略其他的字符。
-f 排序时，将小写字母视为大写字母。
-i 排序时，除了040至176之间的ASCII字符外，忽略其他的字符。
-m 将几个排序好的文件进行合并。
-M 将前面3个字母依照月份的缩写进行排序。
-n 依照数值的大小排序。
-u 意味着是唯一的(unique)，输出的结果是去完重了的。
-o<输出文件> 将排序后的结果存入指定的文件。
-r 以相反的顺序来排序。
-t<分隔字符> 指定排序时所用的栏位分隔字符。
+<起始栏位>-<结束栏位> 以指定的栏位来排序，范围由起始栏位到结束栏位的前一栏位。
--help 显示帮助。
--version 显示版本信息。

示例：
sort testfile 
```

### uniq命令
Linux uniq 命令用于检查及删除文本文件中重复出现的行列，一般与 sort 命令结合使用。
```
语法
uniq [-cdu][-f<栏位>][-s<字符位置>][-w<字符位置>][--help][--version][输入文件][输出文件]

参数：
-c或--count 在每列旁边显示该行重复出现的次数。
-d或--repeated 仅显示重复出现的行列。
-f<栏位>或--skip-fields=<栏位> 忽略比较指定的栏位。
-s<字符位置>或--skip-chars=<字符位置> 忽略比较指定的字符。
-u或--unique 仅显示出一次的行列。
-w<字符位置>或--check-chars=<字符位置> 指定要比较的字符。
--help 显示帮助。
--version 显示版本信息。
[输入文件] 指定已排序好的文本文件。如果不指定此项，则从标准读取数据；
[输出文件] 指定输出的文件。如果不指定此选项，则将内容显示到标准输出设备（显示终端）。

示例：
uniq testfile 
```

### nc(netcat)网络工具
nc的作用
（1）实现任意TCP/UDP端口的侦听，nc可以作为server以TCP或UDP方式侦听指定端口
（2）端口的扫描，nc可以作为client发起TCP或UDP连接
（3）机器之间传输文件
（4）机器之间网络测速   
参数：
```
    -l 用于指定nc将处于侦听模式。指定该参数，则意味着nc被当作server，侦听并接受连接，而非向其它地址发起连接。
    -p <port> 暂未用到（老版本的nc可能需要在端口号前加-p参数，下面测试环境是centos6.6，nc版本是nc-1.84，未用到-p参数）
    -s 指定发送数据的源IP地址，适用于多网卡机 
    -u 指定nc使用UDP协议，默认为TCP
    -v 输出交互或出错信息，新手调试时尤为有用
    -w 超时秒数，后面跟数字 
    -z 表示zero，表示扫描时不发送任何数据
```

### curl网络工具
请求网页：curl "www.baidu.com"
提交header和表单：curl -H "name: value" -d "birthyear=1905&press=OK" www.hotmail. com/when/junk.cgi
上传文件：curl -F upload=@localfilename -F press=OK URL

curl下载：
```
#使用内置option：-o(小写)
# curl -o dodo1.jpg http:www.linux.com/dodo1.JPG
#使用内置option：-O（大写)
# curl -O http://www.linux.com/dodo1.JPG
```


### shell脚本
shell		//脚本
		//执行：1.直接执行 2.source执行 3.bash执行
例：

```
#!/bin/bash	//告诉系统如何执行脚本，当采用直接执行时，必须有此行，且放在首行
#Shows system date	//#为注释部分
echo $(date + %F)	//
exit 0		//脚本返回值，正常退出返回0。之后的shell可以用$?获取执行的结果，如echo $?
```

### 统计日志中ip出现的次数
在chunyun.access.log文件中提取GET请求`GET /weixin/weixin_izp/index.html`到指定文件access.log中：
```
grep -r 'GET /weixin/weixin_izp/index.html' ./chunyun.access.log > ~/access.log
```
提取指定行到指定文件：
```
grep -r '订单Query查询入参' debug.log > access.log
```

统计指定文件中日志次数
每行按空格分隔，输出文本中的第一项， 再按逗号分隔，显示第三块区域，排序，每列旁边统计重复次数，并输出到mycount.log文档
cat access.log |awk '{print $1}'|cut -d, -f3| sort|uniq -c > mycount.log
```
	  ...
      1 2bb4055f150c023d
      1 2cb6e05423048137
      1 2fec5822967cff92
	  ...
```

wc -l access.log：统计行数

按时间维度统计日志次数：
cat debug.log | grep '入参'|  awk -F ' ' '{print $2;}' |  awk -F: '{a[$1":"($2-$2%5)]++} END{for(i in a){split(i,t);print i" 至",t[1]":"t[2]+4," 访问 "a[i] " 次" | "sort -t: -k1n -k2n"}}'

```
1.要提取访问量最大的IP，需要先从日志中把IP段提取出来。 

$ cat aa.txt |awk -F " " '{print $1}' 
127.0.0.1 
192.168.1.100 
192.168.1.100 
192.168.1.100 

（PS，此处也可以用cut命令实现。 

$ cut -d " " -f 1 aa.txt 
127.0.0.1 
192.168.1.100 
192.168.1.100 
192.168.1.100） 

2.对IP进行统计，看各IP出现过多少次 

$ cat aa.txt |awk -F " " '{print $1}' |uniq -c 
      1 127.0.0.1 
      3 192.168.1.100 

（PS：wc -l也可以对行数统计，但统计的是整体的，所有行数。不会分类统计） 

3.按IP出现次数从大到小排列 

$ cat aa.txt |awk -F " " '{print $1}' |uniq -c |sort -r 
      3 192.168.1.100 
      1 127.0.0.1 


 sort  | uniq -c | sort -nr | head -10
是计算重复行并且列出重复量最大的N 条记录的基本用法了
```

### wc命令 统计行数
语法：wc [选项] 文件…
说明：该命令统计给定文件中的字节数、字数、行数。如果没有给出文件名，则从标准输入读取。wc同时也给出所有指定文件的总统计数。字是由空格字符区分开的最大字符串。

该命令各选项含义如下：
- c 统计字节数。
- l 统计行数。
- w 统计字数。

### systemd 和 systemctl
Linux 系统应用管理工具 systemd 关于 nginx 的常用命令：
```
systemctl start nginx    # 启动 Nginx
systemctl stop nginx     # 停止 Nginx
systemctl restart nginx  # 重启 Nginx
systemctl reload nginx   # 重新加载 Nginx，用于修改配置后
systemctl enable nginx   # 设置开机启动 Nginx
systemctl disable nginx  # 关闭开机启动 Nginx
systemctl status nginx   # 查看 Nginx 运行状态
```

### supervisor：linux进程管理工具
传统管理linux进程：
```
一般来说都需要自己编写一个能够实现进程start/stop/restart/reload功能的脚本，然后丢到/etc/init.d/下面。
这么做有很多不好的地方，第一我们要编写这个脚本，这就很耗时耗力了。第二，当这个进程挂掉的时候，linux不会自动重启它的，想要自动重启的话，我们还要自己写一个监控重启脚本
```
supervisor管理进程
```
通过fork/exec的方式把这些被管理的进程，当作supervisor的子进程来启动。我们只要在supervisor的配置文件中，把要管理的进程的可执行文件的路径写进去就OK了。
省下了自己写控制脚本的麻烦了，被管理进程作为supervisor的子进程，当子进程挂掉的时候，父进程可以准确获取子进程挂掉的信息的，所以当然也就可以对挂掉的子进程进行自动重启了，当然重启还是不重启，也要看你的配置文件里面有木有设置autostart=true了
```

安装
```
yum install supervisor(centos 等可用）
apt-get install supervisor(Debian/Ubuntu 等可用）
easy-install supervisor或者pip install supervisor
```

安装好supervisor之后，默认是没有生成配置文件的。可以通过以下命令生成配置文件
```
echo_supervisord_conf > /etc/supervisord.conf
```
我们通常是把配置文件放到/etc/下面，当然也可以放到任意路径下面。

重要配置：
1.
[unix_http_server]
file=/tmp/supervisor.sock;
这玩意跟你开启启动时发现启动不了报一个  unix:///tmp/supervisor.sock no such file 有关，解决方案其实也很简单，给它换个别的路径，比如把/tmp/ 换成/var/log/ 这样
2.
serverurl=...
这跟上面一样，如果改路径的话，这地方也要改
3.
[include]
files= /etc/supervisord.d/*.supervisord
这个表示，supervisor 即将接管的子进程的配置，如图上这样配置，那就代表，我们即将使用supervisor 来接管 /etc/supervisord.d/ 下后缀名为 .supervisord 下配置的进程 东东

启动
以下启动顺序由上到下优先级，依次递减；
```
supervisord                                   #默认去找$CWD/supervisord.conf，也就是当前目录
supervisord                                   #默认$CWD/etc/supervisord.conf，也就当前目录下的etc目录
supervisord                                   #默认去找/etc/supervisord.conf的配置文件
supervisord -c /home/supervisord.conf         #到指定路径下去找配置文件
```

示例：jenkins.supervisord
```
[program:jenkins]
#配置的java启动环境
environment=JAVA_HOME=/usr/local/java/bin
#启动命令
command= /usr/local/java/bin/java -jar /root/app/jenkins/jenkins.war
#jar所在文件目录
directory=/root/app/jenkins/
#用户
user=root
stopsignal=INT
#自动启动
autostart=true
#自动重启
autorestart=true
#重启时间1s
startsecs=1
#错误日志
stderr_logfile=/var/log/jenkins_err.log
#标准打印日志,满50MB区分
stdout_logfile=/var/log/jenkins.log
 
[supervisrod]
```

启动：
这个时候千万别直接supervisorctl reload ，不然会直接报错
正确的做法是要先更新上，再reload
命令顺序应该是:  
```
1. supervisorctl update
2. supervisorctl reload
3. supervisorctl status 可以查看状态是否启动正常

systemctl enable supervisorctl  //加入开机自启动
```

### linux配置本地yum源
1. 添加本地repo命令
```
vi /etc/yum.repos.d/CentOS-Base.repo
```
2. 保存配置
```
Centos7 yum源配置 
[Centos7_v3]
name=Centos7.x
enable=1
baseurl=http://80.80.98.207/Centos7.x
gpgcheck=0
```
3. 删除其他repo
```
  692  2021-07-19 14:40:45  root rm -rf CentOS-CR.repo
  693  2021-07-19 14:40:57  root rm -rf CentOS-Debuginfo.repo
  695  2021-07-19 14:41:22  root rm -rf CentOS-Media.repo  
  697  2021-07-19 14:41:34  root rm -rf CentOS-Vault.repo 
  694  2021-07-19 14:41:10  root rm -rf CentOS-fasttrack.repo 
  696  2021-07-19 14:41:28  root rm -rf CentOS-Sources.repo 
```
4. 清除缓存
```
yum clean all
yum makecache
```
5. 查看是否成功
```
yum list 
```


### linux 软链接和硬链接区别
- 【硬连接】
硬连接指通过索引节点来进行连接。在Linux的文件系统中，保存在磁盘分区中的文件不管是什么类型都给它分配一个编号，称为索引节点号(Inode Index)。在Linux中，多个文件名指向同一索引节点是存在的。一般这种连接就是硬连接。硬连接的作用是允许一个文件拥有多个有效路径名，这样用户就可以建立硬连接到重要文件，以防止“误删”的功能。其原因如上所述，因为对应该目录的索引节点有一个以上的连接。只删除一个连接并不影响索引节点本身和其它的连接，只有当最后一个连接被删除后，文件的数据块及目录的连接才会被释放。也就是说，文件真正删除的条件是与之相关的所有硬连接文件均被删除。

- 【软连接】
另外一种连接称之为符号连接（Symbolic Link），也叫软连接。软链接文件有类似于Windows的快捷方式。它实际上是一个特殊的文件。在符号连接中，文件实际上是一个文本文件，其中包含的有另一文件的位置信息。

硬链接文件有两个限制
1)、不允许给目录创建硬链接；
2)、只有在同一文件系统中的文件之间才能创建链接，而且只有超级用户才有建立硬链接权限。
对硬链接文件进行读写和删除操作时候，结果和软链接相同。但如果我们删除硬链接文件的源文件，硬链接文件仍然存在，而且保留了愿有的内容。
这时，系统就“忘记”了它曾经是硬链接文件。而把他当成一个普通文件。
软链接没有硬链接以上的两个限制，因而现在更为广泛使用，它具有更大的灵活性，甚至可以跨越不同机器、不同网络对文件进行链接

### Linux /usr/bin与/usr/local/bin区别:
/usr/bin下面的都是系统预装的可执行程序，会随着系统升级而改变。
/usr/local/bin目录是给用户放置自己的可执行程序的地方，推荐放在这里，不会被系统升级而覆盖同名文件。
如果两个目录下有相同的可执行程序，谁优先执行受到PATH环境变量的影响。

### export命令
Linux export 命令用于设置或显示环境变量。

在 shell 中执行程序时，shell 会提供一组环境变量。export 可新增，修改或删除环境变量，供后续执行的程序使用。export 的效力仅限于该次登陆操作。

语法：export [-fnp][变量名称]=[变量设置值]
* -f 　代表[变量名称]中为函数名称。
* -n 　删除指定的变量。变量实际上并未删除，只是不会输出到后续指令的执行环境中。
* -p 　列出所有的shell赋予程序的环境变量。

示例
```
# export -p //列出当前的环境变量值
# export MYENV //定义环境变量
# export MYENV=7 //定义环境变量并赋值
```

### 三种方法设置环境变量
- 方法一：使用export命令
  - 普通用户即可修改；**仅对当前登录的终端有效**
  - 实例：将路径/home/admin/test/bin添加到环境变量$PATH中
  - $ export PATH=$PATH:/home/admin/test/bin

- 方法二：修改.bashrc文件
  - 普通用户即可修改；对当前登录用户有效。
```
首先，打开.bashrc文件：
$ vim ~/.bashrc
然后，在该文件中，添加如下内容：`
export PATH=$PATH:/home/dabai/test/bin
最后，保存并退出；再执行如下命令，以使修改的环境变量立即生效：
$ source ~/.bashrc
```
- 方法三：修改profile文件
需要root权值；对所有用户都有效。
```
首先，打开profile文件：
# vim /etc/profile
然后，在该文件中，添加如下内容：
export PATH=$PATH:/home/dabai/test/bin
最后，保存并退出；再执行如下命令，以使修改的环境变量立即生效：
$ source /etc/profile
```

### linux配置防火墙
会报错Failed to start iptables.service: Unit iptables.service failed to load: No such file or directory.

在CentOS 7或RHEL 7或Fedora中防火墙由firewalld来管理，

如果要添加范围例外端口 如 1000-2000
语法命令如下：启用区域端口和协议组合
firewall-cmd [--zone=<zone>] --add-port=<port>[-<port>]/<protocol> [--timeout=<seconds>]
此举将启用端口和协议的组合。端口可以是一个单独的端口 <port> 或者是一个端口范围 <port>-<port> 。协议可以是 tcp 或 udp。
实际命令如下：

添加
firewall-cmd --zone=public --add-port=80/tcp --permanent （--permanent永久生效，没有此参数重启后失效）
firewall-cmd --zone=public --add-port=1000-2000/tcp --permanent

重新载入
firewall-cmd --reload
查看
firewall-cmd --zone= public --query-port=80/tcp
删除
firewall-cmd --zone= public --remove-port=80/tcp --permanent

当然你可以还原传统的管理方式。

执行一下命令：

[plain] view plain copy
systemctl stop firewalld  
systemctl mask firewalld

并且安装iptables-services：
[plain] view plain copy
yum install iptables-services

设置开机启动：
[plain] view plain copy
systemctl enable iptables

[plain] view plain copy
systemctl stop iptables  
systemctl start iptables  
systemctl restart iptables  
systemctl reload iptables

保存设置：
[plain] view plain copy
service iptables save

OK，再试一下应该就好使了

开放某个端口 在/etc/sysconfig/iptables里添加

-A RH-Firewall-1-INPUT -m state --state NEW -m tcp -p tcp --dport 8080 -j ACCEPT



## linux脚本编写(如编写一个java -jar启动java程序)

#### 脚本解析
我们经常会见到这样的脚本启动方式：
```
./start.sh
或
./start.sh start
备注：windows为.bat文件
```

常见的脚本至少要有：
```
start   #启动
stop    #停止
status  #状态
info    #信息
help    #帮助
*       #任意输入匹配
```

#### 脚本主要结构
定义变量：
```
BIN_DIR=$bin_abs_path
DEPLOY_DIR=$BIN_DIR/..
JAR_NAME='managesrv.jar'
VM_OPTS="-server -Xmx1g -Xms1g -Xmn256m -XX:PermSize=128m -Xss256k -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:LargePageSizeInBytes=128m -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70"
SPB_OPTS="--spring.profiles.active=pro"
APP_NAME="managesrv"
JAVA=java
```
定义方法：
```
start() {
 stop                       #方法中调用其他方法
 echo "sleep for stopping"  #打印
 sleep 2                    #休眠
 echo "start $APP_NAME "
 echo "exec command : nohup $JAVA $VM_OPTS -jar $DEPLOY_DIR/lib/$JAR_NAME $SPB_OPTS > /dev/null 2>&1 &"
 nohup $JAVA $VM_OPTS -jar $DEPLOY_DIR/lib/$JAR_NAME  > /dev/null 2>&1 &    #执行启动命令
 sleep 3
 boot_id=`ps -ef |grep java|grep $APP_NAME|grep -v grep|awk '{print $2}'`
 echo "app started pid = ${boot_id}"
}
```
定义参数解析：
```
case $1 in  #case判断
start)      #如果命令为./start.sh start
    start   #执行方法动作
    ;;
stop)       #如果命令为./start.sh stop
    stop
    ;;
esac        #与case配对
exit $?     #退出命令
```


#### 脚本示例
```
#!/bin/bash
current_path=`pwd`
case "`uname`" in
    Linux)
		bin_abs_path=$(readlink -f $(dirname $0))
		;;
	*)
		bin_abs_path=`cd $(dirname $0); pwd`
		;;
esac
BIN_DIR=$bin_abs_path
DEPLOY_DIR=$BIN_DIR/..
JAR_NAME='managesrv.jar'
VM_OPTS="-server -Xmx1g -Xms1g -Xmn256m -XX:PermSize=128m -Xss256k -XX:+DisableExplicitGC -XX:+UseConcMarkSweepGC -XX:+CMSParallelRemarkEnabled -XX:+UseCMSCompactAtFullCollection -XX:LargePageSizeInBytes=128m -XX:+UseFastAccessorMethods -XX:+UseCMSInitiatingOccupancyOnly -XX:CMSInitiatingOccupancyFraction=70"
SPB_OPTS="--spring.profiles.active=pro"
APP_NAME="managesrv"
JAVA=java

start() {
 stop
 echo "sleep for stopping"
 sleep 2
 echo "start $APP_NAME "
 echo "exec command : nohup $JAVA $VM_OPTS -jar $DEPLOY_DIR/lib/$JAR_NAME $SPB_OPTS > /dev/null 2>&1 &"
 nohup $JAVA $VM_OPTS -jar $DEPLOY_DIR/lib/$JAR_NAME  > /dev/null 2>&1 &
 sleep 3
 boot_id=`ps -ef |grep java|grep $APP_NAME|grep -v grep|awk '{print $2}'`
 echo "app started pid = ${boot_id}"
}

stop(){
    boot_id=`ps -ef |grep java|grep $APP_NAME|grep -v grep|awk '{print $2}'`
    count=`ps -ef |grep java|grep $APP_NAME|grep -v grep|wc -l`
    if [ $count != 0 ];then
        kill -9 $boot_id
        echo "Stop $APP_NAME success..."
    else
    	echo "$APP_NAME is not running..."
    fi
}

status() {
  echo "=============================status=============================="
  boot_id=`ps -ef |grep java|grep $APP_NAME|grep -v grep|awk '{print $2}'`
  count=`ps -ef |grep java|grep $APP_NAME|grep -v grep|wc -l`
  if [ $count != 0 ];then
       echo "$APP_NAME is running,PID is $boot_id"
  else
       echo "$APP_NAME is not running!!!"
  fi
  echo "=============================status=============================="
}

info() {
  echo "=============================info=============================="
  echo "APP_LOCATION: $DEPLOY_DIR/lib/$JAR_NAME"
  echo "APP_NAME: $APP_NAME"
  echo "VM_OPTS: $VM_OPTS"
  echo "SPB_OPTS: $SPB_OPTS"
  echo "=============================info=============================="
}

help() {
   echo "start: start server"
   echo "stop: shutdown server"
   echo "status: display status of server"
   echo "info: display info of server"
   echo "help: help info"
}

case $1 in
start)
    start
    ;;
stop)
    stop
    ;;
status)
    status
    ;;
info)
    info
    ;;
help)
    help
    ;;
*)
    help
    ;;
esac
exit $?
```

**命令解析：ps -ef|grep xxx.jar|grep -v grep|awk '{print $2}'**

- 直接使用：ps -ef|grep java
```
ps -ef|grep java
root     1583778       1  0 Mar20 ?        00:59:50 java -jar xxx.jar
root     2628773       1  1 Apr02 ?        00:16:06 java -jar xxxx.jar
root     2777713 2773423  0 13:54 pts/0    00:00:00 grep --color=auto java
```
- grep -v grep：查找除了grep下的所有信息
```
ps -ef|grep java|grep -v grep
root     1583778       1  0 Mar20 ?        00:59:50 java -jar xxx.jar
root     2628773       1  1 Apr02 ?        00:16:07 java -jar xxxx.jar
```

- awk '{print $2}'：取第二个字段输出
所以代码的意思就是 查找除了grep操作的xxx.jar的进程之外的所有进程这一行信息的第二个字段的值并打印（即pid进程号）

- grep -n  打印行号
- grep -E = egrep 匹配正则表达式
- grep -i 忽略大小写

### Memory和Swap
Memory指机器物理内存
在详细介绍swap之前，需要知道的是计算机内存分为物理内存与虚拟内存(注意虚拟内存和虚拟地址空间的区别)。
物理内存是计算机的实际内存大小，由RAM芯片组成。虚拟内存则是虚拟出来的、使用磁盘代替内存。虚拟内存的出现，让机器内存不够的情况得到部分解决。当程序运行起来由操作系统做具体虚拟内存到物理内存的替换和加载(相应的页与段的虚拟内存管理)。这里的虚拟内存即所谓的swap。
当用户提交程序，然后产生进程在机器上运行。机器会判断当前物理内存是否还有空闲允许进程调入内存运行，如果有则直接调入内存进行；如果没有，则会根据优先级选择一个进程挂起，把该进程交换到swap中等待，然后把新的进程调入到内存中运行。根据这种换入和换出，实现了内存的循环利用，让用户感觉不到内存的限制。从这也可以看出swap扮演了一个非常重要的角色，就是暂存被换出的进程。
内存与swap之间是按照内存页为单位来交换数据的，一般Linux中页的大小设置为4Kb。而内存与磁盘则是按照块来交换数据的。
从上可以看出，当物理内存使用完或者达到一定比例之后，我们可以使用swap做临时的内存使用。当物理内存和swap都被使用完那么就会出错，如：out of memory。

### System load 
System load:  0.3193359375
load，其全称是Linux system load averages ，即linux系统负载平均值。
注意两个关键词：一个是“负载”，它衡量的是task（linux 内核中用于描述一个进程或者线程）对系统的需求（CPU、内存、IO等等），第二个关键词是“平均”，它计算的是一段时间内的平均值，分别为 1、5 和 15 分钟值。
system load average由内核负载计算并记录在/proc/loadavg 文件中， 用户态的工具（比如uptime，top等等）读的都是这个文件

系统负载是指系统中等待运行的进程数和不可中断进程数的总和。系统负载不是单个指标，而是多个相关指标的总和。这些指标包括正在运行的进程、处于不可中断状态的进程，以及在等待I/O完成的进程。

判断系统负载是否合理，需要考虑以下因素：
系统CPU的核心数量
系统负载的长期平均水平
系统负载的瞬时水平
系统负载对性能的影响

一般来说，单核CPU的系统：
如果平均负载小于1.0，系统处于正常状态。
如果平均负载在1.0到3.0之间，系统可能处于高负载状态，但仍可接受。
如果平均负载大于3.0，可能需要优化或升级系统。

多核CPU的系统：
负载应该是CPU核心数量的倍数。例如，对于4核CPU，负载应该接近4.0。
如果系统负载高于CPU核心数量，可能是因为有进程在等待I/O操作完成。
实时监控系统负载是一个好方法，可以使用uptime, top, htop, vmstat等命令。

解决方案：
如果系统负载长期高且持续，需要进行性能分析，找出系统瓶颈所在。
优化应用程序代码，减少不必要的资源消耗。
增加CPU或优化硬件配置。
使用更有效的进程调度算法。
使用更高效的I/O调度策略。
配置或优化内存和交换空间的使用。
请根据具体情况采取相应措施。
