# RxJava

## java 中，无效的 interval
timer 也会存在同样的问题。
```kotlin
fun interval(){
    Observable.interval(2,TimeUnit.SECONDS)
            .subscribe { t -> println(t) }
}
```
上面这段代码没有输出。

>When you use the default scheduler (Schedulers.computation()) the observable emits on another thread. If your program exits just after the subscribe then the observable is not given a chance to run. Put in a long sleep just after the subscribe() call and you will see it working.

也就是说程序退出后， observable 还没运行也一块退出了。

一种方法：
```kotlin
Observable.interval(2,TimeUnit.SECONDS,Schedulers.trampoline())
            .subscribe { t -> println(t) }
```
指定运行线程为 `Schedulers.trampoline()`。

## merge

```kotlin
Observable.merge(Observable.interval(1,2, TimeUnit.SECONDS,Schedulers.trampoline()).map { "hello" }, Observable.interval(2, TimeUnit.SECONDS,Schedulers.trampoline()).map { "world" })
            .subscribe(
                    { println(it) },
                    { println(it) }
            )
```
应该交替输出 hello world，但是只是输出 hello。因为两个 interval 在一个线程里，只有第一个运行结束才会运行第二个。

```kotlin
Observable.merge(Observable.interval(1,2, TimeUnit.SECONDS,Schedulers.trampoline()).map { "hello" }, Observable.interval(2, TimeUnit.SECONDS,Schedulers.newThread()).map { "world" })
            .subscribe(
                    { println(it) },
                    { println(it) }
            )
```
这样也只能输出 hello。因为 Schedulers.trampoline() 运行在主线程，interval 会一直占用线程，导致后面的 Observable 不能启动，把 Schedulers.trampoline() 放到最后一个 Observable 就可以输出 hello world。

## 参考链接
- [ RxJava 入门（四）-- interval()的坑](http://blog.csdn.net/u011033906/article/details/59753576)
