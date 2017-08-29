# Java 的装箱和拆箱操作

## 为什么可以划等号

Java 不能和 C++ 一样可以重载运算符，那为什么 Integer Long Folat Double 等基本数据类型对应的对象，可以使用基本数据类型直接赋值？  下面是找到的答案。

> 自动装箱和拆箱从Java 1.5开始引入，目的是将原始类型值转自动地转换成对应的对象。自动装箱与拆箱的机制可以让我们在Java的变量赋值或者是方法调用等情况下使用原始类型或者对象类型更加简单直接。

装箱，就是将基本数据类型转换为相应包装类的对象；拆箱：将对象的值赋给基本数据类型。

装箱和拆箱的操作由编译器来完成，以 Integer 为例：

```java
Integer i = 10;
int j = i;
```

上面的这段代码，编译之后，等价于下面的形式：

```java
Integer i = Integer.valueOf(10);
int j = i.intValue();
```

## True or False

浏览文章时看到这样一段代码：

```java
public static void main(String[] args) {

    Integer i1 = 100;
    Integer i2 = 100;
    Integer i3 = 200;
    Integer i4 = 200;

    System.out.println(i1 == i2);
    System.out.println(i3 == i4);
  }
```

这段代码，会输出什么结果，还没看过 Integer 的源码，猜一下吧，既然会把值自动转换为对象，两个不同的对象，应该会是 false 吧。但是既然出现两种情况，肯定不能全对全错。好吧，输出结果是：

```
true
false
```

什么情况？顺便看看Integer的源码吧。

```java
  /**
   * Returns a {@code Integer} instance for the specified integer value.
   * <p>
   * If it is not necessary to get a new {@code Integer} instance, it is
   * recommended to use this method instead of the constructor, since it
   * maintains a cache of instances which may result in better performance.
   *
   * @param i the integer value to store in the instance.
   * @return a {@code Integer} instance containing {@code i}.
   * @since 1.5
   */
  public static Integer valueOf(int i) {
    return i >= 128 || i < -128 ? new Integer(i) : SMALL_VALUES[i + 128];
  }

  /**
   * A cache of instances used by {@link Integer#valueOf(int)} and auto-boxing
   */
  private static final Integer[] SMALL_VALUES = new Integer[256];

  static {
    for (int i = -128; i < 128; i++) {
      SMALL_VALUES[i + 128] = new Integer(i);
    }
  }
```

果然是个坑。Integer 将 -128 - 127 之间的数缓存到一个对象数组中，对这些数进行装箱操作，就可以不用新建对象，顺便看了一下 Long 里面也是这样。
## 性能问题

```java
Integer sum = 0;
for(int i=1000; i<5000; i++){
  sum+=i;
}
```

一般没人这么写，只是用它来表示自动装箱潜在的性能问题。Integer 没有 ‘+=’ 运算符，它就会先拆箱然后在装箱，执行的时候就等价于下面的代码。

```java
sum = new Integer(sum.intValue + i);
```

哇偶，成功的在循环内新建了4000个对象。

## 写的舒服，但是，要用的小心

基本数据类型，和相应的包装类，虽然可以使用 ‘=’ 进行赋值，但是包装类毕竟不是基本数据类型，使用时还是要注意下面几种情况的。
- 对象的相等比较

  对象的相等比较应该用 ‘equal()’ 而不是 ‘==’ ，‘==’ 用于检查两个对象是否为同一对象，而不是它们是否相等。用错了，可是要后果自负的。

- 混乱的对象和基本数据类型

  基本数据类型不赋值，默认数值为 0 ，但是对象不赋值使用，会抛异常的。

  ```java
  private static Integer count;
  //NullPointerException on unboxing
  if( count <= 0){
    System.out.println("Count is not started yet");
  }
  ```
- 生成无用对象增加 GC 压力

  > 因为自动装箱会隐式地创建对象，像前面提到的那样，如果在一个循环体中，会创建无用的中间对象，这样会增加 GC 压力，拉低程序的性能。所以在写循环时一定要注意代码，避免引入不必要的自动装箱操作。

## 参考链接
- [Autoboxing and Unboxing](https://docs.oracle.com/javase/tutorial/java/data/autoboxing.html)
- [深入剖析Java中的装箱和拆箱](http://www.cnblogs.com/dolphin0520/p/3780005.html)
- [Java中的自动装箱与拆箱](http://www.importnew.com/15712.html)
