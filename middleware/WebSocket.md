

### WebSocket 与 HTTP 的关系
WebSocket 协议和 HTTP 协议之间有密切的关系，WebSocket 连接的建立需要通过 HTTP 握手来协商，其基本流程如下：

在握手阶段，客户端首先发送一个 HTTP 请求，并在请求报头中设置一个 Upgrade 字段，表示希望升级到WebSocket连接。
服务器在确认后，返回一个 HTTP 响应，即将协议切换为 WebSocket 协议，此时就表示升级成功了。
此后，连接从 HTTP 协议升级为 WebSocket 协议，实现了全双工、数据实时通信。

以下是 WebSocket 握手的基本步骤：

客户端发送握手请求： 客户端通过发送一个 HTTP 请求来启动 WebSocket 握手。这个请求中需要包含一些特定的头部字段，以及用于安全验证的 Sec-WebSocket-Key 字段。
```
GET /chat HTTP/1.1
Host: example.com
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Key: dGhlIHNhbXBsZSBub25jZQ==
Sec-WebSocket-Version: 13
```

服务器响应握手： 服务器接收到握手请求后，会验证 Sec-WebSocket-Key 字段，并生成一个响应密钥。如果验证通过，服务器会返回一个 HTTP 响应，表示升级到 WebSocket 协议。
```
HTTP/1.1 101 Switching Protocols
Upgrade: websocket
Connection: Upgrade
Sec-WebSocket-Accept: s3pPLMBiTxaQ9kYGzzhZRbK+xOo=
```

二、WebSocket 数据帧格式
2.2 各字段的详细说明
WebSocket协议的数据帧格式是用于在WebSocket连接上传输数据的基本单位。每个数据帧包含了控制信息和有效负载（Payload Data）。以下是WebSocket数据帧的格式及其各个字段的详细说明：

- FIN (1 bit)：表示消息是否是最后一个数据帧。若为1，则表示是消息的最后一个帧；若为0，则还有后续数据帧。
- RSV1, RSV2, RSV3 (各占 1 bit)：保留位，通常设置为0，用于未来的扩展。
- Opcode (4 bits)：指定数据帧的类型。常见的类型有：
  - 0x0：表示数据帧是一个连续帧。
  - 0x1：表示数据帧是文本帧。
  - 0x2：表示数据帧是二进制帧。
  - 0x8：表示连接关闭帧。
  - 0x9：表示Ping帧，用于心跳检测。
  - 0xA：表示Pong帧，作为对Ping的响应。
  - 其他值为保留或用于扩展。
- MASK (1 bit)：表示Payload Data是否经过掩码加密。客户端发送给服务器的数据帧需要加密，所以MASK位为1。
- Payload length (7 bits, 7+16 bits, 或 7+64 bits)：表示Payload Data的长度。
  - 若值在0~125之间，表示Payload Data的实际长度。
  - 若值为126，则表示后续的16 bits为无符号整数，用于表示Payload Data的长度。
  - 若值为127，则表示后续的64 bits为无符号整数，用于表示Payload Data的长度。
- Extended payload length (16或64 bits)：仅在Payload length为126或127时出现，用于表示实际的Payload Data长度。
- Masking-key (32 bits)：仅在MASK位为1时出现，用于解码Payload Data。Masking-key是一个32位的随机数。
- Payload Data：有效负载数据。如果MASK位为1，则需要对Payload Data进行掩码解密，使用Masking-key进行解码。

3.1 创建 Spring Boot 项目并引入 WebSocket 依赖
引入 WebSocket 依赖非常简单，只需要在创建 SpringBoot 项目的时候，勾选上 WebSocket 就行了：

3.2 继承 TextWebSocketHandler 类
要使用 WebSocket 的相关功能，需要继承 TextWebSocketHandler 类来实现我们自己的 MyWebSocketHandler 类，我创建了一个websocket包，在里面建立一个类 MyWebSocketHandler：
然后需要重写里面的方法。这里我重新了四个方法：
这四个方法分别代表的含义：
- afterConnectionEstablished 方法：在WebSocket连接成功建立后被自动调用。
- handleTextMessage 方法：在WebSocket接收到文本消息时被自动调用。常常用于收到的消息进行转发。
- handleTransportError 方法：在连接出现异常时被自动调用。
- afterConnectionClosed 方法：在连接正常关闭后被自动调用。

3.3 配置资源路径
要想使我们刚才创建的 MyWebSocketHandler 能够在建立 WebSocket 连接之后能够被调用，还需要对其进行配置，即注册该类与资源路径的映射关系。

首先创建一个 config包，然后在里面创建一个类 WebSocketConfig。
这个类需要实现 WebSocketConfigurer 接口，并重写 registerWebSocketHandlers 方法。

注意，需要添加 @Configuration 表示其是一个配置类，并且储存到 Spring 中；另外还需添加 @EnableWebSocket 注解，表示启用WebSocket 支持，使应用能够处理 WebSocket 连接。
```
@Configuration
@EnableWebSocket 
public class WebSocketConfig implements WebSocketConfigurer {
    /**
     * 通过这个方法，把刚才创建好的 Handler 类注册到具体的路径中
     *
     * @param registry WebSocketHandlerRegistry
     */
    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
    }
}
```
最后将刚才的 MyWebSocketHandler 与一个具体的请求路径关联。
```
@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {
    @Autowired
    private MyWebSocketHandler myWebSocketHandler;

    /**
     * 通过这个方法，把刚才创建好的 Handler 类注册到具体的路径中
     *
     * @param registry WebSocketHandlerRegistry
     */
    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        // 当浏览器通过 WebSocket 请求的路径是 '/test' 的时候，就会调用到 MyWebSocketHandler 这个类中的方法
        registry.addHandler(myWebSocketHandler, "/test");
    }
}
```
当建立了 WebSocket 连接并且请求的路径是 /test 的时候，就会调用到 MyWebSocketHandler 这个类中的方法。通过这个配置，将WebSocket 的处理逻辑和路径连接起来，使得 WebSocket 连接能够在应用中得到正确处理和响应。

前端代码
```
<script>
        // 创建一个 WebSocket 实例，连接到服务器的 /test 路径
        let websocket = new WebSocket("ws://localhost:8081/test");

        // WebSocket 连接建立成功的回调函数
        websocket.onopen = function () {
            console.log("WebSocket 连接成功！");
        }

        // WebSocket 收到消息的回调函数
        websocket.onmessage = function (message) {
            console.log("WebSocket 收到消息：" + message.data);
        }

        // WebSocket 连接断开的回调函数
        websocket.onclose = function () {
            console.log("WebSocket 连接断开！");
        }

        // WebSocket 连接异常的回调函数
        websocket.onerror = function () {
            console.log("WebSocket 连接异常！");
        }

        // 获取页面元素
        let messageInput = document.querySelector("#message");
        let sendButton = document.querySelector('#send-button');

        // 发送按钮的点击事件
        sendButton.onclick = function(){
            console.log("WebSocket 发送消息：" + messageInput.value);
            websocket.send(messageInput.value); // 向服务器发送消息
        }
    </script>
```
