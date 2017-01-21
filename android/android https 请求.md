# android OKHttp https 请求

## 相关文档

[Tomcat 配置 https](../java/Tomcat 配置 https.md)

## 信任自签名 CA

### 流程：

- 初始化 keystore
- 通过 keystore 获取 KeyManagers 以及 TrustManagers
- 获取 SSLContext 实例，通过 KeyManagers TrustManagers 进行初始化
- 通过 SSLContext 获取 SSLSocketFactory
- 通过 SSLSocketFactory 初始化 OkHttpClient

### 通过 ca 证书初始化 keystore ，并获取 SSLContext

myca.crt 放于 res/raw 下面。

```java
public static SSLContext getContext(Context context){
   try {
       CertificateFactory cf = CertificateFactory.getInstance("X.509");
       InputStream caInput = context.getResources().openRawResource(R.raw.myca);
       Certificate ca= cf.generateCertificate(caInput);
       caInput.close();
       String keyStoreType = KeyStore.getDefaultType();
       KeyStore keyStore = KeyStore.getInstance(keyStoreType);
       keyStore.load(null, null);
       keyStore.setCertificateEntry("ca", ca);
       TrustManagerFactory tmf = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
       tmf.init(keyStore);
       SSLContext sslContext = SSLContext.getInstance("TLS");
       sslContext.init(null,tmf.getTrustManagers(),null);
       return sslContext;
   }catch (CertificateException | NoSuchAlgorithmException | KeyStoreException | IOException | KeyManagementException e) {
       e.printStackTrace();
   }
   return null;
}
```

### 初始化 OkHttpClient

```java
public static OkHttpClient getClient(Context context){
    SSLContext sslContext = getContext(context);
    if(sslContext == null){
        return null;
    }
    SSLSocketFactory factory = sslContext.getSocketFactory();
    if(factory != null){
        Log.i(TAG, "getClient: ");
        return new OkHttpClient.Builder()
                .sslSocketFactory(factory)
                .hostnameVerifier(new HostnameVerifier() {
                    @Override
                    public boolean verify(String hostname, SSLSession session) {
                        return hostname != null && hostname.equals(session.getPeerHost());
                    }
                })
                .build();
    }
    return null;
}
```

不设置 hostnameVerifier 会报错，不能通过默认的 hostnameVerifier：

```
javax.net.ssl.SSLPeerUnverifiedException: Hostname 192.168.1.50 not verified:
```

## https 客户端认证

- 客户端证书制作，参考[Tomcat 配置 https](../java/Tomcat 配置 https.md)
- 客户端导入 ca 证书

  ```
  keytool -importcert -file ca.cer -alias ca -keystore client.p12
  ```

将 client.p12 ,放到 res/raw 下，这样 client.p12 中包含一个私钥，一个信任的 ca，可以通过 client.p12 获取 KeyManagers 以及 TrustManagers。

```java
public static SSLContext getContext(Context context) {
    try {
        KeyStore keystore = KeyStore.getInstance("PKCS12");
        keystore.load(context.getResources().openRawResource(R.raw.client), "111111".toCharArray());
        TrustManagerFactory tmf = TrustManagerFactory.getInstance(TrustManagerFactory.getDefaultAlgorithm());
        tmf.init(keystore);
        KeyManagerFactory kmf = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
        kmf.init(keystore,"111111".toCharArray());
        SSLContext sslContext = SSLContext.getInstance("TLS");
        sslContext.init(kmf.getKeyManagers(), tmf.getTrustManagers(), null);
        return sslContext;
    } catch (IOException | CertificateException | NoSuchAlgorithmException | KeyManagementException | KeyStoreException | UnrecoverableKeyException e) {
        e.printStackTrace();
    }
    return null;
}
```

## 参考链接

- [网络安全性配置](https://developer.android.com/training/articles/security-config.html)
- [Android Https相关完全解析 当OkHttp遇到Https](http://blog.csdn.net/lmj623565791/article/details/48129405)
- [Java下SSL的使用](http://blog.csdn.net/chw1989/article/details/7584995)
- [深入理解Android之Java Security第一部分](http://blog.csdn.net/innost/article/details/44081147)
- [通过 HTTPS 和 SSL 确保安全( Android 官方教程)](https://developer.android.com/training/articles/security-ssl.html)
