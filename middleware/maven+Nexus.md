# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## maven


或者查看maven helper插件是否存在、升级

### maven常用命令
- mvn clean	对项目进行清理，删除target目录下编译的内容
- mvn compile	编译项目源代码
- mvn test	对项目进行运行测试
- mvn package	打包文件并存放到项目的target目录下，打包好的文件通常都是编译后的class文件
- mvn install	在本地仓库生成仓库的安装包，可供其他项目引用，同时打包后的文件放到项目的target目录下

- 其他命令
```
mvn compile -X		#打包，发生jar的冲突显示冲突的原因
mvn spring-boot:run #已springboot方式启动

mvn dependency:tree 	#显示依赖树
mvn -e		#查看错误的详细信息
mvn compile	#编译源代码
mvn test		#运行测试代码
mvn package	#打包项目
mvn clean	#清除项目
mvn clean install -DskipTests	打包项目到本地仓库
mvn clean package ****  -DskipTests -DskipRat	打包项目跳过测试

mvn -U 		#强制刷新本地仓库不存在release版和所有的snapshots版本。
mvn clean install -P test 						#-P test的意思是使用 test profile 进行项目的构建
mvn clean install -Dmaven.test.skip=true 
mvn clean package -Dmaven.test.skip=true -P dev	#使用dev环境打包 
```

使用示例：
```
1. 编译打包并部署本地代码仓库：
mvn clean install -DskipTests
依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)等７个阶段
注：-DskipTests为跳过测试单元

2. 按dev环境编译打包：
mvn clean package -Dmaven.test.skip=true -P dev
依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)、install等8个阶段

3. 打包并发布到远程仓库
mvn clean deploy
依次执行了clean、resources、compile、testResources、testCompile、test、jar(打包)、install、deploy等９个阶段

```

### maven两种跳过单元测试方法的区别
- mvn package -Dmaven.test.skip=true
  - 不但跳过了单元测试的运行，同时也跳过了测试代码的编译
- mvn package -DskipTests
  - 跳过单元测试，但是会继续编译。如果没时间修改单元测试的bug，或者单元测试编译错误，则使用第一种，不要使用第二种

### mvn clean install 和 mvn install 的区别
根据maven在执行一个生命周期命令时，理论上讲，不做mvn install 得到的jar包应该是最新的，除非使用其他方式修改jar包的内容，但没有修改源代码

平时可以使用mvn install ，不使用clean会节省时间，但是最保险的方式还是mvn clean install，这样可以生成最新的jar包或者其他包 

### maven install和package区别
Maven install 安装指令，其做了两件事情：
1. 将项目打包（jar/war），将打包结果放到项目下的 target 目录下
2. 同时将上述打包结果放到本地仓库的相应目录中，供其他项目或模块引用
   Maven package 打包指令，其就做了一件事：
1. 将项目打包（jar/war），将打包结果放到项目下的 target 目录下

### maven引入本地jar：
        <dependency>
          <groupId>dingding</groupId>
          <artifactId>dingding</artifactId>
          <version>2.8</version>
          <scope>system</scope>
          <systemPath>${project.basedir}/lib/taobao-sdk-java.jar</systemPath>
        </dependency>
处理打包：
```
 <build>
   <resources>
    <resource>
      <directory>lib</directory>
      <targetPath>/BOOT-INF/lib/</targetPath>
      <includes>
        <include>**/*.jar</include>
      </includes>
    </resource>
   </resources>
 </build>
```

### maven安装本地jar到本地仓库：
```
mvn install:install-file
-Dfile=C:\Users\zw\Downloads\fastjson-1.2.4.jar
-DgroupId=com.alibaba
-DartifactId=fastjson
-Dversion=1.2.4
-Dpackaging=jar
```

### Nexus接入：
下载地址：https://www.sonatype.com/products/repository-oss-download
mac下载地址：https://download.sonatype.com/nexus/3/latest-mac.tgz

1. 构建
```
git fetch --tags
git checkout -b release-3.29.2-02 origin/release-3.29.2-02 --
./mvnw clean install
```
2. 解压运行
```
unzip -d target assemblies/nexus-base-template/target/nexus-base-template-*.zip
./target/nexus-base-template-*/bin/nexus console
```