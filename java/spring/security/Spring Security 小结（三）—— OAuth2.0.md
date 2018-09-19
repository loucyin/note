# Spring Security 小结（三）---- OAuth2.0

## 关于 OAuth

OAuth 有 3 个版本 OAuth 1.0 、OAuth 1.0a、OAuth 2.0。

- 由于 OAuth 1.0 有安全漏洞，就出现了 OAuth 1.0a
- 修复完 bug 后的 OAuth 1.0a 过于复杂，就出现了 OAuth2.0

关于 OAuth 详细信息，找参考链接。

## 依赖

```groovy
dependencies {
    implementation "org.jetbrains.kotlin:kotlin-stdlib-jdk8:$kotlinVersion"
    implementation "org.springframework.cloud:spring-cloud-starter-security"
    implementation "org.springframework.boot:spring-boot-starter-web"
    implementation "org.springframework.cloud:spring-cloud-starter-oauth2"
}
```

## 授权中心

### 提供 WebSecurity 配置

```kotlin
@Configuration
@EnableGlobalMethodSecurity(prePostEnabled = true)
@EnableWebSecurity
class SecurityConfig : WebSecurityConfigurerAdapter() {

    override fun configure(auth: AuthenticationManagerBuilder) {
        auth.userDetailsService(userDetailsService())
    }

    override fun configure(http: HttpSecurity) {
        http.authorizeRequests().antMatchers(HttpMethod.OPTIONS).permitAll().anyRequest().authenticated().and()
                .httpBasic().and().csrf().disable()
    }

    @Bean
    override fun authenticationManagerBean(): AuthenticationManager {
        return super.authenticationManagerBean()
    }

    @Bean
    override fun userDetailsService(): UserDetailsService {
        return UserDetailsService {
            User(it, it)
        }
    }
}
```

**注意事项**

- 重写 authenticationManagerBean() 方法，提供 AuthenticationManager bean ，会在授权中心服务的配置中用到；
- 如果有用到 userDetailsService 的地方，重写 userDetailsService() 方法，提供 UserDetailsService bean。

### 提供授权服务配置

```kotlin
@Configuration
@EnableAuthorizationServer
class AuthServerConfig : AuthorizationServerConfigurerAdapter() {

    @Value("\${access_token.validity_period:3600}") // 默认值3600
    var accessTokenValiditySeconds = 3600

    @Autowired
    lateinit var authenticationManager: AuthenticationManager

    override fun configure(security: AuthorizationServerSecurityConfigurer) {
        // 允许客户端通过表单获取授权
        security.allowFormAuthenticationForClients()
    }

    override fun configure(endpoints: AuthorizationServerEndpointsConfigurer) {
        endpoints.tokenStore(tokenStore())
                .accessTokenConverter(accessTokenConverter())
                .authenticationManager(authenticationManager)
    }

    override fun configure(clients: ClientDetailsServiceConfigurer) {
        clients.inMemory()
                .withClient("server")
                .authorizedGrantTypes("authorization_code", "implicit","client_credentials", "password")
                .authorities("ROLE_CLIENT")
                .scopes("xx")
                .accessTokenValiditySeconds(accessTokenValiditySeconds)
                .secret("server");
    }

    @Bean
    fun accessTokenConverter(): JwtAccessTokenConverter {
        val accessTokenConverter = object :JwtAccessTokenConverter(){

        }

        accessTokenConverter.setSigningKey("123")
        return accessTokenConverter
    }

    @Bean
    fun tokenStore(): TokenStore {
        return JwtTokenStore(accessTokenConverter())
    }
}
```

### Spring Boot 1.5 版本的改动

[OAuth 2 Resource Filter](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-1.5-Release-Notes#oauth-2-resource-filter)

> The default order of the OAuth2 resource filter has changed from `3` to `SecurityProperties.ACCESS_OVERRIDE_ORDER - 1`. This places it after the actuator endpoints but before the basic authentication filter chain. The default can be restored by setting `security.oauth2.resource.filter-order = 3`

在 application.yml 中添加：

```yaml
server:
  port: 8191

security:
  oauth2:
    resource:
      filter-order: 3
```

### 启动类

```kotlin
@SpringBootApplication
@RestController
@EnableResourceServer
class AuthServerApp {

    @Autowired
    lateinit var userDetailsService: UserDetailsService

    @RequestMapping("/user")
    fun user(principal: Principal): UserDetails {
        println(principal)
        return userDetailsService.loadUserByUsername(principal.name)
    }
}
fun main(args: Array<String>) {
    SpringApplication.run(AuthServerApp::class.java, *args)
}
```

提供 user 接口，给其他资源服务器获取用户信息。

### 手动配置资源服务器

如果需要添加其他配置选项

```kotlin
@Configuration
@EnableResourceServer
class ResourceServerConfig :ResourceServerConfigurerAdapter(){
    @Value("\${resource.id:spring-boot-application}")
    lateinit var resourceId: String

    override fun configure(resources: ResourceServerSecurityConfigurer) {
        resources.resourceId(resourceId)
    }

    override fun configure(http: HttpSecurity) {
        http.authorizeRequests()
                .anyRequest().authenticated()
    }
}
```

**注意事项**

要配置资源 id ，否则，授权失败。

## 独立的资源服务

### 提供 WebSecurity 配置

```kotlin
@EnableWebSecurity
@EnableGlobalMethodSecurity(prePostEnabled = true)
class SecurityConfig : WebSecurityConfigurerAdapter() {
    override fun configure(http: HttpSecurity) {
        http.authorizeRequests()
                .antMatchers(HttpMethod.OPTIONS).permitAll()
                .anyRequest()
                .authenticated()
    }
}
```

### 启动类

```kotlin
@SpringBootApplication
@EnableOAuth2Sso
@EnableResourceServer
@RestController
class SecurityApp {
    @PreAuthorize("hasRole('ROLE_user')")
    @RequestMapping("/hello")
    fun hello(): String {
        return "hello user"
    }

    @PreAuthorize("hasRole('ROLE_admin')")
    @RequestMapping("/admin")
    fun helloAdmin(): String {
        return "hello admin"
    }
}
fun main(args: Array<String>) {
    SpringApplication.run(SecurityApp::class.java, *args)
}
```

提供两个接口用于测试不同的角色。

### application.yaml

```yml
server:
  port: 8190

security:
  oauth2:
    resource:
      userInfoUri: http://localhost:8191/user
      preferTokenInfo: false
      filter-order: 3
```

## 参考链接

- [理解 OAuth2.0 认证与客户端授权码模式详解](https://segmentfault.com/a/1190000010540911)
