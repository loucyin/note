# 请求 HTTPS

## 请求 HTTPS

发送HTTPS请求， 主要思路是忽略证书验证。

> 支持的https的网站基本都是CA机构颁发的证书，默认情况下是可以信任的。<br>
> 使用自签名证书的网站，大家在使用浏览器访问的时候，一般都是报风险警告

```java
public class MyRequest {
    private static MyRequest sMyRequest = new MyRequest();

    private Retrofit mRetrofit;
    private OkHttpClient mClient;

    private MyRequest() {
        String path = PathKit.getWebRootPath() + File.separator + "res" + File.separator + "duomidai.cer";
        File file = new File(path);
        try {
            InputStream stream = new FileInputStream(file);
            initClient(stream);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }
        mRetrofit = new Retrofit.Builder()
                .baseUrl("https://pay.duomidai.com/")
                .client(mClient)
                .build();
    }

    public void initClient(InputStream... certificates) {
        String path = PathKit.getWebRootPath() + File.separator + "res" + File.separator + "duomidai.cer";

        X509TrustManager trustManager = null;
        SSLSocketFactory sslSocketFactory = null;
        try {
            File file = new File(path);
            InputStream inputStream = new FileInputStream(file);
            trustManager = trustManagerForCertificates(inputStream);
            SSLContext sslContext = SSLContext.getInstance("TLS");
            sslContext.init(null, new TrustManager[] { trustManager }, null);
            sslSocketFactory = sslContext.getSocketFactory();
        } catch (GeneralSecurityException e) {
            throw new RuntimeException(e);
        } catch (FileNotFoundException e) {
            e.printStackTrace();
        }

        mClient = new OkHttpClient.Builder()
                .addNetworkInterceptor(new Interceptor(){

                    @Override
                    public Response intercept(Chain arg0) throws IOException {
                        System.out.println(arg0.request().url().toString());
                        Response originalResponse = arg0.proceed(arg0.request());

                        return originalResponse.newBuilder()
                                .build();
                    }

                })
                .sslSocketFactory(sslSocketFactory, trustManager)
                .hostnameVerifier(new HostnameVerifier() {
                    @Override
                    public boolean verify(String hostname, SSLSession session) {
                        return true;
                    }
                })
                .build();
    }

    private X509TrustManager trustManagerForCertificates(InputStream in) throws GeneralSecurityException {
        CertificateFactory certificateFactory = CertificateFactory.getInstance("X.509");
        Collection<? extends Certificate> certificates = certificateFactory.generateCertificates(in);
        if (certificates.isEmpty()) {
            throw new IllegalArgumentException("expected non-empty set of trusted certificates");
        }

        // Put the certificates a key store.
        char[] password = "password".toCharArray(); // Any password will work.
        KeyStore keyStore = newEmptyKeyStore(password);
        int index = 0;
        for (Certificate certificate : certificates) {
            String certificateAlias = Integer.toString(index++);
            keyStore.setCertificateEntry(certificateAlias, certificate);
        }

        // Use it to build an X509 trust manager.
        KeyManagerFactory keyManagerFactory = KeyManagerFactory.getInstance(KeyManagerFactory.getDefaultAlgorithm());
        keyManagerFactory.init(keyStore, password);
        TrustManagerFactory trustManagerFactory = TrustManagerFactory
                .getInstance(TrustManagerFactory.getDefaultAlgorithm());
        trustManagerFactory.init(keyStore);
        TrustManager[] trustManagers = trustManagerFactory.getTrustManagers();
        if (trustManagers.length != 1 || !(trustManagers[0] instanceof X509TrustManager)) {
            throw new IllegalStateException("Unexpected default trust managers:" + Arrays.toString(trustManagers));
        }
        return (X509TrustManager) trustManagers[0];
    }

    private KeyStore newEmptyKeyStore(char[] password) throws GeneralSecurityException {
        try {
            KeyStore keyStore = KeyStore.getInstance(KeyStore.getDefaultType());
            InputStream in = null;
            keyStore.load(in, password);
            return keyStore;
        } catch (IOException e) {
            throw new AssertionError(e);
        }
    }

    public static <T> T getService(Class<T> service) {
        return sMyRequest.mRetrofit.create(service);
    }
}
```

## QueryMap and FiledMap

```java
public interface PayService {
    @POST("pay_jspay_normal/jspay")
    Call<ResponseBody> getPayResponse(@QueryMap Map<String, String> map);
}
```

使用上面的 Service ，在 @POST 方法中不能获取参数。在 POST 中使用参数对，需要使用 FileMap 注解：

```java
public interface PayService {
    @POST("pay_jspay_normal/jspay")
    Call<ResponseBody> getPayResponse(@FieldMap Map<String, String> map);
}
```

报错：

```
@FieldMap parameters can only be used with form encoding.
```

解决方法：

```java
public interface PayService {
    @POST("pay_jspay_normal/jspay")
    @FormUrlEncoded
    Call<ResponseBody> getPayResponse(@FieldMap Map<String, String> map);
}
```

## 参考链接

- [Chrome浏览器导出HTTPS证书](https://help.aliyun.com/knowledge_detail/40743.html)
- [Android Https相关完全解析 当OkHttp遇到Https](http://blog.csdn.net/lmj623565791/article/details/48129405)
- [Illegal Argument Exception - @Field parameters can only be used with form encoding #683](https://github.com/square/retrofit/issues/683)
- [retrofit Post服务端接受不到参数](https://segmentfault.com/q/1010000004545589)
- [Android HTTPS详解](http://www.tuicool.com/articles/6NvEZj)
