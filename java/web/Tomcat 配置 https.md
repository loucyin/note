# Tomcat 配置 https

## 相关文档

- [Https 自签名证书](./Https 自签名证书.md)

## 生成 keystore 文件

```
keytool -keystore tomcat.keystore -genkey -alias tomcat -keyalg RSA
```

- tomcat.keystore 文件名
- tomcat 别名

## 配置 8443 端口

- 修改 server.xml 加入

```xml
<Connector port="8443"
  protocol="HTTP/1.1"
  SSLEnabled="true"
  maxThreads="150"
  scheme="https"
  secure="true"                 
  clientAuth="false"
  sslProtocol="TLS"
  keystoreFile="/opt/tomcat-9.0.0.M13/tomcat.keystore"
  keystorePass="password"/>
```

- 重启 tomcat
- 访问 <https://localhost:8443>

## 修改端口为 443

环境：

```
ubuntu 16.04
tomcat 9.0 M13
```

将上面的 8443 端口改为 443 端口之后，重启 tomcat ，<https://localhost> 不能访问到网页。<br>
原因：

```
linux非root权限用户不能开启1024以下的端口。
```

解决方法：

- 通过 root 用户启动 tomcat
- 将 443 端口重定向到 8443 端口

  ```
  iptables -t nat -A PREROUTING -p tcp --dport 443 -j REDIRECT --to-port 8443
  ```

## 双向认证

### 生成客户端证书

```
keytool -genkeypair -keyalg RSA -storetype pkcs12 -keystore client.p12 -alias myKey
```

在客户端安装 client.p12

### 导出客户端证书

```
keytool -exportcert -alias myKey -file root.cer -keystore client.p12
```

### 服务端导入客户端证书

```
keytool -importcert -file my.cer -alias client -keystore tomcat.jks
```

### tomcat 配置

```
clientAuth="true"
truststoreFile="tomcat.jks"
truststorePass="111111"
```

clientAuth 默认为 false ，设置为 true 开启服务端对客户端的验证

## 参考链接

- [Configuring Java CAPS for SSL Support](https://docs.oracle.com/cd/E19509-01/820-3503/ggfen/index.html)
- [linux 不能开启443端口](http://blog.csdn.net/wenzuowei110/article/details/7871971)
- [SSL双向认证以及证书的制作和使用](http://www.2cto.com/article/201411/347512.html)
- [数字证书原理](http://www.cnblogs.com/JeffreySun/archive/2010/06/24/1627247.html)
- [用Tomcat服务器配置https双向认证过程实战](http://blog.csdn.net/xxd851116/article/details/18701731)
