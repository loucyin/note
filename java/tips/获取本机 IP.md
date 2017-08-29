# 获取本机 IP

## 单网卡

```java
InetAddress.getLocalHost().getAddress()
```

## 多网卡

```java
/**
 * @return 本机 IP
 * @throws SocketException 获取网络接口时的异常
 */
public static List<String> getLocalIp() throws SocketException {
    Enumeration<NetworkInterface> e = NetworkInterface.getNetworkInterfaces();
    List<String> list = new ArrayList<>();
    while (e.hasMoreElements()) {
        NetworkInterface n = e.nextElement();
        Enumeration ee = n.getInetAddresses();
        while (ee.hasMoreElements()) {
            InetAddress i = (InetAddress) ee.nextElement();
            String ip = bytesToIp(i.getAddress());
            if (ip != null) {
                list.add(ip);
            }
        }
    }
    if (list.size() == 0) {
        return null;
    }
    return list;
}
```
