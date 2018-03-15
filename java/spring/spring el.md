# spring el

application.properties
```
content=Hello properties
values.list=a,b,c,d,e,f
```

通过 `@Value` 注解，将 content 值注入到代码中：

```java
@Value("${content}")
private String content;
```

properties 没有专门的 list 语法，需要通过 Spring el 将 String 转换为 list :
```java
@Value("#{'${values.list}'.split(',')}")
private List<String> valueList;
```

## 其他用法

```java
@Component("itemBean")
public class Item {
    @Value("itemA")//直接注入String
    private String name;
    @Value("10")//直接注入integer
    private int total;
    //getter and setter...
}
```

```java
@Component("customerBean")
public class Customer {
    @Value("#{itemBean}")
    private Item item;
    @Value("#{itemBean.name}")
    private String itemName;
　　//getter and setter...
}
```


## 参考链接

- [Spring3系列6-Spring 表达式语言(Spring EL)](http://www.cnblogs.com/leiOOlei/p/3543222.html)
- [Reading a List from properties file and load with spring annotation @Value
](https://stackoverflow.com/questions/12576156/reading-a-list-from-properties-file-and-load-with-spring-annotation-value)
