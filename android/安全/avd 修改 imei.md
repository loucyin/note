# avd 修改 imei

两种方法：
- 自己编译 emulator ，比较耗费时间
- 修改 emulator 的二进制文件

emulator 路径 ：Sdk/emulator/emulator-x86.exe

修改方法：查找 `+CGSN` ，后面的 15 个数字就是 IMEI。

[HEXEdit 下载地址](http://www.hexedit.com/#)

## 参考链接
- [Device identifier of Android emulator](https://stackoverflow.com/questions/4402262/device-identifier-of-android-emulator)
