# http 几种授权方式

## 授权过程
- 客户端请求 url
- 服务器解析 url ，如果解析不到授权信息，会返回 401 错误

TODO : 整理

## basic

```kotlin
@EnableWebSecurity
class SecurityConfig : WebSecurityConfigurerAdapter() {
    override fun configure(http: HttpSecurity) {
        http.authorizeRequests()
                .antMatchers("/**").hasRole("USER")
                .and()
                .httpBasic()
                .realmName("hello")
                .authenticationEntryPoint { request, response, authException ->
                    response.addHeader("WWW-Authenticate", "Basic realm=\"hello\"")
                    response.status = HttpServletResponse.SC_UNAUTHORIZED
                    val writer = response.writer
                    writer.println("HTTP Status 401 - ")
                }

    }

    @Autowired
    fun configureGlobal(auth: AuthenticationManagerBuilder) {
        auth.inMemoryAuthentication()
                .withUser("user")
                .password("password")
                .roles("USER")
    }
}
```

## digest

## OAuth 1.0

## OAuth 2.0

## Hask Authentication

## AWS Signature

## 参考链接
- [Http认证之Basic认证](http://blog.csdn.net/zmx729618/article/details/51371999)
- [http digest](http://www.jianshu.com/p/18fb07f2f65e)
- [Spring Security Basic Authentication](http://www.baeldung.com/spring-security-basic-authentication)
