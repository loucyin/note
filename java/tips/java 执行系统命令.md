# java 执行系统命令

## 执行脚本文件

```java
Process proc =Runtime.getRuntime().exec("exefile");
```

linux 执行当前目录下的文件要使用 `./exefile`

## 执行系统命令
- windows 下：
```java
String [] cmd={"cmd","/C","copy exe1 exe2"};
Process proc =Runtime.getRuntime().exec(cmd);
```

- linux 下
```java
String [] cmd={"/bin/sh","-c","ln -s exe1 exe2"};
Process proc =Runtime.getRuntime().exec(cmd);
```

## 执行系统命令并弹出命令窗口
- windows 下：
```java
String [] cmd={"cmd","/C","start copy exe1 exe2"};
Process proc =Runtime.getRuntime().exec(cmd);
```

- linux 下
```java
String [] cmd={"/bin/sh","-c","xterm -e ln -s exe1 exe2"};
Process proc =Runtime.getRuntime().exec(cmd);
```

## 参考链接
- [Java Runtime.exec()的使用](http://www.cnblogs.com/mingforyou/p/3551199.html)
