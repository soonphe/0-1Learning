# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## linux常用命令

### java -jar（后台运行一个jar包）
java -jar执行并打印日志：nohup java -jar tron-wallet-1.1-SNAPSHOT.jar >> tron-wallet-1.1.log &

### 用户相关
* `/etc/passwd`： 用户账户的详细信息在此文件中更新。
* `/etc/shadow`： 用户账户密码在此文件中更新。
* `/etc/group`： 新用户群组的详细信息在此文件中更新。
* `/etc/gshadow`： 新用户群组密码在此文件中更新。

### 文件操作相关
open 路径：打开文件
find path option ：查看文件路径，可以指定一个匹配条件（文件名-name，文件类型-type，用户-user，时间戳mtime，权限prem）
```
-X                      -- warn if filename contains characters special to xargs
-d                      -- depth first traversal
```
示例：
```
find / -name 文件名
find / -type f -name "*.log"
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
        
### linux vi文本操作跳转：
gg	# 进入行首
dG	# 文件内容将被全部清空
dd	# 清除一行
ctrl-f # 整页翻页 f就是forword 
ctrl-b # 整页翻页 b就是backward
gg # 跳转文件第一行 -也可输入 :0 或者 :1   回车
G # 移动到这个文件的最后一行  -也可输入 :$   回车
nG #  n 为数字，移动到这个文件的第n行.


### 传输文件
scp -r /home/demo root@服务器IP:/root	//将本极demo目录拷贝到服务器IP的/root目录下

### 服务器内存、磁盘
top		//动态观察程序的变化
		//-d 更新秒数 -p 指定某些PID
		//指定过程中使用按键指令
        //P以CPU使用资源排序，M以Memory使用资源排序，N以PID排序
free -m：查看服务器内存使用情况
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

### 进程命令
查看进程命令：ps（显示与进程相关的PID号）	
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

示例：
ps -ef | grep mmp-front  //mmp-front是进程关键字

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
