# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")

## Java代码生成

### 关于代码生成
代码生成集中方式
- easycode：IDEA插件生成,有几种默认模板
- mybatis-generator-maven-plugin：使用mybatis-generator-maven-plugin插件生成代码，需要配置generatorConfig.xml文件
- mybatisplus：需要maven依赖mybatis-plus和mybatis-plus-generator，添加代码生成器配置类

### easycode插件
1. 安装插件。在IDEA 中找到File --> Settings --> Plugins --> Marketplace，搜索EasyCode并点击Install进行安装。安装后需要重启IDEA。
2. 连接数据库。在IDEA 中打开Database视图，如果没有右侧边栏，可以通过点击加号添加数据源，选择MySQL或其他数据库。添加JDBC驱动，选择驱动jar包的位置，并配置数据库。
3. 生成代码。连接成功后，选择要生成代码的表，鼠标右击选择EasyCode --> Generate Code。这将会弹出窗口让你选择配置。
4. 配置EasyCode。在File --> Settings --> Other Settings --> Easy Code中进行配置，包括Type Mapper（类型映射）、Template Setting（模板设置）和Global Config（全局配置）。这里可以配置数据库中字段类型与Java对象的属性类型的映射，以及选择默认或MybatisPlus等模板。
5. 使用模板。可以自定义模板，包括实体类、controller类等。模板中可以使用宏定义和全局变量等功能。

### maven插件方式
maven插件：
```
            <plugin>
                <groupId>org.mybatis.generator</groupId>
                <artifactId>mybatis-generator-maven-plugin</artifactId>
                <version>1.4.0</version>
                <!-- generatorConfig 配置文件 -->
                <configuration>
                    <configurationFile>${basedir}/src/main/resources/generatorConfig.xml</configurationFile>
                    <overwrite>true</overwrite>
                    <verbose>true</verbose>
                </configuration>
                <dependencies>
                    <!-- mysql驱动(这里的jar包不能省略) -->
                    <dependency>
                        <groupId>mysql</groupId>
                        <artifactId>mysql-connector-java</artifactId>
                        <version>8.0.23</version>
                    </dependency>
                </dependencies>
            </plugin>
```

generatorConfig.xml文件配置
```
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE generatorConfiguration
        PUBLIC "-//mybatis.org//DTD MyBatis Generator Configuration 1.0//EN"
        "http://mybatis.org/dtd/mybatis-generator-config_1_0.dtd">
<generatorConfiguration>
    <context id="Mysql" targetRuntime="MyBatis3">
        <commentGenerator>
            <property name="suppressDate" value="true"/>
            <property name="suppressAllComments" value="true"/>
        </commentGenerator>
        <jdbcConnection driverClass="com.mysql.jdbc.Driver"
                        connectionURL="jdbc:mysql://localhost:3306/test"
                        userId="root"
                        password="root">
        </jdbcConnection>
        <javaTypeResolver>
            <property name="forceBigDecimals" value="false"/>
        </javaTypeResolver>
        <javaModelGenerator targetPackage="com.example.demo.model" targetProject="src/main/java"/>
        <sqlMapGenerator targetPackage="mapper" targetProject="src/main/resources"/>
        <javaClientGenerator targetPackage="com.example.demo.mapper" targetProject="src/main/java" type="XMLMAPPER"/>
        <table tableName="user" domainObjectName="User" enableCountByExample="false" enableUpdateByExample="false"
               enableDeleteByExample="false" enableSelectByExample="false" selectByExampleQueryId="false">
        </table>
    </context>
</generatorConfiguration>
```

### 