# ResourceBundle

## IDEA 下面的 resources 文件夹

这个文件夹是什么鬼？IDEA 官网给的解释：

> Resources include properties files, images, DTDs, and XML files. These files are located under the Classpath of your application, and are usually loaded from the Classpath by means of the following methods:

> - ResourceBundle.getBundle() for the property files and resource bundles
> - loadResourceAsStream() for icons and other files

## 乱码的中文

- 在 resources 下新建一个 string.properties 、string_zh_CN.properties
- 通过下面的代码 :

  ```java
  ResourceBundle bundle = ResourceBundle.getBundle("string");
  System.out.println(bundle.getString(key));
  ```

  打印的竟然是乱码！

## 姿势不对，需要转码

```java
public static void main(String [] args){
    ResourceBundle bundle = ResourceBundle.getBundle("string");
    Enumeration<String> enumeration = bundle.getKeys();
    while (enumeration.hasMoreElements()) {
        String key = enumeration.nextElement();
        String value = changeCoding(bundle.getString(key));
        System.out.println("key : "+key+" , value : "+value);;
    }
}
public static String changeCoding(String str){
    if(str == null){
        return null;
    }

    String string = null;
    try {
        string = new String(str.getBytes("ISO-8859-1"),"utf-8");
    } catch (UnsupportedEncodingException e) {
        e.printStackTrace();
    }
    return string;
}
```

## 神奇的大坑

在 ubuntu 16.04 下面运行 java web 项目上面的代码，输出的竟然又是乱码！

### java 的转码工具 native2ascii

使用方式：

```
native2ascii -encoding utf-8 string.properties str.properties
```

## 避免乱码

打包 web 工程的时候，把 properties 文件进行转码。

gradel 实现：

```groovy
task native2ascii{
    doLast{
        FileTree tree = fileTree(dir: 'src/main/resources')
        tree.include '**/*.properties'
        String srcDir = new File("${projectDir}/src/main/resources")
        String buildDir = new File("${projectDir}/build/resources/main")

        println(srcDir)
        println(buildDir)
        tree.each {File file ->
            String path = file.getAbsolutePath()
            String desPath = path.replace(srcDir,buildDir)
            exec{
                executable = 'native2ascii'
                args = ["-encoding","utf-8",path,desPath]
            }
        }
    }
}

processResources{
    finalizedBy "native2ascii"
}
```

- native2ascii 负责将 `src/main/resources` 下所有的 properties 文件转码到 `build/resources/main` 下面。
- processResources 任务执行完成后会执行 native2ascii 任务

## 参考链接

- [Resource Files](https://www.jetbrains.com/help/idea/2016.3/resource-files.html)
- [IntelliJ IDEA读取资源文件](http://www.linuxidc.com/Linux/2015-02/113325.htm)
- [How to use exec() output in gradle](http://stackoverflow.com/questions/11093223/how-to-use-exec-output-in-gradle)
