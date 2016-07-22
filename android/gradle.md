##  jniLib 使用
- 将 jni library 包放到 libs 文件夹下面
- 在 module  gradle android 下添加代码：
```
sourceSets {
      main {
          jniLibs.srcDir 'libs'
      }
  }
```
- 当 jni libraries 的方法个数大于 65535 时，gradle 编译时会报错。  
错误信息如下：
```
Gradle build finished with 784 error(s) in 37s 874ms
```
原因
> This is a bug in dex merger when the dex files that are being merged have more than 65536 methods (or strings).

  解决方法
>We can fix this by adding  
```
dexOptions { jumboMode = true }
```
In the gradle file. Don’t forget to add this all sub projects too else it may not work.


## 参考链接
- [Android studio: Gradle build finished with error](http://stackoverflow.com/questions/24690388/android-studio-gradle-build-finished-with-error)
