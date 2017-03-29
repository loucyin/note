# spring mybatis

## dependence

```groovy
compile 'mysql:mysql-connector-java:5.1.40'

compile 'org.mybatis:mybatis:3.4.2'
compile 'org.mybatis:mybatis-spring:1.3.1'

compile("org.springframework.boot:spring-boot-starter-web:1.5.2.RELEASE")
compile("org.springframework:spring-web:4.3.7.RELEASE")
compile("com.fasterxml.jackson.core:jackson-databind")
compile group: 'org.springframework', name: 'spring-jdbc', version: '4.3.7.RELEASE'
```

## User
```java
public class User {
    private Integer id;
    private String name;
    private Integer gender;
    private Integer age;

    public Integer getId() {
        return id;
    }

    public void setId(Integer id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public Integer getGender() {
        return gender;
    }

    public void setGender(Integer gender) {
        this.gender = gender;
    }

    public Integer getAge() {
        return age;
    }

    public void setAge(Integer age) {
        this.age = age;
    }
}
```

## UserService

```java
public interface UserService {
    Integer insertUser(User user);
    User getUser(Integer id);
}
```

**两种方式注入：xml 和 注解**

***

## xml 注入

***

## beans.xml
```xml
<?xml version = "1.0" encoding = "UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xsi:schemaLocation="http://www.springframework.org/schema/beans http://www.springframework.org/schema/beans/spring-beans.xsd">

    <bean id="dataSource" class="org.springframework.jdbc.datasource.DriverManagerDataSource">
        <property name="driverClassName" value="com.mysql.jdbc.Driver" />
        <property name="url" value="jdbc:mysql://localhost:3306/demo?useSSL=true&amp;characterEncoding=UTF-8&amp;serverTimezone=UTC" />
        <property name="username" value="root" />
        <property name="password" value="gc2017" />
    </bean>

    <bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
        <property name="dataSource" ref="dataSource" />
        <property name="typeAliasesPackage" value="com.lcy.demo.app"/>
        <property name="mapperLocations" value="classpath*:mapper/*.xml" />
    </bean>

    <bean id="userService" class="com.lcy.demo.app.UserServiceImpl">
        <property name="userMapper" ref="userMapper"/>
    </bean>
    <bean class="org.mybatis.spring.mapper.MapperScannerConfigurer">
        <property name="basePackage" value="com.lcy.demo.app" />
        <property name="sqlSessionFactoryBeanName" value="sqlSessionFactory" />
    </bean>
    <bean id="userController" class="com.lcy.demo.app.UserController">
        <property name="userService" ref="userService"/>
    </bean>
</beans>
```

## Application

通过 SpringBoot 启动程序。

**注意使用 @ImportResource("beans.xml") 引入资源文件**

```java

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.context.annotation.ImportResource;

@SpringBootApplication
@ImportResource("beans.xml")
public class Application {

    public static void main(String[] args) {
        SpringApplication.run(Application.class, args);
    }
}
```

## UserController

```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;
@RestController
public class UserController {

    private UserService userService;

    @RequestMapping("/user")
    public User user(@RequestParam(value = "id",defaultValue = "1") Integer id){
        return userService.getUser(id);
    }

    public void setUserService(UserService userService) {
        this.userService = userService;
    }

    @RequestMapping("/insertUser")
    public Integer insertUser(@RequestParam(value = "name") String name,
                              @RequestParam(value = "age") Integer age,
                              @RequestParam(value = "gender")Integer gender){

        User user = new User();
        user.setAge(age);
        user.setGender(gender);
        user.setName(name);
        return userService.insertUser(user);
    }
}
```

## UserMapper

```java
public interface UserMapper {
    User getUser( Integer userId);

    Integer insertUser(User user);
}
```

UserMapper.xml
```xml
<?xml version="1.0" encoding="UTF-8" ?>
<!DOCTYPE mapper
        PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
        "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.lcy.demo.app.UserMapper">
    <select id="getUser" resultType="com.lcy.demo.app.User" parameterType="Integer">
        select * from user where id = #{userId}
    </select>
    <insert id="insertUser"  parameterType="com.lcy.demo.app.User">
        insert user(name,age,gender) values(#{name},#{age},#{gender})
    </insert>
</mapper>
```

## UserServiceImpl

```java
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.stereotype.Service;

public class UserServiceImpl implements UserService{
    private UserMapper userMapper;

    @Override
    public Integer insertUser(String name, Integer age, Integer gender) {
        return userMapper.insertUser(name,age,gender);
    }

    @Override
    public User getUser(Integer id) {
        return userMapper.getUser(id);
    }

    @Autowired
    public void setUserMapper(UserMapper userMapper) {
        this.userMapper = userMapper;
    }
}
```

****

## 注解注入

****

## Mapper

UserMapper 需要使用 `@Repository` 注册组件。 

```java
import org.apache.ibatis.annotations.Insert;
import org.apache.ibatis.annotations.Param;
import org.apache.ibatis.annotations.Select;
import org.springframework.stereotype.Repository;

@Repository
public interface UserMapper {
    @Select("SELECT * FROM user WHERE id = #{userId}")
    User getUser(@Param("userId") Integer userId);

    @Insert("insert user(name,age,gender) values(#{name},#{age},#{gender})")
    Integer insertUser(@Param("name") String name,@Param("age") Integer age,@Param("gender") Integer gender);
}
```

## Application

和 xml 注入方式不同，不需要 `@ImportResource("beans.xml")` ；但是需要使用 `@MapperScan("com.lcy.demo.crud")` 扫描 UserMapper 所在的包。

在需要注入的地方使用 `@Autowired`。


## 参考链接
- [mybatis](http://www.mybatis.org/mybatis-3/zh/getting-started.html)
- [mybatis-spring](http://www.mybatis.org/spring/zh/)
- [Spring 4 and MyBatis Java Config](https://blog.lanyonm.org/articles/2014/04/21/spring-4-mybatis-java-config.html)
- [Spring Boot](https://projects.spring.io/spring-boot/)
- [Building Java Web Application Using MyBatis With Spring](http://elizabetht.github.io/blog/2013/11/21/student-enrollment-using-mybatis-with-spring/)
