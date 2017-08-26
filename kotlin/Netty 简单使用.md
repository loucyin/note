# Netty

## UDP 基本用法

### 一般流程
1. 新建 NioEventLoopGroup
2. 新建 Bootstrap ，加入 group，设置通道类型，以及 handler
3. 绑定端口，等待接收数据
4. 通过 group 关闭连接

- 关于 handler

  handler 需要继承 `SimpleChannelInboundHandler<DatagramPacket>` ，在 channelRead0 方法中读取接收到的数据。

- 发送数据

  可以通过绑定端口后获取到的 channel 发送数据，也可以通过 ChannelHandlerContext 发送数据。

### 基本用法
- UDP server ，接收客户端数据，返回 Hello , I am Udp server :

  ```kotlin
  class UdpServer{
      fun bind(portLocal:Int) {
          val bossGroup = NioEventLoopGroup()
          try {
              // 创建 boot strap
              val bootStrap = Bootstrap()
              // 初始化
              bootStrap.group(bossGroup)
                      .channel(NioDatagramChannel::class.java)
                      .option(ChannelOption.SO_BROADCAST,true)
                      .handler(UdpServerHandler())
              // 绑定端口，等待
              bootStrap.bind(portLocal).channel().closeFuture().await()
          }finally {
              bossGroup.shutdownGracefully()
          }
      }
  }
  class UdpServerHandler: SimpleChannelInboundHandler<DatagramPacket>() {
      override fun channelRead0(ctx: ChannelHandlerContext, msg: DatagramPacket) {
          // 接收客户端数据
          val buf = msg.content()
          val array = ByteArray(buf.readableBytes())
          buf.readBytes(array)
          println(String(array))
          // 向客户端发送数据
          ctx.writeAndFlush(DatagramPacket(Unpooled.copiedBuffer("Hello , I am Udp server".toByteArray()), msg.sender()))
      }
  }
  fun main(args: Array<String>) {
      UdpServer().bind(12344)
  }
  ```

- UDP client ，向服务端发送 10 次 hello server :

  ```kotlin
  class UdpClient{
      fun connect(port:Int){
          val group = NioEventLoopGroup()
          try {
              // 初始化
              val b = Bootstrap()
              b.group(group)
                      .channel(NioDatagramChannel::class.java)
                      .option(ChannelOption.SO_BROADCAST,true)
                      .handler(ClientHandler())
              val channel = b.bind(0).sync().channel()
              // 向服务端发送 10 条： hello server
              for (i in 1..10) {
                  channel.writeAndFlush(DatagramPacket(Unpooled.copiedBuffer("hello server".toByteArray()), InetSocketAddress("127.0.0.1", port)))
              }
              // 等待 5s
              channel.closeFuture().await(5000)
          }finally {
              group.shutdownGracefully()
          }
      }
  }
  class ClientHandler: SimpleChannelInboundHandler<DatagramPacket>() {
      override fun channelRead0(ctx: ChannelHandlerContext?, msg: DatagramPacket) {
          // 接收服务端数据，并显示
          val buf = msg.content()
          val array = ByteArray(buf.readableBytes())
          buf.readBytes(array)
          println(String(array))
      }
  }
  fun main(args: Array<String>) {
      UdpClient().connect(12344)
  }
  ```

## TCP 基本用法

流程与 UDP 流程相同。

- server 接收数据，返回给客户端 hello client ：

  ```kotlin
  class TcpServer {
      fun bind(port:Int) {
          val bossGroup = NioEventLoopGroup()
          val workGroup = NioEventLoopGroup()
          try {
              val bootStrap = ServerBootstrap()
              bootStrap.group(bossGroup,workGroup)
                      .channel(NioServerSocketChannel::class.java)
                      .childHandler(object :ChannelInitializer<SocketChannel>(){
                          override fun initChannel(ch: SocketChannel) {
                              ch.pipeline().addLast(TcpServerHandler())
                          }
                      })
              val future = bootStrap.bind(port).sync()
              future.channel().closeFuture().sync()
          }finally {
              bossGroup.shutdownGracefully()
              workGroup.shutdownGracefully()
          }
      }
  }
  class TcpServerHandler : ChannelInboundHandlerAdapter(){
      override fun channelRead(ctx: ChannelHandlerContext?, msg: Any?) {
          // 读取数据
          val buf = msg as ByteBuf
          val bytes= ByteArray(buf.readableBytes())
          buf.readBytes(bytes)
          println(String(bytes, Charset.defaultCharset()))
      }
      override fun channelReadComplete(ctx: ChannelHandlerContext?) {
          super.channelReadComplete(ctx)
          val response = "hello client"
          val responseBuf = Unpooled.copiedBuffer(response.toByteArray())
          ctx?.writeAndFlush(responseBuf)
      }
  }
  fun main(args: Array<String>) {
      TcpServer().bind(10002)
  }
  ```

- client 向服务端发送 I am client hello server：

  ```kotlin
  class TcpClient {
      fun connect(port: Int, host: String) {
          val group = NioEventLoopGroup()
          try {
              val b = Bootstrap()
              b.group(group)
                      .channel(NioSocketChannel::class.java)
                      .handler(InitHandler())
              val future = b.connect(host, port).sync()
              future.channel().closeFuture().sync()
          } finally {
              group.shutdownGracefully()
          }
      }
  }
  class InitHandler : ChannelInitializer<SocketChannel>() {
      override fun initChannel(ch: SocketChannel) {
          ch.pipeline().addLast(TcpClientHandler())
      }
  }
  class TcpClientHandler : ChannelInboundHandlerAdapter() {
      override fun channelRead(ctx: ChannelHandlerContext?, msg: Any?) {
          // 读取数据
          val buf = msg as ByteBuf
          val bytes = ByteArray(buf.readableBytes())
          buf.readBytes(bytes)
          println(String(bytes, Charset.defaultCharset()))
      }
      override fun channelReadComplete(ctx: ChannelHandlerContext?) {
          super.channelReadComplete(ctx)
          // 读取完服务端返回数据后关闭 channel
          ctx?.close()
      }

      override fun channelActive(ctx: ChannelHandlerContext?) {
          super.channelActive(ctx)
          // 通道有效后发送数据
          val str = "I am client hello server";
          val buf = Unpooled.copiedBuffer(str.toByteArray())
          ctx?.writeAndFlush(buf)
      }
  }
  fun main(args: Array<String>) {
      TcpClient().connect(10002, "127.0.0.1")
  }
  ```

### handler and childHandler

服务端设置的是 childhandler ，客户端设置的是 handler，他们的区别：

> handler在初始化时就会执行，而childHandler会在客户端成功connect后才执行，这是两者的区别。

### ChannelInboundHandlerAdapter

数据处理 handler 可以通过继承 ChannelInboundHandlerAdapter 实现，不过需要注意，通过 ByteBuf 直接通信时：

**在 channelRead 方法中不要执行 super.channelRead ，否则读取不到数据。**

## TCP 粘包/拆包

> TCP 底层并不了解上层业务数据的具体含义，它会根据 TCP 缓冲区的实际情况进行包的划分，所以在业务上认为，一个完整的包可能会被 TCP 拆分成多个包进行发送，也有可能把多个小的包封装成一个大的数据包发送，这就是所谓的 TCP 粘包和拆包问题。

> 为了解决 TCP 粘包/拆包导致的半包读写问题，Netty 默认提供了多种编解码器用于处理半包。

比如，可以在数据处理 handler 之前加上两个解码器 LineBasedFrameDecoder 和 StringDecoder ，这样我们在 channelRead 方法中就不需要解析 ByteBuf 可以直接拿到 String 数据。

## await sync

netty io 是异步 io。

- ChannelFuture await()：等待，直到异步操作执行完毕，或者到达等待时间。
- ChannelFuture sync()：等待，直到异步操作执行完毕。

## 参考链接
- [Netty:Bootstrap的handler和childHandler](http://blog.csdn.net/bdmh/article/details/49927787)
