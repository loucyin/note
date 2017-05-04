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

## 参考链接
- [SpringMVC访问静态资源的三种方式](http://blog.163.com/koko_qiang/blog/static/207213184201382091154584/)
