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

## groupby
有一系列的事件，想要开多个线程处理，怎么实现？
> 将一个Observable分拆为一些Observables集合，它们中的每一个发射原始Observable的一个子序列

```java
ExecutorService executorService = Executors.newFixedThreadPool(3);
Observable.range(1, 100)
       .groupBy(integer -> {
           return integer%3;
       })
       .subscribe(integerIntegerGroupedObservable -> {
           System.out.println(Thread.currentThread().getId());
           integerIntegerGroupedObservable.subscribeOn(Schedulers.from(executorService))
                   .subscribe(integer -> {
                       System.out.println(integer +" : "+ Thread.currentThread().getId());
                       Thread.sleep(10);
                   });
       });
executorService.shutdown();
```
range 发射完后，会生成 3 个 GroupedObservable，等到 GroupedObservable 被 subscribe 后，GroupedObservable 开始发射数据。

但是我想要的效果是：等 GroupedObservable 被 subscribe 后，range 开始发射数据，groupBy 操作符满足不了需求。只能是自己控制生成 3 个 Observable，然后去 subscribe。


## 参考链接
- [ RxJava 入门（四）-- interval()的坑](http://blog.csdn.net/u011033906/article/details/59753576)
- [ReactiveX/RxJava文档中文版](https://mcxiaoke.gitbooks.io/rxdocs/content/)
