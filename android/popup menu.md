## popup menu 简单用法
- 定义 showPopup 函数
```
public void showPopup(View v)
    {
        PopupMenu popup = new PopupMenu(getContext(), v);
        try
        {
            Field field = popup.getClass().getDeclaredField("mPopup");
            field.setAccessible(true);
            MenuPopupHelper mHelper = (MenuPopupHelper) field.get(popup);
            mHelper.setForceShowIcon(true);
        } catch (Exception e)
        {
            e.printStackTrace();
        }
        MenuInflater inflater = popup.getMenuInflater();
        inflater.inflate(R.menu.main, popup.getMenu());
        popup.show();
    }
```
- 在回调函数中使用，以 onClick 中的使用为例：
```
switch (view.getId())
{
    case R.id.ivMore:
    showPopup(view);
        break;
}
```
