# spring cloud zuul 配置自签名证书

## 场景

有一个服务对外提供 https 服务，但是使用的是自签名证书，通过 zuul 进行代理时，会出错。

错误信息：

```
com.netflix.zuul.exception.ZuulException: Forwarding error
    at org.springframework.cloud.netflix.zuul.filters.route.SimpleHostRoutingFilter.handleException(SimpleHostRoutingFilter.java:246)
    at org.springframework.cloud.netflix.zuul.filters.route.SimpleHostRoutingFilter.run(SimpleHostRoutingFilter.java:226)
```

上面的异常指向 SimpleHostRoutingFilter ：

```java
try {
  CloseableHttpResponse response = forward(this.httpClient, verb, uri, request,
      headers, params, requestEntity);
  setResponse(response);
}
catch (Exception ex) {
  throw new ZuulRuntimeException(handleException(ex));
}
```

zuul 通过 CloseableHttpClient 发起请求的时候出错了，SimpleHostRoutingFilter 是在 ZuulProxyAutoConfiguration 中注入的：

```java
@Bean
@ConditionalOnMissingBean({SimpleHostRoutingFilter.class, CloseableHttpClient.class})
public SimpleHostRoutingFilter simpleHostRoutingFilter(ProxyRequestHelper helper,
    ZuulProperties zuulProperties,
    ApacheHttpClientConnectionManagerFactory connectionManagerFactory,
    ApacheHttpClientFactory httpClientFactory) {
  return new SimpleHostRoutingFilter(helper, zuulProperties,
      connectionManagerFactory, httpClientFactory);
}

@Bean
@ConditionalOnMissingBean({SimpleHostRoutingFilter.class})
public SimpleHostRoutingFilter simpleHostRoutingFilter2(ProxyRequestHelper helper,
                             ZuulProperties zuulProperties,
                             CloseableHttpClient httpClient) {
  return new SimpleHostRoutingFilter(helper, zuulProperties,
      httpClient);
}
```

如果不提供 CloseableHttpClient 通过 simpleHostRoutingFilter 初始化，如果提供了,则通过 simpleHostRoutingFilter2 初始化。

SimpleHostRoutingFilter 中有个变量 `sslHostnameValidationEnabled` 取的是 ZuulProperties 中的 `sslHostnameValidationEnabled` 这个值默认为 `true`，可以通过配置文件或者环境变量设置 `zuul.ssl-hostname-validation-enabled=false` ，这样 zuul 就可以代理所有的 https 请求。

但是我们的目的是限制只能访问自签名的和安全的 https 请求，禁止访问其他的 https 请求，首先想到的是：

- 自定义一个 CloseableHttpClient 支持自签名和安全的 https 请求。

## 支持自签名证书的 CloseableHttpClient

CloseableHttpClient 一般通过 `HttpClientBuilder.create().build();` 创建。HttpClientBuilder 有 4 个和 ssl 相关的方法：

```java
public final HttpClientBuilder setSSLHostnameVerifier(final HostnameVerifier hostnameVerifier) {
    this.hostnameVerifier = hostnameVerifier;
    return this;
}

@Deprecated
public final HttpClientBuilder setSslcontext(final SSLContext sslcontext) {
   return setSSLContext(sslcontext);
}

public final HttpClientBuilder setSSLContext(final SSLContext sslContext) {
   this.sslContext = sslContext;
   return this;
}

public final HttpClientBuilder setSSLSocketFactory(
       final LayeredConnectionSocketFactory sslSocketFactory) {
   this.sslSocketFactory = sslSocketFactory;
   return this;
}
```

- setConnectionManager 会覆盖 setSSLSocketFactory；
- setSSLSocketFactory 会覆盖 setSSLContext；
- setSSLContext 会覆盖 setSSLHostnameVerifier。

这里选择通过 setSSLContext 的方式支持自签名 https 。

### 支持自签名证书的 SSLContext

```java
private SSLContext sslContext(X509Certificate[] trustedX509Certificates) throws Exception {
       // 获取自定义的 TrustManager
       KeyStore customTrustedStore = KeyStore.getInstance(KeyStore.getDefaultType());
       customTrustedStore.load(null, null);
       if (trustedX509Certificates.length > 0) {
           for (int i = 0; i < trustedX509Certificates.length; i++) {
               X509Certificate certificate = trustedX509Certificates[i];
               customTrustedStore.setCertificateEntry(Integer.toString(i), certificate);
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

       SSLContext sslContext = SSLContext.getInstance("TLS");
       sslContext.init(null, new TrustManager[]{customTm}, null);
       return sslContext;
   }
```

通过上面的 sslContext 生成的 CloseableHttpClient 可以访问我们的自签名的 https 服务，但是，访问不了安全的 https 服务。参考 <https://stackoverflow.com/questions/24555890/using-a-custom-truststore-in-java-as-well-as-the-default-one?noredirect=1> 的解决方案改造一下：

```java
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
             customTrustedStore.setCertificateEntry(Integer.toString(i), certificate);
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

     TrustManagerWrapper(X509TrustManager finalDefaultTm, X509TrustManager finalCustomTm) {
         this.defaultTm = finalDefaultTm;
         this.customTm = finalCustomTm;
     }

     @Override
     public X509Certificate[] getAcceptedIssuers() {
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
         try {
             customTm.checkClientTrusted(chain, authType);
         } catch (CertificateException e) {
             defaultTm.checkClientTrusted(chain, authType);
         }
     }
 }
```

这样生成的 SSLContext 就可以支持安全的 https 以及自签名的 https 了。

第一种解决方案：

- 通过配置文件或者其他什么方式，读取证书；
- 初始化 SSLContext；
- 通过 SSLContext 初始化 CloseableHttpClient 注入。

这样就会通过 `simpleHostRoutingFilter2` 生成 SimpleHostRoutingFilter ,zuul 就可以代理到自签名的 https 服务了，简单粗暴，zuul host 支持的参数，就需要自己去做适配了，zuul 支持的连接池，也需要自己去做适配，这就不好咯！

## ApacheHttpClientConnectionManagerFactory

通过 HttpClientBuilder 的文档知道：setConnectionManager 会覆盖 setSSLContext 的设置，那这样，找一下 ApacheHttpClientConnectionManagerFactory 是在什么地方注入的，然而并没有找到。找不到没问题，我们自己注入一个，但是 ApacheHttpClientConnectionManagerFactory 就一个实现类 DefaultApacheHttpClientConnectionManagerFactory ，满足不了需求，只能参考它的实现自定义一个了：

```java
static class CustomHttpClientConnectionManagerFactory implements ApacheHttpClientConnectionManagerFactory {

    SSLContext sslContext;

    @Override
    public HttpClientConnectionManager newConnectionManager(boolean disableSslValidation, int maxTotalConnections, int maxConnectionsPerRoute, long timeToLive, TimeUnit timeUnit, RegistryBuilder registryBuilder) {
        if (registryBuilder == null) {
            registryBuilder = RegistryBuilder.create().register("http", PlainConnectionSocketFactory.INSTANCE);
        }

        // 禁用 ssl 校验时
        if (disableSslValidation) {
            try {
                sslContext = SSLContext.getInstance("SSL");
                sslContext.init(null, new TrustManager[]{new DisableValidationManager()}, new SecureRandom());
                registryBuilder.register("https", new SSLConnectionSocketFactory(sslContext, NoopHostnameVerifier.INSTANCE));
            } catch (NoSuchAlgorithmException | KeyManagementException var10) {
                log.warn("Error creating SSLContext", var10);
            }
        }

        if (sslContext != null) {
            // 使用自定义的 SSLContext
            registryBuilder.register("https", new SSLConnectionSocketFactory(sslContext));
        } else {
            // 默认情况
            registryBuilder.register("https", SSLConnectionSocketFactory.getSocketFactory());
        }

        Registry<ConnectionSocketFactory> registry = registryBuilder.build();
        PoolingHttpClientConnectionManager connectionManager = new PoolingHttpClientConnectionManager(registry, (HttpConnectionFactory) null, (SchemePortResolver) null, (DnsResolver) null, timeToLive, timeUnit);
        connectionManager.setMaxTotal(maxTotalConnections);
        connectionManager.setDefaultMaxPerRoute(maxConnectionsPerRoute);
        return connectionManager;
    }
}
```

第二种方案：

- 通过配置文件或者其他什么方式，读取证书；
- 初始化 SSLContext；
- 通过 SSLContext 初始化 CustomHttpClientConnectionManagerFactory 注入。

OK 搞定收工。
