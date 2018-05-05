# Spring Security

## 依赖配置

```groovy
compile 'org.springframework.cloud:spring-cloud-starter-security:1.2.2.RELEASE'
compile "org.springframework.boot:spring-boot-starter-web:$spring_boot_version"
```

## 两个简单的 restful 接口

```kotlin
@SpringBootApplication
@RestController
class SecurityApp {
    @PreAuthorize("hasRole('ROLE_user')")
    @RequestMapping("/hello")
    fun hello():String{
        return "hello user"
    }

    @PreAuthorize("hasRole('ROLE_admin')")
    @RequestMapping("/admin")
    fun helloAdmin():String{
        return "hello admin"
    }
}

fun main(args: Array<String>) {
    SpringApplication.run(SecurityApp::class.java, *args)
}
```

## 建两个用户

```kotlin
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
class SecurityConfig : WebSecurityConfigurerAdapter() {
    override fun configure(auth: AuthenticationManagerBuilder) {
        auth.inMemoryAuthentication()
                .withUser("user")
                .roles("user")
                .password("user")
                .and()
                .withUser("admin")
                .roles("admin")
                .password("admin")
    }
}
```

**_注意_**

使用 `@PreAuthorize("hasRole('ROLE_user')")` 注解，需要 `@EnableGlobalMethodSecurity(prePostEnabled = true)` 配套使用，否则不起作用。

## application.yml

配置服务端口：

```yaml
server:
  port: 8190
```

## 运行

- 运行 main 方法
- 输入 `http://localhost:8190/hello`，会跳转到 `http://localhost:8190/login`
- 输入用户名 user 密码 user 登录，又会回到 `http://localhost:8190/hello` ，显示 hello user
- 输入 `http://localhost:8190/admin` 会报 403 错误

## basic 认证以及 digest 认证

查看 [http 几种授权方式](../web/http 几种授权方式.md)
