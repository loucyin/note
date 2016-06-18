##  创建 DialogFragment
```java
public class EditNameDialogFragment extends DialogFragment  {  
    @Override  
    public View onCreateView(LayoutInflater inflater, ViewGroup container,  
            Bundle savedInstanceState)  {  
        View view = inflater.inflate(R.layout.fragment_edit_name, container);  
        return view;  
    }  
}  
```
显示 DialogFragment
```java
public void showEditDialog(View view)  {  
    EditNameDialogFragment editNameDialog = new EditNameDialogFragment();  
    editNameDialog.show(getFragmentManager(), "EditNameDialog");  
}  
```

## DialogFragmen 去掉标题
```java
@Override
public View onCreateView(LayoutInflater inflater, ViewGroup container, Bundle savedInstanceState) {
    injected = true;
    getDialog().requestWindowFeature(Window.FEATURE_NO_TITLE);
    return x.view().inject(this, inflater, container);
}
```
## DialogFragmen 全屏
```java
@Override
public void onCreate(Bundle savedInstanceState) {
super.onCreate(savedInstanceState);
setStyle(DialogFragment.STYLE_NORMAL, android.R.style.Theme_Black_NoTitleBar_Fullscreen);
}
```
简单暴力，黑的彻底，状态栏都占满。

## DialogFragmen 全屏底部显示
```java
@Override
 public Dialog onCreateDialog(Bundle savedInstanceState) {
     // 使用不带theme的构造器，获得的dialog边框距离屏幕仍有几毫米的缝隙。
     // Dialog dialog = new Dialog(getActivity());
     Dialog dialog = new Dialog(getActivity(), R.style.CustomDatePickerDialog);
     dialog.requestWindowFeature(Window.FEATURE_NO_TITLE); // must be called before set content
     dialog.setCanceledOnTouchOutside(true);

     // 设置宽度为屏宽、靠近屏幕底部。
     Window window = dialog.getWindow();
     WindowManager.LayoutParams wlp = window.getAttributes();
     wlp.gravity = Gravity.BOTTOM;
     wlp.width = WindowManager.LayoutParams.MATCH_PARENT;
     window.setAttributes(wlp);

     return dialog;
 }
```
黑色半透明样式，靠底部显示。

## 参考链接
- [ Android 官方推荐 : DialogFragment 创建对话框](http://blog.csdn.net/lmj623565791/article/details/37815413/)
- [Android 初学习 - 全屏 DialogFragment 的实现](http://blog.csdn.net/cnmilan/article/details/50546906)
- [如何在屏幕底部显示 DialogFragment 对话框，并且与屏幕等宽？](https://segmentfault.com/q/1010000003883964?_ea=407903)
