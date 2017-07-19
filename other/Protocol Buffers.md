# Protocol Buffers

> Protocol buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data.

Protocol buffers 是一种语言中立、平台中立可扩展的结构化数据序列化机制。

## Protocol 几种数据结构

### message
```
syntax = "proto3";
message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
  map<string, Project> projects = 4;
}
```

message 用于定义数据结构，如果要定义数组可以使用 `repeated`。

```
message SearchResponse {
  repeated Result results = 1;
}
message Result {
  string url = 1;
  string title = 2;
  repeated string snippets = 3;
}
```

### enum
```
enum EnumAllowingAlias {
  option allow_alias = true;
  UNKNOWN = 0;
  STARTED = 1;
  RUNNING = 1;
}
enum EnumNotAllowingAlias {
  UNKNOWN = 0;
  STARTED = 1;
  // RUNNING = 1;  // Uncommenting this line will cause a compile error inside Google and a warning message outside.
}
```

枚举必须有一个 0 值存在，且第一个枚举值必须为 0 。可以通过设置`option allow_alias = true;`给相同的枚举值定义不同的别名。

### service
```
service SearchService {
  rpc Search (SearchRequest) returns (SearchResponse);
}
```

用于声明 rpc service。

## rpc 支持
Protocol Buffers 不提供 rpc 的实现，需要自己实现。

proto 文件：
```
service Foo {
  rpc Bar(FooRequest) returns(FooResponse);
}
```

生成的 java 代码：
- Foo : 抽象类
- Interface ： rpc 非阻塞调用接口
- Stub : 继承 Foo 实现 Interface
- BlockingInterface : rpc 阻塞调用接口
- BlockingStub : 实现 BlockingInterface

实现 rpc ：
- 使用非阻塞方式需要实现：RpcController 与 RpcChannel;
- 使用阻塞方式需要实现：RpcController 与 BlockingRpcChannel;

**Google 提供了一个基于 Protocol Buffers 与 http2 的 rpc 框架 [grpc](https://grpc.io/docs/)。**

## 参考网站
- [Protocol Buffers](https://developers.google.com/protocol-buffers/)
- [java-generated#service](https://developers.google.com/protocol-buffers/docs/reference/java-generated#service)
