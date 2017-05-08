# spring 单元测试

## dependence

```groovy
testCompile group: 'junit', name: 'junit', version: '4.12'
compile group: 'org.springframework', name: 'spring-test', version: '4.3.8.RELEASE'
```

## spring 配置 context:component-scan

代码中会使用 @Service @Component @Controller 等对 class 进行注解，在 xml 中通过配置 context:component-scan 可以把注解过的类加入到 context 中。

```xml
<?xml version = "1.0" encoding = "UTF-8"?>

<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:mvc="http://www.springframework.org/schema/mvc"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:context="http://www.springframework.org/schema/context"
       xsi:schemaLocation="
        http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://www.springframework.org/schema/mvc
        http://www.springframework.org/schema/mvc/spring-mvc.xsd
        http://www.springframework.org/schema/context
        http://www.springframework.org/schema/context/spring-context.xsd">
        <!--配置 component 扫描 package-->
        <context:component-scan base-package="com.gosun.daily.report"/>

        <!--引入需要用到的 xml-->
        <import resource="classpath:spring/service.xml"/>
        <import resource="classpath:spring/mybatis_config.xml"/>

        <!--配置 Autowired-->
        <bean class="org.springframework.beans.factory.annotation.AutowiredAnnotationBeanPostProcessor"/>
</beans>
```

## 获取 ApplicationContext
test 可以访问到 main 中的 resource 资源

静态导包
```java
import static org.junit.Assert.*;
```

测试代码
```java
public class ServiceTest {
    @Test
    public void userServiceTest(){
        ApplicationContext applicationContext = new ClassPathXmlApplicationContext("spring/beans.xml");
        UserService userService = applicationContext.getBean(UserService.class);
        assertNotNull(userService);
    }
}
```

## 注解

通过 ContextConfiguration 可以配置 xml 文件的加载位置，可以通过 @Autowired 获取相应的对象。

```java
@RunWith(SpringJUnit4ClassRunner.class)
@ContextConfiguration(locations = {"classpath:beans.xml"})
public abstract class BaseUnitTest {
    @Autowired
    protected ApplicationContext context;

    @Test
    public abstract void autoWiredTest();
}
```
