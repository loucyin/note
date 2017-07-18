# java reflect
## 运行时初始化一个泛型实例
- 需求
```java
class Response<T>{
  T instance(){
    // 初始化一个实例
    return null;
  }
}
```
- 上面这种方法是没办法初始化一个实例的，由于我们获取实例的时候一般是明确知道实例的类型的，做个折中，取下面的方法。
```java
class Response<T>{
  T instance(Class<T> clazz){
    // 初始化一个实例
    return clazz.newInstance();
  }
}
```
可以通过传入一个 Class<T> 对象 clazz ，通过 clazz 新建一个实例。

- 高级点的用法，获取私有构造方法新建一个对象
```java
constructor = clazz.getDeclaredConstructor(String.class);
constructor.setAccessible(true);
T t = constructor.newInstance("hello");
```

## 参考链接
- [Java 运行时如何获取泛型参数的类型](https://unmi.cc/java-how-to-get-generic-type/)
- [Create instance of generic type in Java?](https://stackoverflow.com/questions/75175/create-instance-of-generic-type-in-java)
