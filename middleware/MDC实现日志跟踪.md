# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


### MDC实现日志跟踪


MDC（Mapped Diagnostic Context，映射调试上下文）是 log4j 和 logback 提供的一种方便在多线程条件下记录日志的功能。某些应用程序采用多线程的方式来处理多个用户的请求。在一个用户的使用过程中，可能有多个不同的线程来进行处理。典型的例子是 Web 应用服务器。当用户访问某个页面时，应用服务器可能会创建一个新的线程来处理该请求，也可能从线程池中复用已有的线程。在一个用户的会话存续期间，可能有多个线程处理过该用户的请求。这使得比较难以区分不同用户所对应的日志。当需要追踪某个用户在系统中的相关日志记录时，就会变得很麻烦。

一种解决的办法是采用自定义的日志格式，把用户的信息采用某种方式编码在日志记录中。这种方式的问题在于要求在每个使用日志记录器的类中，都可以访问到用户相关的信息。这样才可能在记录日志时使用。这样的条件通常是比较难以满足的。MDC 的作用是解决这个问题。

MDC 可以看成是一个与当前线程绑定的哈希表，可以往其中添加键值对。MDC 中包含的内容可以被同一线程中执行的代码所访问。当前线程的子线程会继承其父线程中的 MDC 的内容。当需要记录日志时，只需要从 MDC 中获取所需的信息即可。MDC 的内容则由程序在适当的时候保存进去。对于一个 Web 应用来说，通常是在请求被处理的最开始保存这些数据。


好处
- 如果你的系统已经上线，突然有一天老板说我们增加一些用户数据到日志里分析一下。如果没有MDC我猜此时此刻你应该处于雪崩状态。MDC恰到好处的让你能够实现在日志上突如其来的一些需求
- 如果你是个代码洁癖，封装了公司LOG的操作，并且将处理线程跟踪日志号也封装了进去，但只有使用了你封装日志工具的部分才能打印跟踪日志号，其他部分(比如hibernate、mybatis、httpclient等等)日志都不会体现跟踪号。当然我们可以通过linux命令来绕过这些困扰。
- 使代码简洁、日志风格统一


### 使用
依赖：
- spirng: import org.slf4j.MDC;
- springboot: import org.apache.log4j.MDC;

代码：
```
        //存储日志
        MDC.put("service_name", "my_service1");
        MDC.put("trcode", "123456");
        MDC.put("chid", "999999");
        
        // 清除MDC内容
        MDC.clear();
        
        // 删除key
        MDC.remove(SESSION_KEY);
```








