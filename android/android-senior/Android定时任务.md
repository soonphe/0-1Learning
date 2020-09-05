# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Andorid 定时任务


android定时任务：
1. 使用timer
timer = new Timer();
        //游戏倒计时
        timer.schedule(new TimerTask() {
            @Override
            public void run() {

            }
        }, 1, 1000);
        
        
2.使用handler
            handler = new Handler();
            handler.postDelayed(() -> {
                //要执行的操作
            }, 3000);
