# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## linux常用命令

### java -jar 后台运行一个jar包
java -jar执行并打印日志：
bin]# nohup java -jar tron-wallet-1.1-SNAPSHOT.jar >> tron-wallet-1.1.log &
查看api请求记录：
tail -100f /www/wwwlogs/api.volcanogame.vip.log  |grep POST

### 文件相关
打开文件：open 路径

新建文件：touch test.txt
touch 文件	//自动更新文件为当前时间

cd 绝对路径
cd ~
cd -		//返回刚才访问的目录
pwd -P		//显示当前路径；参数：-P显示出当前路径，而非使用链接(link)路径
ls -al 目录	//-a：列出所有文件（含属性与隐藏文件） 
		//-l：列出文件的详细信息（创建者，创建时间，权限）
		//-s：打印文件大小
		//-t：按时间对文件排序
ls -al --full-time 目录	//呈现文件的修改时间
mkdir -m	//创建文件 -m：权限
		//-p：连同上层“空”目录一起删除
rm -r test	//将整个目录删除，不管test是否为空 -f不用提示确认删除-r删除目录
   mv 文件 目录	//将文件移动到目录
   mv 文件 新文件名	//文件重命名
   mv 文件1 文件2 目录	//将文件1和文件2移动到目录

cp 路径文件 目录	//复制文件，同时可以重命名
cp 路径文件 .	//将路径文件复制到当前目录
cp 目录1 目录2	//将目录1下的所有文件复制到目录2


### 查看文件及内容
find path option	//在一个目录中搜索文件，可以指定一个匹配条件（文件名，文件类型，用户-user，时间戳mtime，权限prem）
tail 		//将某个文件的最后几行显示到终端上
		//-f 监视File文件增长
		//-c 字节位置读取指定文件
        //-n 行位置读取指定文件
cat -n 文件	//查看文件 -n：打印行号
		//-b去除空白行
		//-A显示时文件中的[tab]会以“^I”表示，而空格依然显示空格。断行符是以”$”表示
cat error.log | grep -C 5 'nick' 显示file文件里匹配foo字串那行以及上下5行
cat error.log | grep -B 5 'nick' 显示foo及前5行
cat error.log | grep -A 5 'nick' 显示foo及后5行

nl 文件		//打印文件内容，且添加行号（默认忽略空白行）
		//-b a 空白行打印行号
		//-b a -n rz行号前自动补零（默认6位）
        //-b a -n rz -w 3行号改为3位
        
grep		//文件搜索
		//-b 打印匹配行前面打印该行所在的块号码
		//-c 只打印匹配的行数，不显示匹配的内容
		//-i 忽略大小写
		//-n 打印行号
		//-v 反向检索，只显示不匹配的行
例：ll|grep test.txt	//在ls显示的内容中查看包含test的行


less + 日志（ *空格* 翻页查看。）less不必读整个文件，加载速度会比more更快。
less -N /etc/profile 显示行号
shift + G 命令到文件尾部  然后输入 ？加上你要搜索的关键字例如 ？1213
shift+n  关键字之间进行切换
less的常用动作命令:
    Space 向下滚动一屏；
    /字符串：向下搜索“字符串”的功能
    ?字符串：向上搜索“字符串”的功能
    n：重复前一个搜索（与 / 或 ? 有关）
    N：反向重复前一个搜索（与 / 或 ? 有关）
    b  向后翻一页 (backword)；    
    d  向后翻半页
    f (forword)向下滚动一屏； 

more 是我们最常用的工具之一，最常用的就是显示输出的内容，然后根据窗口的大小进行分页显示，然后还能提示文件的百分比；
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
		// /字符串：文本查找操作，用于从当前光标所在位置开始向文件尾部查找指定字符串的内容，查找的字符串会被加亮显示；(按n查找下一个)
		// ？name：文本查找操作，用于从当前光标所在位置开始向文件头部查找指定字符串的内容，查找的字符串会被加亮显示；(按n查找下一个)
		//x向后删除一个字符，nx向后删除n个字符
		//X向前删除	
		//dd删除光标所在行
		//yy复制光标所在行
		// :wq!保存后离开
		// :q!不保存离开
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
  
### grep文本搜索  
查看文件中的字符串或者进程：grep
grep -a 'nginx' /etc/nginx/nginx.conf	//在nginx.conf文件中查找nginx字符串
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
