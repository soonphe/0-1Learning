# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## 配置ssh免密登录

1.检查是否安装 ssh -version
	ssh localhost

2.在用户目录，使用ls -a查看隐藏文件 .ssh

3.创建key
ssh-keygen -t dsa -P '' -f /root/.ssh/id_dsa

这里解释一下命令的含义(注意区分大小写):
ssh-keygen代表生成密钥;-t表示生成密钥的类型;-P提供密语；-f指定生成的文件.
这个命令执行完毕后会在.ssh文件夹下生成两个文件，
分别是id_dsa、id_dsa.pub,这是SSH的一对私钥和公钥，就像是钥匙和锁。
下一步将id_dsa.pub追加到授权的key中,键入一下命令：

cat /root/.ssh/id_dsa.pub >> /root/.ssh/authorized_keys
此时，免密码登录本机就配置完成了，下面再次输入ssh localhost进行验证，出现下图所示信息代表配置成功了
~~~~
ssh-keygen -t dsa -P '' -f ~/.ssh/id_dsa
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
chmod 0600 ~/.ssh/authorized_keys
~~~~

ssh localhost（即可显示登录成功）