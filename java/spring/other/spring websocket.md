# spring websocket

## 依赖

```groovy
ext.spring_boot_version = '1.5.10.RELEASE'
...
compile "org.springframework.boot:spring-boot-starter-websocket:$spring_boot_version"
```

## stomp 简单应用

### stom 配置

```kotlin
@Configuration
@EnableWebSocketMessageBroker
class WebSocketConfig : AbstractWebSocketMessageBrokerConfigurer(){
    override fun configureMessageBroker(registry: MessageBrokerRegistry) {
        registry.enableSimpleBroker("/topic")
        registry.setApplicationDestinationPrefixes("/app")
    }

    override fun registerStompEndpoints(registry: StompEndpointRegistry) {
        registry.addEndpoint("/gs-guide-websocket")
                .setAllowedOrigins("*")
                .withSockJS()
    }s
}
```

Spring websocket 存在跨域问题，需要允许跨域访问。

### SendTo 注解

```
@Controller
class WebSocketController {
    @MessageMapping("/hello")
    @SendTo("/topic/greetings")
    fun greeting(message: HelloMessage): Greeting {
        println(message)
        return Greeting("Hello ${message.name} !")
    }
}
```

WebSocketController 在接收到 /app/hello 的请求时，会向 /topic/greetings 发送消息。


### js

#### 建立连接

**需要引入 sockjs.js stomp.js jquery.js**

```javascript
function connect(){
    var socket=new SockJS('http://192.168.1.31:8182/gs-guide-websocket');
    stompClient = Stomp.over(socket);
    stompClient.connect({},function (frame){
    show('connected: ' + frame);
  });
}
```

stompClient.connect 的方法签名，headers 是 map 类型，login passcode 是字符串类型：

```
client.connect(headers, connectCallback);
client.connect(headers, connectCallback, errorCallback);
client.connect(login, passcode, connectCallback);
client.connect(login, passcode, connectCallback, errorCallback);
client.connect(login, passcode, connectCallback, errorCallback, host);
```

#### 订阅

```javascript
function subscribe(){
  subscription = stompClient.subscribe('/topic/**',function(greeting){
        show('receive greeting : ' + greeting)
      });
}
```

subscribe 的方法签名:

```
subscribe(destination,callback,headers)
```

- destination 订阅的地址，必须
- callback 接收到订阅消息的回调方法，必须
- 订阅 header
- subscribe 方法会返回一个订阅对象，可以通过订阅对象取消订阅

默认情况下，如果没有在headers额外添加，这个库会默认构建一个独一无二的 ID。在传递 headers 这个参数时，可以使用你自己的 ID ，在 headers 中指定 `{id:myid}`。

取消订阅：
```javascript
function unSubscribe(){
  subscription.unsubscribe()
}
```

#### 发送消息

```javascript
function send(){
  stompClient.send("/topic/test", {aspi: 9,user: 1}, JSON.stringify({'name': 'hello'}));
}
```

send 方法签名：

```
subscribe(destination,headers,content)
```

- destination 订阅的地址，必须
- headers 头信息
- content 内容

## ChannelInterceptor

实现 ChannelInterceptor，在拦截器中，可以通过 StompHeaderAccessor 获取到前端传递过来
的消息。

```kotlin
class MyChannelInterceptor : ChannelInterceptorAdapter() {

    override fun preSend(message: Message<*>?, channel: MessageChannel?): Message<*> {
        println("preSend")
        val accessor = StompHeaderAccessor.wrap(message)
        if (accessor.messageType == SimpMessageType.CONNECT){
            accessor.messageHeaders
            println(accessor)
        }
        return super.preSend(message, channel)
    }

    override fun afterReceiveCompletion(message: Message<*>?, channel: MessageChannel?, ex: Exception?) {
        println("afterReceiveCompletion")
        super.afterReceiveCompletion(message, channel, ex)
    }

    override fun postReceive(message: Message<*>?, channel: MessageChannel?): Message<*> {
        println("postReceive")
        return super.postReceive(message, channel)
    }

    override fun afterSendCompletion(message: Message<*>?, channel: MessageChannel?, sent: Boolean, ex: Exception?) {
        println("afterSendCompletion")
        super.afterSendCompletion(message, channel, sent, ex)
    }

    override fun preReceive(channel: MessageChannel?): Boolean {
        println("preReceive")
        return super.preReceive(channel)
    }

    override fun postSend(message: Message<*>?, channel: MessageChannel?, sent: Boolean) {
        println("postSend")
        super.postSend(message, channel, sent)
    }
}
```

加入到 configer 中：

```kotlin
@Configuration
@EnableWebSocketMessageBroker
class WebSocketConfig : AbstractWebSocketMessageBrokerConfigurer(){
    override fun configureMessageBroker(registry: MessageBrokerRegistry) {
        registry.enableSimpleBroker("/topic")
        registry.setApplicationDestinationPrefixes("/app")
    }

    override fun registerStompEndpoints(registry: StompEndpointRegistry) {
        registry.addEndpoint("/gs-guide-websocket")
                .setAllowedOrigins("*")
                .addInterceptors(MyHandshakeInterceptor())
                .withSockJS()
    }

    override fun configureClientInboundChannel(registration: ChannelRegistration?) {
        registration?.interceptors(MyChannelInterceptor())
    }
}
```

// TODO 拦截器的返回信息


## 参考链接
- [STOMP-WebSocket中文文档](http://blog.csdn.net/quanyuejie/article/details/53896140)
