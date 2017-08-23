# dubbo

## dubbo 是什么
> Dubbo是一个分布式服务框架，致力于提供高性能和透明化的RPC远程服务调用方案，以及SOA服务治理方案。简单的说，dubbo就是个服务框架，如果没有分布式的需求，其实是不需要用的，只有在分布式的时候，才有dubbo这样的分布式服务框架的需求，并且本质上是个服务调用的东东，说白了就是个远程服务调用的分布式框架（告别Web Service模式中的WSdl，以服务者与消费者的方式在dubbo上注册）

- RPC (Remote Procedure Call) : 远程过程调用协议
- SOA (service-oriented architecture) ： 面向服务的体系结构

**dubbo 架构图**

![dubbo 架构](http://dubbo.io/dubbo-architecture.jpg-version=1&modificationDate=1330892870000.jpg)

## 配置依赖
```groovy
compile 'org.springframework.boot:spring-context:4.3.7.RELEASE'
compile 'com.alibaba:dubbo:2.5.3'
compile 'com.101tec:zkclient:0.9'
```

## 接口
```java
public interface DemoService {
    String sayHello(String name);
}
```

## 接口实现
```java
public class DemoServiceImpl implements  DemoService{
    @Override
    public String sayHello(String name) {
        return "hello" + name;
    }
}
```

接口配置，命名为 provider.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://code.alibabatech.com/schema/dubbo
        http://code.alibabatech.com/schema/dubbo/dubbo.xsd
        ">
    <!-- 具体的实现bean -->
    <bean id="demoService"
          class="com.lcy.demo.DemoServiceImpl" />
    <!-- 提供方应用信息，用于计算依赖关系 -->
    <dubbo:application name="anyname_provider" />
    <!-- 使用zookeeper注册中心暴露服务地址 -->
    <dubbo:registry address="zookeeper://127.0.0.1:2181" />
    <!-- 用dubbo协议在20880端口暴露服务 -->
    <dubbo:protocol name="dubbo" port="20880" />
    <!-- 声明需要暴露的服务接口 -->
    <dubbo:service interface="com.lcy.demo.DemoService"
                   ref="demoService" />
</beans>
```

## provider

负责提供 demoService ，先启动 zookeeper ，在运行 main 方法。

```java
public class Provider {
    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"provider.xml"});
        context.start();

        System.in.read(); // 按任意键退出
    }
}
```

## consumer

远程调用 demoService。

```java
public class Consumer {
    public static void main(String[] args) throws Exception {
        ClassPathXmlApplicationContext context = new ClassPathXmlApplicationContext(new String[] {"consumer.xml"});
        context.start();

        DemoService demoService = (DemoService)context.getBean("demoService"); // 获取远程服务代理
        String hello = demoService.sayHello("world"); // 执行远程方法

        System.out.println( hello ); // 显示调用结果
    }
}
```

consumer 配置文件 consumer.xml
```xml
<?xml version="1.0" encoding="UTF-8"?>
<beans xmlns="http://www.springframework.org/schema/beans"
       xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
       xmlns:dubbo="http://code.alibabatech.com/schema/dubbo"
       xsi:schemaLocation="http://www.springframework.org/schema/beans
        http://www.springframework.org/schema/beans/spring-beans.xsd
        http://code.alibabatech.com/schema/dubbo
        http://code.alibabatech.com/schema/dubbo/dubbo.xsd
        ">

    <dubbo:application name="consumer_app" />

    <dubbo:registry address="zookeeper://127.0.0.1:2181" />
    <dubbo:consumer timeout="5000" />

    <dubbo:reference id="demoService"
                     interface="com.lcy.demo.DemoService" />
</beans>
```

TODO : 深入

## 参考链接
- [Dubbo项目demo搭建](http://www.cnblogs.com/fri-yu/p/5981436.html)
- [Dubbo是什么](http://blog.csdn.net/ichsonx/article/details/39008519)
- [JAVA中几种常用的RPC框架介绍](http://blog.csdn.net/zhaowen25/article/details/45443951)
