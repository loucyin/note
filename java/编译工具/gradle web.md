# gradle 运行 Web 项目

## jetty 插件

gradle 默认支持 jetty 插件，但是支持版本为 jetty 6.1.25，不支持 servlet 3 。

> This plugin is deprecated and will be removed in Gradle 4.0\. Consider using the more feature-rich Gretty plugin instead.

jetty 插件被 gradle 弃用了，会在 gradle 4.0 版本中移除，推荐使用 gretty。 gretty 可以使用的 web 容器包括 ： 'jetty7', 'jetty8', 'jetty9', 'tomcat7', 'tomcat8'

## gretty

```groovy
buildscript {
    repositories {
        maven{url 'http://maven.aliyun.com/nexus/content/groups/public/'}
    }
    dependencies {
        classpath "org.akhikhl.gretty:gretty:1.4.0"
    }
}

apply plugin: 'java'
apply plugin: "org.akhikhl.gretty"

gretty {
    servletContainer = 'jetty9'
    port = 8090
    contextPath = ''
    managedClassReload=true
}
```

gretty 的简单配置。

通过 `gradle appRun` 在 web 容器中运行当前项目。

## 社区版 IDEA 调试

- 新建 remote 配置 ，Debug
- 启动项目的 Debug 模式

  ```
  gradle appRunDebug
  ```

## 参考链接

- [Gretty documentation](http://akhikhl.github.io/gretty-doc/index.html)
- [IntelliJ IDEA +gradle+gretty debug j2ee web-application](http://www.chenkaihua.com/2016/02/20/idea-webapp-remote-debug-via-gretty/)
