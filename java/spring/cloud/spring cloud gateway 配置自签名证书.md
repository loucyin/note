# spring cloud gateway 配置自签名证书

之前搞了[spring cloud zuul 配置自签名证书](./spring cloud zuul 配置自签名证书.md),顺便了解了一下 spring cloud gateway 配置自签名证书的实现方式。

## spring cloud gateway 配置项

Spring gateway 有个选项配置证书路径 `spring.cloud.gateway.httpclient.ssl.trusted-x509-certificates` 。

配置之后，自签名的证书代理不了，代理安全的 https 地址也会失败。

### 安全的 https 代理

代理安全的 https 地址失效，报错：`unable to find valid certification path to requested target`，spring cloud gateway 只按配置的证书去验证，并不去验证系统默认信任的证书。

### 自签名 https 代理

自签名证书 CN 用的是 ip 地址，和 zuul 配置用的是同一个证书，异常信息如下 ：

```
Caused by: java.security.cert.CertificateException: No subject alternative names present
    at java.base/sun.security.util.HostnameChecker.matchIP(HostnameChecker.java:137) ~[na:na]
    at java.base/sun.security.util.HostnameChecker.match(HostnameChecker.java:96) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkIdentity(X509TrustManagerImpl.java:459) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkIdentity(X509TrustManagerImpl.java:434) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkTrusted(X509TrustManagerImpl.java:291) ~[na:na]
    at java.base/sun.security.ssl.X509TrustManagerImpl.checkServerTrusted(X509TrustManagerImpl.java:141) ~[na:na]
    at java.base/sun.security.ssl.CertificateMessage$T12CertificateConsumer.checkServerCerts(CertificateMessage.java:620) ~[na:na]
```

然后百度了一波 `No subject alternative names present`，发现是证书的问题，需要在签发证书的时候，在证书里加入 alternative name。keytool 通过根证书签发证书的时候，加上 alternative name：

```sh
keytool -gencert -alias root -infile tomcat.csr -outfile tomcat.cer -keystore root.jks -ext SAN=ip:181.181.1.43
```

这样就可以解决自签名证书报错的问题了，如果自签名证书 CN 不是 ip 地址，代理通过域名进行代理，也可以解决上面的报错问题。

## 自定义

先去 GatewayAutoConfiguration 中找线索。

在 GatewayAutoConfiguration 中，注入了 `reactor.netty.http.client.HttpClient` ，sping cloud gateway 应该是通过这个 HttpClient 实现转发代理。也就是说，如果要实现自定义的效果，就需要自定义 HttpClient 然后注入，与 ssl 相关的配置代码如下:

```java
HttpClientProperties.Ssl ssl = properties.getSsl();
if (ssl.getTrustedX509CertificatesForTrustManager().length > 0
        || ssl.isUseInsecureTrustManager()) {
    httpClient = httpClient.secure(sslContextSpec -> {
        // configure ssl
        SslContextBuilder sslContextBuilder = SslContextBuilder.forClient();

        X509Certificate[] trustedX509Certificates = ssl
                .getTrustedX509CertificatesForTrustManager();
        if (trustedX509Certificates.length > 0) {
            sslContextBuilder.trustManager(trustedX509Certificates);
        } else if (ssl.isUseInsecureTrustManager()) {
            sslContextBuilder.trustManager(InsecureTrustManagerFactory.INSTANCE);
        }

        sslContextSpec.sslContext(sslContextBuilder)
                .defaultConfiguration(ssl.getDefaultConfigurationType())
                .handshakeTimeout(ssl.getHandshakeTimeout())
                .closeNotifyFlushTimeout(ssl.getCloseNotifyFlushTimeout())
                .closeNotifyReadTimeout(ssl.getCloseNotifyReadTimeout());
    });
}
```

仿照 GatewayAutoConfiguration 中 HttpClient 的初始化方式初始化一个 HttpClient 然后替换掉 ssl 相关配置代码注入。按照[spring cloud zuul 配置自签名证书](./spring cloud zuul 配置自签名证书.md)里面生成 SSLContext 的方式生成 SSLContext，再拿 SSLContext 去初始化 HttpClient。

```java
@Bean
public HttpClient httpClient(HttpClientProperties properties) {
    // configure pool resources
    HttpClientProperties.Pool pool = properties.getPool();

    ConnectionProvider connectionProvider;
    if (pool.getType() == DISABLED) {
        connectionProvider = ConnectionProvider.newConnection();
    } else if (pool.getType() == FIXED) {
        connectionProvider = ConnectionProvider.fixed(pool.getName(),
                pool.getMaxConnections(), pool.getAcquireTimeout());
    } else {
        connectionProvider = ConnectionProvider.elastic(pool.getName());
    }

    HttpClient httpClient = HttpClient.create(connectionProvider)
            .tcpConfiguration(tcpClient -> {

                if (properties.getConnectTimeout() != null) {
                    tcpClient = tcpClient.option(ChannelOption.CONNECT_TIMEOUT_MILLIS, properties.getConnectTimeout());
                }

                // configure proxy if proxy host is set.
                HttpClientProperties.Proxy proxy = properties.getProxy();

                if (StringUtils.hasText(proxy.getHost())) {

                    tcpClient = tcpClient.proxy(proxySpec -> {
                        ProxyProvider.Builder builder = proxySpec
                                .type(ProxyProvider.Proxy.HTTP)
                                .host(proxy.getHost());

                        PropertyMapper map = PropertyMapper.get();

                        map.from(proxy::getPort)
                                .whenNonNull()
                                .to(builder::port);
                        map.from(proxy::getUsername)
                                .whenHasText()
                                .to(builder::username);
                        map.from(proxy::getPassword)
                                .whenHasText()
                                .to(password -> builder.password(s -> password));
                        map.from(proxy::getNonProxyHostsPattern)
                                .whenHasText()
                                .to(builder::nonProxyHosts);
                    });
                }
                return tcpClient;
            });

    HttpClientProperties.Ssl ssl = properties.getSsl();
    if (ssl.isUseInsecureTrustManager()) {
        httpClient = httpClient.secure(sslContextSpec -> {
            // configure ssl
            SslContextBuilder sslContextBuilder = SslContextBuilder.forClient();

            X509Certificate[] trustedX509Certificates = ssl
                    .getTrustedX509CertificatesForTrustManager();
            if (trustedX509Certificates.length > 0) {
                sslContextBuilder.trustManager(trustedX509Certificates);
            } else if (ssl.isUseInsecureTrustManager()) {
                sslContextBuilder.trustManager(InsecureTrustManagerFactory.INSTANCE);
            }

            sslContextSpec.sslContext(sslContextBuilder)
                    .defaultConfiguration(ssl.getDefaultConfigurationType())
                    .handshakeTimeout(ssl.getHandshakeTimeout())
                    .closeNotifyFlushTimeout(ssl.getCloseNotifyFlushTimeout())
                    .closeNotifyReadTimeout(ssl.getCloseNotifyReadTimeout());
        });
    } else if (ssl.getTrustedX509CertificatesForTrustManager().length > 0) {
        httpClient = httpClient.secure(sslContextSpec -> {
            try {
                sslContextSpec.sslContext(
                        new JdkSslContext(sslContext(ssl.getTrustedX509CertificatesForTrustManager()),
                                true, null, IdentityCipherSuiteFilter.INSTANCE, null,
                                ClientAuth.NONE, null, false));
            } catch (Exception e) {
                e.printStackTrace();
            }
        });
    }

    return httpClient;
}
/**
  * 使用自签名证书初始化 sslContex
  *
  * @param trustedX509Certificates 证书数组
  * @return sslContext
  * @throws Exception e
  */
 private SSLContext sslContext(X509Certificate[] trustedX509Certificates) throws Exception {
     // 获取默认的 TrustManager
     X509TrustManager defaultTm = null;
     TrustManagerFactory defaultTmf = TrustManagerFactory
             .getInstance(TrustManagerFactory.getDefaultAlgorithm());
     defaultTmf.init((KeyStore) null);
     for (TrustManager tm : defaultTmf.getTrustManagers()) {
         if (tm instanceof X509TrustManager) {
             defaultTm = (X509TrustManager) tm;
             break;
         }
     }

     // 获取自定义的 TrustManager
     KeyStore customTrustedStore = KeyStore.getInstance(KeyStore.getDefaultType());
     customTrustedStore.load(null, null);
     if (trustedX509Certificates.length > 0) {
         for (int i = 0; i < trustedX509Certificates.length; i++) {
             X509Certificate certificate = trustedX509Certificates[i];
             customTrustedStore.setCertificateEntry("ca" + i, certificate);
         }
     }
     TrustManagerFactory customTmf = TrustManagerFactory
             .getInstance(TrustManagerFactory.getDefaultAlgorithm());
     customTmf.init(customTrustedStore);
     X509TrustManager customTm = null;
     for (TrustManager tm : customTmf.getTrustManagers()) {
         if (tm instanceof X509TrustManager) {
             customTm = (X509TrustManager) tm;
             break;
         }
     }

     // 使用自定义类包装
     X509TrustManager tmWrapper = new TrustManagerWrapper(defaultTm, customTm);

     SSLContext sslContext = SSLContext.getInstance("TLS");
     sslContext.init(null, new TrustManager[]{tmWrapper}, null);
     SSLContext.setDefault(sslContext);
     return sslContext;
 }

 static class TrustManagerWrapper implements X509TrustManager {
     final X509TrustManager defaultTm;
     final X509TrustManager customTm;

     private static final Logger log = LoggerFactory.getLogger(TrustManagerWrapper.class);

     TrustManagerWrapper(X509TrustManager finalDefaultTm, X509TrustManager finalCustomTm) {
         this.defaultTm = finalDefaultTm;
         this.customTm = finalCustomTm;
     }

     @Override
     public X509Certificate[] getAcceptedIssuers() {
         log.info("TrustManagerWrapper.getAcceptedIssuers ");
         X509Certificate[] defaultIssuers = defaultTm.getAcceptedIssuers();
         X509Certificate[] customIssuers = customTm.getAcceptedIssuers();
         int defaultSize = Optional.ofNullable(defaultIssuers).map(array -> array.length).orElse(0);
         int customerSize = Optional.ofNullable(customIssuers).map(array -> array.length).orElse(0);
         int length = defaultSize + customerSize;
         X509Certificate[] issuers = new X509Certificate[length];
         if (defaultSize > 0) {
             System.arraycopy(defaultIssuers, 0, issuers, 0, defaultSize);
         }

         if (customerSize > 0) {
             System.arraycopy(customIssuers, 0, issuers, defaultSize, customerSize);
         }
         return issuers;
     }

     @Override
     public void checkServerTrusted(X509Certificate[] chain,
                                    String authType) throws CertificateException {
         log.info("TrustManagerWrapper.checkServerTrusted ");
         try {
             // 先校验自签名证书
             customTm.checkServerTrusted(chain, authType);
         } catch (CertificateException e) {
             // 校验默认
             defaultTm.checkServerTrusted(chain, authType);
         }
     }

     @Override
     public void checkClientTrusted(X509Certificate[] chain,
                                    String authType) throws CertificateException {
         // 作为 server 时，校验客户端
         log.info("TrustManagerWrapper.checkClientTrusted ");
         try {
             customTm.checkClientTrusted(chain, authType);
         } catch (CertificateException e) {
             defaultTm.checkClientTrusted(chain, authType);
         }
     }
 }
```
