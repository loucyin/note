## Eclipse 无法输入中文
问题： 使用 sudo 打开 eclipse 无法输入中文。
解决办法： 更改 eclipse 与 workspace 文件夹的所属用户和用户组为当前用户，使用当前用户打开。
## Android Studio 无法输入中文
解决办法： 在 androidstudio/bin/studio.sh 中添加如下内容
```bash
export XMODIFIERS=@im=fcitx
export QT_IM_MODULE=fcitx
```  
## 关闭启用触摸板
把设置里面的触摸板设置给弄没了。  
禁用：sudo rmmod psmouse   
启用：sudo modprobe psmouse  
