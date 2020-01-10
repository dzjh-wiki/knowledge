# WebSocket
  * WebSocket本质上一种计算机网络应用层的协议，用来弥补http协议在持久通信能力上的不足。

----
## 说明
  * http协议本身是无状态协议，每一个新的http请求，只能通过客户端主动发起，通过 建立连接-->传输数据-->断开连接 的方式来传输数据，传送完连接就断开了，也就是这次http请求已经完全结束了（虽然http1.1增加了keep-alive请求头可以通过一条通道请求多次，但本质上还是一样的）。

## long pull和ajax轮询
  * `ajax轮询`的原理非常简单，让浏览器隔个几秒就发送一次请求，询问服务器是否有新信息。
  * `long poll`其实原理跟`ajax轮询`差不多，都是采用轮询的方式，不过采取的是阻塞模型，也就是说，客户端发起连接后，如果没消息，就一直不返回`Response`给客户端。直到有消息才返回，返回完之后，客户端再次建立连接，周而复始。
  * `ajax轮询`需要服务器有很快的处理速度和资源;`long poll`需要有很高的并发，也就是说同时接待客户的能力。都非常消耗资源。

## WebSocket协议的原理
与http协议一样，WebSocket协议也需要通过已建立的TCP连接来传输数据。  
具体实现上是通过http协议建立通道，然后在此基础上用真正的WebSocket协议进行通信，所以WebSocket协议和http协议是有一定的交叉关系的。  
WebSocket协议是一个独立的基于TCP的协议。它与HTTP唯一的关系是它的握手是由HTTP服务器解释为一个Upgrade请求。  
从概念上讲，WebSocket只是TCP之上的一层，执行以下操作：
  * 为浏览器添加一个web 基于来源的安全模型；
  * 添加一个寻址和协议命名机制来支持在一个IP地址的一个端口的多个主机名的多个服务；
  * 在TCP之上分一个帧机制层以回到TCP基于的IP包机制，但没有长度限制；
  * 包括一个额外的带内（in-band）关闭阶段握手，其被设计来工作在现存的代理和其他中间件。

相比HTTP长连接，WebSocket有以下特点：  
  * 是真正的全双工方式，建立连接后客户端与服务器端是完全平等的，可以互相主动请求。而HTTP长连接基于HTTP，是传统的客户端对服务器发起请求的模式。
  * HTTP长连接中，每次数据交换除了真正的数据部分外，服务器和客户端还要大量交换HTTP header，信息交换效率很低。Websocket协议通过第一个request建立了TCP连接之后，之后交换的数据都不需要发送 HTTP header就能交换数据，这显然和原有的HTTP协议有区别所以它需要对服务器和客户端都进行升级才能实现（主流浏览器都已支持HTML5）。此外还有 multiplexing、不同的URL可以复用同一个WebSocket连接等功能。这些都是HTTP长连接不能做到的。

### WebSocket协议头
请求头中重要的字段：  
```
    Connection:Upgrade

    Upgrade:websocket

    Sec-WebSocket-Extensions:permessage-deflate; client_max_window_bits

    Sec-WebSocket-Key:mg8LvEqrB2vLpyCNnCJV3Q==

    Sec-WebSocket-Version:13
```
  * Connection和Upgrade字段告诉服务器，客户端发起的是WebSocket协议请求

  * Sec-WebSocket-Extensions表示客户端想要表达的协议级的扩展

  * Sec-WebSocket-Key是一个Base64编码值，由浏览器随机生成

  * Sec-WebSocket-Version表明客户端所使用的协议版本

得到的响应头中重要的字段：  
```
    Connection:Upgrade

    Upgrade:websocket

    Sec-WebSocket-Accept:AYtwtwampsFjE0lu3kFQrmOCzLQ=
```
  * Connection和Upgrade字段与请求头中的作用相同

  * Sec-WebSocket-Accept表明服务器接受了客户端的请求

## WebSocket协议的优缺点
优点：  
  * WebSocket协议一旦建议后，互相沟通所消耗的请求头是很小的；
  * 服务器可以向客户端推送消息。

缺点：  
  * 少部分浏览器不支持，浏览器支持的程度与方式有区别。

## WebSocket协议的应用场景
  * 即时聊天通信
  * 多玩家游戏
  * 在线协同编辑/编辑
  * 实时数据流的拉取与推送
  * 体育/游戏实况
  * 实时地图位置

### 使用WebSocket应用于视频的业务思路
  * 使用心跳维护websocket链路，探测客户端端的网红/主播是否在线；
  * 设置负载均衡7层的proxy_read_timeout默认为60s；
  * 设置心跳为50s，即可长期保持Websocket不断开。