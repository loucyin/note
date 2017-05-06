# maven 基础

## 命令行运行
### 编译
exec:java 不会自动编译代码，需要手动编译
```
mvn compile
```

### 执行

- 指定 mainClass
```
mvn exec:java -Dexec.mainClass="com.vineetmanohar.module.Main"
```

- 传递参数
```
mvn exec:java -Dexec.mainClass="com.vineetmanohar.module.Main" -Dexec.args="arg0 arg1 arg2"
```

- 指定 classpath

```
mvn exec:java -Dexec.mainClass="com.vineetmanohar.module.Main" -Dexec.classpathScope=runtime
```

## maven Resources
maven 默认 resources 文件夹下的内容会编译到 classpath 下。但是，在 pom.xml 中配置 resources 节点后，resources 就不会自动编译到 classpath 下。

## 参考链接
- [使用Maven运行Java main的3种方式](http://www.tuicool.com/articles/UJJvim)
