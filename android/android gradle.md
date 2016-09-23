# android gradle

## android dependence version

在 Android 开发中用到的 android support 库比较多：

```
compile 'com.android.support:appcompat-v7:24.2.1'
compile 'com.android.support:support-v4:24.2.1'
compile 'com.android.support:recyclerview-v7:24.2.1'
compile 'com.android.support:design:24.2.1'
compile 'com.android.support:gridlayout-v7:24.2.1'
compile 'com.android.support:recyclerview-v7:24.2.1'
```

它们拥有相同的版本号，一个比较蛋疼的问题就是，每次更新 SDK 版本后，这些库都会更新版本， 不更新版本，就会以警告的形式显示，看着很不舒服，一个一个的改比较麻烦。

定义一个变量：

```
def VERSION = '24.2.1'
```

重写依赖：

```
compile 'com.android.support:appcompat-v7:'+VERSION
compile 'com.android.support:support-v4:'+VERSION
compile 'com.android.support:recyclerview-v7:'+VERSION
compile 'com.android.support:design:'+VERSION
compile 'com.android.support:gridlayout-v7:'+VERSION
compile 'com.android.support:recyclerview-v7:'+VERSION
```

这样升级后，改一个 VERSION 就够了。

## jniLib 使用

- 将 jni library 包放到 libs 文件夹下面
- 在 module gradle android 下添加代码：

  ```
  sourceSets {
      main {
          jniLibs.srcDir 'libs'
      }
  }
  ```

- 当 jni libraries 的方法个数大于 65535 时，gradle 编译时会报错。<br>
  错误信息如下：

  ```
  Gradle build finished with 784 error(s) in 37s 874ms
  ```

  原因

  > This is a bug in dex merger when the dex files that are being merged have more than 65536 methods (or strings).

  解决方法

  > We can fix this by adding

  > ```
  > dexOptions { jumboMode = true }
  > ```

  > In the gradle file. Don't forget to add this all sub projects too else it may not work.

- [Android studio: Gradle build finished with error](http://stackoverflow.com/questions/24690388/android-studio-gradle-build-finished-with-error)
