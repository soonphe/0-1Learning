## ssh远程登录
- ssh生成命令：
  - ssh-keygen -t rsa -C “your_email@example.com” （t为类型，c为注释）
  - ssh-keygen -t rsa -b 2048 -C "<comment>" 
- ssh公钥目录：~/.ssh
- 将新生成的key添加到ssh-agent中：ssh-add ~/.ssh/id_rsa（Mac 系统内置了一个 Keychain 的服务及其管理程序，可以方便的帮你管理各种秘钥，其中包括 ssh 秘钥）
- 将ssh key添加到GitHub中：打开id_rsa.pub文件，里面的信息即为SSH key，将这些信息复制到GitHub的Add SSH key页面即可
- 测试ssh连接：ssh -T git@github.com

### mac使用public_key与private_key登录远程服务器：
1. 在mac下生成public_key与private_key，生成的密钥在~/.ssh/下面
2. 把mac下刚生成的public_key "id_rsa.pub"文件拷贝一份到远端服务器即将需要登录用户家目录下的.ssh/目录下，并命名为authorized_keys.
3. 最后修改本机mac下配置文件，~/.ssh/config，格式如下图
   Host test1
   Hostname 192.168.35.125
   Port 22
   User root
   IdentityFile ~/.ssh/id_rsa
   4.直接执行 ssh test1即可达到所记录的远端服务器

用Terminus登录则创建Keys，选择私钥id_rsa即可

碰到问题：‘Permissions 0644 for '/Users/liuml/.ssh/id_rsa_tz' are too open.’
解决：chmod 600 *