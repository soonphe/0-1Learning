# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")

## maven

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
      <!-- 文件夹名称 -->
      <directory>lib</directory>
      <!-- 模板输出路径，spring为WEB-INF目录，spring-boot为BOOT-INF -->
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

### mvn -v提示Permission denied
权限不够，chmod a+x  /opt/apache-maven-3.2.2/bin/mvn(a:所有用户 +:增加权限 x:执行权限)


### pom文件

pom文件基础结构
```xml
<project xmlns = "http://maven.apache.org/POM/4.0.0"
    xmlns:xsi = "http://www.w3.org/2001/XMLSchema-instance"
    xsi:schemaLocation = "http://maven.apache.org/POM/4.0.0
    http://maven.apache.org/xsd/maven-4.0.0.xsd">
 
    <!-- 模型版本，对于Maven2及Maven 3来说，它只能是4.0.0 -->
    <modelVersion>4.0.0</modelVersion>
    <!-- 公司或者组织的唯一标志，也是打包成jar包路径的依据 必填-->
    <!-- 例如com.companyname.project-group，maven打包jar包的路径：/com/companyname/project-group -->
    <groupId>com.companyname.project-group</groupId>
 
    <!-- 项目的唯一ID，一个groupId下面可能多个项目，就是靠artifactId来区分的 必填 -->
    <artifactId>project</artifactId>
 
    <!-- 项目当前版本，格式为:主版本.次版本.增量版本-限定版本号 必填 -->
    <version>1.0</version>
 
    <!--项目产生的构件类型，包括jar、war、ear、pom等 默认jar，必填 -->
    <packaging>jar</packaging>
    <!-- 项目别名 非必填 -->
    <name>Timber</name>

    <!-- 父类依赖，相当于继承 -->
    <!--如果项目中没有规定某个元素的值，那么父项目中的对应值即为项目的默认值 -->
    <parent>
        <!--被继承的父项目的构件标识符 -->
        <groupId>org.springframework.boot</groupId>
        <!--被继承的父项目的全球唯一标识符 -->
        <artifactId>spring-boot-starter-parent</artifactId>
        <!--被继承的父项目的版本 -->
        <version>2.3.0.RELEASE</version>

        <!-- 父项目的pom.xml文件的相对路径,默认值是../pom.xml。表示将始终从父级仓库中获取，不从本地路径获取 -->
        <!-- 查找顺序为：构建当前项目的地方--)relativePath指定的位置--)本地仓库--)远程仓库 -->
        <!-- 如果默认../pom.xml 没找到父元素的pom，会去本地仓库进行查找，如果本地找不到，会去远程仓库找，如果远程仓库也没有 ，不配置 relativePath 指向父项目的pom则会报错 -->
        <relativePath>../pom.xml</relativePath>
    </parent>

    <!-- 属性配置 -->
    <properties>
        <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
        <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
        <java.version>1.8</java.version>

        <spring.cloud.version>Hoxton.SR8</spring.cloud.version>
        ...
        ...
    </properties>
    
    <!--该元素描述了项目相关的所有依赖。 这些依赖自动从项目定义的仓库中下载 -->
    <!-- pom文件中通过dependencyManagement来声明依赖，通过dependencies元素来管理依赖。dependencyManagement下的子元素只有一个直接的子元素dependencice，其配置和dependencies子元素是完全一致的；而dependencies下只有一类直接的子元素：dependency。-->
    <dependencies>
        <dependency>
            <!--依赖项目的坐标三元素：groupId + artifactId + version -->
            <groupId>org.projectlombok</groupId>
            <artifactId>lombok</artifactId>
            <version>...</version>
            <!------------------- 依赖传递 ------------------->
            <!--依赖排除，即告诉maven只依赖指定的项目，不依赖该项目的这些依赖。此元素主要用于解决版本冲突问题 -->
            <exclusions>
                <exclusion>
                    <artifactId>spring-core</artifactId>
                    <groupId>org.springframework</groupId>
                </exclusion>
            </exclusions>
            <!-- 可选依赖，用于阻断依赖的传递性。如果在项目B中把C依赖声明为可选，那么依赖B的项目中无法使用C依赖 -->
            <optional>true</optional>
            
            <!------------------- 依赖范围 ------------------->
            <!--依赖范围。在项目发布过程中，帮助决定哪些构件被包括进来
                - compile：默认范围，用于编译;影响编译，测试，运行阶段  - provided：类似于编译，但支持jdk或者容器提供，类似于classpath，它只影响到编译，测试阶段
                - runtime: 在执行时需要使用;    - systemPath: 仅用于范围为system。提供相应的路径 
                - test.md: 用于test任务时使用;    - system: 需要外在提供相应的元素。通过systemPath来取得 
                - optional: 当项目自身被依赖时，标注依赖是否传递。用于连续依赖时使用 -->
            <scope>test</scope>
            <!-- 该元素为依赖规定了文件系统上的路径。仅供scope设置system时使用。但是不推荐使用这个元素 -->
            <!-- 不推荐使用绝对路径，如果必须要用，推荐使用属性匹配绝对路径，例如${java.home} -->
            <systemPath>...</systemPath>
        </dependency>
        ...
        ...
    </dependencies>

    <!-- ...Management被管理的依赖，一般在顶层parent依赖中用到，用于统一依赖版本信息，避免依赖冲突，没有真正引入，需要时可以不传版本号引入 -->
    <dependencyManagement>
        <dependencies>
            <dependency>
                <groupId>org.springframework.cloud</groupId>
                <artifactId>spring-cloud-dependencies</artifactId>
                <version>${spring.cloud.version}</version>
                <type>pom</type>
                <scope>import</scope>
            </dependency>
            ...
            ...
        </dependencies>
    </dependencyManagement>
    
    <!-- 环境变量，可根据环境变量打包 -->
    <profiles>
        <profile>
            <id>jdk-1.8</id>
            <activation>
                <activeByDefault>true</activeByDefault>
                <jdk>${java.version}</jdk>
            </activation>
            <properties>
                <maven.compiler.source>${java.version}</maven.compiler.source>
                <maven.compiler.target>${java.version}</maven.compiler.target>
                <maven.compiler.compilerVersion>${java.version}</maven.compiler.compilerVersion>
            </properties>
        </profile>
        <profile>
            <id>dev</id>
            <properties>
                <profiles.active>dev</profiles.active>
            </properties>
            <activation>
                <activeByDefault>true</activeByDefault>
            </activation>
        </profile>
        <profile>
            <id>test</id>
            <properties>
                <profiles.active>test</profiles.active>
            </properties>
        </profile>
        <profile>
            <id>pro</id>
            <properties>
                <profiles.active>pro</profiles.active>
            </properties>
        </profile>
    </profiles>

    <!-- 项目编译构建相关的配置，可配置多种插件，带..Management的也是被管理的插件，没有真正引入 -->
    <build>
        <!--------------------- 路径管理（在遵循约定大于配置原则下，不需要配置） --------------------->
        <!--项目源码目录，当构建项目的时候，构建系统会编译目录里的源码。该路径是相对于pom.xml的相对路径。 -->
        <sourceDirectory />
        <!--该元素设置了项目单元测试使用的源码目录。该路径是相对于pom.xml的相对路径 -->
        <testSourceDirectory />
        <!--被编译过的应用程序class文件存放的目录。 -->
        <outputDirectory />
        <!--被编译过的测试class文件存放的目录。 -->
        <testOutputDirectory />
        <!--项目脚本源码目录，该目录下的内容，会直接被拷贝到输出目录，因为脚本是被解释的，而不是被编译的 -->
        <scriptSourceDirectory />
        <!--产生的构件的文件名，默认值是${artifactId}-${version}。 -->
        <finalName>timber-portal</finalName>
        
        <!-- 资源管理 -->
        <resources>
            <!-- 这里的resource可以配多个 -->
            <resource>
                <!-- 描述了资源的目标输出路径。该路径是相对于target/classes的路径 -->
                <!-- 如果是想要把资源直接放在target/classes下，不需要配置该元素 -->
                <targetPath />
                <!--描述存放资源的目录，该路径相对POM路径 -->
                <directory>src/main/resources</directory>
                <!--是否使用参数值代替参数名。参数值取自文件里配置的属性，文件在filters元素里列出。 -->
                <filtering>true</filtering>
                <!--包含的模式列表，例如**/*.xml，只有符合条件的资源文件才会在打包的时候被放入到输出路径中 -->
                <includes>
                    <include>**/*.xml</include>
                    <include>**/*.properties</include>
                    <include>**/*.yml</include>
                    <include>**/*.sql</include>
                </includes>
                <!--排除的模式列表，例如**/*.xml，符合的资源文件不会在打包的时候会被过滤掉 -->
                <excludes />
            </resource>
        </resources>
        
        <!-- 使用的插件列表 -->
        <plugins>
            <!--plugin元素包含描述插件所需要的信息。 -->
            <plugin>
                <!--插件在仓库里的group ID -->
                <groupId />
                <!--插件在仓库里的artifact ID -->
                <artifactId />
                <!--被使用的插件的版本（或版本范围） -->
                <version />
                <!-- 是否从该插件下载Maven扩展(例如打包和类型处理器) -->
                <!-- 由于性能原因，只有在真需要下载时，该元素才被设置成enabled -->
                <extensions />
                <!--在构建生命周期中执行一组目标的配置。每个目标可能有不同的配置。 -->
                <executions>
                    <!--execution元素包含了插件执行需要的信息 -->
                    <execution>
                        <!--执行目标的标识符，用于标识构建过程中的目标，或者匹配继承过程中需要合并的执行目标 -->
                        <id />
                        <!--绑定了目标的构建生命周期阶段，如果省略，目标会被绑定到源数据里配置的默认阶段 -->
                        <phase />
                        <!--配置的执行目标 -->
                        <goals />
                        <!--配置是否被传播到子POM -->
                        <inherited />
                        <!--作为DOM对象的配置 -->
                        <configuration >
                            <source>1.8</source>
                            <target>1.8</target>
                            <!-- 注解处理器所在的jar包 -->
                            <annotationProcessorPaths>
                                <path>
                                    <groupId>org.mapstruct</groupId>
                                    <artifactId>mapstruct-processor</artifactId>
                                    <version>1.3.1.Final</version>
                                </path>
                                <path>
                                    <groupId>org.projectlombok</groupId>
                                    <artifactId>lombok</artifactId>
                                    <version>1.18.12</version>
                                </path>
                            </annotationProcessorPaths>
                        </configuration>
                    </execution>
                </executions>
                <!--项目引入插件所需要的额外依赖 -->
                <dependencies>
                    <!--参见dependencies/dependency元素 -->
                    <dependency>
                        ......
                    </dependency>
                </dependencies>
                <!--任何配置是否被传播到子项目 -->
                <inherited />
                <!--作为DOM对象的配置 -->
                <configuration />
            </plugin>
        </plugins>
        
        <!-- 插件管理 -->
        <pluginManagement>
            <plugins>
                <plugin>
                    <groupId>org.springframework.boot</groupId>
                    <artifactId>spring-boot-maven-plugin</artifactId>
                    <version>${spring-boot-maven-verison}</version>
                </plugin>
                ...
                ...
            </plugins>
        </pluginManagement>

        <!--------------------- 构建扩展 --------------------->
        <!--使用来自其他项目的一系列构建扩展 -->
        <extensions>
            <!--每个extension描述一个会使用到其构建扩展的一个项目，extension的子元素是项目的坐标 -->
            <extension>
                <!--项目坐标三元素：groupId + artifactId + version -->
                <groupId />
                <artifactId />
                <version />
            </extension>
        </extensions>

        <!--------------------- 其他配置 --------------------->
        <!--当项目没有规定目标（Maven2 叫做阶段）时的默认值 -->
        <defaultGoal />
        <!--构建产生的所有文件存放的目录 -->
        <directory />
        <!--当filtering开关打开时，使用到的过滤器属性文件列表 -->
        <filters />
    </build>
    
    <!-- 表示的是项目打包成库文件后要上传到什么库地址 -->
    <distributionManagement>
        <repository>
            <id>maven-releases</id>
            <name>release</name>
            <url>http://10.10.18.116:8081/content/repositories/releases/</url>
        </repository>
        <snapshotRepository>
            <id>maven-snapshots</id>
            <name>snapshot</name>
            <url>http://10.10.18.116:8081/content/repositories/snapshots/</url>
        </snapshotRepository>
    </distributionManagement>

    <!-- 表示从什么库地址可以下载项目依赖的库文件 -->
    <repositories>
        <repository>
            <!-- id，库的ID -->
            <id>releases</id>
            <!-- name，库的名称 -->
<!--            <name>Nexus</name>-->
            <url>http://10.10.18.116:8081/content/repositories/releases/</url>
            <!-- releases，库中版本为releases的构件，snapshots，库中版本为snapshots的构件
                enabled，是否支持更新
                updatePolicy，构件更新的策略，可选值有daily, always, never, interval:X(其中的X是一个数字，表示间隔的时间，单位min)，默认为daily
                checksumPolicy，校验码异常的策略，可选值有ignore, fail, warn
                layout，在Maven 2/3中都是default，只有在Maven 1.x中才是legacy 
                -->
<!--            <releases>-->
<!--                <enabled>true</enabled>-->
<!--                <updatePolicy>always</updatePolicy>-->
<!--                <checksumPolicy>warn</checksumPolicy>-->
<!--            </releases>-->
<!--            <snapshots>-->
<!--                <enabled>true</enabled>-->
<!--                <updatePolicy>always</updatePolicy>-->
<!--                <checksumPolicy>warn</checksumPolicy>-->
<!--            </snapshots>-->
        </repository>
        <repository>
            <id>snapshots</id>
            <url>http://10.10.18.116:8081/content/repositories/snapshots/</url>
        </repository>
    </repositories>
    
    <!-- pluginRepositories中的repository是以pluginRepository表示的，它表示插件从什么库地址下载。 -->
</project>
```

### dependencyManagement：
为了项目的正确运行，必须让所有的子模块使用依赖项的统一版本，必须确保应用的各个项目的依赖项和版本一致，才能保证测试的和发布的是相同的结果。

通过项目dependencyManagement元素来管理jar包的版本，让子项目中引用一个依赖而不用显示的列出版本号。

Maven会沿着父子层次向上走，直到找到一个拥有dependencyManagement元素的项目，然后它就会使用在这个dependencyManagement元素中指定的版本号。


### 聚合和继承父POM
聚合 VS 继承父POM
虽然聚合通常伴随着父POM的继承关系，但是这两者不是必须同时存在的。

继承父POM是为了抽取统一的配置信息和依赖版本控制，方便子POM直接引用，简化子POM的配置。

聚合（多模块）则是为了方便一组项目进行统一的操作而作为一个大的整体

所以要真正根据这两者不同的作用来使用，不必为了聚合而继承同一个父POM，也不比为了继承父POM而设计成多模块。


### 一方库、二方库、三方库说明：
一方库：本工程中的各模块的相互依赖
二方库：公司内部的依赖库，一般指公司内部的其他项目发布的jar包
三方库：公司之外的开源库， 比如apache、ibm、google等发布的依赖


### spring-boot-maven-plugin打两个包jar/exec.jar
参考文档：https://blog.csdn.net/guduyishuai/article/details/60968728

一、spring-boot-maven-plugin打包出来的jar是不可依赖的

     比如我有一个root工程，type为pom，下面两个spring-boot工程作为它的module，分别为moduleA和moduleB。假如moduleA依赖于moduleB。如果你在moduleB中使用了spring-boot-maven-plugin的默认配置build，或者在root中使用spring-boot-maven-plugin的默认配置build。很遗憾，你在clean package的时候会发现moduleA找不到moduleB中的类。原因就是默认打包出来的jar是不可依赖的。

     解决方案：

    1、调整你的代码，把spring-boot的东西从moduleB中移走。官方文档是这样说的，但是大部分人不会

          这么干吧。

    2、官方告诉我们，你如果不想移代码，好吧，我这样来给你解决，给你打两个jar包，一个用来直接执

          行，一个用来依赖。于是，你需要指定一个属性classifier，这个属性为可执行jar包的名字后缀。比

          如我设置<classifier>exec</classifier>，原项目名为Vehicle-business。

          那么我会得到两个jar：Vehicle-business.jar和Vehicle-bussiness-exec.jar

     官方文档位置：84.5 Use a Spring Boot application as a dependency

    总结：回到聚合maven上，如果你在root工程中使用了spring-boot-maven-plugin作为builder，那么你的依赖module一定要用解决方案二来设置。否则你不在root工程中用spring-boot-maven-plugin作为builder，而在需要打包的module上使用。

---

### Nexus接入：
下载地址：https://www.sonatype.com/products/repository-oss-download
mac下载地址：https://download.sonatype.com/nexus/3/latest-mac.tgz

#### mac使用brew安装：brew install nexus
```
brew install nexus
2.0启动：brew services start nexus
3.0启动：/usr/local/Cellar/nexus/3.38.1-01/libexec/bin/nexus
http://127.0.0.1:8081 	admin/admin123 admin/123456
修改默认端口
/usr/local/Cellar/nexus/2.14.18-01/libexec/conf/nexus.properties
application-port=8088
```

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

#### settings配置本地nexus
在项目的settings.xml文件中配置本地Nexus仓库，可以通过以下步骤进行：
* 找到settings.xml文件。这个文件通常位于Maven的conf目录下，例如：{maven.home}/conf/settings.xml。
* 在settings.xml文件中，找到<profiles>标签。如果不存在，就在<settings>标签内添加。
* 在<profiles>标签内，添加一个<profile>元素，并设置一个唯一的id。
* 在这个<profile>内，添加<repositories>和<pluginRepositories>标签，分别指定本地Nexus仓库作为Maven仓库和插件仓库。
```
        <profile>
            <id>testprofile</id>
            <activation>
            	<activeByDefault>true</activeByDefault>
            </activation>
            <repositories>
                <repository>
                    <id>releases</id>
                    <name>releases</name>
                    <url>http://127.0.0.1:8088/repository/releases</url>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                </repository>

                <repository>
                    <id>snaoshots</id>
                    <url>http://127.0.0.1:8088/repository/snapshots/</url>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                </repository>
                </repositories>
            <pluginRepositories>
                <pluginRepository>
                    <id>releases</id>
                    <url>http://127.0.0.1:8088/repository/releases</url>
                   <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </pluginRepository>
                <pluginRepository>
                    <id>snaoshots</id>
                    <url>http://127.0.0.1:8088/repository/snapshots</url>
                    <releases>
                        <enabled>true</enabled>
                    </releases>
                    <snapshots>
                        <enabled>true</enabled>
                    </snapshots>
                </pluginRepository>
            </pluginRepositories>
        </profile>
        
        <!-- 最后设置配置启用 -->
        <activeProfiles>
            <activeProfile>local-nexus</activeProfile>
            <activeProfile>...</activeProfile>
        </activeProfiles>
```
注意：
- releases.enabled和snapshots.enabled 可分别设置启用与否，如果不设置默认为true
- <url>：Nexus仓库的URL地址，（一定要填正确，可以登录后去设置里面查看仓库链接）

如果需要发布jar包：
```
    <distributionManagement>
        <repository>
            <id>releases</id>
            <name>release</name>
            <url>http://127.0.0.1:8088/repository/maven-releases/</url>
        </repository>
        <snapshotRepository>
            <id>snapshots</id>
            <name>snapshot</name>
            <url>http://127.0.0.1:8088/repository/maven-snapshots/</url>
        </snapshotRepository>
    </distributionManagement>
```


如果需要使用本地镜像：
```
  <mirrors>
    ...
    <mirror>
       <id>central</id>
       <name>central</name>
       <url>http://127.0.0.1:8088/repository/sgcc_maven/</url>
       <mirrorOf>*</mirrorOf>
    </mirror>
  </mirrors>
```
mirrorOf常用值：
- *：匹配所有仓库和仓库组。
- external:*：匹配所有外部仓库和仓库组。
- external:*、!central：匹配所有外部仓库和仓库组，但排除中央仓库。
- repo1,repo2：匹配指定的仓库和仓库组。
- central:配置中央仓库。

