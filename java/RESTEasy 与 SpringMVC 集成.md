# RESTEasy 与 SpringMVC 集成
## Why
- spring restful api 注解不如 javax.ws.rs 的注解简洁
- 还想用 spring framework 提供的便利

## RESTEasy 与 Spring 集成的方式
> Spring和resteasy集成，主要有三种方式：
- 运行于Servlet版本大于等于3.0
- 运行于Servlet版本小于3.0
- 将resteasy和spring mvc整合在一起

## 配置依赖
```groovy
compile group: 'org.jboss.resteasy', name: 'resteasy-jaxrs', version: '3.1.2.Final'
compile group: 'org.jboss.resteasy', name: 'resteasy-jaxb-provider', version: '3.1.2.Final'
compile group: 'org.jboss.resteasy', name: 'resteasy-jackson2-provider', version: '3.1.2.Final'
compile group: 'org.jboss.resteasy', name: 'resteasy-spring', version: '3.1.2.Final'
compile group: 'org.jboss.resteasy', name: 'resteasy-multipart-provider', version: '3.1.2.Final'
compile group: 'org.jboss.resteasy', name: 'resteasy-servlet-initializer', version: '3.1.2.Final'
```

## 配置 web.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
    <servlet>
        <servlet-name>servlet</servlet-name>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/spring-servlet.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>servlet</servlet-name>
        <url-pattern>/*</url-pattern>
    </servlet-mapping>
</web-app>
```

## spring/spring-servlet.xml
```xml
<?xml version = "1.0" encoding = "UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/context http://www.springframework.org/schema/context/spring-context-2.5.xsd
        http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">
    <context:component-scan base-package="com.gosun.daily.report.controller"/>
    <context:annotation-config/>
    <import resource="classpath:spring/mybatis_config.xml"/>
    <import resource="classpath:springmvc-resteasy.xml" />
</beans>
```
springmvc-resteasy.xml 不用自定义，但是需要加进去。
```xml
<import resource="classpath:springmvc-resteasy.xml" />
```

## Controller
```java
@Controller
@Path("user")
public class UserController {
    @Autowired
    UserMapper mapper;
    @GET
    @Path("{id}")
    @Produces({"application/json;charset=UTF-8"})
    public User getUser(@PathParam("id") Integer id){
        User user = mapper.selectByPrimaryKey(id);
        System.out.println(user);
        return user;
    }
}
```

## spring mvc date json format
- CustomObjectMapper
```java
public class CustomObjectMapper extends ObjectMapper {
    private static final String DEFAULT_DATE_FORMAT = "yyyy-MM-dd HH:mm:ss";
    public CustomObjectMapper() {
        SimpleDateFormat dateFormat = new SimpleDateFormat(DEFAULT_DATE_FORMAT);
        setDateFormat(dateFormat);
    }
}
```
- 配置 mvc:annotation-driven
```xml
<?xml version = "1.0" encoding = "UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd">
    <bean id="customObjectMapper" class="com.gosun.daily.report.provider.CustomObjectMapper"/>
    <mvc:annotation-driven>
        <mvc:message-converters>
            <bean class="org.springframework.http.converter.json.MappingJackson2HttpMessageConverter">
                <property name="objectMapper" ref="customObjectMapper"/>
            </bean>
        </mvc:message-converters>
    </mvc:annotation-driven>
</beans>
```

## spring mvc 配置 date json 格式
JacksonConfig
```java
public class JacksonConfig implements ContextResolver<ObjectMapper> {
    private  final ObjectMapper mapper = new ObjectMapper();

    public JacksonConfig() {
        mapper.configure(SerializationFeature.WRITE_DATES_AS_TIMESTAMPS,false);
        mapper.setDateFormat(new SimpleDateFormat("yyyy-MM-dd"));
        // 设置为 null 的值不参加序列化
        mapper.setSerializationInclusion(Include.NON_NULL);
    }

    @Override
    public ObjectMapper getContext(Class<?> aClass) {
        return mapper;
    }
}
```

## 配置 resteasy.providers
```xml
<bean id="resteasy.providers" class="com.gosun.daily.report.provider.JacksonConfig"/>
```

## 参考链接
- [resteasy-springMVC example](https://github.com/resteasy/Resteasy/tree/3.0.4.Final/jaxrs/examples/resteasy-springMVC)
- [SpringFramework4系列之整合Resteasy](https://my.oschina.net/u/1041012/blog/481135)
- [jackson 实体转json 为NULL或者为空不参加序列化](http://www.cnblogs.com/yangy608/p/3936848.html)
