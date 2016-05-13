## 操作剪贴板
```java
ClipboardManager myClipboard= (ClipboardManager)getSystemService(CLIPBOARD_SERVICE);
String text = "text";
ClipData data = ClipData.newPlainText("text",text);
myClipboard.setPrimaryClip(data);
```
