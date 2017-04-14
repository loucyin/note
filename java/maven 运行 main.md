# maven 运行 main 方法

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

## 参考链接
- [使用Maven运行Java main的3种方式](http://www.tuicool.com/articles/UJJvim)
