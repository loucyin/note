# 获取 mac 地址
## android中mac地址存放路径
```
"/sys/class/net/" + networkInterfaceName + "/address";
```
android 系统mac地址存放位置：
/data/nvram/APCFG/WIFI

## 代码
```java
public static String getWifiMacAddress() {
    try {
        String interfaceName = "wlan0";
        List<NetworkInterface> interfaces = Collections.list(NetworkInterface.getNetworkInterfaces());
        for (NetworkInterface intf : interfaces) {
            if (!intf.getName().equalsIgnoreCase(interfaceName)){
                continue;
            }

            byte[] mac = intf.getHardwareAddress();
            if (mac==null){
                return "";
            }

            StringBuilder buf = new StringBuilder();
            for (byte aMac : mac) {
                buf.append(String.format("%02X:", aMac));
            }
            if (buf.length()>0) {
                buf.deleteCharAt(buf.length() - 1);
            }
            return buf.toString();
        }
    } catch (Exception ex) { } // for now eat exceptions
    return "";
}
```
## 参考链接：
- [How to get the missing Wifi MAC Address on Android M Preview?](http://stackoverflow.com/questions/31329733/how-to-get-the-missing-wifi-mac-address-on-android-m-preview)
- [如何修改安卓手机mac地址](http://jingyan.baidu.com/article/e8cdb32b4095e537042bad5d.html)
