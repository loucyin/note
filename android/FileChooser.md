# android FileChooser  
通过inten.setType();设置MIME类型，\*代表通配符，\*/\*，表示所有类型。  
通过Intent.createChooser()来创建启动FileChooser Activity的intent。  
再利用startActivityForResult 来启动filechooser，重写onActivityResult,处理选择结果。  
Provider database路径：/data/data/com.android.providers.media/database。
```java
private static final int FILE_SELECT_CODE = 12;
private void showFileChooser()
    {
        Intent intent = new Intent(Intent.ACTION_GET_CONTENT);
        intent.setType("image/*");
        intent.addCategory(Intent.CATEGORY_OPENABLE);

        try
        {
            startActivityForResult(Intent.createChooser(intent, "Select a File to Upload"), FILE_SELECT_CODE);
        } catch (android.content.ActivityNotFoundException ex)
        {
            Toast.makeText(this, "Please install a File Manager.", Toast.LENGTH_SHORT).show();
        }
    }

    @Override
    protected void onActivityResult(int requestCode, int resultCode, Intent data)
    {
        switch (requestCode)
        {
            case FILE_SELECT_CODE:
                if (resultCode == RESULT_OK)
                {
                    // 获取文件Uri
                    Uri uri = data.getData();

                    //获取文件路径
                    getFilePath(uri);
                }
                break;
        }
        super.onActivityResult(requestCode, resultCode, data);
    }

    private String getFilePath(Uri uri)
    {
        //通过ContentResolver查询文件信息
        Cursor cursor = getContentResolver().query(uri, null, null, null, null);
        if (cursor != null)
        {
            cursor.moveToFirst();

            //文件路径放在_data列里面，获取索引
            int index = cursor.getColumnIndex(MediaStore.MediaColumns.DATA);

            //返回文件路径
            return cursor.getString(index);
        }
        return null;
    }
```
参考资料：  
[MIME参考](http://www.w3school.com.cn/media/media_mimeref.asp)  
[从Uri获取文件信息](http://developer.android.com/training/secure-file-sharing/retrieve-info.html)
[MediaColumns Reference](http://developer.android.com/reference/android/provider/MediaStore.MediaColumns.html)
