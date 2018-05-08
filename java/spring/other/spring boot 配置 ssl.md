# spring boot 配置 ssl

## 配置文件

application.yaml

```yaml
server:
  port: 8181
  ssl:
    key-store: classpath:tomcat.jks
    key-store-password: gc2017
    keyStoreType: JKS
    keyAlias: tomcat
```
