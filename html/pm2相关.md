# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## pm2相关
pm2是一个带有负载均衡功能的Node应用的进程管理器，可以让你简单方便的管理node进程，以达到持续运行node服务的目的

安装pm2：
npm install pm2 -g

### 相关命令
- 启动服务： 
  - pm2 start app.js                //启动app.js应用
  - pm2 start app.js --name demo    //启动应用并设置name
  - pm2 start app.sh                //脚本启动
- 查看启动列表 pm2 list
- 停止服务：
  - pm2 stop all               //停止所有应用
  - pm2 stop [AppName]        //根据应用名停止指定应用
  - pm2 stop [ID]             //根据应用id停止指定应用
- 删除应用：
  - pm2 delete all               //关闭并删除应用
  - pm2 delete [AppName]        //根据应用名关闭并删除应用
  - pm2 delete [ID]            //根据应用ID关闭并删除应用
- 创建开机自启动 pm2 startup
- 更新PM2
  - pm2 updatePM2
  - pm2 update
- 监听模式 pm2 start app.js --watch    //当文件发生变化，自动重启
- 静态服务器 pm2 serve ./dist 9090        //将目录dist作为静态服务器根目录，端口为9090
- 启用集群模式（自动负载均衡）
  - pm2 start app.js -i max  //max表示PM2将自动检测可用CPU的数量并运行尽可能多的进程  //max可以自定义，如果是4核CPU，设置为2者占用2个
- 重新启动 pm2 restart app.js        //同时杀死并重启所有进程。短时间内服务不可用。生成环境推荐使用reload
- 0秒停机重新加载 
  - pm2 reload app.js        //重新启动所有进程，始终保持至少一个进程在运行
  - pm2 gracefulReload all   //优雅地以群集模式重新加载所有应用程序
- 查看每个应用程序占用情况 pm2 monit
- 显示应用程序所有信息 
  - pm2 show [Name]      //根据name查看
  - pm2 show [ID]        //根据id查看
- 日志查看 
  - pm2 logs            //查看所有应用日志
  - pm2 logs [Name]    //根据指定应用名查看应用日志
  - pm2 logs [ID]      //根据指定应用ID查看应用日志
- 保存当前应用列表 pm2 save
- 重启保存的应用列表 pm2 resurrect
- 清除保存的应用列表 pm2 cleardump
- 保存并恢复PM2进程 pm2 update

### 配置文件示例（实际使用自行删除）
```
module.exports = {
    apps : [{
        name      : 'API',      //应用名
        script    : 'app.js',   //应用文件位置
        env: {
            PM2_SERVE_PATH: ".",    //静态服务路径
            PM2_SERVE_PORT: 8080,   //静态服务器访问端口
            NODE_ENV: 'development' //启动默认模式
        },
        env_production : {
            NODE_ENV: 'production'  //使用production模式 pm2 start ecosystem.config.js --env production
        },
        instances:"max",          //将应用程序分布在所有CPU核心上,可以是整数或负数
        watch:true,               //监听模式
        output: './out.log',      //指定日志标准输出文件及位置
        error: './error.log',     //错误输出日志文件及位置，pm2 install pm2-logrotate进行日志文件拆分
        merge_logs: true,         //集群情况下，可以合并日志
        log_type:"json",          //日志类型
        log_date_format: "DD-MM-YYYY",  //日志日期记录格式
    }],
    deploy : {
        production : {
            user : 'node',                      //ssh 用户
            host : '212.83.163.1',              //ssh 地址
            ref  : 'origin/master',             //GIT远程/分支
            repo : 'git@github.com:repo.git',   //git地址
            path : '/var/www/production',       //服务器文件路径
            post-deploy : 'npm install && pm2 reload ecosystem.config.js --env production'  //部署后的动作
        }
    }
};
```
