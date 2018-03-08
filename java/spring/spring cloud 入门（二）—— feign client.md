# spring cloud 入门（二）—— feign client

## 依赖配置

```groovy
compile "org.springframework.boot:spring-boot-starter-web:1.5.10.RELEASE"
compile "org.springframework.cloud:spring-cloud-starter-feign:1.4.2.RELEASE"
```

## feign 是什么
网上找来的解释：

> Feign is a java to http client binder inspired by Retrofit, JAXRS-2.0, and WebSocket. Feign's first goal was reducing the complexity of binding Denominator uniformly to http apis regardless of restfulness.

> Feign是简化Java HTTP客户端开发的工具（java-to-httpclient-binder），它的灵感来自于Retrofit、JAXRS-2.0和WebSocket。Feign的初衷是降低统一绑定Denominator到HTTP API的复杂度，不区分是否为restful。

***

> Feign是一个声明式的Web服务客户端。这使得Web服务客户端的写入更加方便 要使用Feign创建一个界面并对其进行注释。它具有可插入注释支持，包括Feign注释和JAX-RS注释。Feign还支持可插拔编码器和解码器。Spring Cloud增加了对Spring MVC注释的支持，并使用Spring Web中默认使用的HttpMessageConverters。Spring Cloud集成Ribbon和Eureka以在使用Feign时提供负载均衡的http客户端。

## 定义接口

```kotlin
@FeignClient(name = "test",url = "\${serviceUrl}")
interface HelloFeign {
    @RequestMapping(value = "/test")
    fun test():String
}
```

FeiginClient 的 name、url 属性支持占位符，kotlin 中 `$` 是关键字，需要进行使用 `\$`。

## 使用接口

```kotlin
@RestController
@SpringBootApplication
@EnableAutoConfiguration
@EnableFeignClients
class FeignClientApp {

    @Autowired
    lateinit var remote:HelloFeign

    @RequestMapping("/test")
    fun test():String{
        return remote.test()
    }
}

fun main(args: Array<String>) {
    SpringApplication.run(FeignClientApp::class.java)
}
```

## 配置文件 application.yml

```yaml
server:
  port: 8185

serviceUrl: http://localhost:8184
```

调用 HelloFeign test() 方法时，feign 客户端会去请求 `http://localhost:8184/test` 将获取到的结果转换为 String ，返回。

## 参考链接
- [OpenFeign/feign](https://github.com/OpenFeign/feign)
- [Feign介绍](http://blog.csdn.net/zheng0518/article/details/65635357)
- [spring-cloud-feign](https://springcloud.cc/spring-cloud-dalston.html#spring-cloud-feign)
