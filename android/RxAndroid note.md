## 这是个什么鬼
```java
Observable.from(folders)
    .flatMap(new Func1<File, Observable<File>>() {
        @Override
        public Observable<File> call(File file) {
            return Observable.from(file.listFiles());
        }
    })
    .filter(new Func1<File, Boolean>() {
        @Override
        public Boolean call(File file) {
            return file.getName().endsWith(".png");
        }
    })
    .map(new Func1<File, Bitmap>() {
        @Override
        public Bitmap call(File file) {
            return getBitmapFromFile(file);
        }
    })
    .subscribeOn(Schedulers.io())
    .observeOn(AndroidSchedulers.mainThread())
    .subscribe(new Action1<Bitmap>() {
        @Override
        public void call(Bitmap bitmap) {
            imageCollectorView.addImage(bitmap);
        }
    });
```
代码出处：[给 Android 开发者的 RxJava 详解](http://gank.io/post/560e15be2dca930e00da1083)  
第一感觉无从下手，一串懵逼。
### .from
> Observable.from() : 将传入的数组或 Iterable 拆分成具体对象后，依次发送出来。

### .flatMap
### .filter
### .map
### .subscribeOn
### .observeOn
### .subscribe
## 换个方法

## 参考链接
- [RxJava WiKi](https://github.com/ReactiveX/RxJava/wiki)
- [给 Android 开发者的 RxJava 详解](http://gank.io/post/560e15be2dca930e00da1083)  
- [RxJava 与 Retrofit 结合的最佳实践](http://gank.io/post/56e80c2c677659311bed9841)  
- [【译文】RxAndroid and Kotlin (Part1)](http://www.jianshu.com/p/5a730187c8ff)
- [ 深入浅出RxJava（一：基础篇](http://blog.csdn.net/lzyzsd/article/details/41833541)
- [彻底了解RxJava（一）基础知识](https://asce1885.gitbooks.io/android-rd-senior-advanced/content/che_di_le_jie_rxjava_ff08_yi_ff09_ji_chu_zhi_shi.html)
