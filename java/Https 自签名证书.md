# Https 自签名证书

## keytool 使用

### 生成 keystore

```
keytool -genkey -alias client -keystore client.jks
keytool -genkeypair -keyalg RSA -storetype pkcs12 -keystore hhh.p12 -alias myKey
```

> The default keystore type is jks, which is the proprietary type of the keystore implementation provided by Oracle. This is specified by the following line in the security properties file:<br>
> keystore.type=jks<br>
> To have the tools utilize a keystore implementation other than the default, you can change that line to specify a different keystore type. For example, if you have a provider package that supplies a keystore implementation for a keystore type called pkcs12, then change the line to the following:<br>
> keystore.type=pkcs12

### 查看 keystore

```
keytool -list -v -keystore client.jks
```

### 查看证书公钥

```
keytool -list -rfc -keystore root.jks
```

### keystore 导入

```
keytool -importkeystore -deststorepass changeit -destkeypass changeit -destkeystore bbb.jks -deststoretype jks -srckeystore aaa.jks -srcstoretype jks -srcstorepass changeit -alias tomcat
```

## 自签名证书

1. ### 做一根签名的根证书

  ```
  keytool -genkeypair -alias root -keystore root.jks -keyalg RSA -ext BasicConstraints:"critical=ca:true" -ext KeyUsage="keyCertSign"
  ```

2. ### 导出证书

  ```
  keytool -exportcert -alias root -file root.cer -keystore root.jks
  ```

3. ### 制作 tomcat 证书

  ```
  keytool -genkeypair -alias tomcat -keystore tomcat.jks -keyalg RSA
  ```

4. ### 用 tomcat.jks 准备好认证材料

  ```
  keytool -certreq -alias tomcat -file tomcat.csr -keystore tomcat.jks
  ```

5. ### 拿着材料去认证

  ```
  keytool -gencert -alias root -infile tomcat.csr -outfile tomcat.cer -keystore root.jks
  ```

6. ### 导入

  ```
  keytool -importcert -alias tomcat -file tomcat.cer -keystore tomcat.jks
  ```

  毛线竟然报错:

  ```
  keytool 错误: java.lang.Exception: 无法从回复中建立链
  java.lang.Exception: 无法从回复中建立链
  at sun.security.tools.keytool.Main.establishCertChain(Unknown Source)
  at sun.security.tools.keytool.Main.installReply(Unknown Source)
  at sun.security.tools.keytool.Main.doCommands(Unknown Source)
  at sun.security.tools.keytool.Main.run(Unknown Source)
  at sun.security.tools.keytool.Main.main(Unknown Source)
  ```

  需要先信任 root ，然后在导入认证后的证书：

  ```
  keytool -importcert -alias root -keystore tomcat.jks -file root.cer
  keytool -importcert -alias tomcat -file tomcat.cer -keystore tomcat.jks
  ```

7. ### 客户端安装 root.cer

  - 安装 root.cer 到受信任的证书颁发机构。
  - 客户端就会信任所有 root 签名的证书了。

## 文件格式

- ### DER-encoded certificate: .cer, .crt

  .cer/.crt是用于存放证书，它是2进制形式存放的，不含私钥。

- ### PEM-encoded message: .pem

  .pem跟crt/cer的区别是它以Ascii来表示。

- ### PKCS#12 Personal Information Exchange: .pfx, .p12

  pfx/p12用于存放个人证书/私钥，他通常包含保护密码，2进制方式

- ### PKCS#10 Certification Request: .p10

  p10是证书请求

- ### PKCS#7 cert request response: .p7r

  p7r是CA对证书请求的回复，只用于导入

- ### PKCS#7 binary message: .p7b

  p7b以树状展示证书链(certificate chain)，同时也支持单个证书，不含私钥。

## 参考链接

- [keytool](http://docs.oracle.com/javase/8/docs/technotes/tools/windows/keytool.html)
- [keytool制作CA根证书以及颁发二级证书](http://blog.csdn.net/fengwind1/article/details/52191520)
- [数字证书原理](http://www.cnblogs.com/JeffreySun/archive/2010/06/24/1627247.html)
- [密钥对,公钥,证书,私钥,jks,keystore,truststore,cer,pfx名词说明](http://blog.csdn.net/zbuger/article/details/51693101)
- [转换java keytools的keystore证书到OPENSSL的PEM格式文件](http://www.cnblogs.com/interdrp/p/4880891.html)
- [java keytool证书工具使用小结](http://www.cnblogs.com/whatlonelytear/p/5913538.html)
