# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## linux常用命令

### java -jar（后台运行一个jar包）
java -jar执行并打印日志：nohup java -jar tron-wallet-1.1-SNAPSHOT.jar >> tron-wallet-1.1.log &

| ：管道符命令，作用将两个命令隔开，管道符左边命令的输出就会作为管道符右边命令的输入
& ：将下面这个命令放到后台运行
示例：
```
cp -R original/dir/ backup/dir/
```
这个命令的目的是将 original/dir/ 的内容递归地复制到 backup/dir/ 中。
虽然看起来很简单，但是如果原目录里面的文件太大，在执行过程中终端就会一直被卡住。
所以，可以在命令的末尾加上一个 & 号，将这个任务放到后台去执行：

### 用户相关
* `/etc/passwd`： 用户账户的详细信息在此文件中更新。
* `/etc/shadow`： 用户账户密码在此文件中更新。
* `/etc/group`： 新用户群组的详细信息在此文件中更新。
* `/etc/gshadow`： 新用户群组密码在此文件中更新。

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
```

文件操作相关：
```$xslt
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
```
示例：
```
top -o -mem：以内存排序
top -o -cpu：CPU排序
top -o -pid：pid排序
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
        
shutdown -h now/20：25/+10	//系统管理 立刻/时间点/10分钟后
shutdown -r	//系统立刻重启
reboot		//重启
halt		//关闭系统，等同于shutdown -h now和poweroff


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

grep -r 'GET /weixin/weixin_izp/index.html' ./chunyun.access.log > ~/access.log 
cat access.log |awk '{print $1}'|cut -d, -f3|sort|uniq -c > mycount.log    
```$xslt
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

### systemd 和 systemctl
Linux 系统应用管理工具 systemd 关于 nginx 的常用命令：
```
systemctl start nginx    # 启动 Nginx
systemctl stop nginx     # 停止 Nginx
systemctl restart nginx  # 重启 Nginx
systemctl reload nginx   # 重新加载 Nginx，用于修改配置后
systemctl enable nginx   # 设置开机启动 Nginx
systemctl disable nginx  # 关闭开机启动 Nginx
systemctl status nginx   # 查看 Nginx 运行状态
```

### supervisor：linux进程管理工具
传统管理linux进程：
```
一般来说都需要自己编写一个能够实现进程start/stop/restart/reload功能的脚本，然后丢到/etc/init.d/下面。
这么做有很多不好的地方，第一我们要编写这个脚本，这就很耗时耗力了。第二，当这个进程挂掉的时候，linux不会自动重启它的，想要自动重启的话，我们还要自己写一个监控重启脚本
```
supervisor管理进程
```$xslt
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



 