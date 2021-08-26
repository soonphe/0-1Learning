# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

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

