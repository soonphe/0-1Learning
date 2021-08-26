# 0-1Learning

![alt ](../static/common/svg/luoxiaosheng.svg "公众号")
![alt ](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt ](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt ](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Ansible自动化运维
ansible是新出现的自动化运维工具，基于Python开发，集合了众多运维工具（puppet、cfengine、chef、func、fabric）的优点，实现了批量系统配置、批量程序部署、批量运行命令等功能。
ansible是基于模块工作的，本身没有批量部署的能力。真正具有批量部署的是ansible所运行的模块，ansible只是提供一种框架。

其他工具比较：
terraform 和ansible的区别, terraform描述最终状态, ansible对应过程.

### Ansible特性
- 模块化：调用特定的模块，完成特定任务
- 有Paramiko，PyYAML，Jinja2（模板语言）三个关键模块
- 支持自定义模块
- 基于Python语言实现
- 部署简单，基于python和SSH(默认已安装)，agentless
- 安全，基于OpenSSH
- 支持playbook编排任务
- 幂等性：一个任务执行1遍和执行n遍效果一样，不因重复执行带来意外情况
- 无需代理不依赖PKI（无需ssl）
- 可使用任何编程语言写模块
- YAML格式，编排任务，支持丰富的数据结构
- 较强大的多层解决方案


### Ansible核心构成
- ansible（主体）：ansible的核心程序，提供一个命令行接口给用户对ansible进行管理操作；
- Host Inventory（主机清单）：为Ansible定义了管理主机的策略。一般小型环境下我们只需要在host文件中写入主机的IP地址即可，但是到了中大型环境我们有可能需要使用静态inventory或者动态主机清单来生成我们所需要执行的目标主机。
- Core Modules（核心模块）：Ansible执行命令的功能模块，多数为内置的核心模块。
- Custom Modules（拓展模块）：如何ansible自带的模块无法满足我么你的需求，用户可自定义相应的模块来满足自己的需求。
- Connection Plugins（连接插件）：模块功能的补充，如连接类型插件、循环插件、变量插件、过滤插件等，该功能不常用
- Playbook（任务剧本）：编排定义ansible任务集的配置文件，由ansible顺序依次执行，通常是JSON格式的* YML文件
- API：供第三方程序调用的应用程序编程接口

### Ansible工作原理
1. 管理端支持local 、ssh、zeromq 三种方式连接被管理端，默认使用基于ssh的连接－－－这部分对应基本架构图中的连接模块；

2. 可以按应用类型等方式进行Host Inventory（主机群）分类，管理节点通过各类模块实现相应的操作－－－单个模块，单条命令的批量执行，我们可以称之为ad-hoc；

3. 管理节点可以通过playbooks 实现多个task的集合实现一类功能，如web服务的安装部署、数据库服务器的批量备份等。playbooks我们可以简单的理解为，系统通过组合多条ad-hoc操作的配置文件 。

### 安装
```
#brew安装
brew install ansible
#yum安装
#ansible的安装来源于epel仓库，因此在安装前需确保安装了正确的epel源。
yum install -y epel-release

yum install -y ansible
```

### 配置
ansible只需要在控制端（以下简称A）安装和配置即可，节点服务器（以下简称B）上面只需要把控制端的ssh key_pub拷贝过去即可。

配置文件：
/etc/ansible/ansible.cfg 主配置文件，配置ansible工作特性
/etc/ansible/hosts 主机清单
/etc/ansible/roles/ 存放角色的目录


#### 1.先把A上面的ssh key_pub拷贝到B上面。
生成ssh key命令
```
ssh-keygen -t rsa -C "youremail@example.com"
```
在A上面操作ssh-copy-id username@192.168.1.110(B端的登陆用户名和IP)
正常情况即可登陆到B端。

#### 2.在A上设置配置文件
vim /etc/ansible/hosts(如果有直接打开配置，没有目录或文件新建一个)
下面直接配置IP为公钥登陆配置，注释配置为用户名密码配置,两种方式都可以。
```
[alls]
192.168.1.110
#下面的username和password为110服务器的登陆用户名或密码
#192.1
68.1.110 ansible_user=username ansible_ssh_pass=password
```
vim /etc/ansible/ansible.cfg
```
[defaults]

```
最后测试连接:
```
ansible all -m ping            
```

### ansible命令工具
ansible 主程序，临时命令执行工具

ansible-doc 查看配置文档，模块功能查看工具
ansible-doc  ping         查看ping模块帮助
ansible-doc -s ping       查看ping模块的简单说明

ansible-galaxy 下载/上传优秀代码或Roles模块的官网平台
ansible-galaxy list 列出所有已安装的galaxy：
ansible-galaxy install geerlingguy.redis    安装galaxy：
ansible-galaxy remove geerlingguy.redis     删除galaxy：

ansible-playbook 定制自动化任务，编排剧本工具
ansible-pull 远程执行命令的工具
ansible-vault 文件加密工具
ansible-console 基于Console界面与用户交互的执行工具


### ansible命令
- 功能：通过ssh实现配置管理、应用部署、任务执行等功能
- 建议：配置ansible端能基于密钥认证的方式联系各被管理节点
- 格式：ansible < host-pattern>　 [-m module_name]　 [-a args]
- 常用选项：
```
–version               显示版本
-m module              指定模块，默认为command
-v 详细过程             –vv -vvv更详细
–list-hosts            显示主机列表，可简写—list
-k, –ask-pass          提示连接密码，默认Key验证
-K, –ask-become-pass   提示输入sudo
-C, –check             检查，并不执行
-T, –timeout=TIMEOUT   执行命令的超时时间，默认10s
-u, –user=REMOTE_USER  执行远程执行的用户
-b, –become            代替旧版的sudo 切换
```
- < host-pattern> 　 匹配主机的列表
```
ALL   表示列表中的所有主机

    例：ansible all -m  ping   #匹配所有主机
    例：ansible all -m ping -vvv        #打印相关信息
    例：ansible all -a "echo hello"     #在主机上执行打印

*   支持通配符

    例： ansible "*" -m ping  #匹配所有主机
        ansible 192.168.1.* -m ping  #匹配IP地址以192.168.1开头的主机
        ansible “*srvs” -m ping  # 匹配分组名 以 srvs结尾的主机

逻辑或：只要存在websrvs或appsrvs组中的主机
  例：ansible “websrvs:appsrvs”  -m ping 
     ansible “192.168.1.10:192.168.1.20” -m ping

逻辑与：同时在websrvs组和dbsrvs组中的主机
    ansible “websrvs:&dbsrvs”  -m ping

逻辑非： 在websrvs组，但不在dbsrvs组中的主机
    ansible ‘websrvs:!dbsrvs’  -m ping

综合逻辑：
    ansible ‘websrvs:dbsrvs:&appsrvs:!ftpsrvs’ -m -ping

正则表达式：
    ansible “websrvs:&dbsrvs”  -m ping

    ansible “~(web|db).*\.magedu\.com” -m ping

```

### ansible命令执行过程
1. 加载自己的配置文件 默认/etc/ansible/ansible.cfg
2. 加载自己对应的模块文件，如command
3. 通过ansible将模块或命令生成对应的临时py文件，并将该文件传输至远程服务器的对应执行用户 $HOME/.ansible/tmp/ansible-tmp-数字/XXX.PY文件
4. 给文件+x执行
5. 执行并返回结果
6. 删除临时py文件，sleep 0退出

### ansible 执行状态：
- 绿色：执行成功并且不需要做改变的操作
- 黄色：执行成功并且对目标主机做变更
- 红色：执行失败

### ansible的常用模块功能：
查看模块帮助： ansible-doc module
显示模块简要说明： ansible-doc -s module

- command：在远程主机执行命令，默认模块，可忽略-m选项
```
ansible srvs -m command -a ‘service vsftpd start’
```
注意：
使用Command模块执行脚本时，要注意规范，shebang机制，否则将执行失败
不支持管道“|”，变量“$”,以及重定向，需使用shell模块

- shell：和command相似，用shell执行命令
```
ansible srv -m shell -a ‘echo magedu |passwd –stdin wang’
```
注意：
调用bash执行命令 类似 cat /tmp/stanley.md | awk -F‘|’ ‘{print 1,1,2}’ &> /tmp/example.txt
这些复杂命令，即使使用shell也可能会失败。
解决办法：写到脚本，copy到远程，执行，再把需要的结果拉回执行命令的机器

- Script：运行脚本,不需要将脚本复制到被控端
```
 -a “/PATH/TO/SCRIPT_FILE”
 ansible  websrvs  -m  script  -a  f1.sh
```

- copy：从服务器复制文件到客户端
```
ansible srv -m copy -a “src=/root/f1.sh dest=/tmp/f2.sh mode=600 backup=yes”
如目标存在，默认覆盖，此处指定先备份

ansible srv -m copy -a “content=‘test content\n’ dest=/tmp/f1.txt” 
利用内容，直接生成目标文件
```
- Fetch:从客户端取文件至服务器端，copy相反，目录可先tar
```
 ansible srv -m fetch -a ‘src=/root/a.sh dest=/data/scripts’
```
- File：设置文件属性
```
创建新文件：
ansible all -m file -a ‘name=/data/f3 state=touch’
删除文件：
ansible all -m file -a ‘name=/data/f3 state=absent’
```
- Hostname：管理主机名 .生效同时更改文件永久生效
更改一个主机的主机名：
```
ansible node1 -m hostname -a “name=websrv”
```
- Cron：计划任务
支持时间：minute，hour，day，month，weekday
```
创建计划任务：每周1,3,5，每分钟打印，任务名称：warningcron

    ansible all -m cron -a ‘minute=* weekday=1,3,5 
        job=”/usr/bin/wall FBI warning” name=warningcron’
```
- Yum:管理程序包
```
yum安装vsftpd包：（默认state=installd）
    ansible all -m yum -a ‘name=vsftpd’
安装多个包用逗号隔开：
    ansible all -m yum -a ‘name=vsftpd，httpd’
```
- Service：管理服务
```
停止httpd服务：
    ansible srv -m service -a ‘name=httpd state=stopped’

开启httpd服务：
    ansible srv -m service -a ‘name=httpd state=started’
```























