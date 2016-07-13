## 方法
- 手机端，使用 abd shell ，或者 手机命令行。
```
# su
# setprop service.adb.tcp.port 5555
# stop adbd
# start adbd
```
- PC 端
```
adb connect 192.168.1.31:5555
```
## 参考链接
- [使用WIFI连接手机adb](http://blog.csdn.net/hustpzb/article/details/19615675)
