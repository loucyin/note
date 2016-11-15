# JFinal

## 配置一个 JFinal 项目

- 创建一个 Web Dynamic Project Default Output Folder 与 Content directory 要相对应。

  > 此处的 Default out folder 必须要与 WebRoot/WEB-INF/classes 目录完全一致才可 以使用 JFinal 集成的 Jetty 来启动项目。

- 配置

## Jfinal Post 限制上传大小为 10M

解决方法：

1. 重写 JFinalConfig 中的 configConstant 方法，设置 maxPostSize。

  ```java
  @Override
   public void configConstant(Constants arg0)
   {
       arg0.setMaxPostSize(100*Const.DEFAULT_MAX_POST_SIZE);
   }
  ```

2. 通过 Controller 获取文件或者文件列表时，设置 maxPostSize。

  ```java
  mController.getFile(uploadPath, maxPostSize);
  mController.getFile(uploadPath, maxPostSize, encoding);
  mController.getFiles(uploadPath, maxPostSize);
  mController.getFiles(uploadPath, maxPostSize, encoding);
  ```

## jfinal 文件上传 error 500

使用 Jfinal 的文件上传功能要添加依赖库。

> JFinal 自身对第三方无依赖，但当需要第三方功能支持时则需要添加相应的 jar 文件<br>
> 1：cglib-nodep-3.1.jar 支持业务层 AOP，例如在 controller 中调用 enhance(...)<br>
> 2：freemarker-2.3.20.jar 支持 FreeMarker 视图类型<br>
> 3：cos-26Dec2008.jar 支持文件上传功能<br>
> 4：mysql-connector-java-5.1.20-bin.jar 支持 mysql 数据库<br>
> 5：druid-1.0.5.jar 支持 Druid 数据库连接池<br>
> 6：c3p0-0.9.5.1.jar、mchange-commons-java-0.2.10.jar 数据库连接池 c3p0<br>
> 7：jackson-core-2.5.3.jar、jackson-databind-2.4.3.jar、jackson-annotations-2.4.0.jar json与xml操作工具jackson<br>
> 8：fastjson-1.2.6.jar json 操作工具 fastjson<br>
> 9：ehcache-core-2.5.2.jar、slf4j-api-1.6.1.jar、slf4j-log4j12-1.6.1.jar 支持 EhCache，<br>
> 在使用EhCache时需要有ehcache.xml文件<br>
> 10：log4j-1.2.16.jar 支持 log4j 日志，当此文件不存在时，自动切换至 JDK Logger，<br>
> 注意，log4j需要相应的配置文件 log4j.properties，否则当log4j-1.2.16.jar 存在<br>
> 而log4j.properties 不存在时无日志输出。jdk logger 需要的logging.properties文件<br>
> 也在此目录下提供了<br>
> 11：javax.servlet.jsp.jstl-1.2.0.v201105211821.jar 与<br>
> org.apache.taglibs.standard.glassfish-1.2.0.v201112081803.jar<br>
> 支持JSP标准标签库：JSTL(JSP Standard Tag Library)<br>
> 12：sqlite-jdbc-3.7.2.jar 支持 Sqlite 数据库<br>
> 13：ojdbc6.jar Oracle Database 11g Release 2 (11.2.0.3) JDBC Driver<br>
> 14：velocity-1.7.jar、velocity-1.7-dep.jar支持 Velocity 视图<br>
> 注意：在使用tomcat开发或部署项目时，需要删除jetty-server-xxx.jar 文件，以免造成冲突
