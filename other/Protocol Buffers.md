# Protocol Buffers

> Protocol buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data.

Protocol buffers 是一种语言中立、平台中立可扩展的结构化数据序列化机制。

## Protocol 几种数据结构

### message
```proto
syntax = "proto3";
message SearchRequest {
  string query = 1;
  int32 page_number = 2;
  int32 result_per_page = 3;
  map<string, Project> projects = 4;
}
```

message 用于定义数据结构，如果要定义数组可以使用 `repeated`。

```proto
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
```proto
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
```proto
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

## protobuf gradle plugin
google 官方提供了 gradle plugin ，maven 也有 protobuf plugin ，作者长时间不维护了，既然有官方的插件，果断使用 gradle。

使用步骤
- 添加插件仓库，classpath 依赖，引入插件
```groovy
buildscript {
    repositories {
        mavenCentral()
    }
    dependencies {
        classpath 'com.google.protobuf:protobuf-gradle-plugin:0.8.1'
    }
}
apply plugin: 'com.google.protobuf'
```
- 配置 protobuf，protoc 用于配置生成代码的 protoc 版本或者路径
```groovy
protobuf {
    protoc {
        artifact = 'com.google.protobuf:protoc:3.3.0'
        //path = '/usr/local/bin/protoc'
    }
}
```
- 将自动生成的代码加入到工程目录下
```groovy
idea {
    module {
        sourceDirs += file("$projectDir/build/generated/source/proto/main");
    }
}
```

**注意事项：**
- protobuf 在 gradel 下的默认文件夹为 src/main/proto
- import 路径
```
src/main/proto/com/test/service
    Message.proto
src/main/proto/com/test/service
    TestService.proto
```
在 TestService 中引用 Message.proto，因为两个文件在同一个文件夹下，可以使用如下引用：
```
import "Message.proto";
```
使用 protoc 可以编译通过，但是 gradle 编译不过，porotobuf gradle plugin 识别 import 是按 proto 文件夹下的相对路径识别的，需要修改为：
```
import "com/test/service/Message.proto";
```

- porotobuf gradle plugin 会将 proto 文件当做 resource 打包到 jar 包中，在 build.gradle 加入如下代码删除 proto 文件。
```groovy
processResources {
    doLast {
        delete 'build/resources/main/Gosun'
    }
}
```
- 完整的 build.gradle
```groovy
// 插件仓库
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
apply plugin: 'eclipse'
apply plugin: 'maven'
apply plugin: 'com.google.protobuf'
sourceCompatibility = 1.8
repositories {
    mavenCentral()
}
ext {
    protobufVersion = '3.3.1'
    rxjavaVersion = '2.1.1'
    log4jVersion = '2.8.2'
    junitVersion = '4.12'
}
dependencies {
    testCompile "junit:junit:$junitVersion"
    compile "io.reactivex.rxjava2:rxjava:$rxjavaVersion"
    compile "com.google.protobuf:protobuf-java:$protobufVersion"
    compile "org.apache.logging.log4j:log4j-core:$log4jVersion"
    compile "org.apache.logging.log4j:log4j-slf4j-impl:$log4jVersion"
}
// 设置生成代码的 protobuf 版本
protobuf {
    protoc {
        artifact = 'com.google.protobuf:protoc:3.3.0'
    }
}
// 将自动生成的代码加入到 source 中
idea {
    module {
        sourceDirs += file("$projectDir/build/generated/source/proto/main");
    }
}

// 设置字符集
tasks.withType(JavaCompile) {
    options.encoding = "UTF-8"
}
// 打包的时候 plugin 会把 protobuf 当做 resource 打包到 jar 包中，下面的代码用于删除 protobuf 文件
processResources {
    doLast {
        delete 'build/resources/main/Gosun'
    }
}
```

## proto idea plugin
安装 idea proto 插件后的问题：
- import 问题
proto 文件有 package 概念，idea 对 proto 的支持是以 package 为基础的。在工程下，要和 java package 一样去组织 proto 文件，否则 import 失效。


## 参考链接
- [Protocol Buffers](https://developers.google.com/protocol-buffers/)
- [java-generated#service](https://developers.google.com/protocol-buffers/docs/reference/java-generated#service)
- [google/protobuf-gradle-plugin](https://github.com/google/protobuf-gradle-plugin)
- [nnmatveev/idea-plugin-protobuf]()
