# android 反编译 与 二次打包

## apktool

### 安装

- 下载运行脚本
- 下载并重命名 apktool.jar
- 把 jar 包和脚本放到统一文件夹下
- 将运行脚本路径加入到环境变量

详细步骤见 [Install Instructions](https://ibotpeaches.github.io/Apktool/install/)

### 使用

```
# 反编译 test.apk 到 test 文件夹
apktool d test.apk
# build test 文件夹
apktool b test
```

## dex2jar

- 将 classes.dex 从 apk 中解压出来
- 执行 d2j-dex2jar classes.dex 会生成 classes-dex2jar.jar
- 可以通过 jd-gui 或者通过 idea 查看 jar 包反编译后的代码。

## 二次打包

- 修改 smali

  水平高，或者要修改的东西比较简单就直接修改 smali；
  为了安全起见，可以自己新建一个方法，编译成 smali ，插入到要修改的 smali 文件中。

- 重新打包

  ```
  apktool b test
  ```

  执行后会在 test/dist 下生成 test.apk

- 签名

  ```
  jarsigner -verbose -keystore keystore文件路径 -signedjar test-signed.apk test.apk 别名
  ```

## 参考链接
- [Android反编译工具的使用-Android Killer](http://www.cnblogs.com/common1140/p/5198460.html)
- [『Android安全』版优秀和精华帖分类索引](http://bbs.pediy.com/thread-179524.htm)
- [Install Instructions](https://ibotpeaches.github.io/Apktool/install/)
- [dex2jar](https://github.com/pxb1988/dex2jar)
- [Android命令行用已有的keystore对apk进行签名](http://blog.csdn.net/aa464971/article/details/52923571)
