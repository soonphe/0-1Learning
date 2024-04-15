# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## github免密登录
- ssh生成命令：
- ssh-keygen -t rsa -C “your_email@example.com” （t为类型，c为注释）
- ssh-keygen -t rsa -b 2048 -C "<comment>"
- ssh公钥目录：cd ~/.ssh
- 将新生成的key添加到ssh-agent中：ssh-add ~/.ssh/id_rsa（Mac 系统内置了一个 Keychain 的服务及其管理程序，可以方便的帮你管理各种秘钥，其中包括 ssh 秘钥）
- 将ssh key添加到GitHub中：打开id_rsa.pub文件，里面的信息即为SSH key，将这些信息复制到GitHub的Add SSH key页面即可
- 测试ssh连接：ssh -T git@github.com

## 配置ssh免密登录

### 客户端生成公私钥
```
检查是否安装ssh命令
ssh localhost

在用户下的.ssh目录，如果没有可以使用ls -a可以查看隐藏文件
cd ~/.ssh

生成秘钥命令
ssh-keygen -t rsa -C “your_email@example.com”
ssh-keygen -t dsa -P '' -f /root/.ssh/id_dsa
ssh-keygen -t rsa -C "" -b 4096
```
命令解析：ssh-keygen代表生成密钥; -t表示生成密钥的类型; -P提供密语； -f指定生成的文件.

这个命令执行完毕后会在.ssh文件夹下生成两个文件，分别是id_dsa、id_dsa.pub,这是SSH的一对私钥和公钥，就像是钥匙和锁。

说明： RSA 与 DSA 都是非对称加密算法
- DSA 只能用于数字签名，而无法用于加密（某些扩展可以支持加密）；
- RSA 即可作为数字签名，也可以作为加密算法。不过作为加密使用的 RSA 有着随密钥长度增加，性能急剧下降的问题。
- ECC（Elliptic Curves Cryptography）：椭圆曲线算法。相同密钥长度下，安全性能更高。计算量小，处理速度快，在私钥的处理速度上（解密和签名），ECC远 比RSA、DSA快得多。存储空间占用小 ECC的密钥尺寸和系统参数与RSA、DSA相比要小得多， 所以占用的存储空间小得多。

>接下来，系统将提示您输入用于保存 SSH 密钥对的文件路径。
如果您还没有 SSH 密钥对，请按 Enter 使用建议的路径。使用建议的路径通常会允许您的 SSH 客户端自动使用 SSH 密钥对，而无需额外配置。
如果你已经有了一个建议的文件路径的SSH密钥对，则需要输入什么主机此SSH密钥对将在您使用一个新的文件路径和申报.ssh/config文件中，看到工作与非默认的SSH密钥对路径
进行更多信息。
输入文件路径后，系统将提示您输入密码以保护 SSH 密钥对。最佳做法是为 SSH 密钥对使用密码，但这不是必需的，您可以通过按 Enter 跳过创建密码。
注意：
 如果要更改 SSH 密钥对的密码，可以使用
 ssh-keygen -p <keyname>.


### 将公钥 SSH 密钥添加到 GitLab或目标服务器
- Gitlab：
>如果您手动复制了您的公共 SSH 密钥，请确保您复制了ssh-rsa以您的电子邮件开头和结尾的整个密钥。
或者，您可以通过运行ssh -T git@example.com（替换example.com为您的 GitLab 域）并验证您是否收到Welcome to GitLab消息来测试您的设置。

- 目标服务器：
可以复制粘贴id_dsa.pub中的内容或使用scp上传文件到目标服务器
```
将文件目录上传到目标服务器/home路径下
scp -r 文件目录 root@IP:/home/
```
将id_dsa.pub追加到授权key中：
```
cat ~/.ssh/id_dsa.pub >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

### ssh登录
```
ssh root@目标IP
```
也可以配置本机hosts或~/.ssh/config文件访问目标服务器：
修改本机mac下配置文件，~/.ssh/config，格式如下
```
   Host test1              # 设置ssh host缩写
   Hostname 192.168.35.125 # 服务器ip
   Port 22                 # 服务器用户名
   User root               # 服务器端口
   IdentityFile ~/.ssh/id_rsa  # 密钥
   # 注意：可以添加多个服务器

```
执行 `ssh test1`即可达到所记录的远端服务器

### 问题定位：
- ssh不能登录，使用ssh user@IP -v查看详细日志，找到原因并解决。
.ssh目录要700, authorized_keys文件要600

- 检查设置/etc/ssh/sshd_config文件
StrictModes no
AuthorizedKeysFile .ssh/authorized_keys

- 删除/root/.ssh/known_hosts文件

- 重启ssh
systemctl restart sshd.service

