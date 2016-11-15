# Tomcat 配置 https

## 生成 keystore 文件

```
keytool -keystore clientkeystore -genkey -alias client
```

- clientkeystore keystore 文件名
- clent alias keystore 下的别名

## 参考链接

- [Configuring Java CAPS for SSL Support](https://docs.oracle.com/cd/E19509-01/820-3503/ggfen/index.html)
