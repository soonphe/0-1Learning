# 0-1Learning

![alt text](../static/common/svg/luoxiaosheng.svg "公众号")
![alt text](../static/common/svg/luoxiaosheng_learning.svg "学习")
![alt text](../static/common/svg/luoxiaosheng_wechat.svg "微信")


## koa+express
koa和express是node.js的web框架，
koa是express的升级版，更加简洁，更加强大，更加适合中小型项目，
而express更适合大型项目，koa的中间件是基于async/await的，而express是基于回调函数的。

请记住，开发 Express 和 Koa 的是同一拨开发人员。Koa 只是 Express 的一个更轻量级的版本，没有内置提供路由和模板引擎等功能。

### koa
官网：https://www.koajs.net/
安装：$ npm install koa

#### 第一个koa程序 
```
const Koa = require('koa');
const app = new Koa();

// 响应
app.use(ctx => {
  ctx.body = 'Hello Koa';
});

app.listen(3000);
```

#### 如何在webstorm中启动koa项目 
Edit Configurations要选择Nodejs，不能选择npm
- 在Nodejs中选择或填写相关参数
    * 必填选项
    * Node interpreter 选择node所在的位置,默认已有,如果有多个选择尽量选择高版本node
    * Working directory 选择你的express或koa项目所在的根目录
    * Javascript file 选择你的项目的入口文件,针对与express或koa脚手架生成的项目,选择根目录下bin/www或者根目录下app.js都可以 
    * 直接可以以dubug模式启动,可以进行断点调试

#### 中间件 
Koa 是一个中间件框架，可以采用两种不同的方法来实现中间件：

- async function
- common function
以下是使用两种不同方法实现一个日志中间件的示例：

- async functions (node v7.6+)
```
app.use(async (ctx, next) => {
  const start = Date.now();
  await next();
  const ms = Date.now() - start;
  console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
});
```
- Common function
```
// 中间件通常带有两个参数 (ctx, next), ctx 是一个请求的上下文（context）,
// next 是调用执行下游中间件的函数. 在代码执行完成后通过 then 方法返回一个 Promise.

app.use((ctx, next) => {
  const start = Date.now();
  return next().then(() => {
    const ms = Date.now() - start;
    console.log(`${ctx.method} ${ctx.url} - ${ms}ms`);
  });
});
```

#### 上下文、请求和响应
每个中间件都接收一个 Koa 的 Context 对象，该对象封装了一个传入的 http 消息，并对该消息进行了相应的响应。 ctx 通常用作上下文对象的参数名称。
```
app.use(async (ctx, next) => { await next(); });
```

Koa 提供了一个 Request 对象作为 Context 的 request 属性。 Koa的 Request 对象提供了用于处理 http 请求的方法，该请求委托给 node http 模块的IncomingMessage。
这是一个检查请求客户端 xml 支持的示例。
```
app.use(async (ctx, next) => {
  ctx.assert(ctx.request.accepts('xml'), 406);
  // 相当于:
  // if (!ctx.request.accepts('xml')) ctx.throw(406);
  await next();
});
```

Koa提供了一个 Response 对象作为 Context 的 response 属性。 Koa的 Response 对象提供了用于处理 http 响应的方法，该响应委托给 ServerResponse。
Koa 对 Node 的请求和响应对象进行委托而不是扩展它们。这种模式提供了更清晰的接口，并减少了不同中间件与 Node 本身之间的冲突，并为流处理提供了更好的支持。 IncomingMessage 仍然可以作为 Context 上的 req 属性被直接访问，并且ServerResponse也可以作为Context 上的 res 属性被直接访问。
这里是一个使用 Koa 的 Response 对象将文件作为响应体流式传输的示例。
```
app.use(async (ctx, next) => {
  await next();
  ctx.response.type = 'xml';
  ctx.response.body = fs.createReadStream('really_large.xml');
});
```
Context 对象还提供了其 request 和 response 方法的快捷方式。在前面的例子中，可以使用 ctx.type 而不是 ctx.response.type，而 ctx.accepts 可以用来代替 ctx.request.accepts。

#### Koa 应用程序
在执行 new Koa() 时创建的对象被称为 Koa 应用对象。

应用对象是带有 node http 服务的 Koa 接口，它可以处理中间件的注册，将http请求分发到中间件，进行默认错误处理，以及对上下文，请求和响应对象进行配置。


#### 设置
Koa 应用程序设置是应用程序级别的设置，它们影响应用程序的行为。例如，应用程序的名称，环境，代理设置等。
```
app.env 默认是 NODE_ENV 或 "development"
app.keys 签名的 cookie 密钥数组
app.proxy 当真正的代理头字段将被信任时
忽略 .subdomains 的 app.subdomainOffset 偏移量，默认为 2
app.proxyIpHeader 代理 ip 消息头, 默认为 X-Forwarded-For
app.maxIpsCount 从代理 ip 消息头读取的最大 ips, 默认为 0 (代表无限)
```
您可以将设置传递给构造函数:
```
const Koa = require('koa');
const app = new Koa({ proxy: true });
```
或动态的:
```
const Koa = require('koa');
const app = new Koa();
app.proxy = true;
```
