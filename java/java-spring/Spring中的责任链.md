# 0-1Learning

![alt text](../../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../../static/common/svg/luoxiaosheng_wechat.svg "微信")


## Spring中的责任链
参考文档：https://blog.csdn.net/ljw761123096/article/details/79591133

### DispatcherServlet
DispatcherServlet是Spring MVC的核心，处理请求的主要逻辑在方法doDispatch(request, response):
通过doDispatch(request, response)源码，我们可以清晰看到拦截器和controler具体实现原理和流程：
1）、获取HandlerExecutionChain，HandlerExecutionChain是对Controller和它的Interceptors的进行了包装。
2）、获取HandlerAdapter，即Handler对应的适配器。
3）、执行拦截器preHandle() 方法
4）、由HandlerAdapter的handle方法执行Controller的handleRequest,并且返回一个ModelAndView
5）、执行拦截器postHandle方法
6）、执行ModelAndView的render(mv, request, response)方法把合适的视图展现给用户;
7）、执行拦截器afterCompletion() 方法