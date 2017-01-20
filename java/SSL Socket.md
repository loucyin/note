# java SSLSocket

## 相关文档

- [Socket](./Socket.md)
- [Https 自签名证书](./Https 自签名证书.md)

## SSLSocket

- 使用 keytool 生成 tomcat.jks，密码：111111

  ```
  keytool -genkeypair -alias tomcat -keystore tomcat.jks
  ```

- SocketServer 添加：

  ```java
  public static ServerSocket getServerSocket(int port) throws IOException {
     SSLContext context = getContext();
     if(context != null) {
         SSLServerSocketFactory factory = context.getServerSocketFactory();
         if (factory != null) {
             System.out.println("SSLServerSocket");
             return factory.createServerSocket(port);
         }
     }
     System.out.println("ServerSocket");
     return new ServerSocket(port);
  }
  public static SSLContext getContext() {
     try {
         KeyStore keystore = KeyStore.getInstance("JKS");
         keystore.load(SocketServer.class.getClassLoader().getResourceAsStream("tomcat.jks"), "111111".toCharArray());
         KeyManagerFactory kmf = KeyManagerFactory.getInstance("SunX509");
         kmf.init(keystore, "111111".toCharArray());
         SSLContext sslContext = SSLContext.getInstance("SSL");
         sslContext.init(kmf.getKeyManagers(), null, null);
         return sslContext;
     } catch (IOException | CertificateException | NoSuchAlgorithmException | UnrecoverableKeyException | KeyManagementException | KeyStoreException e) {
         e.printStackTrace();
     }
     return null;
  }
  ```

- 生成 trust.jks

  ```
  keytool -exportcert -alias tomcat -keystore tomcat.jks -file my.cer
  keytool -importcert -alias tomcat -keystore trust.jks -file my.cer
  ```

- SocketClient 添加:

  ```java
  public static Socket getSocket(String host, int port) throws IOException {
    SSLContext context = getContext();
    if (context != null) {
        SSLSocketFactory factory = context.getSocketFactory();
        if(factory != null){
            Socket socket = factory.createSocket(host,port);
            if(socket != null){
                System.out.println("create SSL socket");
                return socket;
            }
        }
    }
    System.out.println("create socket");
    return new Socket(host,port);
  }
  public static SSLContext getContext() {
    try {
        KeyStore keystore = KeyStore.getInstance("JKS");
        keystore.load(SocketServer.class.getClassLoader().getResourceAsStream("trust.jks"), "111111".toCharArray());
        TrustManagerFactory tmf = TrustManagerFactory.getInstance("SunX509");
        tmf.init(keystore);
        SSLContext sslContext = SSLContext.getInstance("SSL");
        sslContext.init(null, tmf.getTrustManagers(), null);
        return sslContext;
    } catch (IOException | CertificateException | NoSuchAlgorithmException | KeyManagementException | KeyStoreException e) {
        e.printStackTrace();
    }
    return null;
  }
  ```

## 参考链接

- [Java SSL/TLS 安全通讯协议介绍](http://www.ibm.com/developerworks/cn/java/j-lo-ssltls/)
- [java SSLContext](http://blog.csdn.net/zdx1515888659/article/details/44593967)
- [java中 SSL认证和keystore使用](http://blog.csdn.net/a495614205/article/details/12648939)
