# SQLite

## 依赖
```groovy
compile group: 'org.xerial', name: 'sqlite-jdbc', version: '3.20.1'
```

## jdbc 使用

SQLite jdbc url :
```
jdbc:sqlite:test.db
jdbc:sqlite:E:\test\test.db
```

```kotlin
Class.forName("org.sqlite.JDBC");
val connection = DriverManager.getConnection("jdbc:sqlite:test.db")
val statement = connection.createStatement()
val resultset = statement.executeQuery("select * from test")
while (resultset.next()){
  // 操作 resultset
}
```

##  CreateStatement 和 PrepareStatement

- 在对数据库只执行一次性存取的时侯，用 Statement 对象进行处理。PreparedStatement对象的开销比Statement大，对于一次性操作并不会带来额外的好处。
- PreparedStatement 可以写动态参数 SQL 语句，防止SQL注入
- 无论多少次地使用同一个SQL命令，PreparedStatement都只对它解析和编译一次。当使用Statement对象时，每次执行一个SQL命令时，都会对它进行解析和编译。

## 参考链接

- [JDBC中的Statement和PreparedStatement的区别](http://blog.csdn.net/jiangwei0910410003/article/details/26143977)
