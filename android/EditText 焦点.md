## 取消 EditText 自动获取焦点
在 EditText 的父控件中，添加
```xml
android:focusable="true"
android:focusableInTouchMode="true"
```
## android:windowSoftInputMode 属性
- stateUnspecified：软键盘的状态并没有指定，系统将选择一个合适的状态或依赖于主题的设置
- stateUnchanged：当这个 activity 出现时，软键盘将一直保持在上一个 activity 里的状态，无论是隐藏还是显示
- stateHidden：用户选择 activity 时，软键盘总是被隐藏
- stateAlwaysHidden：当该 Activity 主窗口获取焦点时，软键盘也总是被隐藏的
- stateVisible：软键盘通常是可见的
- stateAlwaysVisible：用户选择 activity 时，软键盘总是显示的状态
- adjustUnspecified：默认设置，通常由系统自行决定是隐藏还是显示
- adjustResize：该 Activity 总是调整屏幕的大小以便留出软键盘的空间
- adjustPan：当前窗口的内容将自动移动以便当前焦点从不被键盘覆盖和用户能总是看到输入内容的部分

## 隐藏输入法
```java
// 隐藏输入法
InputMethodManager methodManager = (InputMethodManager) getSystemService(INPUT_METHOD_SERVICE);
methodManager.hideSoftInputFromWindow(mActSearch.getWindowToken(),0);
```
