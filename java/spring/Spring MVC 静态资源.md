# Spring MVC 静态资源

## 激活Tomcat的defaultServlet来处理静态文件
```xml
<web-app xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xmlns="http://java.sun.com/xml/ns/javaee"
         xsi:schemaLocation="http://java.sun.com/xml/ns/javaee http://java.sun.com/xml/ns/javaee/web-app_3_0.xsd"
         id="WebApp_ID" version="3.0">
    <servlet-mapping>
        <servlet-name>default</servlet-name>
        <url-pattern>*.ico</url-pattern>
    </servlet-mapping>
    <servlet>
        <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
        <servlet-name>dispatcher</servlet-name>
        <init-param>
            <param-name>contextConfigLocation</param-name>
            <param-value>classpath:spring/spring-servlet.xml</param-value>
        </init-param>
    </servlet>
    <servlet-mapping>
        <servlet-name>dispatcher</servlet-name>
        <url-pattern>/</url-pattern>
    </servlet-mapping>

</web-app>
```

## mvc:resources
```xml
<mvc:resources mapping="/static/**" location="/static/"/>
```

## Spring Boot 静态资源

### 默认静态资源映射

Spring Boot 默认将 /** 所有访问映射到以下目录：

```
classpath:/static
classpath:/public
classpath:/resources
classpath:/META-INF/resources
```
在 `classpath:/static` 文件夹下，有一张 a.jpg 的图片，在 `classpath:/public` 文件夹下，有一张 b.jpg 图片。

可以通过下面的路径访问到：
```
http://localhost:8080/a.jpg
http://localhost:8080/b.jpg
```

### 自定义静态资源映射

#### 静态资源配置

```java
@Configuration
public class WebMvcConfig extends WebMvcConfigurerAdapter {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        //将所有/static/** 访问都映射到classpath:/static/ 目录下
        registry.addResourceHandler("/static/**").addResourceLocations("classpath:/static/");
    }
}
```

对于上面的两张图片，依旧可以通过，上面的路径访问到；a.jpg 多了一种访问方式：

```
http://localhost:8080/static/a.jpg
```

#### application 配置文件

在application.properties中添加配置：

```
spring.mvc.static-path-pattern=/static/**
```

**注意：通过 spring.mvc.static-path-pattern 这种方式配置，会使 Spring Boot 的默认配置失效，也就是说，/public /resources 等默认配置不能使用。**

对于上面的两张图片，b.jpg 不在能通过 url 进行访问，a.jpg 只能通过 `http://localhost:8080/static/a.jpg` 进行访问。


## 参考链接
- [SpringMVC访问静态资源的三种方式](http://blog.163.com/koko_qiang/blog/static/207213184201382091154584/)
- [Spring Boot 系列（四）静态资源处理](https://www.cnblogs.com/magicalSam/p/7189476.html)
