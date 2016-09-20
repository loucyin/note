# 演示动画

## 使用方法

```
adb shell
screenrecord --time 5 /sdcard/test.mp4
exit
adb pull /sdcard/test.mp4
```

- 进入 android shell
- 录屏 5 秒，文件保存到 /sdcard/test.mp4
- 退出 android shell
- 将文件拉到当前文件夹

## 参考链接

- [android 4.4 录屏方法](http://blog.csdn.net/gaopo_y/article/details/18310281)
- [android adb pull](http://blog.csdn.net/liuzhidong123/article/details/7971910)
