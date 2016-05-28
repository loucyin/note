## 参考链接
创建 DialogFragment
```java
public class EditNameDialogFragment extends DialogFragment  
{  


    @Override  
    public View onCreateView(LayoutInflater inflater, ViewGroup container,  
            Bundle savedInstanceState)  
    {  
        View view = inflater.inflate(R.layout.fragment_edit_name, container);  
        return view;  
    }  

}  
```
显示 DialogFragment
```java
public void showEditDialog(View view)  
    {  
        EditNameDialogFragment editNameDialog = new EditNameDialogFragment();  
        editNameDialog.show(getFragmentManager(), "EditNameDialog");  
    }  
```
- [ Android 官方推荐 : DialogFragment 创建对话框](http://blog.csdn.net/lmj623565791/article/details/37815413/)
