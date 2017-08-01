# kotlin

## 三目运算符 `?:`
kotlin 里面 `?` 有个特殊的用途，可以判断对象是否为空，不为空的时候执行后面的语句。`?:` 可以在对象为空的时候设置默认值。

java 里的表示方式：
```java
int max = a>b?a:b;
```

kotlin 需要变成：
```kotlin
val max = if(a>b) a else b
```

## for 循环

- 用习惯了的 java for 循环：
```java
for (int i = 0; i < 5; i++) {
    //doSomething
}
```
- kotlin 竟然不支持了，得这么玩：
```kotlin
for (i in 0..4){
    // doSomething
}
```
- 如果增量不是 1 需要使用 `step` 瞧，下面这样的：
```kotlin
for (i in 0..4 step 2){
    // doSomething
}
```
挺洋气。

- 我要是递减怎么办 step 改成 -2 ？ `step` 只支持正整数：
```kotlin
for (i in 4 downTo 0 step 2){
    // doSomething
}
```

- `..` 可以替换成 `until`:
```kotlin
for (i in 0 until 4 step 2){
    // doSomething
}
```

## 位运算
java 里面通过 `&` `|` `^` 实现按位运算，kotlin 不支持这 3 个运算符了，得是这样的：

```kotlin
1.and(1)
1.or(1)
1.xor(1)
```

## 参考链接
- [kotlin-reference](http://kotlinlang.org/docs/reference/)
