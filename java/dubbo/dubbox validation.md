# dubbox validation

## dubbo 支持 validation

在 dubbo-filter dubbo-filter-validation 中有对 Validation 的支持，需要在服务注册的时候声明使用 validation

```xml
<dubbo:service interface="com.gosun.isap.resource.api.box.rest.BoxRestService" ref="boxRestService" protocol="rest" validation="true"/>
```

## resteasy

dubbox 使用 resteasy 实现了 rest 协议，resteasy 使用 validator 依赖于 resteasy-validator-provider-11：

```xml
<dependency>
  <groupId>org.jboss.resteasy</groupId>
  <artifactId>resteasy-validator-provider-11</artifactId>
  <version>3.1.2.Final</version>
</dependency>
```

## dubbox 的 validation

### 使用 dubbo 的 validation

- 在服务注册时声明
- 实现 ExceptionMapper<ApplicationException> ，处理 validation 异常
- 将实现类加入到 rest 协议的 extension 中

```xml
<dubbo:protocol name="rest" server="tomcat" port="8888" contextpath="api/v1"
              extension="com.demo.ApplicationExceptionMapper"/>
```

如果 pom 文件中添加了 resteasy-validator-provider-11 依赖，上面的异常处理不起作用

### 使用 resteay 的 validator

- 实现 ExceptionMapper<ApplicationException> ，处理 validation 异常
- 将实现类加入到 rest 协议的 extension 中

**关于 validation 使用，自行百度关键字 javax validation**
