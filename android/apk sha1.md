## 生成 apk 签名文件
build -> Generate Signed APK
## 获取 apk 签名秘钥的 SHA1 码
使用命令行工具，查看签名文件的SHA1：
```
keytool -v -list -keystore demo.jks    
```
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
