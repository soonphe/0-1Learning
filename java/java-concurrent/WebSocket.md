## WebSocket

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


### Websocket使用UserId
简洁方式
```
// 1. 前端连接时传入userId
let userId = 1;
let ws = new WebSocket('ws://localhost:8080/websocket/' + userId);
  
// 2. 后端接收userId
@OnOpen
public void onOpen(Session session, @PathParam("userId") Integer userId) {
    this.session = session;
    this.userId = userId;
    webSocketSet.add(this);
    addOnlineCount();
    log.info("有新连接加入！当前在线人数为：{}", getOnlineCount());
}

// 3. 后端发送消息
public void sendMessage(String message) {
    for (WebSocketServer item : webSocketSet) {
        try {
            item.session.getBasicRemote().sendText(message);
        } catch (IOException e) {
            e.printStackTrace();
```

**详细方式**
在这个代码实例中，我们定义了一个名为WebSocketConfig的配置类，该类实现了WebSocketConfigurer接口。在registerWebSocketHandlers方法中，我们添加了一个WebSocket处理器myHandler，并将其映射到路径/echo/{userId}。这里的{userId}是一个路径变量，可以用于在WebSocket会话中标识特定用户。我们还通过setAllowedOrigins("*")允许所有源的WebSocket连接。这个例子展示了如何在WebSocket中使用用户ID，并且是一个基本的WebSocket配置模板。
```
java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.*;

@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(myHandler(), "/echo/{userId}")
                .setAllowedOrigins("*");
    }

    @Bean
    public WebSocketHandler myHandler() {
        return new MyCustomWebSocketHandler();
    }
}
```
在Spring WebSocket中，使用路径变量（如{userId}）来区分不同的WebSocket连接是一种常见做法。当用户尝试建立WebSocket连接时，你可以根据路径中的userId来识别用户，并据此定制WebSocket会话的行为。下面是如何在Spring WebSocket中使用这个userId的详细步骤：

自定义WebSocket处理器
首先，你需要一个自定义的WebSocketHandler。在这个处理器中，你可以重写handleTextMessage等方法来处理接收到的消息，并根据用户的ID执行相应的操作。
```
java
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;

public class MyCustomWebSocketHandler extends TextWebSocketHandler {

    @Override
    public void handleTextMessage(WebSocketSession session, TextMessage message) {
        // 从WebSocketSession中获取用户ID
        String userId = session.getAttributes().get("userId").toString();
        
        // 根据用户ID处理消息
        // ...
        
        // 发送响应消息
        session.sendMessage(new TextMessage("Response for user: " + userId));
    }

    @Override
    public void afterConnectionEstablished(WebSocketSession session) {
        // 在连接建立时，从session的路径变量中提取用户ID
        Map<String, Object> pathVariables = session.getAttributes().get(
                WebSocketHandler.WEBSOCKET_URI_TEMPLATE_VARIABLES);
        String userId = (String) pathVariables.get("userId");
        
        // 将用户ID存储到session的attributes中，方便后续使用
        session.getAttributes().put("userId", userId);
        
        // 可以在这里根据用户ID进行额外的初始化操作
        // ...
    }
}
```
注册WebSocket处理器和路径
在WebSocketConfig中，你需要注册这个处理器，并将它映射到一个包含路径变量的URL上。
```
import org.springframework.context.annotation.Configuration;
import org.springframework.web.socket.config.annotation.*;

@Configuration
@EnableWebSocket
public class WebSocketConfig implements WebSocketConfigurer {

    @Override
    public void registerWebSocketHandlers(WebSocketHandlerRegistry registry) {
        registry.addHandler(myHandler(), "/echo/{userId}")
                .addInterceptors(new MyWebSocketInterceptor()) // 如果需要的话，添加拦截器
                .setAllowedOrigins("*");
    }

    @Bean
    public WebSocketHandler myHandler() {
        return new MyCustomWebSocketHandler();
    }
}
```
在Spring WebSocket的WebSocketHandler中，你可以通过访问WebSocketSession对象的getAttributes()方法来获取在连接建立时设置的属性，比如用户的ID。通常，在afterConnectionEstablished方法中，你会从路径变量中提取userId并将其存储在WebSocketSession的attributes中。之后，在处理消息时，你可以从相同的attributes中检索这个userId。
```
import org.springframework.web.socket.CloseStatus;
import org.springframework.web.socket.TextMessage;
import org.springframework.web.socket.WebSocketSession;
import org.springframework.web.socket.handler.TextWebSocketHandler;
import java.util.Map;

public class MyCustomWebSocketHandler extends TextWebSocketHandler {

    private final ConcurrentHashMap<String, WebSocketSession> sessions = new ConcurrentHashMap<>();

    //handleTextMessage方法负责处理接收到的文本消息。它首先尝试从WebSocketSession的attributes中获取userId。如果userId存在且为字符串类型，则根据这个userId处理消息，并发送响应。如果userId不存在或类型不正确，可以选择关闭WebSocket连接或发送一个错误消息。
    @Override
    public void handleTextMessage(WebSocketSession session, TextMessage message) {
        // 从WebSocketSession的attributes中获取用户ID
        Object userIdObj = session.getAttributes().get("userId");
        if (userIdObj != null && userIdObj instanceof String) {
            String userId = (String) userIdObj;
            
            // 根据用户ID处理消息
            System.out.println("Received message from user: " + userId);
            System.out.println("Message content: " + message.getPayload());
            
            // 发送响应消息
            session.sendMessage(new TextMessage("Response for user: " + userId));
        } else {
            // 如果userId不存在或不是String类型，可以关闭连接或发送错误消息
            session.close(CloseStatus.BAD_DATA);
        }
    }

    //afterConnectionEstablished方法在WebSocket连接建立后被调用。它从WebSocketSession的attributes中提取路径变量，然后提取出userId并将其存储回session的attributes中，以便后续的消息处理中使用。
    @Override
    public void afterConnectionEstablished(WebSocketSession session) throws Exception {
        // 获取路径变量
        //Map<String, Object> pathVariables = (Map<String, Object>) session.getAttributes().get(WebSocketHandler.WEBSOCKET_URI_TEMPLATE_VARIABLES);
        //Map<String, Object> pathVariables = session.getAttributes().get(WebSocketHandler.URI_TEMPLATE_VARIABLES_ATTRIBUTE);
        // 从路径变量中提取用户ID
        if (pathVariables != null && pathVariables.containsKey("userId")) {
            String userId = (String) pathVariables.get("userId");
            // 将用户ID存储到session的attributes中
            session.getAttributes().put("userId", userId);
        }
        
        Map<String, Object> pathVariables = (Map<String, Object>) session.getAttributes().get("userId");
        URI uri = session.getUri();
        if (uri != null) {
            String query = uri.getPath();
            if (query != null) {
                String[] split = query.split("=.");
                if (split.length > 1) {
                    String userId = split[1];
                    session.getAttributes().put("userId", userId);
                }
            }
        }
        // 检查是否有相同userId的会话已经存在
        if (sessions.containsKey(userId)) {
            // 如果存在，关闭旧的会话或做其他处理
            WebSocketSession existingSession = sessions.get(userId);
            if (existingSession.isOpen()) {
                existingSession.close(CloseStatus.NORMAL);
            }
        }
        // 将新的会话添加到映射中
        sessions.put(userId, session);
        
        // 其他初始化操作...
    }
    
    //afterConnectionClosed方法在WebSocket连接关闭时被调用，用于从sessions映射中移除对应的会话。
    @Override
    public void afterConnectionClosed(WebSocketSession session, CloseStatus status) throws Exception {
        // 当连接关闭时，从映射中移除对应的会话
        for (String userId : sessions.keySet()) {
            if (sessions.get(userId) == session) {
                sessions.remove(userId);
                break;
            }
        }
        // 其他清理操作...
    }

}
```
前端建立WebSocket连接
在前端，你需要使用JavaScript来建立WebSocket连接。你需要根据用户的ID动态构建WebSocket的URL。
```
let userId = 'someUserId'; // 获取或设置用户的ID
let wsUri = "ws://yourserver.com/echo/" + userId;
let socket = new WebSocket(wsUri);

socket.onmessage = function(event) {
console.log('Received data from server: ', event.data);
};

socket.onopen = function(event) {
console.log('Connection established');
// 发送消息到服务器
socket.send('Hello Server!');
};

socket.onerror = function(error) {
console.error('WebSocket Error: ', error);
};

socket.onclose = function(event) {
console.log('
```