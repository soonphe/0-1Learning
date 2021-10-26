# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## 跨域获取cookie

domain表示的是cookie所在的域，默认为请求的地址，
如网址为www.test.net/test/test.aspx，那么domain默认为www.test.net。

而跨域访问，如域A为t1.test.com，域B为t2.test.com，
一般情况下，域A和域B对应的cookie是不能互相访问的
那么在域A生产一个令域A和域B都能访问的cookie就要将该cookie的domain设置为.test.com；

例：
线上地址：https://jzedu-recruit-betaa.djtest.cn/#/clue/list  //携带有cookie验证，domain为.djtest.cn
修改本机hosts：127.0.0.1  jzedu-recruit-stable.djtest.cn——将线上cookie验证指向本机后台服务
本机后台服务器启动
nginx配置80端口转发：本地真实服务地址：proxy_pass http://localhost:8080/;
最终访问线上地址：http://jzedu-recruit-stable.djtest.cn/#/clue/list
就能通过本机后台cookie验证











