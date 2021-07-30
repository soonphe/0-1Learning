# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")
![alt text](../static/common/svg/luoxiaosheng_gitee.svg "码云")

## Jenkins
Jenkins是一个开源软件项目，是基于Java开发的一种持续集成工具，用于监控持续重复的工作，旨在提供一个开放易用的软件平台，使软件项目可以进行持续集成. 

### 安装部署
基于Docker安装Jenkins环境
docker search jenkins
docker pull jenkins
docker run -p 8080:8080 -p 50000:50000 -v jenkins_data:/var/jenkins_home jenkinsci/blueocean
docker exec -it jenkins_v /bin/bash #进入jenkins容器
systemctl restart  docker    #重启容器

1. 访问http://localhost:8080 jenkins地址
2. 从 Jenkins 控制台日志输出中，复制自动生成的字母数字密码（在 2 组星号之间）。
3. 在解锁 Jenkins页面上，将此密码粘贴到管理员密码字段中，然后单击继续
4. 安装所需的插件（EDAS、Git、Gitlab、NodeJS、Pipeline、Publish Over SSH、SSH plugin、SSH Agent）
5. 创建第一个管理员用户

### 手动安装
下载地址：https://jenkins.io/

rpm安装：
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

yum安装：  yum install jenkins

sudo systemctl daemon-reload    #注册jenkins服务
sudo systemctl start jenkins    #启动jenkins
sudo systemctl status jenkins   #检查 Jenkins 服务的状态


### Jenkins全局工具配置
全局工具是打包的基础：例如maven，没有maven工具，打包是进行不了的

配置路径：系统管理——全局工具配置
- JDK环境安装
- Git
- Maven环境安装
- 全局属性-环境变量
> 说明：可以使用宿主机内的环境，需要让docker挂载相应的目录即可，也可以指定版本自动安装
```
jdk1.8
/opt/edas/jdk/jdk1.8.0_65

Default
git

maven
/opt/maven-3.6.3

node
/node
```


### Jenkins pipeline流水线构建：
Jenkins构建一个项目的方式有很多，如自由风格、maven项目、流水线等。 这里主要介绍流水线构建，流水线编写脚本易于配置和迁移，还能和docker联动。

> 脚本的步骤 ：拉取 -> 代码-> 构建 -> 上传到服务器器目录 -> 启动脚本

> 新的项⽬目主要更更改第⼀步的gitlab 地址url和upload 上传⽬目录remoteDirectory

流水线脚本：
```
pipeline {
   agent any

   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "maven"
   }

   stages {
      stage('Clone') {
         steps {
            // Get some code from a GitHub repository
             git branch: 'master', credentialsId: 'gitlab', url: 'http://192.168.102.34/new-platform/gx-new-order'
         }
      }
      stage('Build') {
          steps {
            withMaven(globalMavenSettingsFilePath: '/opt/maven-3.6.3/conf/settings.xml', jdk: 'jdk1.8', maven: 'maven', mavenSettingsFilePath: '/opt/maven-3.6.3/conf/settings.xml') {
            sh "mvn -Dmaven.test.skip=true clean install -U"
            echo "build success ${WORKSPACE},build id ${BUILD_ID},build tag ${BUILD_TAG}"   // some block
            }
        }
      }
      stage('Upload') {
          steps {
              sh 'echo upload start'
              sshPublisher(publishers: [sshPublisherDesc(configName: '192.168.161.168', transfers: [sshTransfer(cleanRemote: true, excludes: '', execCommand: 'echo upload success', execTimeout: 120000, flatten: false, makeEmptyDirs: true, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'tmp', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
      }
      stage("exec remote shell"){
          steps {
             sshagent(['dev_server']) {
             sh 'ssh root@192.168.161.168 /home/startOrderCore.sh'
            }
          }
      }
   }
}
```

### Jenkins流水线语法（pipeline-syntax生成流水线脚本）：
> 任意Job 左侧都有流水线语法，如：http://jenkins.ywzt.com/job/dev_gx_invoice_web/pipeline-syntax/

> 使用流水线语法可以生成对应的流水线脚本，

> 操作方法：选择对应的操作步骤，如git: Git，输入对的参数，如仓库 URL、分支、凭据等，点击生成流水线脚本即可

示例：
```
git: Git
git branch: 'master', credentialsId: 'gitlab', url: 'http://IP/new-platform/gx-new-order.git'

Sh: Shell Script
sh label: '', script: 'mvn clean package'

sshPublisher: Send build artifacts over SHH
sshPublisher(publishers: [sshPublisherDesc(configName: 'IP', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'tmp', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

Sshagent: SSH Agent
选择账户或添加账户
```

### tomcat方式部署-服务器脚本
```
#!/bin/bash
/home/admin/taobao-tomcat-production-7.0.59.3/bin/catalina.sh stop 1 -force
cd /home/admin/taobao-tomcat-production-7.0.59.3/deploy
rm -rf new-ordersrv
rm -rf new-ordersrv.war
cp /tmp/new-ordersrv.war /home/admin/taobao-tomcat-production-7.0.59.3/deploy/
/home/admin/taobao-tomcat-production-7.0.59.3/bin/catalina.sh start                                                                        
```
注：startup.sh的源代码，其实就是执行catalina.sh start

### vue构建-Pipeline脚本
```
pipeline {
   agent any

   stages {
      stage('clone') {
         steps {
            git credentialsId: 'github', url: 'https://github.com/SmartGim/admin-ui.git'
         }
      }
      stage('build') {
          steps {
              sh 'npm install'
              sh 'npm run build:prod'
          }
      }
      stage('upload') {
          steps {
            sshPublisher(publishers: [sshPublisherDesc(configName: '123.56.232.61', transfers: [sshTransfer(cleanRemote: true, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'www/wwwroot/shutuadmin', remoteDirectorySDF: false, removePrefix: '', sourceFiles: 'dist/**')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
      }
   }
}
```


### jar包方式部署-步骤说明

#### 1.新建任务
操作步骤：新建任务 - 输入任务名称 - 选择流水线方式构建
![](../static/middleware/jenkins_job_create.png "")
> 也可以使用底部的复制选项，选择已创建的任务进行复制操作
> ![](../static/middleware/jenkins_job_create_cppy.png "")

#### 2.jar包方式部署-Pipeline脚本
操作步骤：流水线 - 选择Pipeline script - 编写脚本 
```
pipeline {
   agent any

   tools {
      // Install the Maven version configured as "M3" and add it to the path.
      maven "maven"
   }

   stages {
      stage('Clone') {
         steps {
            // Get some code from a GitHub repository
             git branch: 'master', credentialsId: 'gitlab', url: 'http://192.168.102.34/invoice_center/gx-invoice-web'
         }
      }
      stage('Build') {
          steps {
            withMaven(globalMavenSettingsFilePath: '/opt/maven-3.6.3/conf/settings.xml', jdk: 'jdk1.8', maven: 'maven', mavenSettingsFilePath: '/opt/maven-3.6.3/conf/settings.xml') {
            sh "mvn -Dmaven.test.skip=true clean install -U"
            echo "build success ${WORKSPACE},build id ${BUILD_ID},build tag ${BUILD_TAG}"   // some block
            }
        }
      }
      stage('Upload') {
          steps {
              sh 'echo upload start'
              sshPublisher(publishers: [sshPublisherDesc(configName: '192.168.162.65', transfers: [sshTransfer(cleanRemote: true, excludes: '', execCommand: '/home/startOrderCore.sh', execTimeout: 120000, flatten: false, makeEmptyDirs: true, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'tmp', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.tar.gz')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])
          }
      }

   }
}
```
> 说明： 
> 1.和war包方式部署相比，Clone和Build没有改动，Clone拉取代码、Build时候用mvn打包、Upload识别文件上传，只有Upload环节改动了要去识别tar包文件 
>
> 2./home/startOrderCore.sh 脚本的编写也不相同，war包依赖于tomcat启动，jar使用命令行启动，需要注意

#### 3.jar包方式部署-服务器脚本
脚本放置路径：/home/startOrderCore.sh
```
#!/bin/bash
/opt/27/gx-invoice-web/bin/start.sh stop
echo 'stop success'
cd /opt/27
rm -rf gx-invoice-web
rm -rf gx-invoice-web.tar.gz
cp /tmp/gx-invoice-web.tar.gz /opt/27
tar -zxvf gx-invoice-web.tar.gz
/opt/27/gx-invoice-web/bin/start.sh start
```
> 说明： 
> 1.脚本需要授权jenkins用户执行权限：chmod -R a+rwx /home/startOrderCore.sh 
> 
> 2.这里的start.sh为自行编写，脚本可根据需要自行改动路径或者start.sh

#### 4.jar包方式部署-配置jenkins能访问模板服务器
> 操作路径：Jenkins - 系统管理 - 系统配置 - Publish over SSH - SSH Servers

> 需要配置：Hostname + Username + Remote Directory + Passphrase / Password
![](../static/middleware/jenkins_ssh_config.png "")





