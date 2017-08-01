# 基于 protobuf 的简单 java rpc 实现 

protobuf 支持 rpc 但是没有提供 rpc 的实现，使用时需要自己实现 rpc 。

## build.gradle
### 使用 protobuf 插件
```groovy
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'com.google.protobuf:protobuf-gradle-plugin:0.8.1'
    }
}

apply plugin: 'java'
apply plugin: 'idea'
apply plugin: 'com.google.protobuf'
```

### protobuf 设置
```groovy
// 设置生成代码的 protobuf 版本
protobuf {
    protoc {
        artifact = 'com.google.protobuf:protoc:3.3.0'
    }
}
```

### 添加 protobuf 依赖
```groovy
ext {
    junitVersion = '4.12'
    protobufVersion = '3.3.1'
}
dependencies {
    testCompile "junit:junit:$junitVersion"
    compile "com.google.protobuf:protobuf-java:$protobufVersion"
}
```
**注意：生成 protobuf 的版本与依赖的大版本（都是3.3）应该一致。**

### 配置 idea
protobuf 插件自动生成代码的路径为 `build/generated/source/proto/main/java` ，需要加入到 idea 中的 sourceDir 下。
```groovy
// 将自动生成的代码加入到 source 中
idea {
    module {
        sourceDirs += file("$projectDir/build/generated/source/proto/main/java");
    }
}
```

## Math.proto
```proto
syntax = "proto3";
package rpc;
option java_package = "com.lcy.proto.rpc";
option java_multiple_files = true;
option java_generic_services = true;
message Result {
    uint32 result = 1;
}
message Params {
    uint32 param1 = 1;
    uint32 param2 = 2;
}
message RequestWrapper{
    string service = 1;
    string method = 2;
    Params params = 3;
}
service MathService {
    rpc add(Params) returns(Result);
    rpc sub(Params) returns(Result);
}
```

- 定义了两个数据类型，Params 入参，Result 出参。
- 定义了一个 service ，里面有两个方法，add sub。

## 规则
1. 客户端通过 socket 向服务端发送 MessageWrapper 数据；
2. 服务端直接返回 Result 数据。

## Client 端实现
在生成的 MathService 中提供了两种接口:
- MathService.Interface : 非阻塞式 service 接口，使用该接口需要实现 RpcChannel。
- MathService.BlockingInterface : 阻塞式 service 接口，使用该接口需要实现 BlockingRpcChannel。

```java
public class ClientBlockingRpcChannel implements BlockingRpcChannel {
    private SocketChannel channel;

    public ClientBlockingRpcChannel(String host,int port) throws IOException {
        channel = SocketChannel.open();
        channel.socket().connect(new InetSocketAddress(host,port));
    }
    @Override
    public Message callBlockingMethod(Descriptors.MethodDescriptor method, RpcController controller, Message request, Message responsePrototype) throws ServiceException {
        RequestWrapper wrapper = RequestWrapper.newBuilder()
                .setService(method.getService().getFullName())
                .setMethod(method.getName())
                .setParams((Params) request)
                .build();

        byte[] bytes = wrapper.toByteArray();
        ByteBuffer buffer = ByteBuffer.allocate(bytes.length);
        buffer.put(bytes);
        buffer.flip();
        try {
            channel.write(buffer);
        } catch (IOException e) {
            e.printStackTrace();
        }
        ByteBuffer receive = ByteBuffer.allocate(1024);
        try {
            channel.read(receive);
            receive.flip();
            byte[] resultBytes = new byte[receive.limit()];
            receive.get(resultBytes);
            return responsePrototype.toBuilder()
                    .mergeFrom(resultBytes)
                    .build();
        } catch (IOException e) {
            e.printStackTrace();
        }
        return null;
    }
}
```

Client ：
```java
public static void main(String[] args) throws ServiceException, IOException {
    MathService.BlockingInterface service = MathService.newBlockingStub(new ClientBlockingRpcChannel("192.168.1.40", 12356));
    Params params = Params.newBuilder()
            .setParam1(10)
            .setParam2(8)
            .build();
    Result result = service.add(null, params);
    System.out.println(result);
    result =service.sub(null,params);
    System.out.println(result);
}
```

## server 端实现
server 端要解析 client 发送过来的数据，计算结果，返回。在 RequestWrapper 中，我们传入了 Service fullName，以及 method name，在这里，我们只有一个 MathService ，只需要根据 method name 区分即可。
```java
public static void main(String[] args) throws IOException {
    ServerSocketChannel serverSocketChannel = ServerSocketChannel.open();
    serverSocketChannel.socket().bind(new InetSocketAddress(12356));
    SocketChannel channel = serverSocketChannel.accept();
    while (true){
        ByteBuffer buffer = ByteBuffer.allocate(1024);
        channel.read(buffer);
        buffer.flip();
        byte[] bytes = new byte[buffer.limit()];
        buffer.get(bytes);
        RequestWrapper wrapper = RequestWrapper.newBuilder()
                .mergeFrom(bytes)
                .build();
        Params params = wrapper.getParams();
        Result.Builder builder = Result.newBuilder();
        switch (wrapper.getMethod()) {
            case "add":
                builder.setResult(params.getParam1()+params.getParam2());
                break;
            case "sub":
                builder.setResult(params.getParam1()-params.getParam2());
                break;
        }
        System.out.println(params);
        System.out.println(builder.build());
        byte[] result = builder.build().toByteArray();
        ByteBuffer sendBuffer = ByteBuffer.allocate(result.length);
        sendBuffer.put(result);
        sendBuffer.flip();
        channel.write(sendBuffer);
    }
}
```

## 参考链接
- [基于protobuf的RPC实现](http://blog.csdn.net/kevinlynx/article/details/39379957)
