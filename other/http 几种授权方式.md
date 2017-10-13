# http 几种授权方式

- Basic
- Digest
- OAuth 1.0
- OAuth 1.0a
- OAuth 2.0
- Hask Authentication
- AWS Signature

## 授权过程
- 客户端请求 url
- 服务器解析 request ，如果解析不到授权信息，会返回 401 错误

## basic
通过浏览器访问 basic 认证的 url 会出现输入用户名、密码的对话框。

basic 发送认证信息的两种方式：
- url 前拼接用户名密码

  ```
  http://user:password@localhost:8080
  ```

- 请求头中添加 Authorization ，格式（Basic base64(user:password)）如下：

  ```
  Authorization:Basic dXNlcjpwYXNzd29yZA==
  ```

### 注意
- 由于 base64 算法是可逆的所以，可以将密码进行 md5 运算，转换为不可逆的，但是任然无法避免 Replay Attack
- 在 http 请求中使用 basic 授权方式并不安全，可以通过使用 https 提高安全系数。


通过 Spring Security 使用 basic Authentication：

```kotlin
@Configuration
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

### HttpSecurity 几种鉴权方式

- 登录权限
  ```java
  http.authorizeRequests().antMatchers("/**").authenticated()
  ```
  需要用户通过用户鉴权
- 角色
  ```java
  http.authorizeRequests().antMatchers("/**").hasRole("USER")
  ```
  登录用户是否拥有 USER 角色
- IP
  ```java
  http.authorizeRequests().antMatchers("/**").hasIpAddress("192.168.1.31")
  ```
  需要用户通过 ip 地址访问 url

### 使用数据库提供用户数据

```kotlin
@Autowired
fun configureGlobal(auth: AuthenticationManagerBuilder) {
    auth.jdbcAuthentication()
            .dataSource(dataSource)
            .usersByUsernameQuery("select name as username,password,id > 0 as enabled from t_user where name = ?")
            .authoritiesByUsernameQuery("select A.name as username ,C.name as role from  (select * from t_user where name = ?) as A\n" +
                    "left join  t_user_role B\n" +
                    "on B.user_id = A.id\n" +
                    "left join t_role C\n" +
                        "on C.id = B.role_id")
            .rolePrefix("ROLE_")

    filter.userDetailsService = auth.defaultUserDetailsService
}
```

```sql
CREATE TABLE IF NOT EXISTS `mydb`.`t_user` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(45) NOT NULL,
  `password` VARCHAR(45) NOT NULL,
  PRIMARY KEY (`id`))
ENGINE = InnoDB;
CREATE TABLE IF NOT EXISTS `mydb`.`t_role` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `name` VARCHAR(45) NOT NULL,
  `parent_id` INT NULL,
  PRIMARY KEY (`id`),
  INDEX `fk_parent_id_idx` (`parent_id` ASC),
  CONSTRAINT `fk_parent_role_id`
    FOREIGN KEY (`parent_id`)
    REFERENCES `mydb`.`t_role` (`id`)
    ON DELETE RESTRICT
    ON UPDATE NO ACTION)
ENGINE = InnoDB;
CREATE TABLE IF NOT EXISTS `mydb`.`t_user_role` (
  `id` INT NOT NULL AUTO_INCREMENT,
  `user_id` INT NOT NULL,
  `role_id` INT NOT NULL,
  PRIMARY KEY (`id`),
  INDEX `fk_user_id_idx` (`user_id` ASC),
  INDEX `fk_role_id_idx` (`role_id` ASC),
  UNIQUE INDEX `uk_user_role` (`user_id` ASC, `role_id` ASC),
  CONSTRAINT `fk_ur_user_id`
    FOREIGN KEY (`user_id`)
    REFERENCES `mydb`.`t_user` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION,
  CONSTRAINT `fk_ur_role_id`
    FOREIGN KEY (`role_id`)
    REFERENCES `mydb`.`t_role` (`id`)
    ON DELETE NO ACTION
    ON UPDATE NO ACTION)
ENGINE = InnoDB;
```

通过下面方式指定角色权限的时候：
```java
http.authorizeRequests().antMatchers("/**").hasRole("USER")
```
需要添加前缀`ROLE_`。

## digest

Digest Authentication比Basic安全，但是并不是真正的什么都不怕了，Digest Authentication这种容易方式容易收到Man in the Middle式攻击。

header 授权参数：
```
Authorization:Digest username="user", realm="Contacts Realm via Digest Authentication", nonce="MTUwNzUxODY1OTI2ODpkZmU0YzY0ZDJmMDgzMTZmMTdiY2IwZThlZmY1MmYzMg==", uri="/users/int", response="d76c8ef4d8adfc32fefacd91636d2cc0", qop=auth, nc=00000002, cnonce="e587bc6d02d9eead"
```

参数介绍：
- username: 用户名（网站定义）
- password: 密码
- realm:　服务器返回的realm,一般是域名
- method: 请求的方法
- nonce: 服务器发给客户端的随机的字符串
- nc(nonceCount):请求的次数，用于标记，计数，防止重放攻击
- cnonce(clinetNonce): 客户端发送给服务器的随机字符串
- qop: 保护质量参数,一般是auth,或auth-int,这会影响摘要的算法
- uri: 请求的uri
- response:　客户端根据算法算出的摘要值

digest 算法：
> A1 = username:realm:password
A2 = mthod:uri
HA1 = MD5(A1)
如果 qop 值为“auth”或未指定，那么 HA2 为
HA2 = MD5(A2)=MD5(method:uri)
如果 qop 值为“auth-int”，那么 HA2 为
HA2 = MD5(A2)=MD5(method:uri:MD5(entityBody))
如果 qop 值为“auth”或“auth-int”，那么如下计算 response：
response = MD5(HA1:nonce:nc:cnonce:qop:HA2)
如果 qop 未指定，那么如下计算 response：
response = MD5(HA1:nonce:HA2)


通过 Spring Security 使用 digest Authentication：
- 代码中配置 digest Authentication

  ```kotlin
  @Configuration
  @EnableWebSecurity
  class SecurityConfig : WebSecurityConfigurerAdapter() {
      val digestEntryPoint = digestEntryPoint()
      val filter = digestFilter()

      override fun configure(http: HttpSecurity) {
          http.authorizeRequests()
                  .antMatchers("/**")
                  .hasRole("USER")
                  .and()
                  .addFilter(filter)
                  .httpBasic()
                  .authenticationEntryPoint(digestEntryPoint)
      }

      @Autowired
      fun configureGlobal(auth: AuthenticationManagerBuilder) {
          auth.inMemoryAuthentication()
                  .withUser("user")
                  .password("password")
                  .roles("USER")
          filter.userDetailsService = auth.defaultUserDetailsService
      }

      final fun digestFilter():DigestAuthenticationFilter{
          val digestFilter = DigestAuthenticationFilter()
          digestFilter.setAuthenticationEntryPoint(digestEntryPoint)
          return digestFilter
      }

      final fun digestEntryPoint():DigestAuthenticationEntryPoint{
          val point = DigestAuthenticationEntryPoint()
          point.key = "web test"
          point.realmName = "Contacts Realm via Digest Authentication"
          return point
      }
  }
  ```

- xml 配置
  ```xml
  <?xml version="1.0" encoding="UTF-8"?>
  <beans:beans xmlns="http://www.springframework.org/schema/security"
               xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
               xmlns:beans="http://www.springframework.org/schema/beans"
               xsi:schemaLocation="
          http://www.springframework.org/schema/security
          http://www.springframework.org/schema/security/spring-security.xsd
          http://www.springframework.org/schema/beans
          http://www.springframework.org/schema/beans/spring-beans.xsd">
      <beans:bean id="digestFilter"
                  class="org.springframework.security.web.authentication.www.DigestAuthenticationFilter">
          <beans:property name="userDetailsService" ref="userService" />
          <beans:property name="authenticationEntryPoint" ref="digestEntryPoint" />
      </beans:bean>
      <beans:bean id="digestEntryPoint"
                  class="org.springframework.security.web.authentication.www.DigestAuthenticationEntryPoint">
          <beans:property name="realmName" value="Contacts Realm via Digest Authentication" />
          <beans:property name="key" value="acegi" />
      </beans:bean>
      <!-- the security namespace configuration -->
      <http use-expressions="true" entry-point-ref="digestEntryPoint">
          <intercept-url pattern="/**" access="isAuthenticated()" />
          <custom-filter ref="digestFilter" after="BASIC_AUTH_FILTER" />
      </http>
      <authentication-manager>
          <authentication-provider>
              <user-service id="userService">
                  <user name="user" password="password" authorities="USER" />
              </user-service>
          </authentication-provider>
      </authentication-manager>
  </beans:beans>
  ```

## OAuth

OAuth 有 3 个版本 OAuth 1.0 、OAuth 1.0a、OAuth 2.0。

- 由于 OAuth 1.0 有安全漏洞，就出现了 OAuth 1.0a
- 修复完 bug 后的 OAuth 1.0a 过于复杂，就出现了 OAuth2.0

TODO : 整理

## 参考链接
- [Http认证之Basic认证](http://blog.csdn.net/zmx729618/article/details/51371999)
- [http digest](http://www.jianshu.com/p/18fb07f2f65e)
- [Spring Security Basic Authentication](http://www.baeldung.com/spring-security-basic-authentication)
- [小话HTTP Authentication](http://blog.csdn.net/kiwi_coder/article/details/28677651)
- [Oauth 1.0 1.0a 和 2.0 的之间的区别有哪些？](https://www.zhihu.com/question/19851243)
- [理解OAuth 2.0](http://www.ruanyifeng.com/blog/2014/05/oauth_2_0.html)
