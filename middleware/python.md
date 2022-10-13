# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## python

### 安装
1、首先打开命令，输入python --version回车看看是什么版本
2、然后输入python3 --version回车，看看是否有python3版本(python3和python不一样)
3. brew search python/brew search python3 搜索python，一般安装最新版本即可，如python@3.9
4. brew install python3 安装python3
输入，python3 --version 查看我们的版本，如果是正确的显示3.x的版本，说明我们安装成功了

5. 查看python 安装的路径,输入which python3
6. 然后根据自己的路径配置环境变量
```
export PATH=${PATH}:/usr/local/bin/python3
alias pip="/usr/local/bin/pip3"
alias python="/usr/local/bin/python3"
source ~/.bashrc
```
7。 输入python --version回车，看看是否出现3.x的版本

pip安装No such file or directory
问题：sudo python3 get-pip.py安装pip, 找不到文件get-pip.py，
解决：去手动下载get-pip.py文件，然后进入文件所在目录，再执行上面命令：sudo python3 get-pip.py
下载pip地址：
https://bootstrap.pypa.io/get-pip.py

或者通过命令下载（比较慢）：
curl -sSL https://bootstrap.pypa.io/get-pip.py -o get-pip.py



pip list | grep package_name 命令显示获取已安装包的信息（包名与版本号）
pip show package_name命令能显示该安装的包的相关信息，其中包括它的安装路径。实际上包通常被安装在python安装目录下的lib\site-packages目录下




