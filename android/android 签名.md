# android 签名

## 查看签名信息

命令：

```
keytool -v -list -keystore keystore.jks
```

输出：

```
MD5: 36:01:FC:D4:BE:16:81:FC:D5:10:EA:5E:45:C3:5F:D1
SHA1: A8:34:30:EC:22:27:2B:67:82:F0:E8:65:9E:40:90:3F:5E:0C:12:EA
SHA256: F3:62:49:AE:D6:FA:C0:1A:7D:88:16:57:51:4A:9C:EE:46:76:33:5C:EF:B9:D2:47:7A:A4:F6:A7:41:06:11:94
```

## 修改签名文件密码

> 修改密码和名称，不会导致md5值和sha1值改变

## 修改别名

## 调试中使用签名文件

## 使用 apk 签名文件打包

```groovy
signingConfigs{
    myConfig{
        storeFile file("demo.jks")
        storePassword "zhide2016"
        keyAlias "demo"
        keyPassword "zhide2016"
    }
    myConfigDebug{
        storeFile file("demo_debug.jks")
        storePassword "zhide2016"
        keyAlias "demo_debug"
        keyPassword "zhide2016"
    }
}
buildTypes {
    release {
        signingConfig signingConfigs.myConfig
        minifyEnabled false
        proguardFiles getDefaultProguardFile('proguard-android.txt'), 'proguard-rules.pro'
    }
    debug {
        signingConfig signingConfigs.myConfigDebug
    }
}
```

## 生成 keystore 文件

```
keytool -keystore clientkeystore -genkey -alias client
```

- clientkeystore keystore 文件名
- clent alias keystore 下的别名

## 参考链接

- [Android 修改keystore文件密码、alias名称](http://blog.csdn.net/smz520/article/details/46788799)
- [修改Android签名证书keystore的密码、别名alias以及别名密码](http://www.cnblogs.com/exmyth/p/4722695.html)
- [Configuring Java CAPS for SSL Support](https://docs.oracle.com/cd/E19509-01/820-3503/ggfen/index.html)
