# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## linux


### github免密登录
- ssh生成命令：
- ssh-keygen -t rsa -C “your_email@example.com” （t为类型，c为注释）
- ssh-keygen -t rsa -b 2048 -C "<comment>"
- ssh公钥目录：cd ~/.ssh
- 将新生成的key添加到ssh-agent中：ssh-add ~/.ssh/id_rsa（Mac 系统内置了一个 Keychain 的服务及其管理程序，可以方便的帮你管理各种秘钥，其中包括 ssh 秘钥）
- 将ssh key添加到GitHub中：打开id_rsa.pub文件，里面的信息即为SSH key，将这些信息复制到GitHub的Add SSH key页面即可
- 测试ssh连接：ssh -T git@github.com

###  配置ssh免密登录

#### 客户端生成公私钥
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
命令解析：ssh-keygen代表生成密钥; -t表示生成密钥的类型; -P提供密语； -f指定生成的文件；c为注释

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


#### 将公钥 SSH 密钥添加到 GitLab或目标服务器
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

### #ssh登录
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


### 进程与线程
关系：

一个进程可以创建和撤销另一个线程，同一个进程中的线程可以并发执行。

**死锁的必要条件，怎么处理死锁。**

**Window内存管理方式：段存储，页存储，段页存储。**

**进程的几种状态和转换**

**什么是虚拟内存。**

**Linux下的IPC几种通信方式**

1. 管道(pipe):管道可用于具有亲缘关系的进程间的通信，是一种半双工的方式，数据只能单向流动，允许一个进程和另一个与它有公共祖先的进程之间进行通信。
2. 命名管道(named pipe):命名管道克服了管道没有名字的限制，同时除了具有管道的功能外(也是半双工)，它还允许无亲缘关系进程间的通信。命令管道在文件系统中有对应的文件名。命令管道通过命令mkfifo或系统调用mkfifo来创建。
3. 信号(signal):信号是比较复杂的通信方式，用于通知接收进程有某种事件发生了，除了进程间通信外，进程还可以发送信号给进程本身。
4. 消息队列:消息队列是消息的链接表，包括Posix消息队列和system V消息队列。有足够权限的进程可以向队列中添加消息，被赋予读权限的进程可以读走队列中的消息。消息队列克服了信号承载信息少，管道只能承载无格式字节流以及缓冲区大小受限等缺点。
5. 共享内存:使得多个进程可以访问同一块内存空间，是最快的IPC形式。是针对其他通信机制运行效率低而设计的。往往与其他通信机制，如信号量结合使用，来达到进程间的同步及互斥。
6. 内存映射:内存映射允许任何多个进程间通信每一个使用该机制的进程通过把一个共享的文件映射到自己的进程地址空间来实现它。
7. 信号量(semaphore):主要作为进程间以及同一进程不同线程之间的同步手段。
8. 套接字(Socket):更为一般的进程间通信机制，可用于不同机器之间的进程间通信。

**逻辑地址、物理地址的区别**

**进程调度算法**

**线程之间不存在通信,因为本来就共享同一片内存**

各个线程可以访问进程中的公共变量，资源，所以使用多线程的过程中需要注意的问题是如何防止两个或两个以上的线程同时访问同一个数据，以免破坏数据的完整性。数据之间的相互制约包括
1、直接制约关系,即一个线程的处理结果,为另一个线程的输入,因此线程之间直接制约着，这种关系可以称之为同步关系
2、间接制约关系，即两个线程需要访问同一资源,该资源在同一时刻只能被一个线程访问,这种关系称之为线程间对资源的互斥访问，某种意义上说互斥是一种制约关系更小的同步

### linux线程间同步
* 临界区
    * 每个线程中访问临界资源的代码,一个线程拿到临界区的所有权后,可以多次重入.只有前一个线程放弃,后一个才可以进来
* 互斥量
    * 互斥量就是简化版的信号量
    * 互斥量由于也有线程所有权的概念，故也只能进行线程间的资源互斥访问，不能由于线程同步
    * 由于互斥量是内核对象，因此其可以进行进程间通信，同时还具有一个很好的特性，就是在进程间通信时完美的解决了"遗弃"问题
* 信号量
    * 信号量的用法和互斥的用法很相似，不同的是它可以同一时刻允许多个线程访问同一个资源，PV操作
    * 信号量也是内核对象,也可以进行进程间通信

### linux进程间通信
* 管道
    * 半双工
    * 只能在具有父子关系的进程间使用
    * FIFO的共享内存实现
* 命名管道
    * 以linux中的文件的形式存在
    * 不要求进程有用父子关系,甚至通过网络也是可以的
    * 半双工
* 信号量
    * 有up和down操作
    * 共享内存实现
* 消息队列
    * 队列数据结构
    * 共享内存实现
* 信号
    * 较为复杂的方式,用于通知进程某事件已经发生.例如kill信号
* 共享内存
    * 将同一个物理内存附属到两个进程的虚拟内存中
* socket
    * 可以跨网络通信