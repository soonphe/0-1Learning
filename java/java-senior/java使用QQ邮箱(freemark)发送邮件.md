# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## java使用QQ邮箱(freemark)发送邮件

### 需求说明
QQ邮箱云端配置：用户需要开启云端的SMTP服务（两个）
接收邮件服务器：imap.qq.com，使用SSL，端口号993
发送邮件服务器：smtp.qq.com，使用SSL，端口号465或587
账户名：您的QQ邮箱账户名（如果您是VIP帐号或Foxmail帐号，账户名需要填写完整的邮件地址）

### 1.pom配置
```
        <!--sunmail-->
        <dependency>
            <groupId>com.sun.mail</groupId>
            <artifactId>javax.mail</artifactId>
            <version>${sun.mail.version}</version>
        </dependency>
        <sun.mail.version>1.5.6</sun.mail.version>
        <!-- feemarker Template-->
        <dependency>
            <groupId>org.freemarker</groupId>
            <artifactId>freemarker</artifactId>
            <version>2.3.25-incubating</version>
        </dependency>

```

### 2.applicationContext.xml配置
```
    <!--自动生成器内容模版 freemarker config -->
    <bean id="freeMarkerConfigurer"
          class="org.springframework.web.servlet.view.freemarker.FreeMarkerConfigurer">
        <property name="templateLoaderPath" value="classpath:/codeModel/"/>
        <property name="freemarkerSettings">
            <props>
                <prop key="template_update_delay">0</prop>
                <prop key="default_encoding">UTF-8</prop>
                <prop key="locale">zh_CN</prop>
            </props>
        </property>
        <property name="servletContext" ref="servletContext"/>
    </bean>
    <!-- 使用qq邮箱 -->
    <bean id="javaMailSender" class="org.springframework.mail.javamail.JavaMailSenderImpl">
        <property name="host" value="smtp.qq.com"/>
        <property name="port" value="587"/>
        <property name="username" value="310869749@qq.com"/>
        <!-- qq邮箱的授权码，如果是企业邮箱，则使用登录密码 -->
        <property name="password" value="rypscgvqwnencbci"/>
        <property name="javaMailProperties">
            <props >
                <prop key="mail.smtp.auth">true</prop>
            </props>
        </property>
    </bean>

    <!--配置邮箱的发送者以及标题-->
    <bean id="simpleMailMessage" class="org.springframework.mail.SimpleMailMessage">
        <property name="from" value="310869749@qq.com" />
        <property name="subject" value="邮箱注册验证" />
    </bean>
```

### 3.编写model.mail.fmt模板类
```
model.mail.fmt
<h2><font color="green">${nickName}，您好！</font></h2>
<p>注意：30分钟后链接将失效!</p>
<p>请点击以下链接完成密码重置操作：</p>
<p><a href="www.abc.com" target="_blank">www.abc.com/user/reset?userId=test10011</a></p>
<p>${content}</p>
```

### 4.配置PwdMailSender.java工具类
```java
@Component
public class PwdMailSender{

    @Autowired
    private JavaMailSender javaMailSender;
    @Autowired
    private SimpleMailMessage simpleMailMessage;
    @Autowired
    private FreeMarkerConfigurer freeMarkerConfigurer;

    /**
    *从模板中构建邮件内容
    *
    *@paramnickName用户昵称
    *@paramcontent邮件内容
    *@paramemail接受邮件
    */
    public void send(String nickName,String content,String email){
        Stringto=email;
        Stringtext="";
        Map<String,String>map=newHashMap<String,String>(1);
        map.put("nickName",nickName);
        map.put("content",content);
        try{
            //根据模板内容，动态把map中的数据填充进去，生成HTML
            Templatetemplate=freeMarkerConfigurer.getCownfiguration().getTemplate("model.mail.fmt");
            //map中的key，对应模板中的${key}表达式
            text=FreeMarkerTemplateUtils.processTemplateIntoString(template,map);
        }catch(IOExceptione){
            e.printStackTrace();
        }catch(TemplateExceptione){
            e.printStackTrace();
        }
        sendMail(to,null,text);
    }
    
    /**
    *执行发送邮件
    *
    *@paramto收件人邮箱
    *@paramsubject邮件主题
    *@paramcontent邮件内容
    */
    publicvoidsendMail(String to,String subject,String content){
        try{
            MimeMessagemessage=javaMailSender.createMimeMessage();
            MimeMessageHelpermessageHelper=newMimeMessageHelper(message,true,"UTF-8");
            messageHelper.setFrom(simpleMailMessage.getFrom());
            if(subject!=null){
                messageHelper.setSubject(subject);
            }else{
                messageHelper.setSubject(simpleMailMessage.getSubject());
            }
            messageHelper.setTo(to);
            messageHelper.setText(content,true);
            javaMailSender.send(message);
        }catch(MessagingException e){
            e.printStackTrace();
        }
    }

}
```

### 发送邮件
```
pwdMailSender.send("张三","您正在修改密码，确认请点击以下链接","xxx@qq.com");
```
