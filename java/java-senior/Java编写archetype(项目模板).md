# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Java编写archetype(项目模板)
Archetype是一个Maven项目的模板工具包，它定义了一类项目的基本架构。Archetype为开发人员提供了创建Maven项目的模板，同时它也可以根据已有的Maven项目生成参数化的模板。通过archetype，开发人员可以很方便地将一类项目的最佳实现应用到自己的项目中。在一个Maven项目中，开发者可以通过archetype提供的范例快速入门并了解该项目的结构与特点。

Maven Archetype由下面几个模块组成：
- maven-archetype-plugin：Archetype插件。通过该插件，开发者可以在Maven中使用Archetype。
- archetype-packaging：用于描述archetype的生命周期与构建项目软件包
- archetype-models：用于描述类与引用
- archetype-common：核心类
- archetype-testing：用于测试Maven Archetype的内部组件

Archetype插件有四个目标可以直接使用：
- archetype:create（不推荐）：从archetype 中创建一个Maven项目。
- archetype:generate：从archetype 中创建一个Maven项目，需要开发人员在指定archetype，插件会从远程仓库中自动获取。
- archetype:create-from-project：从已有的项目中生成archetype。
- archetype:crawl：搜索并更新仓库中的archetype。

### Archetype工程的组成
1. Archetype descriptor（archetype.xml），这个文件位于路径src/main/resources/META-INF/maven/，它列出了原型中包含的所有文件，并且给他们做了分类以便Archetype的生成机制可以正确的处理它们;
2. Archetype插件将要拷贝的原型文件，位于路径，src/main/resources/archetype-resources/
3. pom.xml原型文件，位于路径：src/main/resources/archetype-resources
4. 这个工程本身自己的pom.xml

目录示例：
```
0-1Learning-template
├── src
    └── main 
        └── resources
            ├── archetype-resources - 要拷贝的原型文件夹
                ├── src.main...     - 原型项目文件
                └── pom.xml         - 原型项目依赖
            └── META-INF.maven      - archetype描述文件
                └── archetype-metadata.xml 
└── pom.xml -- archetype依赖
```

### 如何生成Archetype项目
有两种方法来创建Archetyp,
- 第一种是通过已有的项目来创建,
- 第二种是通过mvn archetype:generate创建一个简单的JAVA项目或WEB项目,然后进行改造。

在已有maven项目中的pom.xml里面添加maven-archetype-plugin,Maven-resources-plugins,maven-compiler-plugin插件。
利用archetype plugin的create-from-project将maven项目将该maven项目生成为archetype类型项目。
执行命令：
```
mvn archetype:create-from-project
```
注意:在搭建好样板工程之后，在使用mvn archetype:create-from-project命令之前，要先把项目中不相关的工程文件、中间文件、.setting、.class、.project等删除。
生成文件：target\generated-sources

安装Archetype
在上文创建了archetype之后，进入到target\generate-sources\archetype目录，然后在命令行执行：
```
#将模板安装到本地仓库（因为创建的maven项目一般都是从仓库中选择模板）
mvn clean install
#使用插件爬取骨架配置
mvn archetype:crawl
```
执行成功后会在~/.m2/repository/archetype-catalog.xml中多出来一个骨架配置文件:
```
<archetype-catalog xsi:schemaLocation="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-catalog/1.0.0 http://maven.apache.org/xsd/archetype-catalog-1.0.0.xsd"
    xmlns="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-catalog/1.0.0"
    xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <archetypes>
    <archetype>
      <groupId>com.sgcc.ywzt</groupId>
      <artifactId>gx-cloud-service-template</artifactId>
      <version>1.0.0-SNAPSHOT</version>
      <description>quickstart</description>
    </archetype>
  </archetypes>
</archetype-catalog>
```
注：如果mvn archetype:crawl中没有生成对应的骨架文件， 可以使用maven-archetype-plugin插件中的update-local-catalog功能生成
也可以使用命令更新本地文件：
```
mvn archetype:update-local-catalog
```


### 使用archetype模板生成项目
在*空目录*下执行命令(不要在archetype下执行以下命令)
```
mvn archetype:generate -DarchetypeCatalog=local
也可以指定archetype版本：
mvn org.apache.maven.plugins:maven-archetype-plugin:2.4:generate <parameters>
```
此时会给出上文安装的archetype，选择输入编号,就可以了,这里就输入"2",然后输入GroupId,artifactId,就可以了

如果想使用选定的模板生成
```
mvn archetype:generate -DgroupId=com.sgcc.ywzt -DartifactId=gx-order-configsrv -Dversion=1.0.0-SNAPSHOT -Dpackage=com.sgcc.ywzt -DarchetypeArtifactId=gx-cloud-service-template -DarchetypeGroupId=com.sgcc.ywzt -DarchetypeVersion=1.0.0-SNAPSHOT -DdataId=
mvn archetype:generate -DgroupId=com.sgcc.ywzt -DartifactId=gx-es-jobssrv -Dversion=1.0.0-SNAPSHOT -Dpackage=com.sgcc.ywzt -DarchetypeArtifactId=gx-cloud-es2-job-template -DarchetypeGroupId=com.sgcc.ywzt -DarchetypeVersion=1.0.0-SNAPSHOT -DdataId=es-job-srv.yml
```
注：这里的-D后都是可以自定义并且可以在项目获取的变量参数！

变量说明
- groupId    | 组名(业务中台一般情况下不要改)
- artifactId | 工程名字  （gx-authsrv)
- package    | 包名 com.sgcc.ywzt.auth  ywzt后面的要是模块的名字
- dataId     | nacos 上dataId

配置参数获取示例：
```
${groupId}
${artifactId}
```

### archetype命令
archetype:help      帮助命令
archetype:crawl     爬取一个maven仓库，创建目录文件：
archetype:create-from-project   根据一个工程，创建一个新的archetype:
archetype:generate              根据一个archetype，创建一个新的工程:
archetype:jar                   根据当前的archetype工程，创建一个jar包:
archetype:update-local-catalog      更新本地的maven目录：


### 自定义archetype示例
这里列表archetype依赖，原型项目描述和原型项目依赖三个示例，其他原型代码可以自由裁量

archetype依赖：
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
  xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <parent>
    <artifactId>gx-archetypes</artifactId>
    <groupId>com.sgcc.ywzt</groupId>
    <version>1.0.0-SNAPSHOT</version>
  </parent>
  <modelVersion>4.0.0</modelVersion>

  <artifactId>gx-cloud-service-template</artifactId>

  <properties>
    <maven.compiler.source>8</maven.compiler.source>
    <maven.compiler.target>8</maven.compiler.target>
  </properties>

  <build>
    <extensions>
      <extension>
        <groupId>org.apache.maven.archetype</groupId>
        <artifactId>archetype-packaging</artifactId>
        <version>3.0.1</version>
      </extension>
    </extensions>

    <plugins>
      <plugin>
        <artifactId>maven-archetype-plugin</artifactId>
        <version>3.0.1</version>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-compiler-plugin</artifactId>
        <version>2.3.2</version>
        <configuration>
          <source>1.8</source>
          <target>1.8</target>
          <encoding>UTF-8</encoding>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-jar-plugin</artifactId>
        <version>3.2.0</version>
        <configuration>
        </configuration>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-surefire-plugin</artifactId>
        <version>2.22.2</version>
      </plugin>
    </plugins>
  </build>

</project>

```

原型项目描述
```
<archetype-descriptor
  xmlns="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-descriptor/1.1.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/plugins/maven-archetype-plugin/archetype-descriptor/1.1.0 https://maven.apache.org/xsd/archetype-descriptor-1.1.0.xsd"
  name="quickstart">
  <requiredProperties>
    <requiredProperty key="dataId">
      <defaultValue>xxx-srv</defaultValue>
    </requiredProperty>
  </requiredProperties>
  <fileSets>
    <fileSet filtered="true" packaged="true" encoding="UTF-8">
      <directory>src/main/java</directory>
      <includes>
        <include>**/*.java</include>
        <include>**/*.md</include>
      </includes>
    </fileSet>
    <fileSet filtered="true" encoding="UTF-8">
      <directory>src/main/resources</directory>
      <includes>
        <include>**/*.yml</include>
        <include>**/*.properties</include>
      </includes>
    </fileSet>
    <fileSet filtered="true" packaged="true" encoding="UTF-8">
      <directory>src/test/java</directory>
      <includes>
        <include>**/*.java</include>
      </includes>
    </fileSet>
    <fileSet filtered="true" encoding="UTF-8">
      <directory>src/test/resources</directory>
      <includes>
        <include>**/*.properties</include>
        <include>**/*.xml</include>
        <include>**/*.yml</include>
      </includes>
    </fileSet>
  </fileSets>
</archetype-descriptor>
```

原型项目依赖
```
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
	xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
	<modelVersion>4.0.0</modelVersion>

	<parent>
		<groupId>com.sgcc.ywzt</groupId>
		<artifactId>pom-parent</artifactId>
		<version>1.0-SNAPSHOT</version>
	</parent>

	<groupId>${groupId}</groupId>
	<artifactId>${artifactId}</artifactId>
	<version>1.0-SNAPSHOT</version>
	<packaging>jar</packaging>


	<dependencies>
		<dependency>
			<groupId>com.sgcc.ywzt</groupId>
			<artifactId>gx-commons</artifactId>
			<version>1.0-SNAPSHOT</version>
		</dependency>
		<dependency>
			<groupId>com.sgcc.ywzt</groupId>
			<artifactId>gx-web-spring-boot-starter</artifactId>
			<version>1.0-SNAPSHOT</version>
		</dependency>
		<dependency>
			<groupId>com.sgcc.ywzt</groupId>
			<artifactId>gx-codegen</artifactId>
			<version>1.0-SNAPSHOT</version>
		</dependency>
		<dependency>
			<groupId>com.sgcc.ywzt</groupId>
			<artifactId>gx-jpa-spring-boot-starter</artifactId>
			<version>1.0-SNAPSHOT</version>
		</dependency>
		<dependency>
			<groupId>com.sgcc.ywzt</groupId>
			<artifactId>gx-monitor-boot-starter</artifactId>
			<version>1.0-SNAPSHOT</version>
		</dependency>
		<dependency>
			<groupId>com.querydsl</groupId>
			<artifactId>querydsl-apt</artifactId>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>mysql</groupId>
			<artifactId>mysql-connector-java</artifactId>
			<version>5.1.46</version>
		</dependency>
		<dependency>
			<groupId>com.alibaba.cloud</groupId>
			<artifactId>spring-cloud-starter-alibaba-nacos-discovery</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.cloud</groupId>
			<artifactId>spring-cloud-starter-sleuth</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-logging</artifactId>
		</dependency>
		<dependency>
			<groupId>org.springframework.boot</groupId>
			<artifactId>spring-boot-starter-test</artifactId>
		</dependency>
		<dependency>
			<groupId>org.mapstruct</groupId>
			<artifactId>mapstruct</artifactId>
		</dependency>
		<dependency>
			<groupId>com.sgcc.ywzt</groupId>
			<artifactId>gx-security-boot-starter</artifactId>
			<version>1.0-SNAPSHOT</version>
		</dependency>
		<dependency>
			<groupId>com.sgcc.ywzt</groupId>
			<artifactId>ywzt-admin-auth-api</artifactId>
			<version>1.0.0-SNAPSHOT</version>
		</dependency>
	</dependencies>

	<profiles>
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

	<build>
		<resources>
			<resource>
				<directory>src/main/resources</directory>
				<filtering>true</filtering>
				<includes>
					<include>**/*.xml</include>
					<include>**/*.properties</include>
					<include>**/*.yml</include>
					<include>**/*.sql</include>
				</includes>
			</resource>
		</resources>
		<plugins>
			<plugin>
				<groupId>org.springframework.boot</groupId>
				<artifactId>spring-boot-maven-plugin</artifactId>
				<configuration>
					<mainClass>${package}.BootstrapApplication</mainClass>
				</configuration>
				<executions>
					<execution>
						<id>repackage</id>
						<goals>
							<goal>repackage</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-compiler-plugin</artifactId>
				<version>${maven-compiler-plugin.version}</version> <!-- or newer version -->
				<configuration>
					<source>1.8</source>
					<target>1.8</target>
					<annotationProcessorPaths>
						<path>
							<groupId>com.sgcc.ywzt</groupId>
							<artifactId>gx-codegen</artifactId>
							<version>1.0-SNAPSHOT</version>
						</path>
						<path>
							<groupId>org.mapstruct</groupId>
							<artifactId>mapstruct-processor</artifactId>
							<version>${mapstruct-version}</version>
						</path>
						<path>
							<groupId>org.projectlombok</groupId>
							<artifactId>lombok</artifactId>
							<version>${lombok.version}</version>
						</path>
					</annotationProcessorPaths>
				</configuration>
			</plugin>
			<plugin>
				<groupId>com.mysema.maven</groupId>
				<artifactId>apt-maven-plugin</artifactId>
				<version>1.1.3</version>
				<executions>
					<execution>
						<goals>
							<goal>process</goal>
						</goals>
						<configuration>
							<outputDirectory>target/generated-sources/java</outputDirectory>
							<processor>com.querydsl.apt.jpa.JPAAnnotationProcessor</processor>
						</configuration>
					</execution>
				</executions>
			</plugin>
			<plugin>
				<groupId>org.apache.maven.plugins</groupId>
				<artifactId>maven-assembly-plugin</artifactId>
				<configuration>
					<appendAssemblyId>false</appendAssemblyId>
					<finalName>${project.artifactId}</finalName>
					<outputDirectory>${project.basedir}/target</outputDirectory>
					<descriptors>
						<descriptor>src/main/assembly/assembly.xml</descriptor>
					</descriptors>
				</configuration>
				<executions>
					<execution>
						<id>make-assembly</id>
						<phase>package</phase>
						<goals>
							<goal>single</goal>
						</goals>
					</execution>
				</executions>
			</plugin>
		</plugins>
	</build>
</project>

```

业务代码：
```
#set( $symbol_pound = '#' )
#set( $symbol_dollar = '$' )
#set( $symbol_escape = '\' )

package ${package}.infrastructure.config;

import lombok.extern.slf4j.Slf4j;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

@RestControllerAdvice
@Slf4j
public class GlobalExceptionAdvice {


}

```