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
负责将一种  Observable 类型的数据转换为另一种 Observable 类型的数据。
### .filter
过滤 Observable 中的数据
### .map
将 Observable 传递的数据类型，转换成另一种类型
### .subscribeOn
选择方法的执行线程
### .observeOn
选择回调的执行线程
### .subscribe
回调方法

## 我的代码
```java
public void onGetNewsType() {
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("http://192.168.1.28:8080")
                .addConverterFactory(GsonConverterFactory.create())
                .build();
        NewsService service = retrofit.create(NewsService.class);
        Call<ResponseData<List<NewsType>>> call = service.getNewsType();
        call.enqueue(new Callback<ResponseData<List<NewsType>>>() {
            @Override
            public void onResponse(Call<ResponseData<List<NewsType>>> call, Response<ResponseData<List<NewsType>>> response) {
                ResponseData<List<NewsType>> data = response.body();
                if (data != null && data.getErrorCode() == 0) {
                    List<NewsType> list = data.getData();
                    if (list != null) {
                        mTvContent.setText(list.toString());
                    } else {
                        Log.i(TAG, "list: null ");
                    }
                } else {
                    Log.i(TAG, "ResponseData: null");
                }

            }

            @Override
            public void onFailure(Call<ResponseData<List<NewsType>>> call, Throwable t) {
                t.printStackTrace();
            }
        });
    }

    @OnClick(R.id.btnGet)
    public void onGetNewsTypeByRx() {
        Retrofit retrofit = new Retrofit.Builder()
                .baseUrl("http://192.168.1.28:8080")
                .addConverterFactory(GsonConverterFactory.create())
                .addCallAdapterFactory(RxJavaCallAdapterFactory.create())
                .build();
        NewsRxService service = retrofit.create(NewsRxService.class);
        service.getNewsType()
                .map(new Func1<ResponseData<List<NewsType>>, List<NewsType>>() {
                    @Override
                    public List<NewsType> call(ResponseData<List<NewsType>> listResponseData) {
                        if (listResponseData.getErrorCode() == 0) {
                            return listResponseData.getData();
                        }
                        return null;
                    }
                })
                .subscribeOn(Schedulers.io())
                .observeOn(AndroidSchedulers.mainThread())
                .subscribe(
                        new Action1<List<NewsType>>() {
                            @Override
                            public void call(List<NewsType> newsTypes) {
                                if (newsTypes != null) {
                                    mTvContent.setText(newsTypes.toString());
                                }
                            }
                        },
                        new Action1<Throwable>() {
                            @Override
                            public void call(Throwable throwable) {
                                throwable.printStackTrace();
                            }
                        });
    }
```
两种获取 NewsType 数据的方式。

## 参考链接
- [Awesome-RxJava](https://github.com/lzyzsd/Awesome-RxJava)  
- [RxJava WiKi](https://github.com/ReactiveX/RxJava/wiki)  
- [给 Android 开发者的 RxJava 详解](http://gank.io/post/560e15be2dca930e00da1083)  
- [RxJava 与 Retrofit 结合的最佳实践](http://gank.io/post/56e80c2c677659311bed9841)  
- [【译文】RxAndroid and Kotlin (Part1)](http://www.jianshu.com/p/5a730187c8ff)  
- [ 深入浅出RxJava（一：基础篇](http://blog.csdn.net/lzyzsd/article/details/41833541)  
- [彻底了解RxJava（一）基础知识](https://asce1885.gitbooks.io/android-rd-senior-advanced/content/che_di_le_jie_rxjava_ff08_yi_ff09_ji_chu_zhi_shi.html)  
- [Retrofit+RxJava相关](http://www.jianshu.com/p/d73153edeac9)
