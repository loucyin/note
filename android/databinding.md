# android dataBinding

kotlin dataBinding

## gradle 配置

```groovy
apply plugin: 'kotlin-kapt'
android{
  ...
  dataBinding {
       enabled = true
   }
}
dendencies {
  ...
   kapt 'com.android.databinding:compiler:3.0.0-beta2'
}
```

## 简单的绑定
- 定义数据类型

  ```kotlin
  data class User(var name:String,var age:Int)
  ```

- 绑定数据

  ```kotlin
  override fun onCreate(savedInstanceState: Bundle?) {
         super.onCreate(savedInstanceState)
         val binding :ActivityMainBinding = DataBindingUtil.setContentView(this,R.layout.activity_main)
         val user = User("小明",12)
         binding.user = user
         binding.executePendingBindings()
     }
  ```

- 布局文件

  **注意数值类型要转换为 String 在绑定到 TextView 上**

  ```xml
  <?xml version="1.0" encoding="utf-8"?>
  <layout xmlns:android="http://schemas.android.com/apk/res/android">
      <data>
          <variable
              name="user"
              type="com.hzgosun.demo.User" />
      </data>
      <LinearLayout xmlns:tools="http://schemas.android.com/tools"
          android:orientation="vertical"
          android:layout_width="match_parent"
          android:layout_height="match_parent"
          tools:context="com.hzgosun.demo.MainActivity">
          <TextView
              android:id="@+id/tvName"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:text="@{user.name}"/>
          <TextView
              android:id="@+id/tvAge"
              android:layout_width="wrap_content"
              android:layout_height="wrap_content"
              android:text="@{String.valueOf(user.age)}"/>
      </LinearLayout>
  </layout>
  ```

## BaseObservable
上面的 User 对象数据变化后，不会刷新到界面上。

- 修改 User 定义，继承 BaseObservable：

  ```kotlin
  class User(name: String, age: Int) : BaseObservable() {
      @Bindable
      var name:String?= name
      set(value) {
          field = value
          notifyPropertyChanged(BR.name)
      }
      @Bindable
      var age:Int?= age
      set(value) {
          field = value
          notifyPropertyChanged(BR.age)
      }
  }
  ```

  **在 kotlin 中重写 set 时，使用 `field` 代替当前字段，需要手动赋值给 `field`，否则值不会改变**

- 使用 ObservableFields 定义 User

  ```kotlin
  class User(name: String, age: Int) {
      val name = ObservableField<String>()
      val age = ObservableInt()
      init {
          this.name.set(name)
          this.age.set(age)
      }
  }
  ```

- Observable Collections

  android 提供了 ObservableArrayList ObservableArrayMap，用于集合的同步。

## Attribute Setters
dataBinding 提供了基础的 attribute setters，如果系统提供的满足不了需求可以自定义。

炫酷的功能：
```java
@BindingAdapter({"bind:imageUrl", "bind:error"})
public static void loadImage(ImageView view, String url, Drawable error) {
   Picasso.with(view.getContext()).load(url).error(error).into(view);
}
```

```xml
<ImageView app:imageUrl="@{venue.imageUrl}"
app:error="@{@drawable/venueError}"/>
```

## Expression Language

- ??

  ```xml
  android:text="@{user.displayName ?? user.lastName}"
  ```

  等价于

  ```xml
  android:text="@{user.displayName != null ? user.displayName : user.lastName}"
  ```

- 表达式中使用字符串

  外层单引号内层双引号表示字符串

  ```xml
  android:text='@{map["firstName"]}'
  ```

  外层双引号内层反引号

  ```xml
  android:text="@{map[`firstName`]}"
  ```

- 使用 resource
  ```
  android:padding="@{large? @dimen/largePadding : @dimen/smallPadding}"
  android:text="@{@string/nameFormat(firstName, lastName)}"
  ```

## 事件处理

- 方法引用

  ```xml
  android:onClick="@{handlers::onClickFriend}
  ```

- 表达式，方法调用
  ```
  android:onClick="@{(view) -> presenter.onSaveClick(task)}"
  android:onClick="@{() -> presenter.onSaveClick(task)}"
  ```


## 其他

- dataBinding 支持 include 标签，但是不支持 merge

## 参考链接
- [Data Binding Library](https://developer.android.com/topic/libraries/data-binding/index.html)
