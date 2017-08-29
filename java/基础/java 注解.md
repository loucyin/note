# java 注解

> 注解本身不能对代码运行产生影响，但是注解可以作为一个标记，用反射之类的手段获取到这个标记后，就能对标记的内容进行处理。

## 分类
- 内置注解

  Java自带的几个Annotation，如@Override、Deprecated、@SuppressWarnings等；

- 元注解（meta-annotation）

  元注解的作用就是负责注解其他注解。

- 自定义注解

  ```java
  @Target({ METHOD, CONSTRUCTOR, FIELD })
  @Retention(RUNTIME)
  @Documented
  public @interface Inject {}
  ```

## 元注解

Java 5 中定义了 4 个元注解：
- @Target
- @Retention
- @Documented
- @Inherited

Java 8 增加了一个：
- @Repeatable

### @Target

源码说明一切：
```java
public enum ElementType {
    /** Class, interface (including annotation type), or enum declaration */
    TYPE,
    /** Field declaration (includes enum constants) */
    FIELD,
    /** Method declaration */
    METHOD,
    /** Formal parameter declaration */
    PARAMETER,
    /** Constructor declaration */
    CONSTRUCTOR,
    /** Local variable declaration */
    LOCAL_VARIABLE,
    /** Annotation type declaration */
    ANNOTATION_TYPE,
    /** Package declaration */
    PACKAGE,
    /**
     * Type parameter declaration
     *
     * @since 1.8
     */
    TYPE_PARAMETER,
    /**
     * Use of a type
     *
     * @since 1.8
     */
    TYPE_USE
}
```

### @Retention

1. RetentionPolicy.SOURCE : 这种类型的 Annotations 只在源代码级别保留，编译后就会被擦除

  > 值为 SOURCE 大都为 Mark Annotation，这类 Annotation 大都用来校验，比如 Override, Deprecated, SuppressWarnings。

2. RetentionPolicy.CLASS : 这种类型的 Annotations 编译时被保留，在 class 文件中存在，但 JVM 将会忽略

  > 编译多个Java文件时的情况：假如要编译A.java源码文件和B.class文件，其中A类依赖B类，并且B类上有些注解希望让A.java编译时能看到，那么B.class里就必须要持有这些注解信息才行。

3. RetentionPolicy.RUNTIME : 这种类型的 Annotations 将被 JVM 保留，所以他们能在运行时被 JVM 或其他使用反射机制的代码所读取和使用

### @Documented

默认情况下，javadoc 是不包括注解的，如果声明注解时，指定了 @Documented 则会被 javadoc 处理，注解会被包含在生成的文档中。

### @Repeatable

允许在同一申明类型（类，属性，或方法）的多次使用同一个注解。

### @Inherited

默认的父类中的 Annotation 并不会被继承至子类中，@Inherited 可以让子类继承父类中的 Annotation。

java8 之前，不允许同一个注解使用多次，可以这样用:
```java
public @interface Authority {
     String role();
}
public @interface Authorities {
    Authority[] value();
}
public class RepeatAnnotationUseOldVersion {
    @Authorities({@Authority(role="Admin"),@Authority(role="Manager")})
    public void doSomeThing(){
    }
}
```

java8 :
```java
@Repeatable(Authorities.class)
public @interface Authority {
     String role();
}
public @interface Authorities {
    Authority[] value();
}
public class RepeatAnnotationUseNewVersion {
    @Authority(role="Admin")
    @Authority(role="Manager")
    public void doSomeThing(){ }
}
```
## RUNTIME 注解例子

Android 中用于标记 layout 的注解。

```java
@Retention(RetentionPolicy.RUNTIME)
@Target(ElementType.TYPE)
public @interface ContentView {
    @LayoutRes int value() default 0;
}
```

```java
private void initAnnotation(){
    // 当前类是否使用 ContentView 注解
    if (getClass().isAnnotationPresent(ContentView.class)) {
        // 获取注解
        ContentView contentView = getClass().getAnnotation(ContentView.class);
        // 获取注解中的值
        mLayout = contentView.value();
    }
}
```

## apt

APT的全称是Annotation Processing Tool，诞生于Java 6版本，主要用于在编译期根据不同的注解类生成或者修改代码。APT运行于独立的JVM进程中（编译之前），并且在一次编译过程中可能会被多次调用。

TODO : 整理 RetentionPolicy.CLASS 和 RetentionPolicy.SOURCE

## 参考链接
- [Java深度历险（六）——Java注解](http://www.infoq.com/cn/articles/cf-java-annotation)
- [Java注解之Retention、Documented、Inherited介绍](http://www.jb51.net/article/55371.htm)
- [利用APT优雅的实现统一日志格式](http://blog.csdn.net/enweitech/article/details/51915532)
- [Java Code Examples for com.sun.source.util.Trees](http://www.programcreek.com/java-api-examples/index.php?api=com.sun.source.util.Trees)
- [Java Code Examples for com.sun.source.util.JavacTask](http://www.programcreek.com/java-api-examples/index.php?api=com.sun.source.util.JavacTask)
- [java annotation 中 SOURCE 和 CLASS 的区别？](https://www.zhihu.com/question/60835139)
- [Java 8新特性探究（五）重复注解（repeating annotations）](https://my.oschina.net/benhaile/blog/180932)
- [Java Annotation 及几个常用开源项目注解原理简析](http://www.trinea.cn/android/java-annotation-android-open-source-analysis/)
- [ 自定义注解之编译时注解(RetentionPolicy.CLASS)（三）—— 常用接口介绍](http://blog.csdn.net/github_35180164/article/details/52171135)
