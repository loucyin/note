# 扫描包下的 class

指定 package ，扫描 package 下面所有的类

## 获取包路径

通过 classLoader.getResources 获取包的 URL

  ```kotlin
  val packagePath = packageName.replace(ScannerConst.PACKAGE_DIVIDER_CHAR, ScannerConst.PATH_DIVIDER_CHAR)
  val dirs = Thread.currentThread().contextClassLoader.getResources(packagePath)
  ```

URL 两种类型

### file

如果包 url 是文件类型，直接遍历文件夹下的 .class ，如果有子文件夹，需要递归子文件夹下的 .class

```kotlin
val packagePath = url.path
val dir = File(packagePath)
// 遍历 .class
```

### jar
- 将 package 中的 `.` 替换成 `/`
- 通过 `url.openConnection()` 获取到 JarURLConnection，进一步得到 JarFile
- 遍历 JarFile 的 entries()，过滤掉非当前 package 下的文件，非 .class 文件
- 将符合要求的转化成 class

```kotlin
private fun findClassesInJar(packageName: String, url: URL): List<String> {
    val packagePath = packageName.replace('.','/')
    val jarConnection = url.openConnection() as JarURLConnection
    val jar = jarConnection.jarFile
    val entries = jar.entries()
    return entries
            .toList()
            .filter { it.name.startsWith(packagePath) && it.name.endsWith(".class")}
            .map {
                it.name.replace('/','.').replace('$','.')
            }
}
```

## 内部类

- 匿名内部类的文件名：`外部类名$数字.class`
- 非匿名内部类的文件名：`外部类名$内部类名.class`
