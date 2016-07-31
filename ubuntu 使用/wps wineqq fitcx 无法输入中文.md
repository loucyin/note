## 解决方法
可以直接修改desktop配置文件
```
sudo vim /usr/share/applications/wps-office-et.desktop
```
把其中的
```
Exec=/usr/bin/et %f
```
修改为:
```
Exec=env XIM="fcitx" XIM_PROGRAM="fcitx" XMODIFIERS="@im=fcitx" GTK_IM_MODULE="fcitx" QT_IM_MODULE="fcitx" /usr/bin/et %f
```
## 参考链接
- [评论内容](https://segmentfault.com/a/1190000000361008)
- [fxitx 配置](https://wiki.archlinux.org/index.php/Fcitx_%28%E7%AE%80%E4%BD%93%E4%B8%AD%E6%96%87%29#.E4.BD.BF.E7.94.A8_FCITX_.E8.BE.93.E5.85.A5.E4.B8.AD.E6.96.87)
