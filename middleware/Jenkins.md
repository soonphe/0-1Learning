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
4. 安装所需的插件
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

进入到jenkins容器中 echo $JAVA_HOME 获取java环境安装地址

系统管理——系统配置
- JDK环境安装
- Maven环境安装
- 全局属性-环境变量
```
键：path
值：echo $PATH 获取到的值
```
- SSH Servers （配置需要shh访问的服务器IP，用户名，密码）	



### pipeline流水线构建：
Jenkins构建一个项目的方式有很多，如自由风格、maven项目、流水线等。这里主要介绍流水线构建，流水线编写脚本易于配置和迁移，还能和docker联动。

脚本的步骤 ：拉取 代码-> 构建 -> 上传到搭建的⽂文件服务器器⽬目录，⼀一个新的项⽬目主要更更改第⼀步的gitlab 地址url和upload 上传⽬目录remoteDirectory

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
git: Git
git branch: 'master', credentialsId: 'gitlab', url: 'http://IP/new-platform/gx-new-order.git'

Sh: Shell Script
sh label: '', script: 'mvn clean package'

sshPublisher: Send build artifacts over SHH
sshPublisher(publishers: [sshPublisherDesc(configName: 'IP', transfers: [sshTransfer(cleanRemote: false, excludes: '', execCommand: '', execTimeout: 120000, flatten: false, makeEmptyDirs: false, noDefaultExcludes: false, patternSeparator: '[, ]+', remoteDirectory: 'tmp', remoteDirectorySDF: false, removePrefix: 'target', sourceFiles: 'target/*.war')], usePromotionTimestamp: false, useWorkspaceInPromotion: false, verbose: false)])

Sshagent: SSH Agent
选择账户或添加账户

### 脚本说明
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

### 前端vue脚本
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