# java spi 机制

SPI : Service Provider Interface.

## spi 具体约定

> ava spi 的具体约定为:当服务的提供者，提供了服务接口的一种实现之后，在 jar 包的 META-INF/services/ 目录里同时创建一个以服务接口命名的文件。该文件里就是实现该服务接口的具体实现类。而当外部程序装配这个模块的时候，就能通过该 jar 包 META-INF/services/ 里的配置文件找到具体的实现类名，并装载实例化，完成模块的注入。 基于这样一个约定就能很好的找到服务接口的实现类，而不需要再代码里制定。 jdk 提供服务实现查找的一个工具类：java.util.ServiceLoader

## spi 应用场景

### annotation processor

  自定义 annotation 处理器的时候，需要在 META-INF/services/javax.annotation.processing.Processor 文件中注明自定义的 annotation processor。

### jdbc

- 使用 jdbc 时，需要通过 DriverManager 获取数据库连接。

  ```java
  Connection con = DriverManager.getConnection(url , username , password ) ;
  ```

- DriverManager 初始化的时候会调用静态方法 ： `loadInitialDrivers()` ；
在 loadInitialDrivers() 方法中，会通过 `ServiceLoader.load(Driver.class)` 加载所有注册过的 jdbc driver 实现类；
然后获取 `System.getProperty("jdbc.drivers")` ，如果存在就会尝试进行加载。

- com.myslq.jdbc.Driver 在初始化的时候会调用 `java.sql.DriverManager.registerDriver(new Driver());` 向 DriverManager 注册。

- getConnection 会遍历所有注册过的 drivers ，通过 drivers 获取连接。



## 参考链接
- [java中的SPI机制](http://www.cnblogs.com/javaee6/p/3714719.html)
