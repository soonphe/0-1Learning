# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## linux脚本(java -jar启动java程序)

### 脚本解析
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

### 脚本主要结构
定义变量：
```
VM_OPTS="-server -Xmx1g -Xms1g -Xmn256m "
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


### 脚本示例
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

### jvm参数说明：
```
-server 
-Xmx1g              ：设置JVM最大可用内存.默认物理内存的1/4(<1GB)
-Xms1g              ：置JVM初始内存.默认物理内存的1/64(<1GB)。此值可以设置与-Xmx相同,以避免每次垃圾回收完成后JVM重新分配内存.
-Xmn256m            ：设置年轻代大小.整个堆大小=年轻代大小 + 年老代大小 + 持久代大小.持久代一般固定大小为64m,所以增大年轻代后,将会减小年老代大小.此值对系统性能影响较大,Sun官方推荐配置为整个堆的3/8.
-XX:PermSize=128m   ：设置老年代代大小。默认物理内存的1/64
-Xss256k            ：设置每个线程的堆栈大小
-XX:+DisableExplicitGC                  ：关闭System.gc()
-XX:+UseConcMarkSweepGC                 ：设置年老代为并发收集.测试中配置这个以后,-XX:NewRatio=4的配置失效了,原因不明.所以,此时年轻代大小最好用-Xmn设置.
-XX:+CMSParallelRemarkEnabled           ：降低标记停顿
-XX:+UseCMSCompactAtFullCollection      ：在FULL GC的时候， 对年老代的压缩
-XX:LargePageSizeInBytes=128m           ：内存页的大小不可设置过大， 会影响Perm的大小
-XX:+UseFastAccessorMethods             ：原始类型的快速优化
-XX:+UseCMSInitiatingOccupancyOnly      ：使用手动定义初始化定义开始CMS收集
-XX:CMSInitiatingOccupancyFraction=70   ：使用cms作为垃圾回收，使用70％后开始CMS收集
-Xdebug         ：是通知JVM工作在DEBUG模式下，
-Xrunjdwp       ：是通知JVM使用(java debug wire protocol)来运行调试环境。该参数同时了一系列的调试选项：
transport指定了调试数据的传送方式，dt_socket是指用SOCKET模式，另有dt_shmem指用共享内存方式，其中，dt_shmem只适用于Windows平台。
server参数是指是否支持在server模式的VM中.
onthrow指明，当产生该类型的Exception时，JVM就会中断下来，进行调式。该参数可选。
launch指明，当JVM被中断下来时，执行的可执行程序。该参数可选
suspend指明，是否在调试客户端建立起来后，再执行JVM。 
```