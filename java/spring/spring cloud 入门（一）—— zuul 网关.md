# spring cloud 入门（一）—— zuul 网关

## 依赖配置

```groovy
compile group: 'org.springframework.cloud', name: 'spring-cloud-starter-zuul', version: '1.4.3.RELEASE'
```

## 建立 application

```kotlin
@EnableZuulProxy
@SpringBootApplication
@Configuration
class ZuulApp {}

fun main(args: Array<String>) {
    SpringApplication.run(ZuulApp::class.java)
}
```

代码很简单，加个注解，启动就 OK 了。

## 配置文件 application.yml

```yaml
# server 端口号
server:
  port: 8181

zuul:
  # 路由规则
  routes:
    baidu:
      path: /baidu/**
      url: http://www.baidu.com
    api:
      path: /api/**
      url: http://172.18.18.42:8888/api/v1
```

**注意事项**

**路由规则，有顺序，从上向下匹配，匹配到之后，就不在向下查找。**

- config 1
  ```
  zuul:
    # 路由规则
    routes:
      child:
        path: /api/users/**
        url: http://192.168.1.31:8081/users
      parent:
        path: /api/**
        url: http://192.168.1.32:8081/api
  ```
- config 2
  ```
  zuul:
    # 路由规则
    routes:
      parent:
        path: /api/**
        url: http://192.168.1.32:8081/api
      child:
        path: /api/users/**
        url: http://192.168.1.31:8081/users
  ```

请求 `/api/users/login` 时
- `config 1` 会路由到 `http://192.168.1.31:8081/users/login`
- `config 2` 会路由到 `http://192.168.1.32:8081/api/users/login`

## 解决跨域问题

在 Spring 项目中提供一个 CorsFilter bean 。
```kotlin
@EnableZuulProxy
@SpringBootApplication
@Configuration
class ZuulApp {
    @Bean
    fun corsFilter(): CorsFilter {
        val source = UrlBasedCorsConfigurationSource()
        val config = CorsConfiguration()
        config.allowCredentials = true
        config.addAllowedOrigin("*")
        config.addAllowedHeader("*")
        config.addAllowedMethod("OPTIONS")
        config.addAllowedMethod("HEAD")
        config.addAllowedMethod("GET")
        config.addAllowedMethod("PUT")
        config.addAllowedMethod("POST")
        config.addAllowedMethod("DELETE")
        config.addAllowedMethod("PATCH")
        source.registerCorsConfiguration("/**", config)
        return CorsFilter(source)
    }
}
```


## 参考链接

- [Spring Cloud - Zuul Proxy is producing a No 'Access-Control-Allow-Origin' ajax response
](https://stackoverflow.com/questions/28670640/spring-cloud-zuul-proxy-is-producing-a-no-access-control-allow-origin-ajax-r)
- [zuul动态路由加载配置](https://segmentfault.com/a/1190000009458575)
- [ zuul动态配置路由规则，从DB读取](http://blog.csdn.net/tianyaleixiaowu/article/details/77933295?locationNum=5&fps=1)
